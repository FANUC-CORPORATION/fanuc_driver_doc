<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# Overview

This repository contains URDF xacros and mesh assets for FANUC robots as well as examples for composing and visualizing URDFs.

## Repository structure

```text
├── fanuc_crx_description
│   ├── launch
│   │   └── view_crx.launch.py
│   ├── meshes
│   │   ├── crx10ia
│   │   │   ├── collision
│   │   │   │   └── ...
│   │   │   └── visual
│   │   │       └── ...
│   │   └── ... other models
│   ├── robot
│   │   ├── crx10ia.urdf.xacro
│   │   └── ... other example top-level xacro files
│   ├── rviz
│   │   └── view_crx.rviz
│   ├── urdf
│   │   ├── crx10ia_urdf_macro.xacro
│   │   └── ... other models
│   ├── CMakeLists.txt
│   └── package.xml
└── README.md
```

Description files are organized by FANUC robot arm model family.

### Package structure

Packages have the following subdirectories:

- `meshes/`: Visual and collision meshes for each robot.
- `urdf/`: Base xacro macro files for generating a robot description (as a URDF) for a specific robot model.
- `robot/`: Example top-level xacro files that generate a complete URDF containing a robot model.
- `launch/` and `rviz/`: Launch files and configuration for visualizing robots in `robot/`.
