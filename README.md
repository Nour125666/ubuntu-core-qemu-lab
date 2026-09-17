# 🧩 Ubuntu Core 24 QEMU Lab

> A reproducible Ubuntu Core 24 experiment using **Snapcraft**, **ubuntu-image**, **QEMU**, and **OVMF/UEFI** on an `amd64` virtual target.

This repository documents a working **Ubuntu Core 24 environment** assembled and tested in QEMU from an Ubuntu development environment running under **WSL2**.

The purpose of the project is to explore the main Ubuntu Core concepts in practice:

- image assembly
- model assertions
- Snapcraft
- application snaps
- snap-managed services
- QEMU virtualization
- UEFI boot with OVMF
- immutable Linux system architecture

---

## 🚀 Project at a Glance

| Item | Technology |
|---|---|
| Target OS | Ubuntu Core 24 |
| Architecture | `amd64` |
| Virtual Platform | QEMU |
| Firmware | OVMF / UEFI |
| Image Builder | `ubuntu-image` |
| Application Packaging | Snapcraft |
| Package / Service Manager | `snapd` |
| Development Environment | Ubuntu under WSL2 |

---

## ✅ Validation Status

| Feature | Status |
|---|---|
| Ubuntu Core 24 model assertion | ✅ Validated |
| Image assembly with `ubuntu-image` | ✅ Validated |
| QEMU boot | ✅ Validated |
| OVMF / UEFI boot flow | ✅ Validated |
| First-boot Ubuntu Core setup | ✅ Validated |
| Snapcraft project creation | ✅ Validated |
| Custom application snap build | ✅ Validated |
| Snap command execution | ✅ Validated |
| Snap-managed application/service | ✅ Validated |

---

## 🧠 Architecture

Ubuntu Core is assembled from several independently managed components.

```text
                Model Assertion
                      │
                      ▼
                 ubuntu-image
                      │
                      ▼
              Ubuntu Core Image
                      │
                      ▼
                 QEMU + OVMF
                      │
                      ▼
                Ubuntu Core 24
                      │
        ┌─────────────┼─────────────┐
        │             │             │
        ▼             ▼             ▼
    pc gadget     pc-kernel       core24
                                    │
                                    ▼
                                  snapd
                                    │
                                    ▼
                           Application Snaps
```

The custom application follows a separate Snapcraft workflow:

```text
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
 Ubuntu Core Device
```

---

## 📂 Repository Structure

This repository contains the reproducible source and configuration files used during the experiment.

```text
ubuntu-core-qemu-lab/
├── README.md
│
├── model/
│   └── ubuntu-core-24-amd64.model
│
├── snap-project/
│   └── starz-core-demo/
│       ├── snap/
│       │   └── snapcraft.yaml
│       │
│       └── src/
│           └── bin/
│               ├── agent
│               └── hello
│
└── docs/
    └── seed.manifest
```

Generated disk images, built snaps, firmware copies, temporary build directories and backups are intentionally not stored in the repository.

---

## 🔐 Ubuntu Core Model Assertion

Ubuntu Core uses a **model assertion** to define the identity and essential components of a device.

The model used in this experiment is the official Canonical Ubuntu Core 24 `amd64` model.

Important fields include:

```text
model: ubuntu-core-24-amd64
architecture: amd64
base: core24
grade: signed
```

The model defines the main Ubuntu Core components:

```text
pc
 └── gadget snap

pc-kernel
 └── kernel snap

core24
 └── base snap

snapd
 └── snap lifecycle and management

console-conf
 └── optional first-boot configuration application
```

The model assertion is stored at:

```text
model/ubuntu-core-24-amd64.model
```

It is signed by Canonical and does not contain a private signing key.

---

## 📦 Custom Snapcraft Application

The custom Snapcraft project is located under:

```text
snap-project/starz-core-demo/
```

Its structure is:

```text
starz-core-demo/
├── snap/
│   └── snapcraft.yaml
│
└── src/
    └── bin/
        ├── agent
        └── hello
```

The `snapcraft.yaml` file defines how the application is packaged.

A snap can be built from the project directory using:

```bash
cd snap-project/starz-core-demo
snapcraft
```

The generated file is similar to:

```text
starz-core-demo_0.1_all.snap
```

The `.snap` file is a generated build artifact and is therefore not committed to Git.

---

## 🏗️ Ubuntu Core Image Assembly

Ubuntu Core images are assembled using `ubuntu-image`.

Conceptually, the process is:

```text
ubuntu-core-24-amd64.model
             │
             ▼
        ubuntu-image
             │
     ┌───────┼─────────┐
     │       │         │
     ▼       ▼         ▼
  gadget   kernel    core24
     │       │         │
     └───────┼─────────┘
             │
             ▼
           snapd
             │
             ▼
           pc.img
```

The resulting disk image was used as the virtual disk for QEMU.

Generated images such as:

```text
pc.img
pc-old.img
```

are intentionally excluded from this repository.

---

## 🖥️ QEMU and OVMF

The generated Ubuntu Core image was tested using **QEMU**.

OVMF provides the UEFI firmware environment required to boot the virtual machine.

The local development environment used files such as:

```text
OVMF_CODE_4M.secboot.fd
OVMF_VARS_4M.ms.fd
pc.img
```

The boot flow is approximately:

```text
QEMU
 │
 ▼
OVMF / UEFI
 │
 ▼
Ubuntu Core Bootloader
 │
 ▼
Linux Kernel
 │
 ▼
Ubuntu Core 24
 │
 ▼
snapd
 │
 ▼
Application Snaps
```

The OVMF firmware files and generated disk image are runtime/build artifacts and are not committed to this repository.

---

## ⚙️ First Boot

After the image is assembled, Ubuntu Core performs its initialization process during first boot.

The system can then be inspected using standard snap commands.

For example:

```bash
snap list
```

To inspect application services:

```bash
snap services
```

To inspect snap logs:

```bash
snap logs <snap-name>
```

---

## 🔄 Snap-Managed Applications and Services

Ubuntu Core applications are packaged independently from the base operating system.

A snap can expose:

```text
Interactive Commands
        +
Background Services
```

Conceptually:

```text
Application Snap
      │
      ├── command
      │
      └── daemon
             │
             ▼
           snapd
             │
             ▼
      Service Management
```

This separation allows application software to have its own release and update lifecycle.

---

## 🧱 Ubuntu Core Architecture

Traditional embedded Linux images are often assembled as one large root filesystem.

Ubuntu Core uses a different model.

```text
┌─────────────────────────────┐
│        Application Snaps    │
├─────────────────────────────┤
│            snapd            │
├─────────────────────────────┤
│          Base Snap          │
├─────────────────────────────┤
│         Kernel Snap         │
├─────────────────────────────┤
│         Gadget Snap         │
└─────────────────────────────┘
              │
              ▼
       Physical / Virtual
           Hardware
```

A signed **model assertion** defines which components belong to the device.

This architecture provides concepts such as:

- transactional updates
- rollback
- immutable system components
- independent application lifecycle
- explicit device identity
- snap confinement
- separation between platform and application software

---

## 🧪 Why QEMU?

QEMU provides a controlled environment where the Ubuntu Core architecture can be tested before dealing with physical-board-specific problems.

The development progression is:

```text
Ubuntu Core Concepts
        │
        ▼
Snapcraft Application
        │
        ▼
Model Assertion
        │
        ▼
ubuntu-image
        │
        ▼
Bootable Image
        │
        ▼
QEMU Validation
        │
        ▼
Physical Embedded Hardware
```

This makes QEMU a useful first validation stage.

---

## 🐧 Development Environment

The project was developed under **WSL2**.

```text
Windows Host
     │
     ▼
WSL2 Ubuntu
     │
     ▼
Snapcraft / ubuntu-image
     │
     ▼
QEMU
     │
     ▼
Ubuntu Core 24
```

This setup provided a complete Linux-based development environment while running from a Windows workstation.

---

## 🔗 Relationship to the STM32MP1 Project

This repository represents the **generic Ubuntu Core / QEMU experiment**.

A separate project applies the same concepts to the **STM32MP157F-DK2**.

```text
Ubuntu Core QEMU Lab
        │
        │
        │ Understand and validate
        │ Ubuntu Core architecture
        │
        ▼
STM32MP157F-DK2 Ubuntu Core Enablement
        │
        ├── ST OpenSTLinux BSP
        ├── custom kernel snap
        ├── custom gadget snap
        ├── Device Tree
        ├── boot firmware
        └── board-specific storage layout
```

The QEMU project validates the Ubuntu Core workflow.

The STM32MP1 project extends that workflow into **real board enablement**.

---

## 📊 What This Project Demonstrates

### Ubuntu Core

```text
Model Assertions
ubuntu-image
snapd
Immutable Linux Architecture
Transactional System Design
```

### Snapcraft

```text
snapcraft.yaml
Application Snaps
Commands
Services
Application Packaging
```

### Virtualization

```text
QEMU
OVMF
UEFI
Virtual Disk Images
```

### Linux

```text
Boot Flow
Service Management
Shell Environment
Image-Based Deployment
```

### Development Environment

```text
Ubuntu
WSL2
Command-Line Tooling
Reproducible Project Structure
```

---

## 📤 Repository Policy

### Included

```text
README.md
model assertion
seed manifest
snapcraft.yaml
application source/scripts
documentation
```

### Not Included

```text
*.img
*.snap
OVMF firmware copies
Snapcraft build directories
temporary files
backup files
private keys
credentials
authentication tokens
```

This keeps the repository focused on **reproducible source and configuration** instead of generated artifacts.

---

## 🎯 Project Purpose

This project is primarily an **Ubuntu Core learning, experimentation and validation environment**.

It demonstrates a complete workflow from:

```text
Device Model
     │
     ▼
Image Assembly
     │
     ▼
Virtual Boot
     │
     ▼
Snap Application
     │
     ▼
Service Execution
```

It also serves as the conceptual baseline for the more advanced STM32MP1 Ubuntu Core board-enablement work.

---

## 📌 Current Status

> **Ubuntu Core 24 amd64 QEMU environment successfully assembled and validated. ✅**

The next level of the project moves beyond generic virtual hardware toward custom embedded-board support.

---

## 📄 License

The repository contains project-owned scripts/configuration alongside files originating from or describing third-party Ubuntu components.

Any third-party Canonical, Ubuntu, Snapcraft or OVMF component remains subject to its original license.

A separate open-source license can be applied to the original project files and documentation where appropriate.
