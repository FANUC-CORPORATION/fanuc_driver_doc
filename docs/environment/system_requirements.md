<!-- SPDX-FileCopyrightText: 2025-2026 FANUC America Corp.
     SPDX-FileCopyrightText: 2025-2026 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->

# System Requirements

```{note}
This documentation is intended for ROS 2 Jazzy Jalisco.
If you are using a different ROS 2 distribution, select the appropriate branch from the branch selector in the lower-left corner.
```

## Operating System and ROS 2 Distribution

The FANUC ROS 2 Driver supports the following combinations of operating system
and ROS 2 distribution:

- Ubuntu 22.04 LTS / ROS 2 Humble Hawksbill
- Ubuntu 24.04 LTS / ROS 2 Jazzy Jalisco

```{note}
A real-time PREEMPT_RT kernel may be optionally installed, depending on application requirements.
```

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
