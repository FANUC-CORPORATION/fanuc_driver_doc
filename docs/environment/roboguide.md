<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->

# ROBOGUIDE

The FANUC ROS 2 Driver can control virtual robots in ROBOGUIDE.

## Robot Network Configuration

You do not have to configure the network settings of the virtual robot because ROBOGUIDE automatically assigns the port to the localhost of the host computer by default.

If the checkbox "Run virtual robots with loopback address" is checked, uncheck it to make virtual robots visible to the network.
![ROBOGUIDE loopback setting](/_static/images/roboguide_loopback.png "ROBOGUIDE loopback setting")

## Windows Firewall

You should disable the Windows Firewall to establish a UDP connection.

## WSL2 on the Same Windows Machine

Set WSL2's `networkingMode` to `Mirrored` instead of `NAT`, otherwise, UDP packets sent to ROBOGUIDE on the same machine will be blocked.
