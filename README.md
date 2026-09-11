# Linux System Design Notes

Personal study notes on robust Linux system software, multi-process architecture, IPC, event-driven programming, and process supervision.

## Disclaimer

This repository contains only general technical notes based on publicly available Linux APIs, open-source software, and common software architecture patterns.

It does **not** describe any specific company's products, internal architecture, source code, confidential information, or proprietary implementation.

The purpose of this repository is to study modern and robust system software design by referring to publicly available technologies and open-source systems.

## Topics

- Multi-process architecture
- IPC and shared memory
- `select`, `poll`, and `epoll`
- `eventfd`, `timerfd`, and `signalfd`
- `systemd`, restart policies, and watchdogs
- namespaces and cgroups
- process lifecycle and failure isolation
- reference architectures from public open-source systems

## Suggested reading order

1. [Multi-process architecture](docs/multiprocess-architecture.md)
2. [IPC](docs/ipc.md)
3. [epoll and event-driven programming](docs/epoll.md)
4. [systemd and process supervision](docs/systemd.md)
5. [Watchdog and restart](docs/watchdog-and-restart.md)
6. [cgroups and namespaces](docs/cgroups-and-namespaces.md)
7. [Reference architectures](docs/reference-architectures.md)

## Mental model

```text
                 Supervisor
                     |
        +------------+------------+
        |            |            |
     Worker A     Worker B     Worker C
        |            |
        +--- IPC ----+
             |
      shared memory

Event handling:
    epoll
      |- socket
      |- eventfd
      |- timerfd
      `- signalfd

Process management:
    systemd
      |- restart
      |- watchdog
      `- cgroups
```

## Design questions

When studying a system, ask:

1. Why is a process boundary used here?
2. What happens if one worker crashes?
3. Who owns the lifecycle of each process?
4. Which IPC mechanism is appropriate?
5. How are large data and control messages handled differently?
6. How is a hung process detected?
7. How are CPU and memory usage controlled?
8. How is shutdown handled safely?
9. How are logs and failures observed?
10. Which responsibilities belong to the application, and which belong to the OS?

## Goal

The goal is to understand how Linux kernel APIs and common Unix design principles can be combined to build small but robust system software.
