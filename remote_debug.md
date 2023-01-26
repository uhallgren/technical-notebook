# Remote Debugging

This document describes how to remote debug with VSCode to a Sagemcom target. It is a fairly straightforward process.

## Install the gdbserver

In a running container it is possible to find the gdbserver at the following location:

```
/usr/local/sdk/sagem/precompiled_toolchain/gcc-v5.5-2019.03.2/arm-scos-linux-gnueabi/gdbserver
```

```console 
cp /usr/local/sdk/sagem/precompiled_toolchain/gcc-v5.5-2019.03.2/arm-scos-linux-gnueabi/gdbserver .
scp gdbserver Administrator@192.168.1.1://telia/lxc/teliaLxc/root/                                                               
```

## Configure VSCode

The first step is to find the IP address of the container. 

```console
lxc-info teliaLxc
```

It is also possible to check inside the container:

```console 
root@teliaLxc:~# ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default 
    link/sit 0.0.0.0 brd 0.0.0.0
3: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN group default 
    link/tunnel6 :: brd ::
4: ip6gre0@NONE: <NOARP> mtu 1448 qdisc noop state DOWN group default 
    link/gre6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
5: eth0@if20: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 40:65:a3:c6:0e:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.1.65/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
root@teliaLxc:~# 
```

In this case, the IP address is **192.168.1.65**. Create the following *.vscode/launch.json* file or add the remote target to an existing file;

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Remote",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/builddir/test/swan",
            "args": [],
            "stopAtEntry": true,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "miDebuggerServerAddress": "192.168.1.65:2000",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
        }
    ]
}
```

**NOTE:** The *program* should point to the program that should be debugged.

## Debug

The first step is to copy the program to the target.

```console 
scp builddir/test/swan  Administrator@192.168.1.1://telia/lxc/teliaLxc/root/
```

In the container start the gdb server;

```console
root@teliaLxc:~# ./gdbserver localhost:2000 swan
Process /root/swan created; pid = 1343
Listening on port 2000
```

Start the debug from VSCode
