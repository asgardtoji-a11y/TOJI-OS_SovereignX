ARCHITECTURE.md

Project: TOJI OS v2.0 + Sovereign X

Type: Standalone AI‑native Operating System

Owner: Edgar Rodrigues



1\. Overview

TOJI OS v2.0 + Sovereign X is a standalone, AI‑native operating system.

It combines:



A custom OS stack (kernel + init + system services + userland)



A Windows 11–style TOJI Desktop as the primary UI



Sovereign X as a core, privileged system service that observes system events, reasons about them, and orchestrates agents, workflows, and processes across the entire OS.



TOJI OS is the system layer; Sovereign X is the intelligence layer. Together they form a dual‑core OS: one core for computation, one core for autonomous reasoning.



2\. High‑level architecture

2.1 Layered view

Hardware



CPU, RAM, storage, network, input, display.



Kernel layer



Based on a microkernel‑style OS (e.g., Redox‑like).



Responsibilities:



Process scheduling



Memory management



Filesystem and block devices



Basic drivers (keyboard, mouse, display, network)



System calls and hardware abstraction



System layer (TOJI Core)



toji-init (PID 1)



Core system services:



logd (logging)



netd (network)



pkgd (package manager)



proc\_manager (process manager)



ipc\_broker (message bus)



Provides:



Boot orchestration



Service lifecycle



System logging



Networking



Process control



Inter‑process communication fabric



AI layer (Sovereign X)



sovereign\_x\_daemon as a privileged system service.



Responsibilities:



Autonomous reasoning loop



Long‑term memory



Policy engine



Event‑driven automation



Agent and workflow orchestration



Communicates via ipc\_broker with:



System services (e.g., proc\_manager, logd, netd)



TOJI API (user goals, commands, notifications)



Userland layer (TOJI Userland)



TOJI Desktop (Windows 11–style GUI)



TOJI Shell (toji CLI)



TOJI API (local HTTP/WebSocket bridge)



Agents, workflows, memory services



Provides:



Human interface (GUI + CLI)



Agent management



Workflow management



Memory browsing and search



System monitoring and control



3\. Repository and filesystem structure

3.1 Repository layout

text

toji-os/

│

├── kernel/                 # Kernel (fork or submodule)

│   └── ...

│

├── system/                 # TOJI system layer

│   ├── init/               # toji-init (PID 1)

│   ├── services/

│   │   ├── logd/

│   │   ├── netd/

│   │   ├── pkgd/

│   │   ├── proc\_manager/

│   │   └── ipc\_broker/

│   └── ipc/                # IPC utilities

│

├── sovereign\_x/            # AI core

│   ├── src/

│   │   ├── sovereign\_daemon.py

│   │   ├── policy\_engine.py

│   │   ├── memory/

│   │   ├── tools/

│   │   ├── events/

│   │   └── sandbox/

│   └── config/

│

├── toji\_userland/

│   ├── desktop/            # TOJI Desktop (Flutter/native)

│   ├── shell/              # TOJI Shell (CLI)

│   ├── api/                # TOJI API (HTTP/WebSocket)

│   ├── agents/

│   ├── workflows/

│   └── memory\_service/

│

├── rootfs/                 # Root filesystem image

│   ├── bin/

│   ├── sbin/

│   ├── usr/

│   ├── etc/

│   ├── var/

│   └── home/

│

├── boot/                   # Bootloader + kernel image

│   ├── grub.cfg

│   └── toji\_kernel.img

│

└── build/                  # Build scripts and ISO

&#x20;   ├── build\_iso.sh

&#x20;   └── qemu\_run.sh

3.2 Root filesystem key paths

/sbin/toji-init — init process (PID 1)



/sbin/logd — logging daemon



/sbin/netd — network daemon



/sbin/pkgd — package manager daemon



/sbin/proc\_manager — process manager



/sbin/ipc\_broker — IPC message bus



/usr/bin/sovereign\_x\_daemon — Sovereign X core service



/usr/bin/toji\_api — local API server



/usr/bin/toji\_desktop — GUI



/usr/bin/toji — CLI shell



/etc/sovereign/ — policies and config



/var/run/\*.sock — IPC sockets



/var/log/\*.log — logs



/var/lib/sovereign/ — AI memory and state



4\. Boot and init sequence

4.1 Boot flow

Bootloader loads toji\_kernel.img.



Kernel initializes:



Memory, scheduler, drivers



Root filesystem



Kernel execs /sbin/toji-init as PID 1.



4.2 toji-init responsibilities

Mount filesystems:



/ root



/proc, /sys, etc.



Start core system services:



logd



netd



pkgd



proc\_manager



ipc\_broker



Start AI core:



sovereign\_x\_daemon



Start userland:



toji\_api



toji\_desktop



Optional TTY shells



Conceptual flow:



text

kernel → /sbin/toji-init

toji-init:

&#x20; → logd, netd, pkgd, proc\_manager, ipc\_broker

&#x20; → sovereign\_x\_daemon

&#x20; → toji\_api

&#x20; → toji\_desktop

5\. System services

5.1 logd (logging daemon)

Receives log messages via /var/run/logd.sock.



Writes structured logs to /var/log/\*.log.



Used by:



Kernel (optionally)



System services



Sovereign X



TOJI API



5.2 proc\_manager (process manager)

Manages process lifecycle:



spawn, kill, list



Exposes IPC via /var/run/proc.sock.



Executes commands requested by:



toji-init (on boot)



Sovereign X (automation)



TOJI API (user actions)



5.3 netd (network daemon)

Brings up network interfaces.



Provides basic network status via /var/run/net.sock.



Reports connectivity events to Sovereign X via ipc\_broker.



5.4 pkgd (package manager daemon)

Manages installation and updates of userland components.



For v0.1 can be a stub or simple local package installer.



5.5 ipc\_broker (message bus)

Central router for inter‑service communication.



Listens on /var/run/ipc\_broker.sock.



Each service registers with a service name.



Routes messages based on to field in message envelope.



6\. IPC and message model

6.1 Transport

Unix domain socket: /var/run/ipc\_broker.sock.



All services connect as clients and identify themselves.



6.2 Message envelope (JSON)

json

{

&#x20; "from": "service\_name",

&#x20; "to": "target\_service",

&#x20; "type": "event | action | query | response",

&#x20; "event": "event\_name\_if\_applicable",

&#x20; "action": "action\_name\_if\_applicable",

&#x20; "correlation\_id": "optional-id-for-request-response",

&#x20; "payload": { "arbitrary": "data" }

}

6.3 Example messages

Event → Sovereign X:



json

{

&#x20; "from": "proc\_manager",

&#x20; "to": "sovereign\_x",

&#x20; "type": "event",

&#x20; "event": "process\_exited",

&#x20; "payload": { "pid": 1234, "code": 0 }

}

Action ← Sovereign X:



json

{

&#x20; "from": "sovereign\_x",

&#x20; "to": "proc\_manager",

&#x20; "type": "action",

&#x20; "action": "spawn",

&#x20; "payload": {

&#x20;   "cmd": "/usr/bin/agent\_worker",

&#x20;   "args": \["--task", "research", "--id", "123"]

&#x20; }

}

7\. Sovereign X (AI core)

7.1 Role

Sovereign X is a privileged system service that:



Listens to system and user events.



Maintains long‑term memory.



Applies policies to decide what is allowed.



Plans and issues actions to system services and agents.



Logs its reasoning and decisions.



7.2 Components

Event loop: receives messages from ipc\_broker.



Policy engine: reads rules from /etc/sovereign/policies.



Memory backend: stores episodic, semantic, and procedural memory in /var/lib/sovereign/.



Planner: converts events + goals into actions and workflows.



Tool/agent orchestrator: spawns and manages agents via proc\_manager.



7.3 Inputs and outputs

Inputs:



System events:



Process start/exit (proc\_manager)



Network changes (netd)



Critical logs (logd)



User events:



New goals, agent requests (toji\_api)



Outputs:



Actions:



Spawn/kill processes (proc\_manager)



Trigger workflows (toji\_userland/workflows)



Logs:



Decisions and reasoning (logd)



Notifications:



Status and updates (toji\_api → Desktop/Shell)



8\. TOJI userland

8.1 TOJI API

Local HTTP/WebSocket server (e.g., localhost:7777).



Bridges userland (Desktop/Shell) with system services and Sovereign X via ipc\_broker.



Example endpoints:



GET /system/status → queries proc\_manager, netd.



GET /system/processes → process list.



GET /sovereign/goals → current goals from Sovereign X.



POST /sovereign/goal → send new\_goal event to Sovereign X.



GET /memory/search?q=... → memory search.



GET /workflows / POST /workflows/run → workflow engine.



8.2 TOJI Desktop (Windows 11–style)

Visual style:



Mica background for main window.



Acrylic sidebar and overlays.



Rounded corners (8–12px), soft shadows.



Segoe UI Variable (or Inter) typography.



Windows 11‑like accent colors.



Layout:



Top bar (Mica):



TOJI logo, title, search, user icon.



Left sidebar (Acrylic):



Dashboard



Sovereign X



Memory



Workflows



Agents



System



Settings



Main content (Mica cards):



Dashboard: system health, Sovereign status, recent events.



Sovereign X: goals list, decision log, loop status.



Memory: search + detail view.



Workflows: list + DAG editor.



Agents: agent cards with Run buttons.



System: processes, network, storage, logs.



Status bar (Acrylic):



CPU, RAM, network, Sovereign loop status, time.



Data flow:



Desktop → toji\_api (HTTP/WebSocket).



toji\_api ↔ ipc\_broker ↔ system services + Sovereign X.



8.3 TOJI Shell (toji CLI)

Thin CLI client that talks to toji\_api.



Example commands:



toji sys status



toji sovereign goals



toji sovereign goal "research topic"



toji wf list



toji wf run <name>



9\. Memory and workflows

9.1 Memory service

Lives in toji\_userland/memory\_service/.



Provides:



Key‑value and document storage.



Vector search for semantic memory.



Exposed via:



IPC (for Sovereign X).



toji\_api (for Desktop/Shell).



9.2 Workflow engine

Lives in toji\_userland/workflows/.



DAG‑based workflows:



Nodes = tasks/agents.



Edges = dependencies.



Sovereign X can:



Trigger workflows.



Monitor progress via events.



10\. End‑to‑end flow summary

Boot:



Bootloader → kernel → /sbin/toji-init.



Init:



toji-init starts system services, ipc\_broker, Sovereign X, toji\_api, toji\_desktop.



User interaction:



User uses Desktop or Shell → calls toji\_api.



API bridge:



toji\_api sends/receives messages via ipc\_broker.



AI core:



Sovereign X receives events, consults memory and policies, plans actions.



System actions:



Actions go to proc\_manager, workflows, memory, etc.



Feedback:



Logs and notifications flow back through logd and toji\_api to Desktop/Shell.



11\. Roadmap (implementation phases)

Phase 1 — Base OS skeleton



Kernel + toji-init + minimal logd, proc\_manager, ipc\_broker.



Bootable ISO with a simple shell.



Phase 2 — Sovereign X integration



Implement sovereign\_x\_daemon with basic event handling.



Wire system events and actions via ipc\_broker.



Phase 3 — TOJI API + Shell



Implement toji\_api and toji CLI.



Basic system and Sovereign queries.



Phase 4 — TOJI Desktop



Windows 11–style UI with Dashboard, Sovereign, System panels.



Live data via toji\_api.



Phase 5 — Memory + workflows



Integrate memory service and workflow engine.



Expose to Sovereign X and Desktop.



Phase 6 — Policies, agents, polish



Rich policy engine.



Agent catalog.



Performance and UX refinement.

