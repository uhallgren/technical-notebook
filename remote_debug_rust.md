# How to remote debug Rust code

This is as simple as doing it in C/C++.

## Install the gdbserver

In a running container it is possible to find the gdbserver at the following location:

```
/usr/local/sdk/sagem/precompiled_toolchain/gcc-v5.5-2019.03.2/arm-scos-linux-gnueabi/gdbserver
```

```console 
cp /usr/local/sdk/sagem/precompiled_toolchain/gcc-v5.5-2019.03.2/arm-scos-linux-gnueabi/gdbserver .
scp gdbserver Administrator@192.168.1.1://telia/lxc/teliaLxc/root/                                                               
```

# Configure VSCode

The VSCode extension from Microsoft **C/C++ For Visual Studio** (ms-vscode.cpptools) needs to be installed.
The VSCode extension from Vadim Chugunov **CodeLLDB** (adimcn.vscode-lldb) needs to be installed.

# Create a launch target

In the .vscode/launch.json create the following.

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",
            "request": "custom",
            "name": "Remote Debug executable 'hello'",
            "cargo": {
                "args": [
                    "build",
                    "--bin=hello",
                    "--package=hello"
                ],
                "filter": {
                    "name": "hello",
                    "kind": "bin"
                }
            },
            "targetCreateCommands": [
                "target create ${workspaceFolder}/target/armv7-unknown-linux-gnueabi/debug/hello"
            ],
            "processCreateCommands": [
                "gdb-remote 192.168.1.65:2000"
            ],
        },
        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug executable 'hello'",
            "cargo": {
                "args": [
                    "build",
                    "--bin=hello",
                    "--package=hello"
                ],
                "filter": {
                    "name": "hello",
                    "kind": "bin"
                }
            },
            "args": [],
            "cwd": "${workspaceFolder}",
            "initCommands": [
                "settings set target.disable-aslr false"
            ],
        },
        {
            "type": "lldb",
            "request": "launch",
            "name": "Debug unit tests in executable 'hello'",
            "cargo": {
                "args": [
                    "test",
                    "--no-run",
                    "--bin=hello",
                    "--package=hello"
                ],
                "filter": {
                    "name": "hello",
                    "kind": "bin"
                }
            },
            "args": [],
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

## Debug

The first step is to copy the program to the target.

```console 
scp target/armv7-unknown-linux-gnueabi/debug/hello Administrator@192.168.1.1:root
```

In the container start the gdb server;

```console
root@teliaLxc:~# ./gdbserver localhost:2000 hello
Process /root/swan created; pid = 1343
Listening on port 2000
```

Start the debug from VSCode
