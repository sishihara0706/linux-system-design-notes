# Multi-Process Architecture

## Why use multiple processes?

A process boundary is a strong isolation boundary provided by the operating system. A crash, memory corruption, or blocking operation in one process does not automatically corrupt the address space of another process.

A generic Linux application may look like this:

```text
                 UI / Coordinator
                       |
                       | IPC
          +------------+------------+
          |            |            |
       Worker A     Worker B     Worker C
```

This pattern is useful when components have different responsibilities, failure modes, resource requirements, or security requirements.

## Benefits

### Failure isolation

If Worker B crashes with `SIGSEGV`, the other processes can remain alive. A supervisor can detect the termination and decide whether restarting the worker is safe.

### Independent lifecycle

Workers can be started, stopped, restarted, or upgraded independently when the application design allows it.

### Resource isolation

Linux cgroups can account for and limit CPU, memory, I/O, and process counts for groups of processes.

### Clear ownership

Process boundaries force the design to define who owns data and how components communicate. This can reduce accidental coupling, although it also introduces IPC complexity.

## Supervisor pattern

```text
                    Supervisor
                        |
             +----------+----------+
             |          |          |
          Worker A   Worker B   Worker C
```

The supervisor may be responsible for:

- starting workers
- observing process termination
- collecting status
- coordinating shutdown
- applying restart policy

On Linux, some lifecycle responsibilities can instead be delegated to `systemd`.

## Important principle

Do not create a process boundary merely because multiple processes appear more robust. Every boundary adds communication, synchronization, startup/shutdown, error handling, and observability requirements.

Use a process boundary when isolation or independent lifecycle management provides a concrete benefit.
