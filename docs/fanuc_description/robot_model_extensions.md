<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Extending a Robot Model

## Xacro conventions

We use the following xacro naming conventions to improve reusability of the files:

- `*.urdf.xacro`: This is a top-level xacro file your launch file will use to generate the full robot description (as a URDF).
- `*_urdf_macro.xacro`: This file only contains `xacro:macro` definitions that generate URDF for use in `*_urdf.xacro` or other `*_urdf_macro.xacro` files.
- `*_ros2_control_macro.xacro`: Similarly, this file only contains `xacro:macro` definitions that generate `ros2_control` tags for a URDF.

## Example top-level xacro composition

Below are examples show how to reuse the provided descriptions files.
The tree structure reflects the `xacro:include` hierarchy of xacro files.

### For URDF visualization

`package://fanuc_crx_description/crx10ia.urdf.xacro` provides an example of basic URDF formation without extensions for ros2_control.

```text
├── package://fanuc_crx_description/crx10ia.urdf.xacro
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
```

### For physical hardware

`package://fanuc_hardware_interface/crx10ia.urdf.xacro` provides an example of using the `fanuc_crx_description` `xacro:macro` definitions and augmenting them with `ros2_control` tags.

```text
├── package://fanuc_hardware_interface/crx10ia.urdf.xacro
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
│   ├── package://fanuc_hardware_interface/crx_physical_ros2_control_macro.xacro
```

### For mock hardware

`package://fanuc_hardware_interface/crx10ia.urdf.xacro` accepts a xacro argument `use_mock` the selects which of the packaged ros2_control `xacro:macro` definition files to use.
When `use_mock` is set to true, the `crx_mock_ros2_control_macro.xacro` file is selected instead of `crx_physical_ros2_control_macro.xacro`.

```text
├── package://fanuc_hardware_interface/crx10ia.urdf.xacro
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
│   ├── package://fanuc_hardware_interface/crx_mock_ros2_control_macro.xacro
```

### For your custom simulation

This file structure enables simple reuse of CRX URDF macros with custom ros2_control tags in the case you leverage other simulators.
In this case, your top-level xacro would expose additional xacro arguments to select the additional simulator and use the corresponding ros2_control `xacro:macro` definition file.

```text
├── package://MY_WORKCELL/crx10ia.urdf.xacro
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
│   ├── package://CUSTOM_SIMULATOR/crx_CUSTOM_ros2_control_macro.xacro
```

## For your custom workcell with a gripper

Finally, as you incorporate additional URDF and ros2_control definitions to your robot, we recommend a file structure similar to the below.

```text
├── package://MY_WORKCELL/MY_ROBOT_GEN1.urdf.xacro # Defines the robot base geometry
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
│   ├── package://fanuc_hardware_interface/crx_physical_ros2_control_macro.xacro
│   ├── package://GRIPPER_VENDOR_DESCRIPTION/GRIPPER_urdf_macro.xacro
│   ├── package://GRIPPER_VENDOR_DESCRIPTION/GRIPPER_physical_ros2_control_macro.xacro
```

This will improve composability as the robot is refined.

```text
├── package://MY_WORKCELL/MY_ROBOT_GEN2.urdf.xacro # Defines the robot base geometry
│   ├── package://fanuc_crx_description/crx10ia_urdf_macro.xacro
|       ├── package://fanuc_crx_description/fanuc_common_urdf_macro.xacro
│   ├── package://fanuc_hardware_interface/crx_physical_ros2_control_macro.xacro
│   ├── package://GRIPPER_VENDOR_2_DESCRIPTION/GRIPPER_urdf_macro.xacro
│   ├── package://GRIPPER_VENDOR_2_DESCRIPTION/GRIPPER_physical_ros2_control_macro.xacro
```
