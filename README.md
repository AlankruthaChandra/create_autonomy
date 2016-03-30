# create_autonomy

**This branch is no longer active. Please check the default branch (*-devel) for latest development.**

[ROS](http://ros.org) driver for iRobot's [Create 1 & 2](http://www.irobot.com/About-iRobot/STEM/Create-2.aspx).  
This package wraps the C++ library [libcreate][libcreate], which uses iRobot's [Open Interface Specification][oi_spec].

[](* Documentation: TODO)
* ROS wiki page: TODO [](http://wiki.ros.org/create_autonomy)
[](* Code API: TODO)
* Author: [Jacob Perron](http://jacobperron.ca) ([Autonomy Lab](http://autonomylab.org), [Simon Fraser University](http://www.sfu.ca))

## Build Status

TravisCI (Ubuntu _Trusty_, ROS _Indigo_ and _Jade_) ![Build Status](https://api.travis-ci.org/AutonomyLab/create_autonomy.svg?branch=master)

## Supported Robots

| Model     | Support    |
|-----------|------------|
| Create 1  |  Yes       |
| Create 2  _(firmware >= 3.2.6)_ |  Yes       |
| Roomba Original Series | No *     |
| Roomba 400 Series |  Yes * |
| Roomba 500 Series |  No *  |
| Roomba 600 Series |  Yes * |
| Roomba 700 Series |  No *  |
| Roomba 800 Series |  No * |
| Roomba 900 Series |  No *  |

_* Not verified. Anyone who is able to verify that this driver works or not is encouraged to contact [Jacob](https://jacobperron.ca) with their findings or open an issue._

## Features

|  Feature      |  Status       |
|---------------|---------------|
|  Odometry     | Available     |
|  Safe mode    | Planned       |
|  Clean demo   | N/A       |    
|  Dock demo    | Available     |
|  Drive wheels | N/A       |
|  Drive (v,w)  | Available     |
|  Brush motors | Planned       |
|  LEDs         | Available     |
|  Digit LEDs   | Available     |
|  Sound        | Planned [#5](https://github.com/AutonomyLab/create_autonomy/issues/5)      |
|  Wheeldrop    | Planned [#6](https://github.com/AutonomyLab/create_autonomy/issues/6)      |
|  Bumpers      | Planned [#6](https://github.com/AutonomyLab/create_autonomy/issues/6)    |
|  Cliff sensor | Planned       |
|  Dirt detect  | N/A       |    
|Omni IR sensor | Available     |
| Left IR sensor| N/A       |    
|Right IR sensor| N/A       |    
|  Battery info | Available     |
|  Light sensors| Planned       |
|Create 1 support| Available    |
| **_Diagnostics_** |           |
|Corrupt Packets| Planned       |
| Phyiscal tests| Planned       |

## Install

#### Prerequisites 

* [ROS](http://wiki.ros.org/ROS/Installation) _Indigo_ or _Jade_
* Ubuntu packages: `python-rosdep`, `python-catkin-tools`

```bash
$ sudo apt-get install python-rosdep python-catkin-tools`
```

#### Compiling

1. Create a catkin workspace
``` bash
$ mkdir -p create_ws/src
$ cd create_ws
$ catkin init
```
2. Clone this repo
```bash
$ git clone https://github.com/AutonomyLab/create_autonomy.git
```
3. Install dependencies
```bash
$ rosdep update
$ rosdep install --from-paths src -i
```
4. Build
```bash
$ catkin build
```
#### USB Permissions
5. In order to connect to Create over USB, ensure your user is in the dialout group
```bash
$ sudo usermod -a -G dialout $USER
```
6. Logout and login for permission to take effect

## Running create_driver

### Setup

Connect computer to Create's 7-pin serial port
  - If using Create 1, ensure that nothing is connected to Create's DB-25 port

### Launch file

```bash
$ roslaunch create_driver create.launch [create_1:=false]
```

### Parameters

 Name       |  Description |  Default 
------------|--------------|----------
`loop_hz`   |  Frequency of internal update loop |  `10.0`
`dev`       |  Serial port |  `/dev/ttyUSB0`
`create_1`  |  Is robot model Create 1 (Roomba 400 series)?  | `false`
`latch_cmd_duration` | If this many seconds passes without receiving a velocity command the robot stops | `0.2`


### Publishers

 Topic       | Description  | Type 
-------------|--------------|------
 `odom`      |  Robot odometry according to wheel encoders | [nav_msgs/Odometry][odometry]
 `clean_button` | 'clean' button is pressed ('play' button for Create 1) | [std_msgs/Empty][empty]
 `day_button` |  'day' button is pressed | [std_msgs/Empty][empty]
 `hour_button` | 'hour' button is pressed | [std_msgs/Empty][empty]
 `minute_button` | 'minute' button is pressed | [std_msgs/Empty][empty]
 `dock_button` | 'dock' button is pressed ('advance' button for Create 1) | [std_msgs/Empty][empty]
 `spot_button` | 'spot' button is pressed | [std_msgs/Empty][empty]
 `battery/voltage` | Voltage of the robot's battery (mV) | [std_msgs/UInt16][uint16]
 `battery/current` | Current flowing through the robot's battery (mA). Positive current implies charging | [std_msgs/Int16][int16]
 `battery/charge` | The current charge of the robot's battery (mAh) | [std_msgs/UInt16][uint16]
 `battery/capacity` | The estimated charge capacity of the robot's battery (mAh) | [std_msgs/UInt16][uint16]
 `battery/charge_ratio` | Charge / capacity | [std_msgs/Float32][float32]
 `battery/temperature` | The temperature of the robot's battery (degrees Celsius) | [std_msgs/Int16][int16]
 `ir_omni` | The IR character currently being read by the omnidirectional receiver. Value 0 means no character is being received | [std_msgs/UInt16][uint16]

### Subscribers

Topic       | Description   | Type
------------|---------------|------
`cmd_vel` | Drives the robot's wheels according to a forward and angular velocity | [geometry_msgs/Twist][twist]
`debris_led` | Enable / disable the blue 'debris' LED | [std_msgs/Bool][bool]
`spot_led`   | Enable / disable the 'spot' LED | [std_msgs/Bool][bool]
`dock_led`   | Enable / disable the 'dock' LED | [std_msgs/Bool][bool]
`check_led`  | Enable / disable the 'check robot` LED | [std_msgs/Bool][bool]
`power_led`  | Set the 'power' LED color and intensity. Accepts 1 or 2 bytes, the first represents the color between green (0) and red (255) and the second (optional) represents the intensity with brightest setting as default (255) | [std_msgs/UInt8MultiArray][uint8multiarray]
`set_ascii` | Sets the 4 digit LEDs. Accepts 1 to 4 bytes, each representing an ASCII character to be displayed from left to right | [std_msgs/UInt8MultiArray][uint8multiarray]
`dock` | Activates the demo docking behaviour. Robot enters _Passive_ mode meaning the user loses control (See [OI Spec][oi_spec]) | [std_msgs/Empty][empty]
`undock` | Switches robot to _Full_ mode giving control back to the user | [std_msgs/Empty][empty]

## Commanding your Create

You can move the robot around by sending [geometry_msgs/Twist][twist] messages to the topic `cmd_vel`:

```
linear.x  (+)     Move forward (m/s)
          (-)     Move backward (m/s)
angular.z (+)     Rotate counter-clockwise (rad/s)
          (-)     Rotate clockwise (rad/s)
```
` -0.5 <= linear.x <= 0.5` and `-4.25 <= angular.z <= 4.25`

### Teleoperation

`create_tools` comes with a launch file for teleoperating Create with a joystick.

```bash
$ roslaunch create_tools joy_teleop.launch [joy_config:=xbox360]
```

There exists configuration files for the [Xbox 360 wired controller](https://www.amazon.ca/Microsoft-Xbox-360-Wired-Controller/dp/B003ZSN600) and the [Logitech F710 controller](http://gaming.logitech.com/en-ca/product/f710-wireless-gamepad). You can adapt these files for your preferred joystick configuration.

[libcreate]:  https://github.com/AutonomyLab/libcreate
[oi_spec]:  https://www.adafruit.com/datasheets/create_2_Open_Interface_Spec.pdf
[odometry]:  http://docs.ros.org/api/nav_msgs/html/msg/Odometry.html
[empty]:  http://docs.ros.org/api/std_msgs/html/msg/Empty.html
[uint16]:  http://docs.ros.org/api/std_msgs/html/msg/UInt16.html
[int16]:  http://docs.ros.org/api/std_msgs/html/msg/Int16.html
[twist]:  http://docs.ros.org/api/geometry_msgs/html/msg/Twist.html
[bool]:  http://docs.ros.org/api/std_msgs/html/msg/Bool.html
[uint8multiarray]:  http://docs.ros.org/api/std_msgs/html/msg/UInt8MultiArray.html
[float32]:  http://docs.ros.org/api/std_msgs/html/msg/Bool.html
