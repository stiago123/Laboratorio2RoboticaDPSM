<h1 align="center"; style="text-align:center;">Laboratory 2: Introduction to ROS</h1>

<p align="center";style="font-size:50px; text-align:center; line-height : 50px;  margin-top : 0; margin-bottom : 0; "> <br> Universidad Nacional de Colombia</p>

<p align="center";style="font-size:50px; text-align:center; line-height : 50px;  margin-top : 0; margin-bottom : 0; "> <br> Robotics</p>

<p align="center";style="font-size:50px; text-align:center; line-height : 40px;  margin-top : 0; margin-bottom : 0; "> <br> Santiago Mariño (samrinoj) - Daniel Pineda (dpinedasi)</p>


<p align="center"; style="font-size:50px; text-align:center; line-height : 30px; margin-top : 0; "> <br>16 de Septiembre de 2022</p>


## Methodology


### ROS connection with MATLAB

**1.** ROS and turtlesim are initialized in ubuntu terminals with *roscore* and *rosrun turtlesim turtlesim node* codes

**2.** In MATLAB a script is created that allows the connection with ROS and sends a message to turtle1 to move it

```
%%
rosinit; %Conexion con nodo maestro
%%
velPub = rospublisher('/turtle1/cmd_vel','geometry_msgs/Twist'); %Creaci ́on publicador
velMsg = rosmessage(velPub); %Creaci ́on de mensaje
%%
velMsg.Linear.X = 1; %Valor del mensaje
send(velPub,velMsg); %Envio
pause(1)
``` 

The code used can be verified with the graphical interface of ROS, it is observed how the turtle advances one unit of its relative x axis

**3.** The code that allows you to subscribe to the pose topic of turtle1 is added, then with *lattest message* the last message obtained is captured:

 ```
a=rossubscriber("/turtle1/pose","turtlesim/Pose");
PosX=a.LatestMessage.X
 ```
The mentioned subscription and a PosX variable to show the last message on the screen are highlighted.

**4.** With the Absolut Teleport service you can send data to modify the pose of turtle1. To do this, a client is created that accesses the service, with this client the message is created with the values ​​of X, Y and Angle.

 ```
Client = rossvcclient('/turtle1/teleport_absolute');
msg=rosmessage(Client);
msg.X=3;
msg.Y=3;
msg.Theta=2;
response=call(Client,msg);
pause(1)
 
 ``` 

**5.** To end the connection of the Turtlesim node with MATLAB, use the *rosshutdown* command.
```console
%%
rosshutdown
```

## ROS connection with Python

### Objectives

Write a code that allows to operate the turtle1 with the keyboard so that they fulfill the following functions:
- Move forward and backward with the W and S keys
- Turn clockwise and counterclockwise with keys D and A
- Return to its central position and orientation with the R key
- Turn 180 ° with the SPACE key

## Methodology

**1.** The fundamental function to move turtle 1 that allows us to modify the speed and direction of its movement and its duration (cases of the keys W,A,S,D):
```
def pubVel(vel_x, ang_z, t):
    pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
    rospy.init_node('velPub', anonymous=False)
    vel = Twist()
    vel.linear.x = vel_x
    vel.angular.z = ang_z
    #rospy.loginfo(vel)
    endTime = rospy.Time.now() + rospy.Duration(t)
    while rospy.Time.now() < endTime:
        pub.publish(vel)
```
**2.** For the cases of R and SPACE, it is necessary to use a function that allows changing the turtle's pose instantly, as seen in the case of matlab, this is done thanks to the absolute teleport and relative teleport service:

The absolute teleport function is used because it is necessary to change the pose to an absolute position no matter where the turtle is located.
```
def teleport(x, y, ang):
    rospy.wait_for_service('/turtle1/teleport_absolute')
    try:
        teleportA = rospy.ServiceProxy('/turtle1/teleport_absolute', TeleportAbsolute)
        resp1 = teleportA(x, y, ang)
        #print('Teleported to x: {}, y: {}, ang: {}'.format(str(x),str(y),str(ang)))
    except rospy.ServiceException as e:
        print(str(e))
```
For the 180° turn, as it is a change of pose that can happen for any position of the turtle in which it is, it is necessary to use a relative teleport
```
def teleportRel(x, ang):
    rospy.wait_for_service('/turtle1/teleport_relative')
    try:
        teleportR = rospy.ServiceProxy('/turtle1/teleport_relative', TeleportRelative)
        resp1 = teleportR(x, ang)
        #print('Relatively teleported to x: {}, ang: {}'.format(str(x),str(ang)))
    except rospy.ServiceException as e:
        print(str(e))
```
**3.** Finally, each of the cases is set with its corresponding function and input values.

```
def on_press(key):
    key =getkey()
    if key == 'a':
        pubVel(0,1,0.5)
    elif key == 'd':
        pubVel(0,-1,0.5)
    elif key == 'w':
        pubVel(1,0,0.5)
    elif key == 's':
        pubVel(-1,0,0.5)    
    elif key == 'd':
        pubVel(0,-1,0.5)
    elif key == 'r':
        teleport(5.5,5.5,0)
    elif key == ' ':
        teleportRel(0,mt.pi)
    elif key == 'c':
        clear()
```
To do this, the getkey python library is used.

**4.** The script made (*myTeleopKey.py*) is included in the CMakeLists.txt file.
 
 **5.** In a terminal enter the command *catkin build* to save the changes made
 
 **6.** The code is tested by running Turtlesim, surfacing and running the file created with the command *rosrun hello turtle myTeleopKey.py*.

## Analysis and results

### ROS connection with MATLAB

* The connection of ROS with matlab and the change of the position of the turtle is evidenced
* It is not possible to subscribe twice in a row to a node, because if you want to check for example *pose* it is recommended to execute the command again to display the last message obtained, since being subscribed and subscribing again will produce an error
* It is necessary to create a client to make a publication, this is seen in the line of code shown in the previous section.

 ### ROS connection with Python 

* We can see that both with the execution of the myTeleopKey code and with the catkin workspace we can control the movement and pose of the turtle

* Although the speed function allows us to determine the angle or direction that the turtle has with its angular speed and time, but using multiples of pi to change this angle we realize that this has a precision error and change the angle given a teleport relative has no accuracy errors.

 ## Conclusions


* Working with nodes, being able to subscribe and publish things is a great work advantage that ROS provides in motion control, after understanding the logic and the services that exist, it easily allows the performance of the robotics.

* It was verified how MATLAB is a useful tool to have interactions with the ROS nodes, in such a way that it was possible to connect, disconnect the MATLAB node and make publications and subscriptions.

* An ease of connection was generally experienced in MATALB, although there was not much literature to conduct research. Compared with python it has slower execution times, python feels more scalable due to the number of libraries it has to contribute, but it is necessary to know each function properly to make connection and later publications subscriptions.

## Additional

We tried to add an additional function with the keys using the clean service which allows to clean the trace lines of the movement of the turtle

```
def clear():
    rospy.wait_for_service('clear')
    try:
        clear_bg = rospy.ServiceProxy('clear', srv.Empty)
    except rospy.ServiceException as e:
        print(str(e))

```
The problem that was had is that this is not a totally turtlesim service, so the use of the service is not carried out in the same way as with the teleportabsolute.








	



