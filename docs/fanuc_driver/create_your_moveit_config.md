<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Create Your Own MoveIt Config

This guide shows a procedure to create a minimal MoveIt configuration package to move your FANUC robot with fanuc_driver.

```{note}
* This guide uses the following names. Replace them with ones for your robot system.
  * Robot model: R-1000iA/100F
    * robot_model: r1000ia_100f (URDF's name: r1000ia_100f.urdf.xacro)
    * robot_series: r1000ia (central part of the description package's name: fanuc_r1000ia_description)
  * MoveIt config package name: fanuc_r1000ia_moveit_config
```

## MoveIt Setup Assistant

Follow the official [MoveIt Setup Assistant Tutorial](https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html).

* Select a xacro file in the robot directory (ex. `fanuc_r1000ia_description/robot/r1000ia_100f.urdf.xacro`) as the URDF file to load as the Start Screen.
* Create a kinematic chain from base_link to flange as the planning group.
* Just push the "Auto Add ~" buttons on `ROS 2 Controllers` page and `MoveIt Controllers` page.
* Select the position command, position state, and velocity state and push the `Add interfaces` button on the `ros2_control URDF Modifications` page.
![ros2_control on setup assistant](/_static/images/moveit_setup_ros2_control.png "ros2_control on setup assistant")

## Modifications

The configuration package requires some modifications to move fanuc robots with fanuc_hardware_interface.

1. Copy `fanuc_moveit_config/config/moveit_controllers.yaml` into `your_moveit_config_package/config` directory.
2. Copy `fanuc_moveit_config/launch/fanuc_moveit_template.launch.py` into `your_moveit_config_package/launch` directory.

## Launching

```bash
# without physical robot
ros2 launch fanuc_r1000ia_moveit_config fanuc_moveit_template.launch.py robot_model:=r1000ia_100f robot_series:=r1000ia moveit_config:=fanuc_r1000ia_moveit_config use_mock:=true
# with physical robot
ros2 launch fanuc_r1000ia_moveit_config fanuc_moveit_template.launch.py robot_model:=r1000ia_100f robot_series:=r1000ia moveit_config:=fanuc_r1000ia_moveit_config use_mock:=false robot_ip:={your robot's ip address}
```
