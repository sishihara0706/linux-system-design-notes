# Watchdog and Restart

A process can fail in more than one way.

## Crash failure

```text
running
   |
SIGSEGV / abort / error
   |
process exits
```

A supervisor can observe the exit and apply a restart policy.

## Hang failure

A harder case is:

```text
process exists
PID exists
but useful work has stopped
```

Possible causes include:

- deadlock
- infinite loop
- blocked IPC
- stalled event loop

A simple restart-on-exit policy cannot detect this because the process has not exited.

## Watchdog concept

A healthy process periodically reports that it is alive and making expected progress.

```text
Worker                    Supervisor
  |                           |
  |------ heartbeat --------->|
  |------ heartbeat --------->|
  |------ heartbeat --------->|
  X                           |
                              |
                       watchdog timeout
                              |
                         recovery action
```

systemd supports service watchdog notifications for applications designed to participate in this protocol.

## Restart is not recovery by itself

Automatically restarting every failed process can make a system worse if state is corrupted or the failure repeats rapidly.

A robust design asks:

- Is restart safe?
- What state must be reconstructed?
- Is IPC state still valid?
- Is shared memory consistent?
- Should dependent processes reconnect?
- Should repeated crashes trigger backoff or escalation?

The important design problem is not simply "restart the process" but "restore the system to a known valid state."
