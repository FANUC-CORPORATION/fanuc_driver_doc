<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Controller Usage

Currently supported FANUC-specific controllers include:

* **scaled_joint_trajectory_controller**:  a variation of the standard joint_trajectory_controller, which allows the speed to be scaled up or down between 0% and 100%.
* **fanuc_force_sensor_broadcaster**:  publishes the robot resultant force/torque at the flange and the force sensor type.
* **fanuc_gpio_controller**:  provides the ability to access controller data such as I/O, numeric registers, position registers, robot status, and payload.

## fanuc_controllers/scaled_joint_trajectory_controller

This controller is a variation of the standard joint_trajectory_controller, which allows the speed to be scaled up or down between 0% and 100%.

### Speed scaling

* Speed scaling is a float value ranging from 0 to 100, representing the percentage of robot execution speed following the trajectory.
* The `scaled_joint_trajectory_controller` subscribes to this speed scaling value and generates commands that automatically scale the speed up or down between 0% (robot stops) and 100% (robot runs at full execution speed).
* The `/speed_scaling_factor` topic is provided to allow adjustment of the robot speed.

### Collaborative speed clamping

* Collaborative robots are equipped with the collaborative speed clamping function. The robot controller automatically reduces the commanded speed to ensure it does not exceed the collaborative speed limit.
* The `scaled_joint_trajectory_controller` receives the `Status/collaborative_speed_scaling` value from fanuc_driver via the status interface. This minimizes deviation between the commanded and the actual positions.
* This function is disabled when the disabling input for the collaborative speed clamping is ON. Refer to Collaborative Robot Function Manual for details.

```{note}
* When the original trajectory is not smooth or impossible for the robot model, the motion may become jittery or there is a possibility that the speed may exceed the limits even when the speed clamping function is active.
* The speed is decreased before reaching the limit with reasonably conservative assumptions because the robot controller does not know the future trajectory. For maximum performance, disable the function and create trajectories in which any part of the robot stay below the collaborative speed limit.
```

## fanuc_controllers/fanuc_force_sensor_broadcaster

This controller publishes the robot resultant force/torque at the flange and the force sensor type. This controller is available with robot controller software V9.40P/85 or later and ROS 2 driver v1.1.1.

* Robot resultant force/torque at the flange will be published to the topic `/fanuc_force_sensor_broadcaster/force_sensor` (with force sensor type included) via the `fanuc_force_sensor_broadcaster`.
* `fanuc_force_sensor_broadcaster` will provide the service `/fanuc_force_sensor_broadcaster/cfg_force_sensor` to reset the force sensor and/or change the force sensor type.
* Available force sensor types are defined as 1 for EMBEDDED force sensor, 2 for EXTERNAL force sensor, and 0 as Unselected. Force sensor type will be pre-configured based on the robot model when launching the driver. CRX robots will have the default force sensor type as EMBEDDED (1). Non-CRX robots will have the default force sensor type as Unselected (0). After launch, the force sensor type can be changed via the service, but an invalid force sensor type setting will be ignored by the robot controller.
* `fanuc_force_sensor_broadcaster` will be automatically activated with both the moveit config launch and the forward command launch.

```{note}
* Robot resultant force/torque at the flange will also be published to the topic `/force_torque_sensor_broadcaster/wrench` via the standard `force_torque_sensor_broadcaster`.
* `force_torque_sensor_broadcaster` will also be automatically activated with both the moveit config launch and the forward command launch.
```

## fanuc_controllers/fanuc_gpio_controller

This controller provides the ability to access controller data such as I/O, numeric registers, position registers, robot status, and payload.

### Published topics

* `~/io_cmd [fanuc_msgs/msg/IOCmd]`: Synchronized bool I/O commands (e.g. `DO`, `RO`, `F`)
* `~/io_state [fanuc_msgs/msg/IOState]`: Synchronized bool I/O states (e.g. `DO`, `DI`, `RO`, `RI`, `F`)
* `~/analog_io_cmd [fanuc_msgs/msg/AnalogIOCmd]`: Synchronized analog I/O commands (e.g. `AO`)
* `~/analog_io_state [fanuc_msgs/msg/AnalogIOState]`: Synchronized analog I/O states (e.g. `AO`, `AI`)
* `~/num_reg_cmd [fanuc_msgs/msg/NumRegCmd]`: Synchronized numeric register commands
* `~/num_reg_state [fanuc_msgs/msg/NumRegState]`: Synchronized numeric register states
* `~/connection_status [fanuc_msgs/msg/ConnectionStatus]`: Publishes whether the robot is connected or not. If this is `false`, no commands can be sent to the robot.
* `~/robot_status [fanuc_msgs/msg/RabotStatus]`: Synchronized robot status (e.g. `in_error`, `tp_enabled`, `e_stopped`, `motion_possible`, `contact_stop_mode`). The `contact_stop_mode` publishes an integer with the following definitions (0: NONE (The robot is not in collaborative mode, or the safety sensor is disabled), 1: SAFE, 2: STOP, 3: DSBL, 4: ESCP).
* `~/robot_status_ext [fanuc_msgs/msg/RobotStatusExt]`: Asynchronized robot status (e.g. `error_code`, `in_motion`, `drives_powered`, `gen_override`, `speed_clamp_limit`)
* `~/collaborative_speed_scaling [fanuc_msgs/msg/CollaborativeSpeedScaling]`: Publishes the robot controller's collaborative speed clamping scaling value, either 0 or 1. For non-collaborative robots, this value is alawys 1. The same value will be automatically applied to the `scaled_joint_trajectory_controller` via the status interface to minimize path deviation. If you want to further define your own scaling, you can use the topic `/speed_scaling_factor`.

### Advertised services

* `~/get_bool_io [fanuc_msgs/srv/GetBoolIO]`: Get asynchronized bool I/O (e.g. `DO`, `DI`, `RO`, `RI`, `F`)
* `~/get_analog_io [fanuc_msgs/srv/GetAnalogIO]`: Get asynchronized analog I/O (e.g. `AO`, `AI`)
* `~/get_group_io [fanuc_msgs/srv/GetGroupIO]`: Get asynchronized group I/O (e.g. `GO`, `GI`)
* `~/get_num_reg [fanuc_msgs/srv/GetNumReg]`: Get asynchronized numeric register
* `~/get_pos_reg [fanuc_msgs/srv/GetPosReg]`: Get asynchronized position register
* `~/set_bool_io [fanuc_msgs/srv/SetBoolIO]`: Set asynchronized bool I/O (e.g. `DO`, `RO`, `F`)
* `~/set_analog_io [fanuc_msgs/srv/SetAnalogIO]`: Set asynchronized analog I/O (e.g. `AO`)
* `~/set_group_io [fanuc_msgs/srv/SetGroupIO]`: Set asynchronized group I/O (e.g. `GO`)
* `~/set_num_reg [fanuc_msgs/srv/SetNumReg]`: Set asynchronized numeric register
* `~/set_pos_reg [fanuc_msgs/srv/SetPosReg]`: Set asynchronized position register
* `~/set_gen_override [fanuc_msgs/srv/SetGenOverride]`: Set robot GenOverride. This value needs to stay at 100 when the hardware interface is in active state.
* `~/set_payload_id [fanuc_msgs/srv/SetPayloadID]`: Set the robot payload schedule number.
* `~/set_payload_value [fanuc_msgs/srv/SetPayloadValue]`: Set the robot payload value.
* `~/set_payload_comp [fanuc_msgs/srv/SetPayloadComp]`: Set the robot payload compensation.

### Getting and setting asynchronized position register

Robot controller software V9.40P/80 or later and ROS 2 driver v1.1.0 or later support getting and setting asynchronized position register via ROS 2 services.

#### Getting asynchronized position register [fanuc_gpio_controller/get_pos_reg]

* If the position register is in Cartesian representation, you can read the configuration and position. If the position register is in Joint representation, you can read the joint angles.
* Irrelevant fields will still be shown in the response as zeros. Please ignore.

#### Setting asynchronized position register [fanuc_gpio_controller/set_pos_reg]

* If the position register is in Cartesian representation, the configuration and position are required. If the position register is in Joint representation, joint angles are required.
* The representation string ('Cartesian' or 'Joint') is NOT case-sensitive. If the representation is not specified or mis-specified, it will use 'Cartesian' as the default.
* If a required field is missing in the service call, that field will be automatically set to 0 by default.

### Setting payload value and payload compensation

Robot controller software V9.40P/80 or later and ROS 2 driver v1.1.0 or later support setting payload value and payload compensation on the fly via ROS 2 services.

#### Setting payload value [fanuc_gpio_controller/set_payload_value]

* ROS 2 service units:
  * Mass: kg
  * Center of Gravity: m
  * Inertia: kgm^2
* "use_in" is a Boolean that indicates whether the robot should set the inertia values. True means the robot will set the inertia values. False means the robot will ignore the inertia values.
* All arguments in the service are required. If an argument is missing in the service call, that argument will be automatically set to 0 by default.
* This service is intended to be used by non-collaborative robots. Collaborative robots should use the service to set payload compensation. In case collaborative robots use this service, the payload value will still be set accordingly, but since payload is part of the DCS settings, Stream Motion and RMI will end and the ROS 2 driver will post a "no longer streaming" error. Users need to apply DCS, cycle power, confirm payload, and relaunch ROS 2 driver to continue.
* If the payload schedule number is invalid or out of range, the service won't return successful.

#### Setting payload compensation [fanuc_gpio_controller/set_payload_comp]

* ROS 2 service units:
  * Mass: kg
  * Center of Gravity: m
  * Inertia: kgm^2
* All arguments in the service are required. If an argument is missing in the service call, that argument will be automatically set to 0 by default.
* This service is intended to be used by collaborative robots only.
* Under any of the following conditions, the service won't be successful:
  * The service is used with non-collaborative robots.
  * The payload schedule does not match the current controller payload schedule (i.e., the payload compensation schedule).
  * Payload compensation is not fully enabled on the robot (please see the Operator's Manual (Collaborative Robot Function) for details on enabling payload compensation).

### Configuring high-frequency I/O

Selected GPIO Topics support high-frequency update. The update rate is up to the robot controller sampling rate. The selection of high-frequency updated I/O is configured through a YAML configuration file before the driver starts. This section describes behaviors and limitations of the YAML file, and provides an outline of the YAML file format.

#### Behaviors

The GPIO Controller’s YAML configuration file behaves as follows:

* The GPIO YAML configuration file is passed directly into the Hardware Interface during the launch process. An example of the GPIO configuration file is provided in the driver project (`fanuc_driver/fanuc_hardware_interface/config/example_gpio_config.yaml`).
* The Hardware Interface will validate the YAML file early during initialization and provide an indication of any errors.
* The configuration file defines the memory map for each topic type.
* Invalid configurations will cause a Hardware Interface initialization failure and an error will be reported in the logfile.
* If a topic is not configured (e.g., no Analog I/O is needed in the application), the topic will remain empty.
* When outputs or numeric registers are added to the command section of the GPIO configuration YAML file, once the ROS 2 driver is launched those output and numeric register values in the controller will be set to false or zero.

#### Limitations

The number of data points to be accessed is limited in the high-frequency update cycle. Therefore, some limitations are necessary. A GPIO Process Time Factor (PTF) will be used to limit the amount of I/O accessed in the communication cycle. The user can configure the YAML according to PTF considerations.

The GPIO PTF specification is as follows:

* Configuration is set once based on the YAML file at driver launch.
* The limit of the PTF is 32 when the sample rate is 1ms. The limit of PTF is (32 \* (sample rate (ms))).
* PTF is the total of all YAML configurations (io_cmd, io_state, num_reg_cmd, num_reg_state, etc.).
* When unassigned I/O is specified in the YAML file, an error occurs at connection.
* Default (worst) PTF is the same value as the “length” for a YAML configuration line.

##### PTF optimization best practices

:::{note}
**For Digital I/O:** (PTF will be INT((“length” + 31)/32))
:::

* A configuration is in a same rack, slot, and contiguous I/O assignment

Additional conditions for digital output:

* The length of a yaml configuration is a multiple of 32
* (\[yaml’s start index\] – \[I/O config’s start index of RANGE\] + \[I/O config’s START\] - 1) is multiple of 32

Digital I/O Example:

```yaml
Robot I/O Assignment:
  #         RANGE       RACK  SLOT START
  1   DO [    1-   10]     0    0     0
  1   DO [   11-  100]    34    1     1

YAML:
  { type: DO, start: 11, length: 64} --> 2 PTF
  { type: DO, start: 21, length: 57} --> 57 PTF
```

For Analog I/O:

* The best practice: (PTF will be INT((“length” + 1)/2))
  * A configuration is in a same rack, slot, and contiguous I/O assignment

  Additional condition for analog output:
  * The length of a configuration is multiple of 2
  * (\[yaml’s start index\] – \[I/O config’s index\] + \[I/O config’s CHANNEL\] - 1) is multiple of 2

For Numeric Registers, PTF is the same value as the YAML “length”.

Other limitations:

* GI/GO are not supported.
* I/O simulation is not supported. Real I/O value is always accessed.

#### YAML File Outline

An outline of the YAML file is as follows:

```yaml
gpio_topic_config:
  io_state: [
    { type: < I/O type >, start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]

  io_cmd: [
    { type: < I/O type >, start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]

  analog_io_cmd: [
    { type: < I/O type >, start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]

  analog_io_state: [
    { type: < I/O type >, start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]

  num_reg_cmd: [
    { start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]

  num_reg_state: [
    { start: < start pt. >, length: < number of consec. pts. > },
    { < array element N > }
  ]
```

#### Example YAML File

```yaml
gpio_topic_config:
  io_state:
    - type: DI
      start: 11
      length: 1
    - type: DO
      start: 11
      length: 1
    - type: RI
      start: 1
      length: 2
    - type: RO
      start: 1
      length: 2
    - type: F
      start: 1
      length: 32
  io_cmd:
    - type: DO
      start: 11
      length: 1
    - type: RO
      start: 1
      length: 1
    - type: F
      start: 1
      length: 32
  num_reg_state:
    - start: 1
      length: 3
  num_reg_cmd:
    - start: 3
      length: 1
```
