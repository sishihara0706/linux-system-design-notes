# cgroups and Namespaces

Namespaces and cgroups solve different problems and are major building blocks of modern Linux isolation.

## Namespaces: what can a process see?

Namespaces provide separate views of kernel resources.

Examples include:

- PID namespace
- mount namespace
- network namespace
- UTS namespace
- IPC namespace
- user namespace
- cgroup namespace

Conceptually:

```text
same Linux kernel
       |
 +-----+-----+
 |           |
namespace A namespace B
 |           |
processes   processes
```

Processes can therefore have different views of process IDs, network interfaces, mounts, hostnames, and other resources while sharing the same kernel.

## cgroups: how much can a process use?

Control groups organize processes hierarchically and support resource accounting and control.

Depending on the controller and configuration, cgroups can manage resources such as:

- CPU
- memory
- I/O
- process counts

Conceptually:

```text
Linux
 |
 +-- group A
 |    |- worker-a
 |    `- worker-b
 |
 `-- group B
      `- worker-c
```

## Why containers use both

A useful simplified model is:

```text
namespace -> isolation / visibility
cgroups   -> resource accounting and control
```

A container is not one special kernel object called a "container." User-space runtimes combine several Linux mechanisms, including namespaces, cgroups, filesystem/mount mechanisms, capabilities, and other security controls, to provide the container abstraction.

## systemd connection

systemd uses cgroups to track services and their processes. This is one reason service management and resource management fit together naturally on modern Linux.
