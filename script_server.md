# Script Server

## Configure SSH
The script server is reached via a Bastion host. It is needed to configure the *~/.ssh/config* file. Add the following:

```console
Host bastion
  Hostname bastion2-sn4.m-sp.skanova.net
  PubkeyAcceptedAlgorithms +ssh-rsa
  HostkeyAlgorithms +ssh-rsa
  ControlPath ~/.ssh/my-socket1
  ControlMaster auto
```

This will make it possible to connect to the bastion using the short name ‘bastion’. The ControlPath and ControlMaster settings make it possible to open another terminal window and perform an ssh connection without the use of a password.

## Login to the bastion

Connect to the bastion and use the password from SafeNet 3300 token generator. Press the on button and enter your pin code. The password will be visible on the display.

```console
% ssh bastion
my-user@bastion2-sn4.m-sp.skanova.net's password: 
Last login: Mon Jan 23 09:49:22 2023 from 78-66-12-224-no
 
 
         *** BASTION2 SN4 LIST OF SHAME ***
   Below are the users that take most disk space. This server
   is for temporary storage only. Remove unneeded files.

      579MB  tomi kokko (kokkoto1)
      494MB  dan johansson (dajo63)
      159MB  sami toivonen (toivosa3)
      91MB  jens larm (jela39)
      74MB  tomi putkonen (putkoto1)
      38MB  nils magnusson (nimagn)
      24MB  mathias bjorken (mabj40)
my-user    pts/3        78-66-12-224-no6 Sun Jan 22 21:31 - 21:31  (00:00)
```

## Connect to the script server

The next step is to connect from the *bastion* to the script server itself. No password is needed because the configuration is automatically obtained from the *bastion* server.

```console 
my-user@bastion2-sn4 ~> ssh rgw-script1-sn92.han.skanova.net
Last login: Sun Jan 22 20:27:03 2023 from bastion2-sn4.m-sp.skanova.net

#########################################################################################

Deployed by Telia Company

    ###### !!! THIS SYSTEM IS UNSUPPORTED !!! ###### 
    
System information
    Host Name: rgw-script1-sn92.han.skanova.net
    OS Version: Red Hat Enterprise Linux 6.10

OS Support information
    Telia OS Phase out Date (Major Version): 2018-11-30 (-1515 days from now)
    Telia OS End of Support Date (Major Version): 2020-11-30 (-784 days from now)

OS Patching information
    OS Last Patched: 2020-11-16 01:03:16 (797 days ago)
    OS Patch Method: Automatic
    OS Patch Maintenance Window: Patch week 2 - Monday 01:00-04:00

Required Actions
    - This server is UNSUPPORTED and you need to perform LCM activities
      and DECOMMISSION this server or UPGRADE to a supported operating system. 

    ###### !!! THIS SYSTEM IS UNSUPPORTED !!! ###### 

##########################################################################################

[my-user@rgw-script1-sn92 ~]$ 
```

## Connect to an RGW

It is now possible to connect to an RGW. This is done differently depending on the model of the RGW. It is possible to connect to **any** RGW in the Telia Company network, so make sure that the connection is done to the right RGW.

### Sagemcom router

Connect to a Sagemcom router (this is the one in the lab close to where you normally sit).

```console
[my-user@rgw-script1-sn92 ~]$ ssh -p 60022 -i rgwv2.id_rsa support@10.151.68.26


BusyBox v1.30.1 (2022-12-14 01:53:26 CET) built-in shell (ash)
Enter 'help' for a list of built-in commands.

root@home:~# 
```

Control that this is the RGW that you believe it is.

```console
root@home:~# xmo-client -p Device/DeviceInfo/SerialNumber
path : Device/DeviceInfo/SerialNumber
value : 'LK18323DP886230'
root@home:~# xmo-client -p Device/WiFi/SSIDs/SSID[@uid=1]/SSID
path : Device/WiFi/SSIDs/SSID[@uid='1']/SSID
value : '#In-Rust-We-Trust'
root@home:~# 

```

### Technicolor router

Connect to a Technicolor router (I have no example IP for this).

```console
$ ssh -p 60022 -i rgwv2.id_rsa  tchroot@1.2.3.4


BusyBox v1.31.0 () built-in shell (ash)

  _______              __           __              __             
 |_     _|.-----.----.|  |--.-----.|__|.----.-----.|  |.-----.----.
   |   |  |  -__|  __||     |     ||  ||  __|  _  ||  ||  _  |   _|
   |___|  |_____|____||__|__|__|__||__||____|_____||__||_____|__|  
                 N E X T   G E N E R A T I O N   G A T E W A Y
 --------------------------------------------------------------------
 NG GATEWAY SIGNATURE DRINK
 --------------------------------------------------------------------
  * 1 oz Vodka          Pour all ingredients into mixing
  * 1 oz Triple Sec     tin with ice, strain into glass.
  * 1 oz Orange juice
 --------------------------------------------------------------------

Product: gcnt-x_telia
Release: Damson (19.4)
Version: 19.4.0856-4581007-20220720094317-5bde4b5646ee7061e320c605e10e3aaa98af4706

Hash config:         5bde4b5646ee7061e320c605e10e3aaa98af4706
Hash openwrt:        e08fa9adac2b18d19b452fb651b31a348e517fe0
Hash kernel:         5ed178a6837126ca99b6fa7c00c31e219b6dc555
Hash technicolor:    13a919ccee68dab7e86fe3a5d84539160be164de
Hash routing:        0a4c07e8ddf257269ac2d548dc70823679ef3ae8
Hash custo:          db11238956af76ac330afdd259d0837a43819457
Hash lte:            0444574fca10f0239a383a79b504c630d63d8ec2
Hash packages:       fd051bf6caed34ce415be2c834068a9f28b6a469

Bootloader: 21.05.1395-0000000-20210205075431-c82c4f289bbd35e048aab42640d9d284a37e3ae9

root@OpenWrt:/home/tchroot# 
```

The commands to verify are different for a Technicolor router.

```console
root@OpenWrt:/home/tchroot# uci get env.var.serial
CP1234567
root@OpenWrt:/home/tchroot# uci get wireless.wl1.ssid
#Telia123456
root@OpenWrt:/home/tchroot# 

````


