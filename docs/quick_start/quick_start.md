<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Quick Start

This repository hosts the source code of the FANUC ROS 2 Driver project, a ros2_control high-bandwidth streaming driver.
This project will allow you to develop a ROS 2 application to control a FANUC virtual or real robot.

```{note}
This guide assumes basic familiarity with ROS 2, Ubuntu, and FANUC hardware.
```

## System Requirements

- **Operating System:** Ubuntu 22.04 LTS (optionally with real-time PREEMPT_RT kernel installed)
- **ROS 2 Distribution:** Humble Hawksbill
- **FANUC Robot Controller:**
  - R-30iB Mini Plus
  - R-50iA
- **Software Options:**
  - J519 Stream Motion and R912 Remote Motion, or
  - S636 External Control Package (includes both J519 and R912)
- **FANUC Robot:** See [the supported models page](../fanuc_description/supported_models.md).

---

## 1. Install Dependencies

### Set up the ROS 2 Environment

Follow the official [ROS 2 Installation Guide](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html) for the Desktop Install of ROS 2 Humble.

### Configure the ROS 2 Environment

We recommend performing the following tasks in the [ROS 2 Configuring Environment](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html):

- Add sourcing to your shell startup script
- The ROS_LOCALHOST_ONLY variable

## 2. Install FANUC software packages

Choose one of the following installation methods:

1. Source Build of FANUC Packages
2. Debian Install of FANUC Packages

### Method 1: Source Build of FANUC Packages

```bash
echo "Installing and configuring git-lfs"
sudo apt install git-lfs
git lfs install

echo "Checking out GitHub repositories"
mkdir ~/ws_fanuc/src -p
cd ~/ws_fanuc/src
git clone https://github.com/FANUC-CORPORATION/fanuc_description.git
git clone --recurse-submodules https://github.com/FANUC-CORPORATION/fanuc_driver.git

echo "Installing FANUC dependencies"
cd ~/ws_fanuc
sudo apt update
rosdep update
rosdep install --ignore-src --from-paths src -y

echo "Building FANUC libraries"
colcon build --symlink-install --cmake-args -DBUILD_TESTING=1 -DBUILD_EXAMPLES=1
```

### Method 2: Debian Install of FANUC Packages

Debian packages will be provided at a future date.

## 3. Launching URDF Visualization

The `view_crx` launch file visualizes a URDF model in RViz and provides slider bars to visualize a specific joint state.

```bash
source /opt/ros/humble/setup.bash
source ~/ws_fanuc/install/setup.bash
ros2 launch fanuc_crx_description view_crx.launch.py robot_model:=crx10ia
```

![Starting RViz view after running view_crx.launch.py.](/_static/images/joint_state_publisher.png "RViz with JointStatePublisher")

## 4. Create Moveit Config

`fanuc_moveit_config` is an example MoveIt configuration package which supports the following robot models.

- CRX-5iA
- CRX-10iA
- CRX-10iA/L
- CRX-20iA/L
- CRX-30iA
- CRX/30-18A

When you want to use other models, create your MoveIt configuration package following this [page](../fanuc_driver/create_your_moveit_config.md).

## 5. Launching with Mock Hardware

The `fanuc_moveit` launch file starts ROS processes to control a URDF model using ros2_control and MoveIt2.

```bash
source /opt/ros/humble/setup.bash
source ~/ws_fanuc/install/setup.bash
ros2 launch fanuc_moveit_config fanuc_moveit.launch.py robot_model:=crx10ia use_mock:=true
```

RViz will launch with a visualization of the CRX-10iA.

![Starting RViz view after running fanuc_moveit.launch.py.](/_static/images/mock_hw_start.png "Starting RViz view")

Drag the 3-D arrows to set a goal pose for the robot.
Then click `Plan & Execute` to simulate the robot planning and executing a trajectory to the goal.

![RViz view after moving IMarker.](/_static/images/mock_hw_trajectory.png "Dragging IMarker")

### Dynamically scaling trajectory execution

The `fanuc_moveit` launch file configures a Scaled Joint Trajectory Controller (SJTC), which enables us to slow down and pause trajectories while they are executed.

Set the trajectory speed scaling factor to 10% by Slider Publisher value.
Drag the IMarker to a new location and click `Plan & Execute`.
The robot will move slowly to the goal.
Now, set the speed scaling factor to 0% and see it pause its motion.
Set it back to 100% and the robot will complete the remainder of the trajectory at its nominal speed.

## 6. Launching with Physical Hardware

Now we will use the same SJTC on the physical hardware.
We will use the same `fanuc_moveit` launch file, but provide a different set of arguments that will use the physical hardware interface instead of mock hardware.

### Robot Controller Setup

Requires software version:

- R-30iB Mini Plus: V9.40P/77 or later
- R-50iA: [contact FANUC](#obtain-support)

Requires software options:

- J519 Stream Motion and R912 Remote Motion, or
- S636 External Control Package (includes J519 and R912)

Confirm that the required robot controller software is installed

1. Display **FULL MENUS**.
2. Select **STATUS**.
3. Select **Version ID**.

    ![STATUS Version ID screen.](/_static/images/STATUS-VersionID-SOFTWARE-1.png "Select Version ID")

4. Select **CONFIG**.

    ![STATUS Version ID screen.](/_static/images/STATUS-VersionID-CONFIG-2.png "Select CONFIG")

    ```{note}
    Software options are listed in alphabetical order.
    ```

5. Cursor until you find **Stream Motion J519 and Remote Motion R912** or **S636 External Control Package**.
6. If you cannot find Stream Motion J519 and Remote Motion R912, or S636 External Control Package, [contact FANUC](#obtain-support) to obtain the software option.

### Establish Payload Settings

Accurately setting your robot's payload is important.

#### Prior to `ROS 2 driver v1.1.0` and `controller software V9.40P/82`

Changing payload requires the client to execute the following steps:

1. Bring the robot to a stop.
2. Deactivate the hardware interface.
3. Change the payload schedule.
4. Reactivate the hardware interface.
5. Resume your application.

#### Later than `ROS 2 driver v1.1.0` and `controller software V9.40P/82`

You can use ROS 2 service to change payload value or payload compensation on the fly. See [Setting payload value and payload compensation](/docs/fanuc_driver/controller_usage.md#setting-payload-value-and-payload-compensation).

### Driver Network Configuration

The ROS 2 Control Driver requires a network connection to the robot, which can be either port 1 or port 2.
It is recommended that the ROS 2 network be on a port that is isolated from all other Ethernet communications.

First, we will set IP addresses for each of the Ethernet connections.

If your computer has two network interfaces (one to connect to the Internet and another to connect to robot controller) each needs a different connection profile.
To set these up, click the arrow at the upper right corner of the screen, and then click `Settings` > `Network`.

Unplug the Ethernet cable between the computer and your network infrastructure.
One of the two wired interfaces will now show up as "Cable unplugged."
Click the gear icon next to that wired adapter.

- Under the `Identity` tab, give this profile a name (like "Internet").
- Click `Apply` to accept the default values for the other settings.
- Plug the Ethernet cable back in.

Now, click the gear icon next to the other network adapter.

- Under the `Identity` tab, assign a name to the connection profile, such as "Robot".
- Under the `IPv4` tab, select `Manual` as the `IPv4 Method`.
- Enter `192.168.1.101` for the Address, and `255.255.255.0` for the Netmask.
- Click `Apply`.

![Configure static IP for driver.](/_static/images/configure_static_ip.png "Configuring a static IP address for the driver")

### Robot Network Configuration

After defining your port, you now need to configure that port using the Host Communication screen.

![Configure robot's static IP.](/_static/images/Host_Comm.png "Configuring the robot's static IP address")

### Launching CRX-10iA with Driver

```bash
source /opt/ros/humble/setup.bash
source ~/ws_fanuc/install/setup.bash
ros2 launch fanuc_moveit_config fanuc_moveit.launch.py robot_model:=crx10ia robot_ip:="192.168.1.100"
```

You can command motion using the same IMarker workflow used earlier.

```{note}
See [Controller Usage](/docs/fanuc_driver/controller_usage/) to learn more about the available controllers.
```

### Setting I/O Values

To monitor I/O status from pendant follow these instructions on the pendant.

![Click on three lines to open pendant side menu](/_static/images/pendant-home-menu.png "Click on three lines to open pendant side menu")

![Then choose the I/O option](/_static/images/pendant-left-bar.png "Then choose the I/O option")

Then the following window will show all the I/O types and their current status.

![I/O types and their current status](/_static/images/pendant-gpio-DI.png "I/O types and their current status")

Now change a bool I/O via the ROS 2 CLI:

```bash
ros2 service call /fanuc_gpio_controller/set_bool_io fanuc_msgs/srv/SetBoolIO "{io_type: {type: 'DO'}, index: 1, value: true}"
```

The pendant will now show the I/O Status as ON:

![DO1 port is on](/_static/images/pendant-gpio-DO1-on.png "DO1 port is on")

```{note}
See [Controller Customization](/docs/fanuc_driver/controller_customization/) to learn more about monitoring and setting I/O via ros2_control and the command line.
```

## Obtain Support

Contact your local FANUC sales representative.
To expedite your request, please provide information such as robot model, configuration, and application, so we can determine how best to support your request.
