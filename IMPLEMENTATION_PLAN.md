IMPLEMENTATION\_PLAN.md

Project: TOJI OS v2.0 + Sovereign X

Type: Standalone AI‑native Operating System

Scope: From bootable skeleton to full AI‑governed OS with Windows 11–style desktop



1\. Goals and milestones

Goal: Ship a bootable ISO of TOJI OS v2.0 + Sovereign X with:



Custom init + core services



Sovereign X wired as AI core



TOJI API, Shell, and Windows 11–style Desktop



Milestones:



M1: Base OS boots to shell



M2: Core services + IPC broker running



M3: Sovereign X receiving events and issuing actions



M4: TOJI API + Shell usable



M5: TOJI Desktop (Dashboard + System + Sovereign panels)



M6: Memory + workflows integrated



2\. Phase 1 — Base OS skeleton

Objective: Bootable system with kernel + toji-init + minimal services.



Task 1.1: Integrate/fork kernel into kernel/



Configure build to produce toji\_kernel.img



Add boot/grub.cfg and build/build\_iso.sh



Task 1.2: Implement /sbin/toji-init



Mount /, /proc, /sys



Start a minimal shell (temporary) from /bin/sh



Task 1.3: Create basic rootfs/ layout



bin/, sbin/, usr/, etc/, var/, home/



Deliverable M1: ISO that boots into a shell via toji-init



3\. Phase 2 — Core system services and IPC broker

Objective: Stand up logd, proc\_manager, netd, pkgd, ipc\_broker.



Task 2.1: Implement logd



Socket: /var/run/logd.sock



Append logs to /var/log/system.log



Task 2.2: Implement proc\_manager



Socket: /var/run/proc.sock



Commands: spawn, kill, list



Task 2.3: Implement netd (minimal)



Bring up primary interface



Provide status via /var/run/net.sock



Task 2.4: Implement ipc\_broker



Socket: /var/run/ipc\_broker.sock



Service registration (service\_name)



Route messages by to field



Task 2.5: Update toji-init



Start logd, netd, pkgd, proc\_manager, ipc\_broker



Deliverable M2: Boot shows all services running; simple test client can send messages via ipc\_broker



4\. Phase 3 — Sovereign X core wiring

Objective: Sovereign X runs as a system service, receives events, sends actions.



Task 3.1: Implement sovereign\_x\_daemon



Connect to /var/run/ipc\_broker.sock as sovereign\_x



Main loop: receive → handle\_event → plan\_actions → send



Task 3.2: Implement policy engine



Load YAML/JSON policies from /etc/sovereign/policies



Enforce allow/deny on actions



Task 3.3: Implement memory backend



Store data under /var/lib/sovereign/



Provide simple get, put, append, search API



Task 3.4: Wire system events



proc\_manager → process\_started, process\_exited



netd → network\_up, network\_down



logd → critical\_log



Task 3.5: Wire actions



spawn/kill via proc\_manager



Logging decisions via logd



Task 3.6: Update toji-init



Start /usr/bin/sovereign\_x\_daemon after core services



Deliverable M3: Sovereign X reacts to process/network events and can spawn a test agent process



5\. Phase 4 — TOJI API and Shell

Objective: Human‑usable interface via HTTP and CLI.



Task 4.1: Implement toji\_api



Bind to localhost:7777



Connect to ipc\_broker as toji\_api



Task 4.2: Implement core endpoints



GET /system/status → query proc\_manager, netd



GET /system/processes



GET /sovereign/goals (stub from Sovereign X)



POST /sovereign/goal → send new\_goal event



Task 4.3: Implement TOJI Shell (/usr/bin/toji)



Map commands to HTTP calls:



toji sys status



toji sovereign goals



toji sovereign goal "..."



Task 4.4: Update toji-init



Start toji\_api after Sovereign X



Deliverable M4: From TTY, user can run toji sys status and toji sovereign goal "..." and see effects



6\. Phase 5 — TOJI Desktop (Windows 11–style)

Objective: Graphical control center for system + Sovereign X.



Task 5.1: Set up TOJI Desktop project in toji\_userland/desktop/



Choose toolkit (Flutter recommended)



Implement main window with Mica‑like background



Task 5.2: Implement navigation layout



Top bar: logo, title, search, user icon



Left sidebar: Dashboard, Sovereign X, Memory, Workflows, Agents, System, Settings



Status bar: CPU, RAM, network, Sovereign loop status, time



Task 5.3: Implement panels (v1)



Dashboard: system health, Sovereign status, recent events



System: processes list, basic metrics



Sovereign X: goals list, loop status, recent decisions



Task 5.4: Wire Desktop to toji\_api



HTTP for data, WebSocket for live updates



Task 5.5: Update toji-init



Start toji\_desktop as part of user session



Deliverable M5: Boot lands in TOJI Desktop; user can see system status and Sovereign info



7\. Phase 6 — Memory and workflows

Objective: Make Sovereign X and Desktop truly useful with memory + workflows.



Task 6.1: Implement memory\_service in toji\_userland/memory\_service/



Local store + optional vector search



IPC interface for Sovereign X



Task 6.2: Implement workflow engine in toji\_userland/workflows/



DAG definition format (YAML/JSON)



Runner that uses proc\_manager and agents



Task 6.3: Expose via toji\_api



GET /memory/search



GET /workflows



POST /workflows/run



Task 6.4: Extend Desktop



Memory panel: search + detail view



Workflows panel: list + basic DAG view



Task 6.5: Extend Sovereign X



Use memory for context



Trigger workflows as actions



Deliverable M6: Sovereign X can store/retrieve memory and trigger workflows; Desktop can inspect both



8\. Phase 7 — Policies, agents, and polish

Objective: Turn the system from prototype into a coherent AI OS.



Task 7.1: Expand policy engine



Fine‑grained rules for actions, resources, schedules



Task 7.2: Build agent catalog



Define agent types and configs in toji\_userland/agents/



UI in Agents panel to run/configure them



Task 7.3: UX and performance polish



Smooth animations, better error handling, logging



Task 7.4: Documentation and tooling



Developer docs



Scripts for building ISO and running in QEMU



9\. Suggested implementation order (tight loop)

Boot + init + shell (M1)



Core services + IPC broker (M2)



Sovereign X minimal loop (M3)



TOJI API + Shell (M4)



TOJI Desktop core panels (M5)



Memory + workflows (M6)



Policies, agents, polish (M7)

