🧩 Ubuntu Core 24 QEMU Lab

A reproducible Ubuntu Core 24 experiment using Snapcraft, ubuntu-image, QEMU, and OVMF/UEFI on an amd64 virtual target.

This repository documents a working Ubuntu Core environment built and tested in QEMU from a WSL2 Ubuntu development host.

The project was created as a practical way to understand how Ubuntu Core is assembled, how a device is defined through a model assertion, how custom applications are packaged as snaps, and how a complete Ubuntu Core disk image can be booted and validated before moving to real embedded hardware.

🚀 Project at a Glance

Target OS: Ubuntu Core 24
Architecture: amd64
Virtual Platform: QEMU
Firmware: OVMF / UEFI
Image Builder: ubuntu-image
Application Packaging: Snapcraft
Package / Service Manager: snapd
Development Host: Ubuntu under WSL2

✅ What Was Validated

Feature

Status

Ubuntu Core 24 model assertion

✅

Image assembly with ubuntu-image

✅

QEMU boot

✅

OVMF / UEFI boot flow

✅

First-boot Ubuntu Core setup

✅

Snapcraft project creation

✅

Custom application snap build

✅

Snap command execution

✅

Snap-managed background service

✅

🧠 Architecture

The project follows the standard Ubuntu Core model:

Canonical Model Assertion
          │
          ▼
     ubuntu-image
          │
          ▼
  Ubuntu Core Disk Image
          │
          ▼
      QEMU + OVMF
          │
          ▼
      Ubuntu Core 24
          │
          ├── pc gadget snap
          ├── pc-kernel snap
          ├── core24 base snap
          ├── snapd
          └── optional application snaps

The custom application is developed independently with Snapcraft:

Application Source
       │
       ▼
  snapcraft.yaml
       │
       ▼
    Snapcraft
       │
       ▼
 Custom .snap
       │
       ▼
Install / test on Ubuntu Core

📂 Repository Structure

ubuntu-core-qemu-lab/
├── README.md
├── LICENSE
├── .gitignore
│
├── model/
│   └── ubuntu-core-24-amd64.model
│
├── snap-project/
│   └── starz-core-demo/
│       ├── snap/
│       │   └── snapcraft.yaml
│       └── src/
│           └── bin/
│               ├── agent
│               └── hello
│
└── docs/
    └── seed.manifest

Generated files such as disk images, built snaps, firmware copies, build directories and backups are intentionally excluded from Git.

🔐 Ubuntu Core Model Assertion

The QEMU image uses the official Canonical Ubuntu Core 24 amd64 model:

model: ubuntu-core-24-amd64
architecture: amd64
base: core24
grade: signed

The model defines the essential Ubuntu Core components:

pc          → gadget snap
pc-kernel   → kernel snap
core24      → base snap
snapd       → snap management / lifecycle
console-conf → optional first-boot configuration application

This repository keeps the model assertion under:

model/ubuntu-core-24-amd64.model

The model is signed by Canonical and contains no private signing key.

📦 Custom Snapcraft Project

The custom application snap is stored under:

snap-project/starz-core-demo/

Current structure:

starz-core-demo/
├── snap/
│   └── snapcraft.yaml
└── src/
    └── bin/
        ├── agent
        └── hello

The snap can be built with:

cd snap-project/starz-core-demo
snapcraft

The resulting .snap file is a generated build artifact and is therefore not committed to Git.

🏗️ Image Assembly

The Ubuntu Core image is assembled from the signed model assertion using ubuntu-image.

Conceptually:

ubuntu-core-24-amd64.model
          │
          ▼
      ubuntu-image
          │
          ├── pc gadget
          ├── pc-kernel
          ├── core24
          ├── snapd
          └── console-conf
          │
          ▼
        pc.img

pc.img is a generated disk image and is not stored in the Git repository.

🖥️ QEMU / OVMF

The generated disk image was booted using QEMU with OVMF firmware.

The local development directory used files such as:

OVMF_CODE_4M.secboot.fd
OVMF_VARS_4M.ms.fd
pc.img

These files are intentionally excluded from Git because they are generated or third-party runtime artifacts.

The Git repository should instead document how to obtain or generate them.

🔄 Snap Services

Ubuntu Core applications can expose both interactive commands and background daemons.

Typical inspection commands include:

snap list

snap services

snap logs <snap-name>

This project was used to validate the idea that product software can be packaged and managed independently from the base Ubuntu Core system.

🧱 Why Ubuntu Core Is Different

A traditional embedded Linux image is often treated as one root filesystem.

Ubuntu Core separates the system into independently managed components:

Kernel Snap
     +
Gadget Snap
     +
Base Snap
     +
snapd
     +
Application Snaps
     +
Signed Model Assertion

This architecture introduces:

transactional updates

rollback

immutable system components

explicit device identity

snap confinement

independent application lifecycle

clear separation between board support and product software

🧪 Why QEMU First?

QEMU provides a controlled environment for learning Ubuntu Core before adding board-specific complexity.

The progression is:

Ubuntu Core concepts
        │
        ▼
Snapcraft application
        │
        ▼
Model assertion
        │
        ▼
ubuntu-image
        │
        ▼
Bootable amd64 image
        │
        ▼
QEMU validation

This creates a baseline before moving to hardware-specific work such as STM32MP1 board enablement.

🔗 Relationship to the STM32MP1 Project

This repository is the generic Ubuntu Core / QEMU baseline.

A separate project handles the STM32MP157F-DK2 board-specific work:

Ubuntu Core QEMU Lab
        │
        │ validates Ubuntu Core concepts
        ▼
STM32MP157F-DK2 Board Enablement
        │
        ├── custom kernel snap
        ├── custom gadget snap
        ├── ST OpenSTLinux BSP
        ├── Device Tree
        ├── boot firmware
        └── board-specific storage layout

The QEMU lab demonstrates the Ubuntu Core workflow.

The STM32MP1 project applies the same architecture to real embedded hardware.

📤 What Is Included in Git

✅ Included

README.md
LICENSE
.gitignore
ubuntu-core-24-amd64.model
seed.manifest
snapcraft.yaml
application scripts/source
documentation

❌ Excluded

pc.img
pc-old.img
*.snap
OVMF firmware files
Snapcraft build directories
backup files
logs
private keys
credentials
tokens

🛠️ Skills Demonstrated

Ubuntu Core

Model Assertions · ubuntu-image · snapd · Immutable Linux

Snapcraft

snapcraft.yaml · Application Snaps · Services

Virtualization

QEMU · OVMF · UEFI

Embedded Linux

Image Assembly · Boot Flow · Service Management

Development Environment

Linux · WSL2 · Shell

🎯 Project Purpose

This repository is a learning, experimentation and validation environment for Ubuntu Core.

It establishes a working Ubuntu Core baseline before moving to more advanced, board-specific embedded Linux work.

📄 License

Choose an explicit license for the source code, scripts and documentation you own.

Third-party Ubuntu, Canonical and OVMF components remain subject to their own licenses and are not relicensed by this repository.
