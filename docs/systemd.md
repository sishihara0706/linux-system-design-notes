# systemd and Process Supervision

`systemd` is the service manager used by many Linux distributions. For long-running applications it can own responsibilities that otherwise have to be implemented by an application-specific launcher or supervisor.

## Basic idea

Instead of manually starting processes:

```text
shell
  |- worker-a
  |- worker-b
  `- worker-c
```

services can be managed independently:

```text
                 systemd
                    |
        +-----------+-----------+
        |           |           |
    worker-a     worker-b     worker-c
```

## Responsibilities systemd can handle

- service startup and shutdown
- dependency ordering
- restart policies
- environment and execution settings
- logging integration with the journal
- watchdog integration
- cgroup-based process tracking and resource control

## Minimal service example

```ini
[Unit]
Description=Example worker

[Service]
ExecStart=/opt/example/bin/worker
Restart=on-failure
RestartSec=2

[Install]
WantedBy=multi-user.target
```

If the worker exits unexpectedly, `Restart=on-failure` tells systemd to start it again.

## Application supervisor vs OS supervisor

A useful architectural question is:

> Should the GUI/coordinator own worker lifecycle, or should the operating system own it?

Application-owned lifecycle can be appropriate when workers are tightly coupled to one application session.

OS-owned lifecycle can be useful when workers should remain independently manageable, restartable, observable, or resource-controlled.

The right answer depends on startup ordering, shared state, safety requirements, and what recovery actually means for the application.
