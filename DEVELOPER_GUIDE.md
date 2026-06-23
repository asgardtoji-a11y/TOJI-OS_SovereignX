Project: TOJI OS v2.0 + Sovereign X

Audience: Core developers, contributors, and system integrators

Purpose: Provide a complete technical guide for building, modifying, and extending the OS



1\. Introduction

TOJI OS v2.0 + Sovereign X is a standalone AI‑native operating system.

This guide explains how to:



Build the OS from source



Understand the system architecture



Develop system services



Extend Sovereign X



Build and debug the TOJI Desktop



Work with agents, workflows, and memory



Contribute safely and consistently



This is the developer‑facing manual for the entire platform.



2\. System Requirements

Build Host

Linux (Ubuntu 22.04+ recommended)



Rust (latest stable)



Python 3.11+



Flutter SDK (for Desktop)



QEMU (for testing)



GRUB tools (for ISO creation)



Make or Ninja



Target Hardware

x86\_64 CPU



2GB RAM minimum



4GB recommended



Basic VGA or framebuffer



Standard keyboard/mouse



Basic network interface



3\. Repository Structure Overview

Code

toji-os/

│

├── kernel/                 # Kernel source (fork or submodule)

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

4\. Build Instructions

4.1 Build the Kernel

From the repo root:



Code

cd kernel

make clean

make

Output:

boot/toji\_kernel.img



4.2 Build System Services

Code

cd system

cargo build --release

Outputs go to:

rootfs/sbin/



4.3 Build Sovereign X

Code

cd sovereign\_x

pip install -r requirements.txt

Output:

rootfs/usr/bin/sovereign\_x\_daemon



4.4 Build TOJI API

Code

cd toji\_userland/api

pip install -r requirements.txt

Output:

rootfs/usr/bin/toji\_api



4.5 Build TOJI Desktop (Flutter)

Code

cd toji\_userland/desktop

flutter build linux --release

Output:

rootfs/usr/bin/toji\_desktop



4.6 Build the ISO

Code

cd build

./build\_iso.sh

Output:

toji-os.iso



5\. Running the OS

5.1 Run in QEMU

Code

cd build

./qemu\_run.sh

5.2 Boot on real hardware

Flash the ISO to USB using:



Balena Etcher



Rufus



dd



Boot from USB and select TOJI OS.



6\. System Architecture (Developer View)

6.1 Boot Sequence

Bootloader loads toji\_kernel.img



Kernel initializes hardware



Kernel executes /sbin/toji-init



toji-init starts:



logd



netd



pkgd



proc\_manager



ipc\_broker



sovereign\_x\_daemon



toji\_api



toji\_desktop



6.2 IPC Broker

All services communicate via:



Code

/var/run/ipc\_broker.sock

Message format

json

{

&#x20; "from": "service\_name",

&#x20; "to": "target\_service",

&#x20; "type": "event | action | query | response",

&#x20; "payload": { }

}

6.3 System Services

logd

Receives logs



Writes to /var/log/\*.log



proc\_manager

Spawns/kills processes



Reports process events



netd

Brings up network



Reports connectivity



pkgd

Manages packages (stub in v0.1)



ipc\_broker

Routes all messages



Maintains service registry



7\. Sovereign X Development

7.1 Sovereign X Loop

python

while True:

&#x20;   msg = broker.receive()

&#x20;   decision = handle\_event(msg)

&#x20;   actions = plan(decision)

&#x20;   for a in actions:

&#x20;       broker.send(a)

7.2 Adding New Event Handlers

Add to:



Code

sovereign\_x/src/events/

Register in:



Code

handle\_event()

7.3 Adding New Actions

Add to:



Code

sovereign\_x/src/actions/

8\. TOJI API Development

TOJI API is the bridge between:



Desktop



Shell



Sovereign X



System services



Endpoints

Code

GET /system/status

GET /system/processes

GET /sovereign/goals

POST /sovereign/goal

GET /memory/search

GET /workflows

POST /workflows/run

Adding new endpoints

Add a file under:



Code

toji\_userland/api/routes/

Register in:



Code

main.py

9\. TOJI Desktop Development

9.1 UI Framework

Flutter



Windows 11 Fluent‑style components



Mica + Acrylic effects



9.2 Panels

Dashboard



Sovereign X



System



Memory



Workflows



Agents



Settings



9.3 Adding a new panel

Create a folder under:



Code

toji\_userland/desktop/lib/panels/

Add a route in:



Code

navigation.dart

Add API calls in:



Code

services/api\_client.dart

10\. Memory Service Development

Memory types:



Episodic



Semantic



Procedural



Policy memory



Stored under:



Code

/var/lib/sovereign/

Search API exposed via:



Code

GET /memory/search

11\. Workflow Engine Development

Workflows are DAGs:



Code

workflows/

&#x20; example.yaml

Nodes = tasks

Edges = dependencies



Workflow runner uses:



proc\_manager



Agents



12\. Agents Development

Agents live under:



Code

toji\_userland/agents/

Each agent includes:



Config



Entry point



Capabilities



Permissions



Sovereign X can spawn agents via proc\_manager.



13\. Coding Standards

Rust: rustfmt, Clippy



Python: Black, Ruff



Flutter: dart format



Commit messages: Conventional Commits



Branching: feature/\*, fix/\*, release/\*



14\. Testing

Unit Tests

Rust: cargo test



Python: pytest



Flutter: flutter test



Integration Tests

QEMU boot tests



IPC routing tests



Sovereign X decision loop tests



15\. Contributing

Fork the repo



Create a feature branch



Write clean, documented code



Add tests



Submit PR



Pass CI



Await review



16\. Roadmap

See:



ARCHITECTURE.md



IMPLEMENTATION\_PLAN.md



project.json

