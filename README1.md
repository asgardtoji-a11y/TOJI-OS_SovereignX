TOJI OS v2.0 + Sovereign X

A Standalone AI‑Native Operating System

TOJI OS v2.0 + Sovereign X is a next‑generation, AI‑native operating system designed from the ground up to integrate autonomous intelligence into the core of the OS itself.

It combines:



A custom OS stack (kernel → init → system services → userland)



A Windows 11–style graphical desktop



Sovereign X, a privileged AI governor that observes system events, reasons about them, and orchestrates agents, workflows, and processes across the entire OS



This is not a Linux distro.

This is not a Windows layer.

This is a new class of operating system.



1\. Project Vision

TOJI OS aims to redefine what an operating system can be by embedding an autonomous reasoning engine directly into the system layer.

Sovereign X acts as a second “core” of the OS — one dedicated to:



Understanding system state



Managing long‑term memory



Enforcing policies



Executing workflows



Coordinating agents



Making decisions



The result is an OS that is self‑aware, adaptive, and capable of autonomous operation.



2\. Architecture Overview

TOJI OS is built in five layers:



1\. Kernel Layer

A microkernel‑style foundation providing:



Process scheduling



Memory management



Filesystem



Drivers



System calls



2\. System Layer (TOJI Core)

Includes:



toji-init (PID 1)



logd (logging)



netd (network)



pkgd (package manager)



proc\_manager (process control)



ipc\_broker (message bus)



This layer provides the essential OS services.



3\. AI Layer (Sovereign X)

A privileged system service responsible for:



Autonomous reasoning



Event handling



Policy enforcement



Memory management



Workflow orchestration



Agent control



4\. Userland Layer

Includes:



TOJI Desktop (Windows 11–style GUI)



TOJI Shell (toji CLI)



TOJI API (local HTTP/WebSocket bridge)



Agents, workflows, memory service



5\. Applications

User‑installed agents, workflows, and tools.



3\. Repository Structure

Code

toji-os/

│

├── kernel/                 # Kernel (fork or submodule)

├── system/                 # Init + system services

│   ├── init/

│   ├── services/

│   └── ipc/

│

├── sovereign\_x/            # AI core

│   ├── src/

│   └── config/

│

├── toji\_userland/          # Desktop, Shell, API, agents, workflows

│   ├── desktop/

│   ├── shell/

│   ├── api/

│   ├── agents/

│   ├── workflows/

│   └── memory\_service/

│

├── rootfs/                 # Root filesystem

├── boot/                   # Bootloader + kernel image

└── build/                  # ISO build scripts

4\. Boot Sequence

Bootloader loads toji\_kernel.img



Kernel initializes hardware and mounts rootfs



Kernel executes /sbin/toji-init



toji-init starts:



logd, netd, pkgd, proc\_manager, ipc\_broker



sovereign\_x\_daemon



toji\_api



toji\_desktop



5\. Inter‑Process Communication

All system services communicate through the IPC Broker:



Socket: /var/run/ipc\_broker.sock



JSON message envelope:



from



to



type



event or action



payload



This enables a clean, modular, service‑oriented OS.



6\. Sovereign X

Sovereign X is the intelligence core of the OS.



Responsibilities

Listen to system events



Apply policies



Maintain long‑term memory



Plan actions



Spawn/kill processes



Trigger workflows



Log decisions



Notify the user



Inputs

System events (proc\_manager, netd, logd)



User goals (toji\_api)



Outputs

Actions to system services



Logs



Notifications



7\. TOJI Desktop (Windows 11 Style)

The official GUI for TOJI OS.



Features

Mica background



Acrylic sidebar



Rounded Fluent UI geometry



Dashboard



Sovereign X panel



System panel



Memory panel



Workflows panel



Agents panel



Settings



Communication

TOJI Desktop communicates exclusively with toji\_api.



8\. TOJI Shell

A command‑line interface for interacting with the OS.



Examples

Code

toji sys status

toji sovereign goals

toji sovereign goal "research topic"

toji wf list

toji wf run <workflow>

9\. Build \& Run

Build ISO

Code

./build/build\_iso.sh

Run in QEMU

Code

./build/qemu\_run.sh

10\. Implementation Roadmap

The project is developed in phases:



Base OS skeleton



Core system services



Sovereign X wiring



TOJI API + Shell



TOJI Desktop



Memory + workflows



Policies, agents, polish



Release engineering



See IMPLEMENTATION\_PLAN.md for full details.



11\. Project Management

A complete GitHub Project Board is included:



Epics



Issues



Status columns



Priorities



Timeline view



See project.json.



12\. License

Choose your license (MIT, Apache 2.0, custom).

Placeholder:



Code

© 2026 Sovereign Systems. All rights reserved.

13\. Author

Edgar Rodrigues  

Creator of TOJI OS v2.0 + Sovereign X

