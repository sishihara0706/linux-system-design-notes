# IPC on Linux

IPC (Inter-Process Communication) lets processes exchange data and synchronization events.

## Common mechanisms

| Mechanism | Good for |
|---|---|
| pipe | simple parent/child byte stream |
| Unix domain socket | local bidirectional messaging |
| `socketpair()` | bidirectional communication between related processes |
| shared memory / `mmap()` | large or high-throughput data |
| semaphore | synchronization |
| `eventfd` | lightweight event notification / counters |
| signal | simple asynchronous process notification |

## Control plane vs data plane

A useful design pattern is to separate small control messages from large data transfers.

```text
Worker A                     Worker B
   |                            |
   |--- Unix socket ----------->|  control message
   |                            |
   +------ shared memory -------+  large data
```

For example, a process can place a large buffer in shared memory and send only metadata or a notification through a socket or `eventfd`.

This avoids repeatedly copying large payloads through a message channel.

## Shared memory warning

Shared memory is fast because processes can access the same memory pages, but this also removes some of the isolation that makes multi-process designs attractive.

The design must define:

- ownership
- lifetime
- synchronization
- consistency
- what happens when a process crashes while updating data

## Choosing an IPC mechanism

Ask what the communication actually represents:

- byte stream -> pipe/socket
- structured request/response -> Unix domain socket or higher-level IPC
- large shared buffer -> shared memory
- wake-up notification -> eventfd
- process lifecycle event -> signal/pidfd/wait mechanisms

The IPC mechanism should follow the data and failure model rather than being chosen only for performance.
