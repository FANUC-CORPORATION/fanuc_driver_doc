<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->

# System Requirements

## Operating System

Ubuntu 22.04 LTS (optionally with real-time PREEMPT_RT kernel installed)

## ROS 2 Distribution

Humble Hawksbill

## FANUC Robot Controller

- R-30iB Plus series
  - R-30iB Plus
  - R-30iB Mate Plus
  - R-30iB Mini Plus
- R-50iA series
  - R-50iA
  - R-50iA Mate

## Software Options

- J519 Stream Motion and R912 Remote Motion, or
- S636 External Control Package (includes both J519 and R912)

```{note}
The FANUC ROS 2 Driver does not require J568 and J570 even though they have "ROS 2" in their software option name.
```

## Controller Software Version

- R-30iB Plus, R-30iB Mate Plus: V9.40P/81 or later
- R-30iB Mini Plus: V9.40P/77 or later
- R-50iA series: V10.10P/26 or later
