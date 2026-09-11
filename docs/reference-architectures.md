# Public Reference Architectures

This document studies general design ideas visible in publicly documented and open-source systems. It is not a description of any private or proprietary system.

## Chromium: process isolation

Chromium is a useful reference for understanding why a large GUI application may deliberately use multiple processes.

A simplified mental model is:

```text
             Browser process
                    |
                 IPC layer
        +-----------+-----------+
        |           |           |
    Renderer     Renderer     Utility
    process      process      process
```

Important lessons:

- a GUI application does not have to be a single process
- process boundaries can contain failures
- IPC becomes part of the architecture
- the coordinator should avoid doing every kind of work itself

## PostgreSQL: mature multi-process server

PostgreSQL is a useful reference for a long-running, mature multi-process system.

Simplified model:

```text
               PostgreSQL
                   |
        +----------+----------+
        |          |          |
     backend    backend    background
     process    process      worker
        \          |          /
         +---- shared state --+
```

Useful areas to study include process lifecycle, shared memory, synchronization, failure handling, and background workers.

## systemd: supervision and lifecycle

systemd is useful for studying what responsibilities can be delegated from an application to the operating system:

```text
                systemd
                   |
       +-----------+-----------+
       |           |           |
    service A   service B   service C
```

It provides a useful reference for restart policies, dependencies, watchdogs, logging, and cgroup-based service tracking.

## Containers: composing kernel primitives

Linux containers demonstrate another important design principle: a powerful abstraction can be built by composing smaller kernel mechanisms.

```text
container abstraction
       |
       +-- namespaces
       +-- cgroups
       +-- mounts / root filesystem
       +-- capabilities
       +-- security mechanisms
       `-- userspace runtime
```

## What to copy from large systems

Do not copy the scale or complexity of Chromium, PostgreSQL, Kubernetes, or large cloud systems without a concrete need.

Instead, study the problem-solving principles:

```text
problem
   |
   v
failure model
   |
   v
responsibility boundaries
   |
   v
kernel / OS mechanisms
   |
   v
smallest design that solves the problem
```

The useful question is not "How can I make this look like a large technology company's system?"

The useful question is "What problem forced a mature system to adopt this design, and does the same problem exist here?"
