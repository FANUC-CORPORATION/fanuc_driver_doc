<!-- SPDX-FileCopyrightText: 2025-2026 FANUC America Corp.
     SPDX-FileCopyrightText: 2025-2026 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Overview

This repository contains the FANUC hardware interface implementation, FANUC ros2_control controllers, ros2_control configuration files, MoveIt2 configuration files, and examples for launching motion planning with mock hardware and physical hardware.

## Repository Layout

| Folder | Description |
| :----- | :---------- |
| fanuc_controllers | Controllers specifically made for FANUC manipulators. |
| fanuc_forward_command | Configuration and example for using `forward_command_controller` from `ros2_controllers` with FANUC manipulators. Detailed documentation can be found in fanuc_forward_command/README.md. |
| fanuc_hardware_interface | Hardware interface for FANUC robot controllers. The URDFs for FANUC robot models are provided in fanuc_hardware_interface/robot. |
| fanuc_libs | Library functions for FANUC external control protocols. |
| fanuc_msgs | Provides the messages and services supported on FANUC controllers. |
| fanuc_moveit_config | MoveIt Configuration for a FANUC robot. |
| slider_publisher | Provides scaling input to the Scaled Joint Trajectory controller. |
| rmi_msgs | Provides the messages and services supported on fanuc_rmi_controller. |

## Feature List

### Starting with `ROS 2 driver v2.0.0` and `controller software V9.40P/77, V10.10P/26`

- Scaled joint trajectory controller.
- Automatic handling of missed command packets.
- Automatic smoothing if command exceeds acceleration and jerk limits of the robot.
- GPIO controller.
- Getting and setting I/O and numeric registers.
- Getting robot status.
- Setting payload schedule.

### Starting with `ROS 2 driver v2.0.0` and `controller software V9.40P/84, V10.10P/28`

- Automatic speed clamping if command exceeds collaborative speed limit of the robot.
- Forward position controller.
- Getting and setting position registers.
- Setting payload value and payload compensation.

### Starting with `ROS 2 driver v2.0.0` and `controller software V9.40P/85, V10.10P/32`

- Force sensor broadcaster

### Starting with `ROS 2 driver v2.1.0`

- Alarm Recovery.
- Switching motion control between ros2_control and the robot controller.

### Starting with `ROS 2 driver v2.2.0`

- Operating the robot in group 1 of the multi-group control system.

### Starting with `ROS 2 driver v2.2.0` and `controller software V9.40P/89, V10.10P/33`

- Getting UI and UO through the `/get_bool_io` service.

### Starting with `ROS 2 driver v2.3.0`

- FANUC RMI controller
