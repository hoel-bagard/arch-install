## Does not seem to be necessary, kept here just in case.

### Server NTPD

On the FreeIPA server: [documentation](https://docs.fedoraproject.org/en-US/fedora/rawhide/system-administrators-guide/servers/Configuring_NTP_Using_ntpd/) and [this one](https://docs.fedoraproject.org/en-US/Fedora/17/html/System_Administrators_Guide/sect-Configuring_the_Date_and_Time-Command_Line_Configuration-Network_Time_Protocol.html)
Disable Chrony

```
systemctl stop chronyd
systemctl disable chronyd
```

Install ntp:

```
yum install -y ntpsec
```

Gave up for now (ntpd can't start, `ConditionCapability=CAP_SYS_TIME`)\
(Potentially because running in a docker)

### Client NTPD

[NTP](https://wiki.archlinux.org/title/Network_Time_Protocol_daemon) can be installed with `pacman -S ntp`, but it does not seem to be necessary.
