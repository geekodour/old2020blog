# Logging in linux

### Old
- Kernel uses the `printk()` to log messages to a ring buffer.
    - Made available to the user in two ways:
        - `/proc/kmsg`
        - `sys_syslog()` sys call
    - Two applications that help in accessing the ring bufer:
        - dmesg : run by user
        - klogd(8) : daemon
            - uses `/proc/kmsg` or `sys_syslog()`
            - after fetching messages it sends them to `syslogd`
- In the userspace, `syslogd`
    - listens on:
        - unix domain sockets: `/dev/log`
        - UDP port 514 (syslog protocol)
        - klogd
    - Whites the messages to some file in:
        - `/log`
        - named pipe
        - send to some remote hosts (using syslog protocol)
- Userspace applications
    - uses `libc` function `syslog(3)`
        - `syslog(3)` sends these messages to `/dev/log`
            - `/dev/log` is then read by `syslogd` 

Other projects like `rsyslogd` and `syslog-ng` aim in replacing plain old `syslogd`

### New
- The kernel generates output to an in-memory buffer.
    - Application softwares can access this in two ways:
        - `/proc/kmsg` : read once, so can't be shared
        - `/dev/kmsg` : character device
            - rw interface, sharable
    - Applications use `syslog()`
        - connect to `AF_LOCAL` datagram socket `/dev/log` and write log entries to it.

## Info about other projects
---


### Syslog-ng
- Enhanced logging daemon
- Central logging
- Roles:
  - collector
    - collect system or application logs
      - system
        - `/dev/log`, journal, sun system etc
      - from the network: TCP/UDP/TLS
      - data from applications
        - sockets or pipes
        - application output
  - processor
    - classify, normalize and structure logs with builtin parsers
    - rewrite and reformat messages, eg. anonmyzation
    - enrich data etc
  - filter
    - removing unnecessary logs (eg. debug logs)
    - Message routing
  - storage or forwarder
    - Flat files, Network, TLS, SQl etc
    - Hadoop, MongoDB, Elk, Kafka etc
