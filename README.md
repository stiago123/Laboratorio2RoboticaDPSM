<h1 align="center"; style="text-align:center;">Laboratorio 1: Parte 2 Introducción ROS</h1>

<p align="center";style="font-size:50px; text-align:center; line-height : 50px;  margin-top : 0; margin-bottom : 0; "> <br> Universidad Nacional de Colombia</p>

<p align="center";style="font-size:50px; text-align:center; line-height : 50px;  margin-top : 0; margin-bottom : 0; "> <br> Robótica</p>

<p align="center";style="font-size:50px; text-align:center; line-height : 40px;  margin-top : 0; margin-bottom : 0; "> <br> Santiago Mariño (samrinoj) - Daniel Pineda (dpinedasi)</p>


<p align="center"; style="font-size:50px; text-align:center; line-height : 30px; margin-top : 0; "> <br>16 de Septiembre de 2022</p>


##Methodology


### ROS connection with MATLAB

**1.** ROS and turtlesim are initialized in ubuntu terminals with *roscore* and *rosrun turtlesim turtlesim node* codes

**2.** In MATLAB a script is created that allows the connection with ROS and sends a message to turtle1 to move it

```
%%
rosinit; %Conexi ́on con nodo maestro
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

### ROS connection with Python



	



