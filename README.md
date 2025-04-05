## WiFi Signal Strength Logger and Visualizer

This repository contains slightly modified code by Michael Wimble, the original is here:

https://github.com/wimblerobotics/wifi_logger_visualizer

All credits go to Michael Wimble.

**Note:** this software runs under **Ubuntu 24.04** and **ROS2 Jazzy**. Familiarity with these environments is required.

## Use cases

WiFi Signal Strength Logger can be used as part of a robot, or (maybe) as an independent device. 

When the package is compiled and deployed as a robot's component (_Node_), the _wifi_logger_node.py_ node subscribes to robot's source of coordinates (odometry or GPS topic) 
and logs WiFi signal data in a _sqlite3_ database.
The _wifi_visualizer_node.py_ node queries the database and publishes three "costmap" topics, which can be displayed in RViz2.

When used on a dedicated device, it could do the logging part, with the _wifi_logger_node.py_ and ROS2 GPS Node running. A Raspberry Pi can host this software.

A companion program (_HeatMapper_) is used to display accumulated data from _sqlite3_ database (or, potentially, overlay it on a Google map when surveying outdoors), 
creating a _WiFi coverage map_ (a.k.a. "_heat map_").
It can be used after the logger completed the survey, or while doing the survey.

## Build and Deployment

Install Python prerequisites, WiFi query tools and database:
```
sudo apt install python3-scipy wireless-tools sqlite3
```
This is how to build the package:
```
mkdir -p ~/wifi_ws/src
cd ~/wifi_ws/src
git clone https://github.com/slgrobotics/wifi_strength_logger.git
cd ~/wifi_ws

sudo rosdep init    # do it once, if you haven't done it before
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -r -y

cd ~/wifi_ws
colcon build
```

## Running the Nodes

This package provides three main nodes:

1. **WiFi Logger Node**: Collects WiFi signal data (link quality, signal level, bit rate) at the robot's current position and stores it in a SQLite database.
2. **WiFi Visualizer Node**: Reads data from the database and publishes cost maps showing WiFi signal strength across the environment.
3. **Heat Mapper Node**: Provides alternative visualization options including heat maps and value markers.

## Features

- Real-time WiFi signal data collection
- Automatic position tracking using odometry and transforms
- SQLite database storage for persistent data
- Three different cost map visualizations:
  - Link Quality (0-1)
  - Signal Level (-90 to -30 dBm)
  - Bit Rate (0-1000 Mb/s)
- Configurable interpolation distance
- Automatic database change detection
- Optimized for real-time performance
- Heat map visualization with matplotlib
- Value markers with color-coded signal strength
- Text annotations showing exact signal values
- Configurable scale factor for visualization

Parameters:
- `standalone`: Whether to display matplotlib visualization (true) or publish markers (false) (default: false)
- `db_path`: Path to the SQLite database file (default: **wifi_data.db** in current directory)
- `scale_factor`: Scale factor for the heat map visualization (default: 1.0)
- `text_size`: Size of the text markers in meters (default: 0.08)
- `do_publish_markers`: Whether to publish value markers (default: true). Markers are published to the **/wifi_heat_markers** topic.
- `do_publish_text_markers`: Whether to publish text markers (default: true) Markers are published to the **/wifi_heat_text_markers** topic.

The heat mapper node can operate in two modes:
1. **Standalone Mode**: Displays a matplotlib heat map with signal strength values
2. **ROS Mode**: Publishes visualization markers that can be viewed in rviz2:
   - Value markers showing signal strength with color gradients
   - Text markers displaying exact signal values
   - Topics:
     - **/wifi_heat_markers**: Color-coded value markers
     -  **/wifi_heat_text_markers**: Text annotations with signal values


To run either node separately or together use the following commands:
```
cd ~/wifi_ws
source install/setup.bash

ros2 launch wifi_logger_visualizer wifi_logger.launch.py

ros2 launch wifi_logger_visualizer wifi_visualizer.launch.py

ros2 launch wifi_logger_visualizer wifi_logger_visualizer.launch.py
```
**Note:** _sqlite3_ database file will be created in the directory where you run Logger node: 
```
-rw-r--r--  1 ros ros 102400 Apr  2 12:09 wifi_data.db
```
Refer to [database](https://github.com/slgrobotics/wifi_strength_logger/tree/main/database) folder for viewing the data.

## Useful Links

Similar software for iOS and Android (just for reference, I haven't used any of these):
- [WiFi Heatmap](https://play.google.com/store/apps/details?id=ua.com.wifisolutions.wifiheatmap&hl=en_US&pli=1)
- [ekahau WiFi Heatmaps](https://www.ekahau.com/solutions/wi-fi-heatmaps/)
- [NetSpot](https://www.netspotapp.com/wifi-heat-map/best-wifi-heatmap-software.html)  (available for [iOS](https://apps.apple.com/us/app/netspot-wifi-analyzer/id1490247223))

ROS2 Jazzy setup:
- Intel Desktop: https://github.com/slgrobotics/robots_bringup/tree/main/Docs/ROS-Jazzy
- Raspberry Pi: https://github.com/slgrobotics/robots_bringup/tree/main/Docs/Ubuntu-RPi

For _robot software_ see:
- My Robots - [Plucky, Dragger, Turtle](https://github.com/slgrobotics/robots_bringup)

## License

Please see [MIT License](https://github.com/slgrobotics/wifi_strength_logger/blob/main/LICENSE) for this repository
