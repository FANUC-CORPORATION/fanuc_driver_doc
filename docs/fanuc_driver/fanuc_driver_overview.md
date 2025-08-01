<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Overview

This repository contains the FANUC hardware interface implementation, ros2_control Scaled Joint Trajectory Controller (SJTC), ros2_control configuration files, MoveIt2 configuration files, and examples for launching motion planning with mock hardware and physical hardware.

## Repository Layout

| Folder | Description |
| :----- | :---------- |
| fanuc_controllers | Controllers specifically made for FANUC manipulators. |
| fanuc_hardware_interface | Hardware interface for FANUC robot controllers. The URDFs for FANUC robot models are provided in fanuc_hardware_interface/robot. |
| fanuc_libs | Library functions for FANUC external control protocols. |
| fanuc_msgs | Provides the messages and services supported on FANUC controllers. |
| fanuc_moveit_config | MoveIt Configuration for a FANUC robot. |
| slider_publisher | Provides scaling input to the Scaled Joint Trajectory controller.|

## Feature List

- Scaled Joint trajectory controller.
- Automatic handling of missed command packets.
- Automatic smoothing if command exceeds acceleration and jerk limits of the robot.
- GPIO controller.
- Getting and setting I/O and numeric registers.
