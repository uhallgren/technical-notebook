# SSH timeout

During development on the Sagemcom router, it is very annoying that you get automatically logged out. This can be avoided by changing the SSH access timeout to a greater value.

You may check which UserSessions that exists with
```console
xmo-client -p Device/UserAccounts/Users/User[2]/CurrentSessions/UserSession
```
Try to increase the timeout

```console
xmo-client -p Device/UserAccounts/Users/User[2]/SshAccessTimeout -s 18000
mo-client -p Device/UserAccounts/Users/User[2]/CurrentSessions/UserSession/Timeout -s 18000
```

