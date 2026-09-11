# epoll and Event-Driven Programming

`epoll` is a Linux interface for efficiently waiting for events on many file descriptors.

## Basic flow

```text
epoll_create1()
      |
epoll_ctl()       register file descriptors
      |
epoll_wait()      sleep until something becomes ready
      |
handle events
      |
repeat
```

Core APIs:

```c
int epoll_create1(int flags);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
int epoll_wait(int epfd, struct epoll_event *events,
               int maxevents, int timeout);
```

These libc functions expose Linux system-call interfaces.

## Typical event sources

An event loop can monitor more than network sockets:

```text
                    epoll_wait()
                         |
        +----------------+----------------+
        |                |                |
      socket          timerfd          eventfd
        |                |                |
     message          timeout          worker event
```

Other useful file-descriptor-based interfaces include `signalfd`.

This is an important Linux design idea: many different event sources can be represented as file descriptors and handled through one event loop.

## Common events

- `EPOLLIN` - readable
- `EPOLLOUT` - writable
- `EPOLLERR` - error condition
- `EPOLLHUP` - hangup
- `EPOLLET` - edge-triggered mode

## Why this matters for multi-process software

A coordinator process can use one event loop to observe IPC sockets, timers, and worker notifications without dedicating one blocking thread to every source.

```text
Coordinator
    |
    +-- IPC socket from Worker A
    +-- IPC socket from Worker B
    +-- timerfd
    +-- eventfd
    `-- signalfd
             |
          epoll
```

Start with level-triggered behavior before using edge-triggered (`EPOLLET`) mode. Edge-triggered designs normally require careful nonblocking I/O and draining until `EAGAIN`.
