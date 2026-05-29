<!-- SPDX-FileCopyrightText: 2026 FANUC America Corp.
     SPDX-FileCopyrightText: 2026 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->

# How to Configure 1kHz Mode

The motion control rate of the fanuc_driver can be increased up to 1kHz.

This page describes how to configure 1kHz mode.

## System Requirements

### FANUC Robot Controller Hardware

* R-50iA series controller

### Software Options

* S647 STMO/HSPD COMM ADD-ON

```{note}

S647 has several limitations.

* Some software options cannot be installed simultaneously with S647.
* Some robot models do not support S647.

 Please contact your FANUC sales representative to verify whether your robot configuration supports S647.

```

## Configuration

### FANUC Robot Controller

1. Open the `Stream Motion` screen.
2. Set `High Speed Communication` to ENABLE.
3. Restart the robot controller.
4. Confirm that `Communication Interval` is displayed as `1` on the screen.

![stmo_hspd_screen](/_static/images/stmo_hspd_screen.png "Stream Motion setup screen")

### ROS 2

* Set `update_rate` to 1000Hz or larger in ros2_controllers.yaml.
* Set `out_cmd_interp_buff_target` to 1 in fanuc_hardware_interface/robot/\*.urdf.xacro to minimize communication latency.

```{note}
* 1kHz mode requires either a real-time kernel or a sufficiently high-performance PC to ensure stable operation. If high jitter is overserved from the PC causing the robot to drift or vibrate, please increase `out_cmd_interp_buff_target` or disable `High Speed Communication` to use a lower `update_rate`.
* On collaborative robots, speed clamping introduces additional latency. This latency can be avoided by disabling the speed clamping function by I/O. Refer to Collaborative Robot Function Manual for details.
```
