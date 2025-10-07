<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# FANUC Unboxing

Here we describe in detail how to set up a new FANUC robot for users with the ros2_control driver.

## FANUC Setup

The following connections must be made:

| Cable         | Robot Base | Robot Controller | ROS 2 PC |
|:------------- |:----------:|:----------------:|:--------:|
| Ethernet      |            | x                | x        |
| Power         | x          |                  |          |
| Teach Pendant |            | x                |          |
| RMP / Robot Connection Cable         | x          | x                |          |
| GND           | x          | x                |          |

As an example, the following images show connections for a CRX-10iA/L robot on an R-30iB Mini Plus controller.
For other robots, refer to FANUC robot documentation.

![Robot base connections.](/_static/images/robot-base-connections.png "Connect cables to the robot base")

![Robot controller connections.](/_static/images/robot-controller-connections.png "Connect cables to the robot controller")

## Networking Setup

### Networking Equipment Considerations

Good network design is critical for reliable operation. It is important to pay special attention to wiring guidelines and environmental conditions affecting the cable system and equipment. It is also necessary to control network traffic to avoid wasted network bandwidth and device resources.

Keep in mind the following wiring guidelines and environmental considerations:

- Use category 5e twisted pair (or better) rated for 1Gbps Ethernet applications and the application environment. Consider shielded versus unshielded twisted pair cabling.
- Pay careful attention to wiring guidelines such as maximum length from the switch to the device (100 meters).
- Do not exceed the recommended bending radius of the specific cabling being used.
- Use connectors appropriate to the environment. There are various industrial Ethernet connectors in addition to the standard open RJ45 that should be used where applicable.
- Route the wire runs away from electrical or magnetic interference or cross at ninety degrees to minimize induced noise on the Ethernet network.

### Keep the following in mind as you manage network traffic

- Control or eliminate collisions by limiting the collision domain.
- Control broadcast traffic by limiting the broadcast domain.
- Control multicast traffic with multicast aware switches (support for IGMP snooping).
- Use QOS (Quality of Service) techniques in very demanding applications.

Collisions are a traditional concern on an Ethernet network but can be completely avoided by using switches—rather than hubs—and full duplex connections.
It is critical to use switches and full duplex connections for any Ethernet network, because it reduces the collision domain to only one device so that no collisions will occur.
The robot interface will auto negotiate by default and use the fastest connection possible.
