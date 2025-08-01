<!-- SPDX-FileCopyrightText: 2025 FANUC America Corp.
     SPDX-FileCopyrightText: 2025 FANUC CORPORATION

     SPDX-License-Identifier: Apache-2.0
-->
<!-- markdownlint-disable MD013 -->
# fanuc_driver_doc

This repository holds the source code and configuration for generating
the FANUC ROS 2 Documentation site.

---

## Table of Contents

- [fanuc\_driver\_doc](#fanuc_driver_doc)
  - [Table of Contents](#table-of-contents)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Build the Documentation](#build-the-documentation)
  - [Licensing](#licensing)

---

## Prerequisites

Before you get started, make sure you have:

- Python 3.10 or higher
- [pipenv](https://pipenv.pypa.io/en/latest/installation.html)
  (for virtual environment and dependency management)
- Git LFS (to track large assets)
- pre-commit (to enforce code style and checks)

---

## Installation

1. Clone the repository

   ```bash
   git clone https://github.com/FANUC-CORPORATION/fanuc_driver_doc.git
   cd fanuc_driver_doc
   ```

2. Initialize Git LFS

   ```bash
   git lfs install
   ```

3. Install dependencies with pipenv

   ```bash
   pipenv install
   ```

4. Activate the virtual environment

   ```bash
   pipenv shell
   ```

5. Install pre-commit hooks

   ```bash
   pre-commit install
   ```

---

## Build the Documentation

- Build with multiversion support:

  ```bash
  sphinx-multiversion . _build/html
  cd _build/html
  python3 -m http.server 8000
  ```

  > :red_circle: **NOTE:** This method will require manual steps to rebuild and serve the site locally.

- Build without multiversion support:

  ```bash
  sphinx-autobuild . _build/html
  ```

  > This method will serve the site and provide auto-rebuild and with live-reloading.

---

## Licensing

The original FANUC ROS 2 Driver Documentation source code and associated documentation
including these web pages are Copyright (C) 2025 FANUC America Corporation
and FANUC CORPORATION.

Any modifications or additions to source code or documentation
contributed to this project are Copyright (C) the contributor,
and should be noted as such in the comments section of the modified file(s).

FANUC ROS 2 Driver Documentation is licensed under
     [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0)

Please see the LICENSE folder in the root directory for the full texts of these licenses.
