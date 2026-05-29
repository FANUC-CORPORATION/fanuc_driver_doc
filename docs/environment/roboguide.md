<!-- SPDX-FileCopyrightText: 2025-2026 FANUC America Corp.
     SPDX-FileCopyrightText: 2025-2026 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->

# ROBOGUIDE

The FANUC ROS 2 Driver can control virtual robots in ROBOGUIDE.

## ROBOGUIDE's Plug-Ins and software options

The fanuc_driver works with HandlingPRO without any additional ROBOGUIDE Plug-Ins.

When you create a workcell with a robot, install the required software options into the virtual robot controller.

1. Select `Create Robot using Robot Creation Wizard` when creating a workcell.
![ROBOGUIDE: Create a workcell](/_static/images/roboguide_option_01.png "ROBOGUIDE: Create a workcell")
2. Select the required software options in the wizard.
![ROBOGUIDE: Select software options](/_static/images/roboguide_option_02.png "ROBOGUIDE: Select software options")

## Robot Network Configuration

You do not have to configure the network settings of the virtual robot because ROBOGUIDE automatically assigns the port to the localhost of the host computer by default.

If the checkbox "Run virtual robots with loopback address" is checked, uncheck it to make virtual robots visible to the network.
![ROBOGUIDE loopback setting](/_static/images/roboguide_loopback.png "ROBOGUIDE loopback setting")

## Windows Firewall

You should disable the Windows Firewall to establish a UDP connection.

## WSL2 on the Same Windows Machine

Set WSL2's `networkingMode` to `Mirrored` instead of `NAT`, otherwise, UDP packets sent to ROBOGUIDE on the same machine will be blocked.

## CRX's DI/DO setting

The CRX series uses `example_gpio_config.yaml`, which assigns DI/DO[101-112]. ROBOGUIDE's default DI/DO assignment splits the DI/DO indices, causing the GPIO configuration packet to fail.

Please perform one of the following procedures to avoid this failure:

### Using `fanuc_gpio_config_small.yaml`

You can avoid the failure by using a smaller gpio configuration file.

```bash
ros2 launch fanuc_moveit_config fanuc_moveit.launch.py gpio_config_path:=config/example_gpio_config_small.yaml
```

### Using `fanuc_gpio_config.yaml`

If you want to keep using the default one, configure the virtual robot controller's DI/DO.

1. Display the DO screen from the I/O screen.
![How to open the DO screen](/_static/images/roboguide_dio_01.png)
2. Display the DO's configuration screen.
![How to open the DO configuration screen](/_static/images/roboguide_dio_02.png)
3. Delete the assignment for DO[105-144].
![How to delete the DO assignment](/_static/images/roboguide_dio_03.png)
4. Change the assignment range from 104 to 144 so that DO[65-144] are assigned continuously.
![How to assign DOs](/_static/images/roboguide_dio_04.png)
5. Perform the same procedure on the DI screen.
6. Repower the controller.
