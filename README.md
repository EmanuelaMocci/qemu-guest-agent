# QEMU Guest Agent

QEMU Guest Agent version for FreeBSD guest

## WARNING!!!

This port provides as is and now in deep development status. Many commands is not working, for example "fsfreeze". We try make a patches for vcpu and fs features at FreeBSD.
Command reference and current status in FreeBSD can be found [here](https://github.com/aborche/qemu-guest-agent/blob/master/supported_command_reference.txt)

## Getting Started

Before using and compiling QEMU Guest Agent, be sure module "virtio_console"
is loaded

### Prerequisites

Run following command in console

```
# kldload virtio_console
```

For permanent loading virtio_console driver add next line to /boot/loader.conf

```
virtio_console_load="YES"
```

And check filesystem contents

```
# ls -al /dev/vtcon/
total 1
dr-xr-xr-x   2 root  wheel  512 Oct 22 16:05 .
dr-xr-xr-x  11 root  wheel  512 Oct 22 16:05 ..
lrwxr-xr-x   1 root  wheel   10 Oct 22 16:05 com.redhat.spice.0 -> ../ttyV0.1
lrwxr-xr-x   1 root  wheel   10 Oct 22 16:05 org.qemu.guest_agent.0 -> ../ttyV0.2
```

### Installing

Clone this repo and run `make` and then `make install`

```
Give the example
```

Add next lines to `/etc/rc.conf` file

```
qemu_guest_agent_enable="YES"
qemu_guest_agent_flags="-d -v -l /var/log/qemu-ga.log"
```

Flags means:
 -d - daemon
 -v - verbose
 -l /var/log/qemu-ga.log - log all to file

## Run Agent

After installing qemu-guest-agent, run it with command:

```
# service qemu-guest-agent start
```

## Check Logs

Navigate to `/var/log` directory and check file `qemu-ga.log`.
You should see next log records.

```
1540239465.752180: debug: disabling command: guest-suspend-disk
1540239465.752205: debug: disabling command: guest-suspend-ram
1540239465.752215: debug: disabling command: guest-suspend-hybrid
1540239465.752224: debug: disabling command: guest-network-get-interfaces
1540239465.752233: debug: disabling command: guest-get-vcpus
1540239465.752241: debug: disabling command: guest-set-vcpus
1540239465.752250: debug: disabling command: guest-get-memory-blocks
1540239465.752259: debug: disabling command: guest-set-memory-blocks
1540239465.752267: debug: disabling command: guest-get-memory-block-size
1540239465.752276: debug: disabling command: guest-get-fsinfo
1540239465.752285: debug: disabling command: guest-fsfreeze-status
1540239465.752293: debug: disabling command: guest-fsfreeze-freeze
1540239465.752302: debug: disabling command: guest-fsfreeze-freeze-list
1540239465.752310: debug: disabling command: guest-fsfreeze-thaw
1540239465.752318: debug: disabling command: guest-get-fsinfo
1540239465.752327: debug: disabling command: guest-fstrim
```

## Send commands from QEMU

Open console at Linux QEMU/KVM machine, locate name of FreeBSD virtual
machine, and run command

```
# virsh qemu-agent-command freebsd-nginx-12  '{"execute":"guest-get-osinfo"}'
```

Returned string must be like

```
{"return":{"kernel-release":"12.0-BETA1","kernel-version":"FreeBSD 12.0-BETA1 r339443 GENERIC","machine":"amd64"}}
```

Also you can check `qemu-ga.log` for detailed info

```
1540239784.932925: debug: read data, count: 59, data: {"execute":"guest-sync", "arguments":{"id":1540239785425}}

1540239784.932996: debug: process_event: called
1540239784.933100: debug: processing command
1540239784.933182: debug: sending count: 26, data: [{"return": 1540239785425}]
1540239784.933589: debug: read data, count: 31, data: {"execute":"guest-get-osinfo"}

1540239784.933668: debug: process_event: called
1540239784.933689: debug: processing command
1540239784.934186: debug: sending count: 121, data: [{"return": {"kernel-release": "12.0-BETA1", "kernel-version": "FreeBSD 12.0-BETA1 r339443 GENERIC", "machine": "amd64"}}]
```

If you do not need verbose logging, just remove '-v' flag from qemu_guest_agent_flags section in `/etc/rc.conf`

## QAPI command reference and struct

* [qapi-schema](https://github.com/qemu/qemu/blob/master/qga/qapi-schema.json)

## Port authors

* **Kaltashkin Eugene** - [aborche](https://github.com/aborche)

## License

This project is licensed under the BSD License - see the [LICENSE.md](LICENSE.md) file for details

