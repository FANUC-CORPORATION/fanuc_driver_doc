<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Controller Usage

Currently supported FANUC-specific controllers include:

* **scaled_joint_trajectory_controller**:  a variation of the standard joint_trajectory_controller, which allows the speed to be scaled up or down between 0% and 100%.
* **fanuc_gpio_controller**:  provides the ability to access controller data such as I/O, numeric registers, and robot status.

## fanuc_controllers/scaled_joint_trajectory_controller

This controller is a variation of the standard joint_trajectory_controller, which allows the speed to be scaled up or down between 0% and 100%.

### Speed scaling

* Speed scaling is a float value ranging from 0 to 100, representing the percentage of robot execution speed following the trajectory.
* The `scaled_joint_trajectory_controller` subscribes to this speed scaling value and generates commands that automatically scale the speed up or down between 0% (robot stops) and 100% (robot runs at full execution speed).
* At first release, the `/speed_scaling_factor` topic is provided to allow adjustment of the robot speed.

## fanuc_controllers/fanuc_gpio_controller

This controller provides the ability to access controller data such as I/O, numeric registers, and robot status.

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
* `~/collaborative_speed_scaling [fanuc_msgs/msg/CollaborativeSpeedScaling]`: Publishes the robots current collaborative speed scaling, between 0 to 1. Use this as a reference for the `scaled_joint_trajectory_controller` to avoid the robot violating the collaborative speed limit. This is only available for collaborative robots in collaborative mode.

### Advertised services

* `~/get_bool_io [fanuc_msgs/srv/GetBoolIO]`: Get asynchronized bool I/O (e.g. `DO`, `DI`, `RO`, `RI`, `F`)
* `~/get_analog_io [fanuc_msgs/srv/GetAnalogIO]`: Get asynchronized analog I/O (e.g. `AO`, `AI`)
* `~/get_group_io [fanuc_msgs/srv/GetGroupIO]`: Get asynchronized group I/O (e.g. `GO`, `GI`)
* `~/get_num_reg [fanuc_msgs/srv/GetNumReg]`: Get asynchronized numeric register
* `~/set_bool_io [fanuc_msgs/srv/SetBoolIO]`: Set asynchronized bool I/O (e.g. `DO`, `RO`, `F`)
* `~/set_analog_io [fanuc_msgs/srv/SetAnalogIO]`: Set asynchronized analog I/O (e.g. `AO`)
* `~/set_group_io [fanuc_msgs/srv/SetGroupIO]`: Set asynchronized group I/O (e.g. `GO`)
* `~/set_num_reg [fanuc_msgs/srv/SetNumReg]`: Set asynchronized numeric register
* `~/set_gen_override [fanuc_msgs/srv/SetGenOverride]`: Set robot GenOverride. This value needs to stay at 100 when the hardware interface is in active state.
* `~/set_payload_id [fanuc_msgs/srv/SetPayloadID]`: Sets the robot payload schedule number.

### Configuring high-frequency I/O

Selected GPIO Topics support high-frequency update. The update rate is up to the robot controller sampling rate. The selection of high-frequency updated I/O is configured through a YAML configuration file before the driver starts. This section describes behaviors and limitations of the YAML file, and provides an outline of the YAML file format.

#### Behaviors

The GPIO Controller’s YAML configuration file behaves as follows:

* The GPIO YAML configuration file is passed directly into the Hardware Interface during the launch process. An example of the GPIO configuration file is provided in the driver project (`fanuc_driver/fanuc_hardware_interface/config/example_gpio_config.yaml`).
* The Hardware Interface will validate the YAML file early during initialization and provide an indication of any errors.
* The configuration file defines the memory map for each topic type.
* Invalid configurations will cause a Hardware Interface initialization failure and an error will be reported in the logfile.
* If a topic is not configured (e.g., no Analog I/O is needed in the application), the topic will remain empty.

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
