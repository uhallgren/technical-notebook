# SSH timeout

During development on the Sagemcom router, it is very annoying that you get automatically logged out. This can be avoided by changing the SSH access timeout to a greater value.

```console
xmo-client -p Device/UserAccounts/Users/User[2]/SshAccessTimeout -s 18000
```

