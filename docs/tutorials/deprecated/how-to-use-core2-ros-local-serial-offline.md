---
title: How to use CORE2-ROS local serial offline
sidebar_label: 3. How to use CORE2-ROS local serial offline
id: how-to-use-core2-ros-local-serial-offline
---

Local serial is an UART communication between CORE2 and SBC in CORE2-ROS. By default, this serial interface is used for communication with Husarion Cloud and allows you to use all functionalities of Cloud. If you don't want the connection with cloud, or just need this serial interface for another purposes, you can change its behavior. In this short tutorial we will show you how to disable the communication with Cloud.

First we will show how to run communication normaly and point all needed changes.

## Default communication ##

Few lines that should including your code flashed on CORE2:

```cpp
void hMain()
{
    //RPi.init(230400); // uncomment if UpBoard
    platform.begin(&RPi);
    nh.getHardware()->initWithDevice(&platform.LocalSerial);
    nh.initNode();
    nh.subscribe(sub);
// ...
}
```
or if you using UpBoard:
```cpp
void hMain()
{
    RPi.init(230400); // uncomment if using UpBoard
    //platform.begin(&RPi); // comment this line
    nh.getHardware()->initWithDevice(&RPi); // change here
    nh.initNode();
    nh.subscribe(sub);
// ...
```

After flashing your code you just have to connect with your SBC (ssh, remote desktop client) and run in first terminal window: 
```
roscore
```
and in another window run:
```
/opt/husarion/tools/rpi-linux/ros-core2-client /dev/ttyCORE2
```

Now all of included topics are avaleiable in ROS. You can check a list od them by taping:
```
rostopic list
```

## Offline communication ##

For start you have to open a console and disable Husarion service:
```
systemctl disable husarnet-configurator
```
and reboot device (you can reenable it later with `systemctl enable husarnet-configurator`).

We also have to change few lines in CORE2 code:

```cpp
void hMain()
{
    //platform.begin(&RPi); // comment this line
    nh.getHardware()->initWithDevice(&RPi); // change here
    nh.initNode();
    nh.subscribe(sub);
// ...
```

Next as always we have to open a terminal and run:
```
roscore
```
and in second terminal we run depending on the model of our SBC.

On Asus Tinker Board:
```
/opt/husarion/tools/rpi-linux/ros-core2-client /dev/ttyS1
```
On Raspberry Pi:
```
/opt/husarion/tools/rpi-linux/ros-core2-client /dev/serial0
```
On UpBoard:
```
/opt/husarion/tools/rpi-linux/ros-core2-client /dev/ttyS4 _baud:=230400
```

