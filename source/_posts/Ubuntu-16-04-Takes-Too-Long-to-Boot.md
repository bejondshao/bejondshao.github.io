layout: post
title: Ubuntu 16.04 Takes Too Long to Boot
tags:
  - Linux
  - Ubuntu
categories:
  - Linux
  - Ubuntu
date: 2018-05-10 10:40:00
---
This issue might happen to any ubuntu versions. It happends ubuntu 16.04 is widely used. It takes around 1 minute to boot. I'm afraid I'm using a fake ubuntu, especially a fake xubuntu:joy:.
Here are some ways to speed up booting:
1. Check out services that start up on booting.

`$ sudo systemd-analyze blame`
And the list:
```
         25.661s apt-daily.service
         19.036s apt-daily-upgrade.service
          8.411s vmware-USBArbitrator.service
          8.395s gpu-manager.service
          8.390s ModemManager.service
          7.347s dev-sda9.device
          6.647s NetworkManager-wait-online.service
          6.459s grub-common.service
          6.122s alsa-restore.service
          5.664s NetworkManager.service
          5.459s accounts-daemon.service
          5.325s nmbd.service
          4.398s snapd.service
          4.384s teamviewerd.service
          4.186s apport.service
          4.180s systemd-logind.service
          4.010s networking.service
          3.958s lm-sensors.service
          3.926s rsyslog.service
          2.873s winbind.service
          2.816s avahi-daemon.service
          2.723s udisks2.service
          2.549s thermald.service
```
@ `apt-daily.service`
This is [Debian bug #844453](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=844453). apt-daily.service shouldn't be run during boot, but only some time afterward. One workaround is edit `apt-daily.timer`
`$ sudo systemctl edit apt-daily.timer`
and paste the following text into the editor window:
```
# apt-daily timer configuration override
[Timer]
OnBootSec=15min
OnUnitActiveSec=1d
AccuracySec=1h
RandomizedDelaySec=30min
```
This changes the "timer" that triggers apt-daily.service to run at a random time between 15 min and 45 min after boot.

##### Attention: My system failed to boot after doing below `/etc/systemd/system.conf` actions, please prepare a USB installation before doing this. If it fails, you can use live system to revert, good luck:joy:
And for others recommendations, I also change `/etc/systemd/system.conf` file. Uncomment
```
#DefaultTimeoutStartSec=90s
#DefaultTimeoutStopSec=90s 
```
and change to
```
DefaultTimeoutStartSec=10s
DefaultTimeoutStopSec=10s 
```
@ `apt-daily-upgrade.service` helps daily apt upgrade and clean. I don't see I need to enable it. So I disable it by:
`$ sudo systemctl disable apt-daily-upgrade.service`
or you can also treat this service just like `apt-daily.service` by running:
`$ sudo systemctl edit apt-daily-upgrade.timer` and paste settings above.
@ For `vmware-USBArbitrator.service` I tried to disable it but failed. I disabled `vmware.service` and `vmware-workstation-server.service`

2. If your booting is extremely slow, like several minutes. It probably has problem about disk alaising. Please refer [here](https://askubuntu.com/a/809350/817986).

**References:**
[[1] Stackoverflow](https://askubuntu.com/a/897432/817986)
[[2] Stackoverflow](https://askubuntu.com/a/888199/817986)
[[3] `systemctl mask` vs `systemctl disable`](https://askubuntu.com/a/816378/817986)

**Extends:**
[[1] systemctl 命令完全指南
](https://linux.cn/article-5926-1.html)