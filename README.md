## WiFi Signal Strength Logger

This repository contains slightly modified code by Michael Wimble, the original is here:

https://github.com/wimblerobotics/Sigyn/tree/main/wifi_signal_visualizer

All credits go to Michael Wimble and his work on [Sigyn](https://github.com/wimblerobotics/Sigyn) robot, which has been my inspiration and the source of shameless borrowing.

## Use cases

My version of WiFi Signal Strength Logger can be used as part of a robot, or as an independent device. 

When the package is compiled and deployed as a robot's component (_Node_), it subscribes to robot's source of coordinates (odometry or GPS topic) and logs WiFi signal data in a _sqlite3_ database.

When used on a dedicated device, it does the same - but that device should have ROS2 GPS Node running.

A companion program (_Visualizer_) is used to overlay accumulated data from _sqlite3_ on a Google map.

## Build and Deployment

