# Qemu KVM Centos 7.0 更新和安装 http

> 原文：<https://dev.to/psnebc/qemu-kvm-centos-70-update-and-install-http-2p8e>

[http://192 . 168 . 122 . 224/](https://i.imgur.com/fIYoQz6.png)T2】

```
➜  ~ sudo yum clean all
[sudo] Passwort für psnebc: 
Geladene Plugins: fastestmirror
Quellen werden aufgeräumt:base extras updates
Cleaning up everything
Maybe you want: rm -rf /var/cache/yum, to also free up space taken by orphaned data from disabled or removed repos
Cleaning up list of fastest mirrors
➜  ~ sudo yum -y update
Geladene Plugins: fastestmirror
base                                                                                                                       | 3.6 kB  00:00:00     
extras                                                                                                                     | 3.4 kB  00:00:00     
updates                                                                                                                    | 3.4 kB  00:00:00     
(1/4): base/7/x86_64/group_gz                                                                                              | 156 kB  00:00:03     
(2/4): extras/7/x86_64/primary_db                                                                                          | 185 kB  00:00:03     
(3/4): base/7/x86_64/primary_db                                                                                            | 5.7 MB  00:00:07     
(4/4): updates/7/x86_64/primary_db                                                                                         | 6.9 MB  00:00:08     
Determining fastest mirrors
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket NetworkManager.x86_64 1:1.8.0-9.el7 markiert, um aktualisiert zu werden
--------> Paket NetworkManager.x86_64 1:1.8.0-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket NetworkManager-libnm.x86_64 1:1.8.0-9.el7 markiert, um aktualisiert zu werden
--------> Paket NetworkManager-libnm.x86_64 1:1.8.0-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket NetworkManager-team.x86_64 1:1.8.0-9.el7 markiert, um aktualisiert zu werden
--------> Paket NetworkManager-team.x86_64 1:1.8.0-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket NetworkManager-tui.x86_64 1:1.8.0-9.el7 markiert, um aktualisiert zu werden
--------> Paket NetworkManager-tui.x86_64 1:1.8.0-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket NetworkManager-wifi.x86_64 1:1.8.0-9.el7 markiert, um aktualisiert zu werden
--------> Paket NetworkManager-wifi.x86_64 1:1.8.0-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket bash.x86_64 0:4.2.46-28.el7 markiert, um aktualisiert zu werden
--------> Paket bash.x86_64 0:4.2.46-29.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket bind-libs-lite.x86_64 32:9.9.4-50.el7 markiert, um aktualisiert zu werden
--------> Paket bind-libs-lite.x86_64 32:9.9.4-51.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket bind-license.noarch 32:9.9.4-50.el7 markiert, um aktualisiert zu werden
--------> Paket bind-license.noarch 32:9.9.4-51.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket binutils.x86_64 0:2.25.1-31.base.el7 markiert, um aktualisiert zu werden
--------> Paket binutils.x86_64 0:2.25.1-32.base.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket cpio.x86_64 0:2.11-24.el7 markiert, um aktualisiert zu werden
--------> Paket cpio.x86_64 0:2.11-25.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket cryptsetup-libs.x86_64 0:1.7.4-3.el7 markiert, um aktualisiert zu werden
--------> Paket cryptsetup-libs.x86_64 0:1.7.4-3.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket device-mapper-persistent-data.x86_64 0:0.7.0-0.1.rc6.el7 markiert, um aktualisiert zu werden
--------> Paket device-mapper-persistent-data.x86_64 0:0.7.0-0.1.rc6.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket dhclient.x86_64 12:4.2.5-58.el7.centos markiert, um aktualisiert zu werden
--------> Paket dhclient.x86_64 12:4.2.5-58.el7.centos.3 markiert, um eine Aktualisierung zu werden
--------> Paket dhcp-common.x86_64 12:4.2.5-58.el7.centos markiert, um aktualisiert zu werden
--------> Paket dhcp-common.x86_64 12:4.2.5-58.el7.centos.3 markiert, um eine Aktualisierung zu werden
--------> Paket dhcp-libs.x86_64 12:4.2.5-58.el7.centos markiert, um aktualisiert zu werden
--------> Paket dhcp-libs.x86_64 12:4.2.5-58.el7.centos.3 markiert, um eine Aktualisierung zu werden
--------> Paket dracut.x86_64 0:033-502.el7 markiert, um aktualisiert zu werden
--------> Paket dracut.x86_64 0:033-502.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket dracut-config-rescue.x86_64 0:033-502.el7 markiert, um aktualisiert zu werden
--------> Paket dracut-config-rescue.x86_64 0:033-502.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket dracut-network.x86_64 0:033-502.el7 markiert, um aktualisiert zu werden
--------> Paket dracut-network.x86_64 0:033-502.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket glibc.x86_64 0:2.17-196.el7 markiert, um aktualisiert zu werden
--------> Paket glibc.x86_64 0:2.17-196.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket glibc-common.x86_64 0:2.17-196.el7 markiert, um aktualisiert zu werden
--------> Paket glibc-common.x86_64 0:2.17-196.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket grub2.x86_64 1:2.02-0.64.el7.centos markiert, um veraltet zu werden
--------> Paket grub2.x86_64 1:2.02-0.65.el7.centos.2 markiert, um Aufräumen zu werden
--------> Paket grub2-common.noarch 1:2.02-0.64.el7.centos markiert, um aktualisiert zu werden
--------> Paket grub2-common.noarch 1:2.02-0.65.el7.centos.2 markiert, um eine Aktualisierung zu werden
--------> Paket grub2-pc.x86_64 1:2.02-0.64.el7.centos markiert, um aktualisiert zu werden
--------> Paket grub2-pc.x86_64 1:2.02-0.65.el7.centos.2 markiert, um eine Aktualisierung zu werden
--------> Paket grub2-pc-modules.noarch 1:2.02-0.64.el7.centos markiert, um aktualisiert zu werden
--------> Paket grub2-pc-modules.noarch 1:2.02-0.65.el7.centos.2 markiert, um eine Aktualisierung zu werden
--------> Paket grub2-tools.x86_64 1:2.02-0.64.el7.centos markiert, um veraltet zu werden
--------> Paket grub2-tools.x86_64 1:2.02-0.65.el7.centos.2 markiert, um Aufräumen zu werden
--------> Paket grub2-tools-extra.x86_64 1:2.02-0.64.el7.centos markiert, um aktualisiert zu werden
--------> Paket grub2-tools-extra.x86_64 1:2.02-0.65.el7.centos.2 markiert, um Aufräumen zu werden
--------> Paket grub2-tools-minimal.x86_64 1:2.02-0.64.el7.centos markiert, um aktualisiert zu werden
--------> Paket grub2-tools-minimal.x86_64 1:2.02-0.65.el7.centos.2 markiert, um Aufräumen zu werden
--------> Paket initscripts.x86_64 0:9.49.39-1.el7 markiert, um aktualisiert zu werden
--------> Paket initscripts.x86_64 0:9.49.39-1.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket iptables.x86_64 0:1.4.21-18.0.1.el7.centos markiert, um aktualisiert zu werden
--------> Paket iptables.x86_64 0:1.4.21-18.3.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl100-firmware.noarch 0:39.31.5.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl100-firmware.noarch 0:39.31.5.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl1000-firmware.noarch 1:39.31.5.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl1000-firmware.noarch 1:39.31.5.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl105-firmware.noarch 0:18.168.6.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl105-firmware.noarch 0:18.168.6.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl135-firmware.noarch 0:18.168.6.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl135-firmware.noarch 0:18.168.6.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl2000-firmware.noarch 0:18.168.6.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl2000-firmware.noarch 0:18.168.6.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl2030-firmware.noarch 0:18.168.6.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl2030-firmware.noarch 0:18.168.6.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl3160-firmware.noarch 0:22.0.7.0-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl3160-firmware.noarch 0:22.0.7.0-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl3945-firmware.noarch 0:15.32.2.9-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl3945-firmware.noarch 0:15.32.2.9-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl4965-firmware.noarch 0:228.61.2.24-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl4965-firmware.noarch 0:228.61.2.24-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl5000-firmware.noarch 0:8.83.5.1_1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl5000-firmware.noarch 0:8.83.5.1_1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl5150-firmware.noarch 0:8.24.2.2-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl5150-firmware.noarch 0:8.24.2.2-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl6000-firmware.noarch 0:9.221.4.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl6000-firmware.noarch 0:9.221.4.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl6000g2a-firmware.noarch 0:17.168.5.3-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl6000g2a-firmware.noarch 0:17.168.5.3-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl6000g2b-firmware.noarch 0:17.168.5.2-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl6000g2b-firmware.noarch 0:17.168.5.2-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl6050-firmware.noarch 0:41.28.5.1-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl6050-firmware.noarch 0:41.28.5.1-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl7260-firmware.noarch 0:22.0.7.0-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl7260-firmware.noarch 0:22.0.7.0-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket iwl7265-firmware.noarch 0:22.0.7.0-56.el7 markiert, um aktualisiert zu werden
--------> Paket iwl7265-firmware.noarch 0:22.0.7.0-58.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket kernel.x86_64 0:3.10.0-693.21.1.el7 markiert, um installiert zu werden
--------> Paket kernel-tools.x86_64 0:3.10.0-693.el7 markiert, um aktualisiert zu werden
--------> Paket kernel-tools.x86_64 0:3.10.0-693.21.1.el7 markiert, um eine Aktualisierung zu werden
--------> Paket kernel-tools-libs.x86_64 0:3.10.0-693.el7 markiert, um aktualisiert zu werden
--------> Paket kernel-tools-libs.x86_64 0:3.10.0-693.21.1.el7 markiert, um eine Aktualisierung zu werden
--------> Paket kexec-tools.x86_64 0:2.0.14-17.el7 markiert, um aktualisiert zu werden
--------> Paket kexec-tools.x86_64 0:2.0.14-17.2.el7 markiert, um eine Aktualisierung zu werden
--------> Paket kmod.x86_64 0:20-15.el7 markiert, um aktualisiert zu werden
--------> Paket kmod.x86_64 0:20-15.el7_4.7 markiert, um eine Aktualisierung zu werden
--------> Paket kmod-libs.x86_64 0:20-15.el7 markiert, um aktualisiert zu werden
--------> Paket kmod-libs.x86_64 0:20-15.el7_4.7 markiert, um eine Aktualisierung zu werden
--------> Paket kpartx.x86_64 0:0.4.9-111.el7 markiert, um aktualisiert zu werden
--------> Paket kpartx.x86_64 0:0.4.9-111.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libblkid.x86_64 0:2.23.2-43.el7 markiert, um aktualisiert zu werden
--------> Paket libblkid.x86_64 0:2.23.2-43.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libdb.x86_64 0:5.3.21-20.el7 markiert, um aktualisiert zu werden
--------> Paket libdb.x86_64 0:5.3.21-21.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket libdb-utils.x86_64 0:5.3.21-20.el7 markiert, um aktualisiert zu werden
--------> Paket libdb-utils.x86_64 0:5.3.21-21.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket libgcc.x86_64 0:4.8.5-16.el7 markiert, um aktualisiert zu werden
--------> Paket libgcc.x86_64 0:4.8.5-16.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libgomp.x86_64 0:4.8.5-16.el7 markiert, um aktualisiert zu werden
--------> Paket libgomp.x86_64 0:4.8.5-16.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libmount.x86_64 0:2.23.2-43.el7 markiert, um aktualisiert zu werden
--------> Paket libmount.x86_64 0:2.23.2-43.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libpciaccess.x86_64 0:0.13.4-3.el7_3 markiert, um aktualisiert zu werden
--------> Paket libpciaccess.x86_64 0:0.13.4-3.1.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket libstdc++.x86_64 0:4.8.5-16.el7 markiert, um aktualisiert zu werden
--------> Paket libstdc++.x86_64 0:4.8.5-16.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket libteam.x86_64 0:1.25-5.el7 markiert, um aktualisiert zu werden
--------> Paket libteam.x86_64 0:1.25-6.el7_4.3 markiert, um eine Aktualisierung zu werden
--------> Paket libuuid.x86_64 0:2.23.2-43.el7 markiert, um aktualisiert zu werden
--------> Paket libuuid.x86_64 0:2.23.2-43.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket linux-firmware.noarch 0:20170606-56.gitc990aae.el7 markiert, um aktualisiert zu werden
--------> Paket linux-firmware.noarch 0:20170606-58.gitc990aae.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket microcode_ctl.x86_64 2:2.1-22.el7 markiert, um aktualisiert zu werden
--------> Paket microcode_ctl.x86_64 2:2.1-22.5.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket ncurses.x86_64 0:5.9-13.20130511.el7 markiert, um aktualisiert zu werden
--------> Paket ncurses.x86_64 0:5.9-14.20130511.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket ncurses-base.noarch 0:5.9-13.20130511.el7 markiert, um aktualisiert zu werden
--------> Paket ncurses-base.noarch 0:5.9-14.20130511.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket ncurses-libs.x86_64 0:5.9-13.20130511.el7 markiert, um aktualisiert zu werden
--------> Paket ncurses-libs.x86_64 0:5.9-14.20130511.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket nss.x86_64 0:3.28.4-8.el7 markiert, um aktualisiert zu werden
--------> Paket nss.x86_64 0:3.28.4-15.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket nss-softokn.x86_64 0:3.28.3-6.el7 markiert, um aktualisiert zu werden
--------> Paket nss-softokn.x86_64 0:3.28.3-8.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket nss-softokn-freebl.x86_64 0:3.28.3-6.el7 markiert, um aktualisiert zu werden
--------> Paket nss-softokn-freebl.x86_64 0:3.28.3-8.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket nss-sysinit.x86_64 0:3.28.4-8.el7 markiert, um aktualisiert zu werden
--------> Paket nss-sysinit.x86_64 0:3.28.4-15.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket nss-tools.x86_64 0:3.28.4-8.el7 markiert, um aktualisiert zu werden
--------> Paket nss-tools.x86_64 0:3.28.4-15.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket openssh.x86_64 0:7.4p1-11.el7 markiert, um aktualisiert zu werden
--------> Paket openssh.x86_64 0:7.4p1-13.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket openssh-clients.x86_64 0:7.4p1-11.el7 markiert, um aktualisiert zu werden
--------> Paket openssh-clients.x86_64 0:7.4p1-13.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket openssh-server.x86_64 0:7.4p1-11.el7 markiert, um aktualisiert zu werden
--------> Paket openssh-server.x86_64 0:7.4p1-13.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket python-gobject-base.x86_64 0:3.22.0-1.el7 markiert, um aktualisiert zu werden
--------> Paket python-gobject-base.x86_64 0:3.22.0-1.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket python-perf.x86_64 0:3.10.0-693.el7 markiert, um aktualisiert zu werden
--------> Paket python-perf.x86_64 0:3.10.0-693.21.1.el7 markiert, um eine Aktualisierung zu werden
--------> Paket selinux-policy.noarch 0:3.13.1-166.el7 markiert, um aktualisiert zu werden
--------> Paket selinux-policy.noarch 0:3.13.1-166.el7_4.9 markiert, um eine Aktualisierung zu werden
--------> Paket selinux-policy-targeted.noarch 0:3.13.1-166.el7 markiert, um aktualisiert zu werden
--------> Paket selinux-policy-targeted.noarch 0:3.13.1-166.el7_4.9 markiert, um eine Aktualisierung zu werden
--------> Paket sudo.x86_64 0:1.8.19p2-10.el7 markiert, um aktualisiert zu werden
--------> Paket sudo.x86_64 0:1.8.19p2-11.el7_4 markiert, um eine Aktualisierung zu werden
--------> Paket systemd.x86_64 0:219-42.el7 markiert, um aktualisiert zu werden
--------> Paket systemd.x86_64 0:219-42.el7_4.10 markiert, um eine Aktualisierung zu werden
--------> Paket systemd-libs.x86_64 0:219-42.el7 markiert, um aktualisiert zu werden
--------> Paket systemd-libs.x86_64 0:219-42.el7_4.10 markiert, um eine Aktualisierung zu werden
--------> Paket systemd-sysv.x86_64 0:219-42.el7 markiert, um aktualisiert zu werden
--------> Paket systemd-sysv.x86_64 0:219-42.el7_4.10 markiert, um eine Aktualisierung zu werden
--------> Paket teamd.x86_64 0:1.25-5.el7 markiert, um aktualisiert zu werden
--------> Paket teamd.x86_64 0:1.25-6.el7_4.3 markiert, um eine Aktualisierung zu werden
--------> Paket tuned.noarch 0:2.8.0-5.el7 markiert, um aktualisiert zu werden
--------> Paket tuned.noarch 0:2.8.0-5.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket tzdata.noarch 0:2017b-1.el7 markiert, um aktualisiert zu werden
--------> Paket tzdata.noarch 0:2018d-1.el7 markiert, um eine Aktualisierung zu werden
--------> Paket util-linux.x86_64 0:2.23.2-43.el7 markiert, um aktualisiert zu werden
--------> Paket util-linux.x86_64 0:2.23.2-43.el7_4.2 markiert, um eine Aktualisierung zu werden
--------> Paket wpa_supplicant.x86_64 1:2.6-5.el7 markiert, um aktualisiert zu werden
--------> Paket wpa_supplicant.x86_64 1:2.6-5.el7_4.1 markiert, um eine Aktualisierung zu werden
--------> Paket yum.noarch 0:3.4.3-154.el7.centos markiert, um aktualisiert zu werden
--------> Paket yum.noarch 0:3.4.3-154.el7.centos.1 markiert, um eine Aktualisierung zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                                       Arch                   Version                                       Paketquelle             Größe
==================================================================================================================================================
Installieren:
 grub2                                         x86_64                 1:2.02-0.65.el7.centos.2                      updates                  29 k
     ersetzt  grub2.x86_64 1:2.02-0.64.el7.centos
 grub2-tools                                   x86_64                 1:2.02-0.65.el7.centos.2                      updates                 1.8 M
     ersetzt  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 grub2-tools-extra                             x86_64                 1:2.02-0.65.el7.centos.2                      updates                 993 k
     ersetzt  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 grub2-tools-minimal                           x86_64                 1:2.02-0.65.el7.centos.2                      updates                 170 k
     ersetzt  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 kernel                                        x86_64                 3.10.0-693.21.1.el7                           updates                  43 M
Aktualisieren:
 NetworkManager                                x86_64                 1:1.8.0-11.el7_4                              updates                 1.6 M
 NetworkManager-libnm                          x86_64                 1:1.8.0-11.el7_4                              updates                 1.2 M
 NetworkManager-team                           x86_64                 1:1.8.0-11.el7_4                              updates                 156 k
 NetworkManager-tui                            x86_64                 1:1.8.0-11.el7_4                              updates                 224 k
 NetworkManager-wifi                           x86_64                 1:1.8.0-11.el7_4                              updates                 184 k
 bash                                          x86_64                 4.2.46-29.el7_4                               updates                 1.0 M
 bind-libs-lite                                x86_64                 32:9.9.4-51.el7_4.2                           updates                 733 k
 bind-license                                  noarch                 32:9.9.4-51.el7_4.2                           updates                  84 k
 binutils                                      x86_64                 2.25.1-32.base.el7_4.2                        updates                 5.4 M
 cpio                                          x86_64                 2.11-25.el7_4                                 updates                 210 k
 cryptsetup-libs                               x86_64                 1.7.4-3.el7_4.1                               updates                 223 k
 device-mapper-persistent-data                 x86_64                 0.7.0-0.1.rc6.el7_4.1                         updates                 400 k
 dhclient                                      x86_64                 12:4.2.5-58.el7.centos.3                      updates                 282 k
 dhcp-common                                   x86_64                 12:4.2.5-58.el7.centos.3                      updates                 174 k
 dhcp-libs                                     x86_64                 12:4.2.5-58.el7.centos.3                      updates                 130 k
 dracut                                        x86_64                 033-502.el7_4.1                               updates                 321 k
 dracut-config-rescue                          x86_64                 033-502.el7_4.1                               updates                  56 k
 dracut-network                                x86_64                 033-502.el7_4.1                               updates                  98 k
 glibc                                         x86_64                 2.17-196.el7_4.2                              updates                 3.6 M
 glibc-common                                  x86_64                 2.17-196.el7_4.2                              updates                  11 M
 grub2-common                                  noarch                 1:2.02-0.65.el7.centos.2                      updates                 726 k
 grub2-pc                                      x86_64                 1:2.02-0.65.el7.centos.2                      updates                  29 k
 grub2-pc-modules                              noarch                 1:2.02-0.65.el7.centos.2                      updates                 845 k
 initscripts                                   x86_64                 9.49.39-1.el7_4.1                             updates                 435 k
 iptables                                      x86_64                 1.4.21-18.3.el7_4                             updates                 428 k
 iwl100-firmware                               noarch                 39.31.5.1-58.el7_4                            updates                 152 k
 iwl1000-firmware                              noarch                 1:39.31.5.1-58.el7_4                          updates                 215 k
 iwl105-firmware                               noarch                 18.168.6.1-58.el7_4                           updates                 236 k
 iwl135-firmware                               noarch                 18.168.6.1-58.el7_4                           updates                 245 k
 iwl2000-firmware                              noarch                 18.168.6.1-58.el7_4                           updates                 239 k
 iwl2030-firmware                              noarch                 18.168.6.1-58.el7_4                           updates                 248 k
 iwl3160-firmware                              noarch                 22.0.7.0-58.el7_4                             updates                 1.5 M
 iwl3945-firmware                              noarch                 15.32.2.9-58.el7_4                            updates                  90 k
 iwl4965-firmware                              noarch                 228.61.2.24-58.el7_4                          updates                 103 k
 iwl5000-firmware                              noarch                 8.83.5.1_1-58.el7_4                           updates                 296 k
 iwl5150-firmware                              noarch                 8.24.2.2-58.el7_4                             updates                 149 k
 iwl6000-firmware                              noarch                 9.221.4.1-58.el7_4                            updates                 169 k
 iwl6000g2a-firmware                           noarch                 17.168.5.3-58.el7_4                           updates                 312 k
 iwl6000g2b-firmware                           noarch                 17.168.5.2-58.el7_4                           updates                 312 k
 iwl6050-firmware                              noarch                 41.28.5.1-58.el7_4                            updates                 245 k
 iwl7260-firmware                              noarch                 22.0.7.0-58.el7_4                             updates                 1.1 M
 iwl7265-firmware                              noarch                 22.0.7.0-58.el7_4                             updates                 3.5 M
 kernel-tools                                  x86_64                 3.10.0-693.21.1.el7                           updates                 5.2 M
 kernel-tools-libs                             x86_64                 3.10.0-693.21.1.el7                           updates                 5.1 M
 kexec-tools                                   x86_64                 2.0.14-17.2.el7                               updates                 333 k
 kmod                                          x86_64                 20-15.el7_4.7                                 updates                 121 k
 kmod-libs                                     x86_64                 20-15.el7_4.7                                 updates                  50 k
 kpartx                                        x86_64                 0.4.9-111.el7_4.2                             updates                  73 k
 libblkid                                      x86_64                 2.23.2-43.el7_4.2                             updates                 176 k
 libdb                                         x86_64                 5.3.21-21.el7_4                               updates                 719 k
 libdb-utils                                   x86_64                 5.3.21-21.el7_4                               updates                 132 k
 libgcc                                        x86_64                 4.8.5-16.el7_4.2                              updates                  98 k
 libgomp                                       x86_64                 4.8.5-16.el7_4.2                              updates                 154 k
 libmount                                      x86_64                 2.23.2-43.el7_4.2                             updates                 178 k
 libpciaccess                                  x86_64                 0.13.4-3.1.el7_4                              updates                  26 k
 libstdc++                                     x86_64                 4.8.5-16.el7_4.2                              updates                 301 k
 libteam                                       x86_64                 1.25-6.el7_4.3                                updates                  47 k
 libuuid                                       x86_64                 2.23.2-43.el7_4.2                             updates                  79 k
 linux-firmware                                noarch                 20170606-58.gitc990aae.el7_4                  updates                  35 M
 microcode_ctl                                 x86_64                 2:2.1-22.5.el7_4                              updates                 786 k
 ncurses                                       x86_64                 5.9-14.20130511.el7_4                         updates                 304 k
 ncurses-base                                  noarch                 5.9-14.20130511.el7_4                         updates                  68 k
 ncurses-libs                                  x86_64                 5.9-14.20130511.el7_4                         updates                 316 k
 nss                                           x86_64                 3.28.4-15.el7_4                               updates                 849 k
 nss-softokn                                   x86_64                 3.28.3-8.el7_4                                updates                 310 k
 nss-softokn-freebl                            x86_64                 3.28.3-8.el7_4                                updates                 214 k
 nss-sysinit                                   x86_64                 3.28.4-15.el7_4                               updates                  60 k
 nss-tools                                     x86_64                 3.28.4-15.el7_4                               updates                 501 k
 openssh                                       x86_64                 7.4p1-13.el7_4                                updates                 509 k
 openssh-clients                               x86_64                 7.4p1-13.el7_4                                updates                 654 k
 openssh-server                                x86_64                 7.4p1-13.el7_4                                updates                 458 k
 python-gobject-base                           x86_64                 3.22.0-1.el7_4.1                              updates                 294 k
 python-perf                                   x86_64                 3.10.0-693.21.1.el7                           updates                 5.2 M
 selinux-policy                                noarch                 3.13.1-166.el7_4.9                            updates                 437 k
 selinux-policy-targeted                       noarch                 3.13.1-166.el7_4.9                            updates                 6.5 M
 sudo                                          x86_64                 1.8.19p2-11.el7_4                             updates                 1.1 M
 systemd                                       x86_64                 219-42.el7_4.10                               updates                 5.2 M
 systemd-libs                                  x86_64                 219-42.el7_4.10                               updates                 378 k
 systemd-sysv                                  x86_64                 219-42.el7_4.10                               updates                  72 k
 teamd                                         x86_64                 1.25-6.el7_4.3                                updates                 112 k
 tuned                                         noarch                 2.8.0-5.el7_4.2                               updates                 234 k
 tzdata                                        noarch                 2018d-1.el7                                   updates                 481 k
 util-linux                                    x86_64                 2.23.2-43.el7_4.2                             updates                 2.0 M
 wpa_supplicant                                x86_64                 1:2.6-5.el7_4.1                               updates                 1.2 M
 yum                                           noarch                 3.4.3-154.el7.centos.1                        updates                 1.2 M

Transaktionsübersicht
==================================================================================================================================================
Installieren    5 Pakete
Aktualisieren  85 Pakete

Gesamte Downloadgröße: 162 M
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/90): NetworkManager-wifi-1.8.0-11.el7_4.x86_64.rpm                                                                      | 184 kB  00:00:00     
(2/90): NetworkManager-tui-1.8.0-11.el7_4.x86_64.rpm                                                                       | 224 kB  00:00:00     
(3/90): bash-4.2.46-29.el7_4.x86_64.rpm                                                                                    | 1.0 MB  00:00:00     
(4/90): bind-license-9.9.4-51.el7_4.2.noarch.rpm                                                                           |  84 kB  00:00:00     
(5/90): NetworkManager-libnm-1.8.0-11.el7_4.x86_64.rpm                                                                     | 1.2 MB  00:00:01     
(6/90): bind-libs-lite-9.9.4-51.el7_4.2.x86_64.rpm                                                                         | 733 kB  00:00:01     
(7/90): cpio-2.11-25.el7_4.x86_64.rpm                                                                                      | 210 kB  00:00:00     
(8/90): device-mapper-persistent-data-0.7.0-0.1.rc6.el7_4.1.x86_64.rpm                                                     | 400 kB  00:00:00     
(9/90): dhclient-4.2.5-58.el7.centos.3.x86_64.rpm                                                                          | 282 kB  00:00:00     
(10/90): dhcp-common-4.2.5-58.el7.centos.3.x86_64.rpm                                                                      | 174 kB  00:00:00     
(11/90): dhcp-libs-4.2.5-58.el7.centos.3.x86_64.rpm                                                                        | 130 kB  00:00:00     
(12/90): cryptsetup-libs-1.7.4-3.el7_4.1.x86_64.rpm                                                                        | 223 kB  00:00:00     
(13/90): dracut-033-502.el7_4.1.x86_64.rpm                                                                                 | 321 kB  00:00:00     
(14/90): dracut-network-033-502.el7_4.1.x86_64.rpm                                                                         |  98 kB  00:00:00     
(15/90): NetworkManager-1.8.0-11.el7_4.x86_64.rpm                                                                          | 1.6 MB  00:00:02     
(16/90): dracut-config-rescue-033-502.el7_4.1.x86_64.rpm                                                                   |  56 kB  00:00:00     
(17/90): grub2-2.02-0.65.el7.centos.2.x86_64.rpm                                                                           |  29 kB  00:00:00     
(18/90): grub2-common-2.02-0.65.el7.centos.2.noarch.rpm                                                                    | 726 kB  00:00:01     
(19/90): grub2-pc-2.02-0.65.el7.centos.2.x86_64.rpm                                                                        |  29 kB  00:00:00     
(20/90): glibc-2.17-196.el7_4.2.x86_64.rpm                                                                                 | 3.6 MB  00:00:02     
(21/90): binutils-2.25.1-32.base.el7_4.2.x86_64.rpm                                                                        | 5.4 MB  00:00:04     
(22/90): grub2-tools-extra-2.02-0.65.el7.centos.2.x86_64.rpm                                                               | 993 kB  00:00:00     
(23/90): grub2-pc-modules-2.02-0.65.el7.centos.2.noarch.rpm                                                                | 845 kB  00:00:01     
(24/90): grub2-tools-minimal-2.02-0.65.el7.centos.2.x86_64.rpm                                                             | 170 kB  00:00:00     
(25/90): grub2-tools-2.02-0.65.el7.centos.2.x86_64.rpm                                                                     | 1.8 MB  00:00:01     
(26/90): iwl100-firmware-39.31.5.1-58.el7_4.noarch.rpm                                                                     | 152 kB  00:00:00     
(27/90): iptables-1.4.21-18.3.el7_4.x86_64.rpm                                                                             | 428 kB  00:00:00     
(28/90): iwl1000-firmware-39.31.5.1-58.el7_4.noarch.rpm                                                                    | 215 kB  00:00:00     
(29/90): iwl105-firmware-18.168.6.1-58.el7_4.noarch.rpm                                                                    | 236 kB  00:00:00     
(30/90): iwl135-firmware-18.168.6.1-58.el7_4.noarch.rpm                                                                    | 245 kB  00:00:00     
(31/90): initscripts-9.49.39-1.el7_4.1.x86_64.rpm                                                                          | 435 kB  00:00:00     
(32/90): iwl2000-firmware-18.168.6.1-58.el7_4.noarch.rpm                                                                   | 239 kB  00:00:00     
(33/90): iwl3945-firmware-15.32.2.9-58.el7_4.noarch.rpm                                                                    |  90 kB  00:00:00     
(34/90): iwl2030-firmware-18.168.6.1-58.el7_4.noarch.rpm                                                                   | 248 kB  00:00:00     
(35/90): iwl4965-firmware-228.61.2.24-58.el7_4.noarch.rpm                                                                  | 103 kB  00:00:00     
(36/90): iwl5150-firmware-8.24.2.2-58.el7_4.noarch.rpm                                                                     | 149 kB  00:00:00     
(37/90): iwl6000-firmware-9.221.4.1-58.el7_4.noarch.rpm                                                                    | 169 kB  00:00:00     
(38/90): iwl5000-firmware-8.83.5.1_1-58.el7_4.noarch.rpm                                                                   | 296 kB  00:00:00     
(39/90): iwl6000g2a-firmware-17.168.5.3-58.el7_4.noarch.rpm                                                                | 312 kB  00:00:00     
(40/90): iwl6050-firmware-41.28.5.1-58.el7_4.noarch.rpm                                                                    | 245 kB  00:00:00     
(41/90): iwl6000g2b-firmware-17.168.5.2-58.el7_4.noarch.rpm                                                                | 312 kB  00:00:00     
(42/90): NetworkManager-team-1.8.0-11.el7_4.x86_64.rpm                                                                     | 156 kB  00:00:08     
(43/90): iwl7260-firmware-22.0.7.0-58.el7_4.noarch.rpm                                                                     | 1.1 MB  00:00:00     
(44/90): iwl3160-firmware-22.0.7.0-58.el7_4.noarch.rpm                                                                     | 1.5 MB  00:00:02     
(45/90): iwl7265-firmware-22.0.7.0-58.el7_4.noarch.rpm                                                                     | 3.5 MB  00:00:03     
(46/90): kexec-tools-2.0.14-17.2.el7.x86_64.rpm                                                                            | 333 kB  00:00:00     
(47/90): kmod-20-15.el7_4.7.x86_64.rpm                                                                                     | 121 kB  00:00:00     
(48/90): kmod-libs-20-15.el7_4.7.x86_64.rpm                                                                                |  50 kB  00:00:00     
(49/90): kpartx-0.4.9-111.el7_4.2.x86_64.rpm                                                                               |  73 kB  00:00:00     
(50/90): libblkid-2.23.2-43.el7_4.2.x86_64.rpm                                                                             | 176 kB  00:00:00     
(51/90): kernel-tools-3.10.0-693.21.1.el7.x86_64.rpm                                                                       | 5.2 MB  00:00:04     
(52/90): libdb-utils-5.3.21-21.el7_4.x86_64.rpm                                                                            | 132 kB  00:00:00     
(53/90): libgcc-4.8.5-16.el7_4.2.x86_64.rpm                                                                                |  98 kB  00:00:00     
(54/90): libgomp-4.8.5-16.el7_4.2.x86_64.rpm                                                                               | 154 kB  00:00:00     
(55/90): libdb-5.3.21-21.el7_4.x86_64.rpm                                                                                  | 719 kB  00:00:00     
(56/90): libpciaccess-0.13.4-3.1.el7_4.x86_64.rpm                                                                          |  26 kB  00:00:00     
(57/90): libmount-2.23.2-43.el7_4.2.x86_64.rpm                                                                             | 178 kB  00:00:00     
(58/90): libteam-1.25-6.el7_4.3.x86_64.rpm                                                                                 |  47 kB  00:00:00     
(59/90): libstdc++-4.8.5-16.el7_4.2.x86_64.rpm                                                                             | 301 kB  00:00:00     
(60/90): libuuid-2.23.2-43.el7_4.2.x86_64.rpm                                                                              |  79 kB  00:00:00     
(61/90): kernel-tools-libs-3.10.0-693.21.1.el7.x86_64.rpm                                                                  | 5.1 MB  00:00:07     
(62/90): microcode_ctl-2.1-22.5.el7_4.x86_64.rpm                                                                           | 786 kB  00:00:01     
(63/90): ncurses-base-5.9-14.20130511.el7_4.noarch.rpm                                                                     |  68 kB  00:00:00     
(64/90): ncurses-libs-5.9-14.20130511.el7_4.x86_64.rpm                                                                     | 316 kB  00:00:00     
(65/90): ncurses-5.9-14.20130511.el7_4.x86_64.rpm                                                                          | 304 kB  00:00:00     
(66/90): nss-softokn-3.28.3-8.el7_4.x86_64.rpm                                                                             | 310 kB  00:00:00     
(67/90): nss-3.28.4-15.el7_4.x86_64.rpm                                                                                    | 849 kB  00:00:00     
(68/90): nss-sysinit-3.28.4-15.el7_4.x86_64.rpm                                                                            |  60 kB  00:00:00     
(69/90): nss-softokn-freebl-3.28.3-8.el7_4.x86_64.rpm                                                                      | 214 kB  00:00:00     
(70/90): openssh-7.4p1-13.el7_4.x86_64.rpm                                                                                 | 509 kB  00:00:00     
(71/90): nss-tools-3.28.4-15.el7_4.x86_64.rpm                                                                              | 501 kB  00:00:00     
(72/90): openssh-clients-7.4p1-13.el7_4.x86_64.rpm                                                                         | 654 kB  00:00:00     
(73/90): openssh-server-7.4p1-13.el7_4.x86_64.rpm                                                                          | 458 kB  00:00:00     
(74/90): python-gobject-base-3.22.0-1.el7_4.1.x86_64.rpm                                                                   | 294 kB  00:00:00     
(75/90): selinux-policy-3.13.1-166.el7_4.9.noarch.rpm                                                                      | 437 kB  00:00:00     
(76/90): glibc-common-2.17-196.el7_4.2.x86_64.rpm                                                                          |  11 MB  00:00:17     
(77/90): sudo-1.8.19p2-11.el7_4.x86_64.rpm                                                                                 | 1.1 MB  00:00:01     
(78/90): python-perf-3.10.0-693.21.1.el7.x86_64.rpm                                                                        | 5.2 MB  00:00:04     
(79/90): systemd-libs-219-42.el7_4.10.x86_64.rpm                                                                           | 378 kB  00:00:00     
(80/90): systemd-sysv-219-42.el7_4.10.x86_64.rpm                                                                           |  72 kB  00:00:00     
(81/90): teamd-1.25-6.el7_4.3.x86_64.rpm                                                                                   | 112 kB  00:00:00     
(82/90): tuned-2.8.0-5.el7_4.2.noarch.rpm                                                                                  | 234 kB  00:00:00     
(83/90): tzdata-2018d-1.el7.noarch.rpm                                                                                     | 481 kB  00:00:00     
(84/90): selinux-policy-targeted-3.13.1-166.el7_4.9.noarch.rpm                                                             | 6.5 MB  00:00:06     
(85/90): wpa_supplicant-2.6-5.el7_4.1.x86_64.rpm                                                                           | 1.2 MB  00:00:00     
(86/90): util-linux-2.23.2-43.el7_4.2.x86_64.rpm                                                                           | 2.0 MB  00:00:02     
(87/90): yum-3.4.3-154.el7.centos.1.noarch.rpm                                                                             | 1.2 MB  00:00:00     
(88/90): systemd-219-42.el7_4.10.x86_64.rpm                                                                                | 5.2 MB  00:00:09     
(89/90): linux-firmware-20170606-58.gitc990aae.el7_4.noarch.rpm                                                            |  35 MB  00:00:24     
kernel-3.10.0-693.21.1.el7.x86 FAILED                                          ====================-            ]  80 kB/s | 120 MB  00:08:55 ETA 
http://mirror.infonline.de/centos/7.4.1708/updates/x86_64/Packages/kernel-3.10.0-693.21.1.el7.x86_64.rpm: [Errno 12] Timeout on http://mirror.infonline.de/centos/7.4.1708/updates/x86_64/Packages/kernel-3.10.0-693.21.1.el7.x86_64.rpm: (28, 'Operation too slow. Less than 1000 bytes/sec transferred the last 30 seconds')
Anderer Spiegelserver wird versucht.
(90/90): kernel-3.10.0-693.21.1.el7.x86_64.rpm                                                                             |  43 MB  00:00:22     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            2.1 MB/s | 162 MB  00:01:16     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Aktualisieren    : libgcc-4.8.5-16.el7_4.2.x86_64                                                                                         1/179 
  Aktualisieren    : 1:grub2-common-2.02-0.65.el7.centos.2.noarch                                                                           2/179 
  Aktualisieren    : 1:grub2-pc-modules-2.02-0.65.el7.centos.2.noarch                                                                       3/179 
  Aktualisieren    : ncurses-base-5.9-14.20130511.el7_4.noarch                                                                              4/179 
  Aktualisieren    : 32:bind-license-9.9.4-51.el7_4.2.noarch                                                                                5/179 
  Aktualisieren    : tzdata-2018d-1.el7.noarch                                                                                              6/179 
  Aktualisieren    : bash-4.2.46-29.el7_4.x86_64                                                                                            7/179 
  Aktualisieren    : nss-softokn-freebl-3.28.3-8.el7_4.x86_64                                                                               8/179 
  Aktualisieren    : glibc-common-2.17-196.el7_4.2.x86_64                                                                                   9/179 
  Aktualisieren    : glibc-2.17-196.el7_4.2.x86_64                                                                                         10/179 
  Aktualisieren    : libstdc++-4.8.5-16.el7_4.2.x86_64                                                                                     11/179 
  Aktualisieren    : ncurses-libs-5.9-14.20130511.el7_4.x86_64                                                                             12/179 
  Aktualisieren    : systemd-libs-219-42.el7_4.10.x86_64                                                                                   13/179 
  Aktualisieren    : libuuid-2.23.2-43.el7_4.2.x86_64                                                                                      14/179 
  Aktualisieren    : cpio-2.11-25.el7_4.x86_64                                                                                             15/179 
  Aktualisieren    : libblkid-2.23.2-43.el7_4.2.x86_64                                                                                     16/179 
  Installieren     : 1:grub2-tools-minimal-2.02-0.65.el7.centos.2.x86_64                                                                   17/179 
  Aktualisieren    : libmount-2.23.2-43.el7_4.2.x86_64                                                                                     18/179 
  Aktualisieren    : util-linux-2.23.2-43.el7_4.2.x86_64                                                                                   19/179 
  Aktualisieren    : openssh-7.4p1-13.el7_4.x86_64                                                                                         20/179 
  Aktualisieren    : 12:dhcp-libs-4.2.5-58.el7.centos.3.x86_64                                                                             21/179 
  Aktualisieren    : nss-softokn-3.28.3-8.el7_4.x86_64                                                                                     22/179 
  Aktualisieren    : nss-3.28.4-15.el7_4.x86_64                                                                                            23/179 
  Aktualisieren    : nss-sysinit-3.28.4-15.el7_4.x86_64                                                                                    24/179 
  Aktualisieren    : 1:NetworkManager-libnm-1.8.0-11.el7_4.x86_64                                                                          25/179 
  Aktualisieren    : 12:dhcp-common-4.2.5-58.el7.centos.3.x86_64                                                                           26/179 
  Aktualisieren    : cryptsetup-libs-1.7.4-3.el7_4.1.x86_64                                                                                27/179 
  Aktualisieren    : libteam-1.25-6.el7_4.3.x86_64                                                                                         28/179 
  Aktualisieren    : teamd-1.25-6.el7_4.3.x86_64                                                                                           29/179 
  Aktualisieren    : 32:bind-libs-lite-9.9.4-51.el7_4.2.x86_64                                                                             30/179 
  Aktualisieren    : libdb-5.3.21-21.el7_4.x86_64                                                                                          31/179 
  Aktualisieren    : python-gobject-base-3.22.0-1.el7_4.1.x86_64                                                                           32/179 
  Aktualisieren    : kpartx-0.4.9-111.el7_4.2.x86_64                                                                                       33/179 
  Aktualisieren    : python-perf-3.10.0-693.21.1.el7.x86_64                                                                                34/179 
  Aktualisieren    : kmod-libs-20-15.el7_4.7.x86_64                                                                                        35/179 
  Aktualisieren    : kernel-tools-libs-3.10.0-693.21.1.el7.x86_64                                                                          36/179 
  Aktualisieren    : binutils-2.25.1-32.base.el7_4.2.x86_64                                                                                37/179 
  Aktualisieren    : dracut-033-502.el7_4.1.x86_64                                                                                         38/179 
  Aktualisieren    : kmod-20-15.el7_4.7.x86_64                                                                                             39/179 
  Aktualisieren    : systemd-219-42.el7_4.10.x86_64                                                                                        40/179 
  Aktualisieren    : initscripts-9.49.39-1.el7_4.1.x86_64                                                                                  41/179 
  Installieren     : 1:grub2-tools-2.02-0.65.el7.centos.2.x86_64                                                                           42/179 
  Installieren     : 1:grub2-tools-extra-2.02-0.65.el7.centos.2.x86_64                                                                     43/179 
  Aktualisieren    : 1:grub2-pc-2.02-0.65.el7.centos.2.x86_64                                                                              44/179 
  Aktualisieren    : 12:dhclient-4.2.5-58.el7.centos.3.x86_64                                                                              45/179 
  Aktualisieren    : dracut-network-033-502.el7_4.1.x86_64                                                                                 46/179 
  Aktualisieren    : systemd-sysv-219-42.el7_4.10.x86_64                                                                                   47/179 
  Aktualisieren    : 1:wpa_supplicant-2.6-5.el7_4.1.x86_64                                                                                 48/179 
  Aktualisieren    : 1:NetworkManager-1.8.0-11.el7_4.x86_64                                                                                49/179 
  Aktualisieren    : linux-firmware-20170606-58.gitc990aae.el7_4.noarch                                                                    50/179 
  Aktualisieren    : selinux-policy-3.13.1-166.el7_4.9.noarch                                                                              51/179 
  Aktualisieren    : selinux-policy-targeted-3.13.1-166.el7_4.9.noarch                                                                     52/179 
  Installieren     : kernel-3.10.0-693.21.1.el7.x86_64                                                                                     53/179 
  Aktualisieren    : 1:NetworkManager-team-1.8.0-11.el7_4.x86_64                                                                           54/179 
  Aktualisieren    : 1:NetworkManager-tui-1.8.0-11.el7_4.x86_64                                                                            55/179 
  Aktualisieren    : 1:NetworkManager-wifi-1.8.0-11.el7_4.x86_64                                                                           56/179 
  Aktualisieren    : kexec-tools-2.0.14-17.2.el7.x86_64                                                                                    57/179 
  Installieren     : 1:grub2-2.02-0.65.el7.centos.2.x86_64                                                                                 58/179 
  Aktualisieren    : openssh-server-7.4p1-13.el7_4.x86_64                                                                                  59/179 
  Aktualisieren    : tuned-2.8.0-5.el7_4.2.noarch                                                                                          60/179 
  Aktualisieren    : 2:microcode_ctl-2.1-22.5.el7_4.x86_64                                                                                 61/179 
  Aktualisieren    : dracut-config-rescue-033-502.el7_4.1.x86_64                                                                           62/179 
  Aktualisieren    : kernel-tools-3.10.0-693.21.1.el7.x86_64                                                                               63/179 
  Aktualisieren    : libdb-utils-5.3.21-21.el7_4.x86_64                                                                                    64/179 
  Aktualisieren    : nss-tools-3.28.4-15.el7_4.x86_64                                                                                      65/179 
  Aktualisieren    : openssh-clients-7.4p1-13.el7_4.x86_64                                                                                 66/179 
  Aktualisieren    : yum-3.4.3-154.el7.centos.1.noarch                                                                                     67/179 
  Aktualisieren    : ncurses-5.9-14.20130511.el7_4.x86_64                                                                                  68/179 
  Aktualisieren    : device-mapper-persistent-data-0.7.0-0.1.rc6.el7_4.1.x86_64                                                            69/179 
  Aktualisieren    : iptables-1.4.21-18.3.el7_4.x86_64                                                                                     70/179 
  Aktualisieren    : libpciaccess-0.13.4-3.1.el7_4.x86_64                                                                                  71/179 
  Aktualisieren    : libgomp-4.8.5-16.el7_4.2.x86_64                                                                                       72/179 
  Aktualisieren    : sudo-1.8.19p2-11.el7_4.x86_64                                                                                         73/179 
  Aktualisieren    : iwl5000-firmware-8.83.5.1_1-58.el7_4.noarch                                                                           74/179 
  Aktualisieren    : iwl135-firmware-18.168.6.1-58.el7_4.noarch                                                                            75/179 
  Aktualisieren    : iwl6000g2a-firmware-17.168.5.3-58.el7_4.noarch                                                                        76/179 
  Aktualisieren    : iwl2030-firmware-18.168.6.1-58.el7_4.noarch                                                                           77/179 
  Aktualisieren    : iwl2000-firmware-18.168.6.1-58.el7_4.noarch                                                                           78/179 
  Aktualisieren    : iwl3945-firmware-15.32.2.9-58.el7_4.noarch                                                                            79/179 
  Aktualisieren    : iwl105-firmware-18.168.6.1-58.el7_4.noarch                                                                            80/179 
  Aktualisieren    : iwl7260-firmware-22.0.7.0-58.el7_4.noarch                                                                             81/179 
  Aktualisieren    : iwl6050-firmware-41.28.5.1-58.el7_4.noarch                                                                            82/179 
  Aktualisieren    : iwl100-firmware-39.31.5.1-58.el7_4.noarch                                                                             83/179 
  Aktualisieren    : iwl3160-firmware-22.0.7.0-58.el7_4.noarch                                                                             84/179 
  Aktualisieren    : iwl7265-firmware-22.0.7.0-58.el7_4.noarch                                                                             85/179 
  Aktualisieren    : 1:iwl1000-firmware-39.31.5.1-58.el7_4.noarch                                                                          86/179 
  Aktualisieren    : iwl6000-firmware-9.221.4.1-58.el7_4.noarch                                                                            87/179 
  Aktualisieren    : iwl4965-firmware-228.61.2.24-58.el7_4.noarch                                                                          88/179 
  Aktualisieren    : iwl6000g2b-firmware-17.168.5.2-58.el7_4.noarch                                                                        89/179 
  Aktualisieren    : iwl5150-firmware-8.24.2.2-58.el7_4.noarch                                                                             90/179 
  Aufräumen        : tuned-2.8.0-5.el7.noarch                                                                                              91/179 
  Aufräumen        : dracut-config-rescue-033-502.el7.x86_64                                                                               92/179 
  Aufräumen        : selinux-policy-targeted-3.13.1-166.el7.noarch                                                                         93/179 
  Aufräumen        : 1:grub2-2.02-0.64.el7.centos.x86_64                                                                                   94/179 
  Aufräumen        : 1:grub2-pc-2.02-0.64.el7.centos.x86_64                                                                                95/179 
  Aufräumen        : 1:grub2-pc-modules-2.02-0.64.el7.centos.noarch                                                                        96/179 
  Aufräumen        : selinux-policy-3.13.1-166.el7.noarch                                                                                  97/179 
  Aufräumen        : yum-3.4.3-154.el7.centos.noarch                                                                                       98/179 
  Aufräumen        : iwl5000-firmware-8.83.5.1_1-56.el7.noarch                                                                             99/179 
  Aufräumen        : iwl135-firmware-18.168.6.1-56.el7.noarch                                                                             100/179 
  Aufräumen        : iwl6000g2a-firmware-17.168.5.3-56.el7.noarch                                                                         101/179 
  Aufräumen        : iwl2030-firmware-18.168.6.1-56.el7.noarch                                                                            102/179 
  Aufräumen        : iwl2000-firmware-18.168.6.1-56.el7.noarch                                                                            103/179 
  Aufräumen        : iwl3945-firmware-15.32.2.9-56.el7.noarch                                                                             104/179 
  Aufräumen        : iwl105-firmware-18.168.6.1-56.el7.noarch                                                                             105/179 
  Aufräumen        : iwl7260-firmware-22.0.7.0-56.el7.noarch                                                                              106/179 
  Aufräumen        : iwl6050-firmware-41.28.5.1-56.el7.noarch                                                                             107/179 
  Aufräumen        : iwl100-firmware-39.31.5.1-56.el7.noarch                                                                              108/179 
  Aufräumen        : iwl3160-firmware-22.0.7.0-56.el7.noarch                                                                              109/179 
  Aufräumen        : linux-firmware-20170606-56.gitc990aae.el7.noarch                                                                     110/179 
  Aufräumen        : iwl7265-firmware-22.0.7.0-56.el7.noarch                                                                              111/179 
  Aufräumen        : 1:iwl1000-firmware-39.31.5.1-56.el7.noarch                                                                           112/179 
  Aufräumen        : iwl6000-firmware-9.221.4.1-56.el7.noarch                                                                             113/179 
  Aufräumen        : iwl4965-firmware-228.61.2.24-56.el7.noarch                                                                           114/179 
  Aufräumen        : iwl6000g2b-firmware-17.168.5.2-56.el7.noarch                                                                         115/179 
  Aufräumen        : iwl5150-firmware-8.24.2.2-56.el7.noarch                                                                              116/179 
  Aufräumen        : 1:NetworkManager-tui-1.8.0-9.el7.x86_64                                                                              117/179 
  Aufräumen        : kernel-tools-3.10.0-693.el7.x86_64                                                                                   118/179 
  Aufräumen        : nss-tools-3.28.4-8.el7.x86_64                                                                                        119/179 
  Aufräumen        : openssh-server-7.4p1-11.el7.x86_64                                                                                   120/179 
  Aufräumen        : openssh-clients-7.4p1-11.el7.x86_64                                                                                  121/179 
  Aufräumen        : 1:NetworkManager-team-1.8.0-9.el7.x86_64                                                                             122/179 
  Aufräumen        : 1:NetworkManager-wifi-1.8.0-9.el7.x86_64                                                                             123/179 
  Aufräumen        : 1:NetworkManager-1.8.0-9.el7.x86_64                                                                                  124/179 
  Aufräumen        : 1:NetworkManager-libnm-1.8.0-9.el7.x86_64                                                                            125/179 
  Aufräumen        : nss-sysinit-3.28.4-8.el7.x86_64                                                                                      126/179 
  Aufräumen        : nss-3.28.4-8.el7.x86_64                                                                                              127/179 
  Aufräumen        : kexec-tools-2.0.14-17.el7.x86_64                                                                                     128/179 
  Aufräumen        : device-mapper-persistent-data-0.7.0-0.1.rc6.el7.x86_64                                                               129/179 
  Aufräumen        : 1:wpa_supplicant-2.6-5.el7.x86_64                                                                                    130/179 
  Aufräumen        : teamd-1.25-5.el7.x86_64                                                                                              131/179 
  Aufräumen        : 1:grub2-tools-2.02-0.64.el7.centos.x86_64                                                                            132/179 
  Aufräumen        : 1:grub2-tools-extra-2.02-0.64.el7.centos.x86_64                                                                      133/179 
  Aufräumen        : dracut-network-033-502.el7.x86_64                                                                                    134/179 
  Aufräumen        : 12:dhclient-4.2.5-58.el7.centos.x86_64                                                                               135/179 
  Aufräumen        : initscripts-9.49.39-1.el7.x86_64                                                                                     136/179 
  Aufräumen        : 32:bind-libs-lite-9.9.4-50.el7.x86_64                                                                                137/179 
  Aufräumen        : 1:grub2-tools-minimal-2.02-0.64.el7.centos.x86_64                                                                    138/179 
  Aufräumen        : nss-softokn-3.28.3-6.el7.x86_64                                                                                      139/179 
  Aufräumen        : openssh-7.4p1-11.el7.x86_64                                                                                          140/179 
  Aufräumen        : 2:microcode_ctl-2.1-22.el7.x86_64                                                                                    141/179 
  Aufräumen        : libdb-utils-5.3.21-20.el7.x86_64                                                                                     142/179 
  Aufräumen        : ncurses-5.9-13.20130511.el7.x86_64                                                                                   143/179 
  Aufräumen        : sudo-1.8.19p2-10.el7.x86_64                                                                                          144/179 
  Aufräumen        : libgomp-4.8.5-16.el7.x86_64                                                                                          145/179 
  Aufräumen        : iptables-1.4.21-18.0.1.el7.centos.x86_64                                                                             146/179 
  Aufräumen        : 12:dhcp-common-4.2.5-58.el7.centos.x86_64                                                                            147/179 
  Aufräumen        : 12:dhcp-libs-4.2.5-58.el7.centos.x86_64                                                                              148/179 
  Aufräumen        : systemd-sysv-219-42.el7.x86_64                                                                                       149/179 
  Aufräumen        : systemd-219-42.el7.x86_64                                                                                            150/179 
  Aufräumen        : dracut-033-502.el7.x86_64                                                                                            151/179 
  Aufräumen        : kmod-20-15.el7.x86_64                                                                                                152/179 
  Aufräumen        : util-linux-2.23.2-43.el7.x86_64                                                                                      153/179 
  Aufräumen        : libmount-2.23.2-43.el7.x86_64                                                                                        154/179 
  Aufräumen        : libblkid-2.23.2-43.el7.x86_64                                                                                        155/179 
  Aufräumen        : binutils-2.25.1-31.base.el7.x86_64                                                                                   156/179 
  Aufräumen        : systemd-libs-219-42.el7.x86_64                                                                                       157/179 
  Aufräumen        : cryptsetup-libs-1.7.4-3.el7.x86_64                                                                                   158/179 
  Aufräumen        : kmod-libs-20-15.el7.x86_64                                                                                           159/179 
  Aufräumen        : cpio-2.11-24.el7.x86_64                                                                                              160/179 
  Aufräumen        : libuuid-2.23.2-43.el7.x86_64                                                                                         161/179 
  Aufräumen        : kpartx-0.4.9-111.el7.x86_64                                                                                          162/179 
  Aufräumen        : libdb-5.3.21-20.el7.x86_64                                                                                           163/179 
  Aufräumen        : libteam-1.25-5.el7.x86_64                                                                                            164/179 
  Aufräumen        : kernel-tools-libs-3.10.0-693.el7.x86_64                                                                              165/179 
  Aufräumen        : python-gobject-base-3.22.0-1.el7.x86_64                                                                              166/179 
  Aufräumen        : python-perf-3.10.0-693.el7.x86_64                                                                                    167/179 
  Aufräumen        : libpciaccess-0.13.4-3.el7_3.x86_64                                                                                   168/179 
  Aufräumen        : 1:grub2-common-2.02-0.64.el7.centos.noarch                                                                           169/179 
  Aufräumen        : 32:bind-license-9.9.4-50.el7.noarch                                                                                  170/179 
  Aufräumen        : glibc-common-2.17-196.el7.x86_64                                                                                     171/179 
  Aufräumen        : libstdc++-4.8.5-16.el7.x86_64                                                                                        172/179 
  Aufräumen        : ncurses-libs-5.9-13.20130511.el7.x86_64                                                                              173/179 
  Aufräumen        : bash-4.2.46-28.el7.x86_64                                                                                            174/179 
  Aufräumen        : nss-softokn-freebl-3.28.3-6.el7.x86_64                                                                               175/179 
  Aufräumen        : glibc-2.17-196.el7.x86_64                                                                                            176/179 
  Aufräumen        : ncurses-base-5.9-13.20130511.el7.noarch                                                                              177/179 
  Aufräumen        : tzdata-2017b-1.el7.noarch                                                                                            178/179 
  Aufräumen        : libgcc-4.8.5-16.el7.x86_64                                                                                           179/179 
  Überprüfung läuft: kmod-20-15.el7_4.7.x86_64                                                                                              1/179 
  Überprüfung läuft: iwl5150-firmware-8.24.2.2-58.el7_4.noarch                                                                              2/179 
  Überprüfung läuft: util-linux-2.23.2-43.el7_4.2.x86_64                                                                                    3/179 
  Überprüfung läuft: kernel-3.10.0-693.21.1.el7.x86_64                                                                                      4/179 
  Überprüfung läuft: 12:dhcp-common-4.2.5-58.el7.centos.3.x86_64                                                                            5/179 
  Überprüfung läuft: iwl6000g2b-firmware-17.168.5.2-58.el7_4.noarch                                                                         6/179 
  Überprüfung läuft: iwl4965-firmware-228.61.2.24-58.el7_4.noarch                                                                           7/179 
  Überprüfung läuft: iwl6000-firmware-9.221.4.1-58.el7_4.noarch                                                                             8/179 
  Überprüfung läuft: 1:iwl1000-firmware-39.31.5.1-58.el7_4.noarch                                                                           9/179 
  Überprüfung läuft: 1:NetworkManager-1.8.0-11.el7_4.x86_64                                                                                10/179 
  Überprüfung läuft: 12:dhcp-libs-4.2.5-58.el7.centos.3.x86_64                                                                             11/179 
  Überprüfung läuft: 1:grub2-2.02-0.65.el7.centos.2.x86_64                                                                                 12/179 
  Überprüfung läuft: iwl7265-firmware-22.0.7.0-58.el7_4.noarch                                                                             13/179 
  Überprüfung läuft: libteam-1.25-6.el7_4.3.x86_64                                                                                         14/179 
  Überprüfung läuft: 32:bind-libs-lite-9.9.4-51.el7_4.2.x86_64                                                                             15/179 
  Überprüfung läuft: nss-sysinit-3.28.4-15.el7_4.x86_64                                                                                    16/179 
  Überprüfung läuft: libstdc++-4.8.5-16.el7_4.2.x86_64                                                                                     17/179 
  Überprüfung läuft: openssh-server-7.4p1-13.el7_4.x86_64                                                                                  18/179 
  Überprüfung läuft: dracut-network-033-502.el7_4.1.x86_64                                                                                 19/179 
  Überprüfung läuft: tzdata-2018d-1.el7.noarch                                                                                             20/179 
  Überprüfung läuft: libgcc-4.8.5-16.el7_4.2.x86_64                                                                                        21/179 
  Überprüfung läuft: 1:grub2-tools-extra-2.02-0.65.el7.centos.2.x86_64                                                                     22/179 
  Überprüfung läuft: 1:wpa_supplicant-2.6-5.el7_4.1.x86_64                                                                                 23/179 
  Überprüfung läuft: cryptsetup-libs-1.7.4-3.el7_4.1.x86_64                                                                                24/179 
  Überprüfung läuft: glibc-2.17-196.el7_4.2.x86_64                                                                                         25/179 
  Überprüfung läuft: iptables-1.4.21-18.3.el7_4.x86_64                                                                                     26/179 
  Überprüfung läuft: linux-firmware-20170606-58.gitc990aae.el7_4.noarch                                                                    27/179 
  Überprüfung läuft: libmount-2.23.2-43.el7_4.2.x86_64                                                                                     28/179 
  Überprüfung läuft: iwl3160-firmware-22.0.7.0-58.el7_4.noarch                                                                             29/179 
  Überprüfung läuft: dracut-033-502.el7_4.1.x86_64                                                                                         30/179 
  Überprüfung läuft: ncurses-libs-5.9-14.20130511.el7_4.x86_64                                                                             31/179 
  Überprüfung läuft: iwl100-firmware-39.31.5.1-58.el7_4.noarch                                                                             32/179 
  Überprüfung läuft: 1:NetworkManager-team-1.8.0-11.el7_4.x86_64                                                                           33/179 
  Überprüfung läuft: 12:dhclient-4.2.5-58.el7.centos.3.x86_64                                                                              34/179 
  Überprüfung läuft: iwl6050-firmware-41.28.5.1-58.el7_4.noarch                                                                            35/179 
  Überprüfung läuft: iwl7260-firmware-22.0.7.0-58.el7_4.noarch                                                                             36/179 
  Überprüfung läuft: libdb-5.3.21-21.el7_4.x86_64                                                                                          37/179 
  Überprüfung läuft: openssh-7.4p1-13.el7_4.x86_64                                                                                         38/179 
  Überprüfung läuft: ncurses-5.9-14.20130511.el7_4.x86_64                                                                                  39/179 
  Überprüfung läuft: systemd-219-42.el7_4.10.x86_64                                                                                        40/179 
  Überprüfung läuft: python-gobject-base-3.22.0-1.el7_4.1.x86_64                                                                           41/179 
  Überprüfung läuft: iwl105-firmware-18.168.6.1-58.el7_4.noarch                                                                            42/179 
  Überprüfung läuft: 1:grub2-common-2.02-0.65.el7.centos.2.noarch                                                                          43/179 
  Überprüfung läuft: 32:bind-license-9.9.4-51.el7_4.2.noarch                                                                               44/179 
  Überprüfung läuft: device-mapper-persistent-data-0.7.0-0.1.rc6.el7_4.1.x86_64                                                            45/179 
  Überprüfung läuft: 1:NetworkManager-tui-1.8.0-11.el7_4.x86_64                                                                            46/179 
  Überprüfung läuft: kexec-tools-2.0.14-17.2.el7.x86_64                                                                                    47/179 
  Überprüfung läuft: iwl3945-firmware-15.32.2.9-58.el7_4.noarch                                                                            48/179 
  Überprüfung läuft: nss-softokn-freebl-3.28.3-8.el7_4.x86_64                                                                              49/179 
  Überprüfung läuft: tuned-2.8.0-5.el7_4.2.noarch                                                                                          50/179 
  Überprüfung läuft: libdb-utils-5.3.21-21.el7_4.x86_64                                                                                    51/179 
  Überprüfung läuft: libpciaccess-0.13.4-3.1.el7_4.x86_64                                                                                  52/179 
  Überprüfung läuft: selinux-policy-targeted-3.13.1-166.el7_4.9.noarch                                                                     53/179 
  Überprüfung läuft: 2:microcode_ctl-2.1-22.5.el7_4.x86_64                                                                                 54/179 
  Überprüfung läuft: kpartx-0.4.9-111.el7_4.2.x86_64                                                                                       55/179 
  Überprüfung läuft: 1:NetworkManager-wifi-1.8.0-11.el7_4.x86_64                                                                           56/179 
  Überprüfung läuft: iwl2000-firmware-18.168.6.1-58.el7_4.noarch                                                                           57/179 
  Überprüfung läuft: iwl2030-firmware-18.168.6.1-58.el7_4.noarch                                                                           58/179 
  Überprüfung läuft: cpio-2.11-25.el7_4.x86_64                                                                                             59/179 
  Überprüfung läuft: 1:grub2-pc-2.02-0.65.el7.centos.2.x86_64                                                                              60/179 
  Überprüfung läuft: python-perf-3.10.0-693.21.1.el7.x86_64                                                                                61/179 
  Überprüfung läuft: 1:grub2-tools-2.02-0.65.el7.centos.2.x86_64                                                                           62/179 
  Überprüfung läuft: 1:grub2-tools-minimal-2.02-0.65.el7.centos.2.x86_64                                                                   63/179 
  Überprüfung läuft: iwl6000g2a-firmware-17.168.5.3-58.el7_4.noarch                                                                        64/179 
  Überprüfung läuft: teamd-1.25-6.el7_4.3.x86_64                                                                                           65/179 
  Überprüfung läuft: iwl135-firmware-18.168.6.1-58.el7_4.noarch                                                                            66/179 
  Überprüfung läuft: ncurses-base-5.9-14.20130511.el7_4.noarch                                                                             67/179 
  Überprüfung läuft: nss-tools-3.28.4-15.el7_4.x86_64                                                                                      68/179 
  Überprüfung läuft: kmod-libs-20-15.el7_4.7.x86_64                                                                                        69/179 
  Überprüfung läuft: 1:NetworkManager-libnm-1.8.0-11.el7_4.x86_64                                                                          70/179 
  Überprüfung läuft: systemd-sysv-219-42.el7_4.10.x86_64                                                                                   71/179 
  Überprüfung läuft: libblkid-2.23.2-43.el7_4.2.x86_64                                                                                     72/179 
  Überprüfung läuft: kernel-tools-libs-3.10.0-693.21.1.el7.x86_64                                                                          73/179 
  Überprüfung läuft: nss-softokn-3.28.3-8.el7_4.x86_64                                                                                     74/179 
  Überprüfung läuft: dracut-config-rescue-033-502.el7_4.1.x86_64                                                                           75/179 
  Überprüfung läuft: openssh-clients-7.4p1-13.el7_4.x86_64                                                                                 76/179 
  Überprüfung läuft: systemd-libs-219-42.el7_4.10.x86_64                                                                                   77/179 
  Überprüfung läuft: selinux-policy-3.13.1-166.el7_4.9.noarch                                                                              78/179 
  Überprüfung läuft: iwl5000-firmware-8.83.5.1_1-58.el7_4.noarch                                                                           79/179 
  Überprüfung läuft: libgomp-4.8.5-16.el7_4.2.x86_64                                                                                       80/179 
  Überprüfung läuft: bash-4.2.46-29.el7_4.x86_64                                                                                           81/179 
  Überprüfung läuft: initscripts-9.49.39-1.el7_4.1.x86_64                                                                                  82/179 
  Überprüfung läuft: binutils-2.25.1-32.base.el7_4.2.x86_64                                                                                83/179 
  Überprüfung läuft: sudo-1.8.19p2-11.el7_4.x86_64                                                                                         84/179 
  Überprüfung läuft: nss-3.28.4-15.el7_4.x86_64                                                                                            85/179 
  Überprüfung läuft: yum-3.4.3-154.el7.centos.1.noarch                                                                                     86/179 
  Überprüfung läuft: 1:grub2-pc-modules-2.02-0.65.el7.centos.2.noarch                                                                      87/179 
  Überprüfung läuft: glibc-common-2.17-196.el7_4.2.x86_64                                                                                  88/179 
  Überprüfung läuft: kernel-tools-3.10.0-693.21.1.el7.x86_64                                                                               89/179 
  Überprüfung läuft: libuuid-2.23.2-43.el7_4.2.x86_64                                                                                      90/179 
  Überprüfung läuft: selinux-policy-3.13.1-166.el7.noarch                                                                                  91/179 
  Überprüfung läuft: nss-3.28.4-8.el7.x86_64                                                                                               92/179 
  Überprüfung läuft: iwl6000-firmware-9.221.4.1-56.el7.noarch                                                                              93/179 
  Überprüfung läuft: nss-sysinit-3.28.4-8.el7.x86_64                                                                                       94/179 
  Überprüfung läuft: 1:NetworkManager-libnm-1.8.0-9.el7.x86_64                                                                             95/179 
  Überprüfung läuft: iwl3945-firmware-15.32.2.9-56.el7.noarch                                                                              96/179 
  Überprüfung läuft: 1:grub2-2.02-0.64.el7.centos.x86_64                                                                                   97/179 
  Überprüfung läuft: systemd-libs-219-42.el7.x86_64                                                                                        98/179 
  Überprüfung läuft: 1:grub2-tools-extra-2.02-0.64.el7.centos.x86_64                                                                       99/179 
  Überprüfung läuft: nss-tools-3.28.4-8.el7.x86_64                                                                                        100/179 
  Überprüfung läuft: libmount-2.23.2-43.el7.x86_64                                                                                        101/179 
  Überprüfung läuft: tzdata-2017b-1.el7.noarch                                                                                            102/179 
  Überprüfung läuft: sudo-1.8.19p2-10.el7.x86_64                                                                                          103/179 
  Überprüfung läuft: dracut-config-rescue-033-502.el7.x86_64                                                                              104/179 
  Überprüfung läuft: iwl2000-firmware-18.168.6.1-56.el7.noarch                                                                            105/179 
  Überprüfung läuft: 1:iwl1000-firmware-39.31.5.1-56.el7.noarch                                                                           106/179 
  Überprüfung läuft: glibc-common-2.17-196.el7.x86_64                                                                                     107/179 
  Überprüfung läuft: libdb-5.3.21-20.el7.x86_64                                                                                           108/179 
  Überprüfung läuft: glibc-2.17-196.el7.x86_64                                                                                            109/179 
  Überprüfung läuft: iwl5150-firmware-8.24.2.2-56.el7.noarch                                                                              110/179 
  Überprüfung läuft: kpartx-0.4.9-111.el7.x86_64                                                                                          111/179 
  Überprüfung läuft: iwl100-firmware-39.31.5.1-56.el7.noarch                                                                              112/179 
  Überprüfung läuft: libuuid-2.23.2-43.el7.x86_64                                                                                         113/179 
  Überprüfung läuft: nss-softokn-3.28.3-6.el7.x86_64                                                                                      114/179 
  Überprüfung läuft: 1:NetworkManager-1.8.0-9.el7.x86_64                                                                                  115/179 
  Überprüfung läuft: libpciaccess-0.13.4-3.el7_3.x86_64                                                                                   116/179 
  Überprüfung läuft: iwl5000-firmware-8.83.5.1_1-56.el7.noarch                                                                            117/179 
  Überprüfung läuft: libgomp-4.8.5-16.el7.x86_64                                                                                          118/179 
  Überprüfung läuft: teamd-1.25-5.el7.x86_64                                                                                              119/179 
  Überprüfung läuft: 1:grub2-tools-2.02-0.64.el7.centos.x86_64                                                                            120/179 
  Überprüfung läuft: 12:dhclient-4.2.5-58.el7.centos.x86_64                                                                               121/179 
  Überprüfung läuft: iwl4965-firmware-228.61.2.24-56.el7.noarch                                                                           122/179 
  Überprüfung läuft: 1:wpa_supplicant-2.6-5.el7.x86_64                                                                                    123/179 
  Überprüfung läuft: 2:microcode_ctl-2.1-22.el7.x86_64                                                                                    124/179 
  Überprüfung läuft: iwl6000g2a-firmware-17.168.5.3-56.el7.noarch                                                                         125/179 
  Überprüfung läuft: systemd-219-42.el7.x86_64                                                                                            126/179 
  Überprüfung läuft: device-mapper-persistent-data-0.7.0-0.1.rc6.el7.x86_64                                                               127/179 
  Überprüfung läuft: 1:NetworkManager-tui-1.8.0-9.el7.x86_64                                                                              128/179 
  Überprüfung läuft: kernel-tools-libs-3.10.0-693.el7.x86_64                                                                              129/179 
  Überprüfung läuft: kmod-20-15.el7.x86_64                                                                                                130/179 
  Überprüfung läuft: selinux-policy-targeted-3.13.1-166.el7.noarch                                                                        131/179 
  Überprüfung läuft: 1:NetworkManager-wifi-1.8.0-9.el7.x86_64                                                                             132/179 
  Überprüfung läuft: linux-firmware-20170606-56.gitc990aae.el7.noarch                                                                     133/179 
  Überprüfung läuft: iwl6050-firmware-41.28.5.1-56.el7.noarch                                                                             134/179 
  Überprüfung läuft: iwl135-firmware-18.168.6.1-56.el7.noarch                                                                             135/179 
  Überprüfung läuft: 12:dhcp-libs-4.2.5-58.el7.centos.x86_64                                                                              136/179 
  Überprüfung läuft: cryptsetup-libs-1.7.4-3.el7.x86_64                                                                                   137/179 
  Überprüfung läuft: openssh-7.4p1-11.el7.x86_64                                                                                          138/179 
  Überprüfung läuft: libteam-1.25-5.el7.x86_64                                                                                            139/179 
  Überprüfung läuft: 1:NetworkManager-team-1.8.0-9.el7.x86_64                                                                             140/179 
  Überprüfung läuft: openssh-server-7.4p1-11.el7.x86_64                                                                                   141/179 
  Überprüfung läuft: initscripts-9.49.39-1.el7.x86_64                                                                                     142/179 
  Überprüfung läuft: 1:grub2-pc-modules-2.02-0.64.el7.centos.noarch                                                                       143/179 
  Überprüfung läuft: 1:grub2-tools-minimal-2.02-0.64.el7.centos.x86_64                                                                    144/179 
  Überprüfung läuft: openssh-clients-7.4p1-11.el7.x86_64                                                                                  145/179 
  Überprüfung läuft: tuned-2.8.0-5.el7.noarch                                                                                             146/179 
  Überprüfung läuft: kernel-tools-3.10.0-693.el7.x86_64                                                                                   147/179 
  Überprüfung läuft: 1:grub2-pc-2.02-0.64.el7.centos.x86_64                                                                               148/179 
  Überprüfung läuft: iwl6000g2b-firmware-17.168.5.2-56.el7.noarch                                                                         149/179 
  Überprüfung läuft: 1:grub2-common-2.02-0.64.el7.centos.noarch                                                                           150/179 
  Überprüfung läuft: iwl105-firmware-18.168.6.1-56.el7.noarch                                                                             151/179 
  Überprüfung läuft: bash-4.2.46-28.el7.x86_64                                                                                            152/179 
  Überprüfung läuft: dracut-network-033-502.el7.x86_64                                                                                    153/179 
  Überprüfung läuft: ncurses-libs-5.9-13.20130511.el7.x86_64                                                                              154/179 
  Überprüfung läuft: iptables-1.4.21-18.0.1.el7.centos.x86_64                                                                             155/179 
  Überprüfung läuft: kmod-libs-20-15.el7.x86_64                                                                                           156/179 
  Überprüfung läuft: iwl2030-firmware-18.168.6.1-56.el7.noarch                                                                            157/179 
  Überprüfung läuft: kexec-tools-2.0.14-17.el7.x86_64                                                                                     158/179 
  Überprüfung läuft: python-gobject-base-3.22.0-1.el7.x86_64                                                                              159/179 
  Überprüfung läuft: libblkid-2.23.2-43.el7.x86_64                                                                                        160/179 
  Überprüfung läuft: systemd-sysv-219-42.el7.x86_64                                                                                       161/179 
  Überprüfung läuft: dracut-033-502.el7.x86_64                                                                                            162/179 
  Überprüfung läuft: ncurses-base-5.9-13.20130511.el7.noarch                                                                              163/179 
  Überprüfung läuft: yum-3.4.3-154.el7.centos.noarch                                                                                      164/179 
  Überprüfung läuft: libgcc-4.8.5-16.el7.x86_64                                                                                           165/179 
  Überprüfung läuft: 12:dhcp-common-4.2.5-58.el7.centos.x86_64                                                                            166/179 
  Überprüfung läuft: 32:bind-libs-lite-9.9.4-50.el7.x86_64                                                                                167/179 
  Überprüfung läuft: libstdc++-4.8.5-16.el7.x86_64                                                                                        168/179 
  Überprüfung läuft: binutils-2.25.1-31.base.el7.x86_64                                                                                   169/179 
  Überprüfung läuft: ncurses-5.9-13.20130511.el7.x86_64                                                                                   170/179 
  Überprüfung läuft: iwl3160-firmware-22.0.7.0-56.el7.noarch                                                                              171/179 
  Überprüfung läuft: 32:bind-license-9.9.4-50.el7.noarch                                                                                  172/179 
  Überprüfung läuft: iwl7265-firmware-22.0.7.0-56.el7.noarch                                                                              173/179 
  Überprüfung läuft: util-linux-2.23.2-43.el7.x86_64                                                                                      174/179 
  Überprüfung läuft: nss-softokn-freebl-3.28.3-6.el7.x86_64                                                                               175/179 
  Überprüfung läuft: libdb-utils-5.3.21-20.el7.x86_64                                                                                     176/179 
  Überprüfung läuft: python-perf-3.10.0-693.el7.x86_64                                                                                    177/179 
  Überprüfung läuft: iwl7260-firmware-22.0.7.0-56.el7.noarch                                                                              178/179 
  Überprüfung läuft: cpio-2.11-24.el7.x86_64                                                                                              179/179 

Installiert:
  grub2.x86_64 1:2.02-0.65.el7.centos.2                                  grub2-tools.x86_64 1:2.02-0.65.el7.centos.2                             
  grub2-tools-extra.x86_64 1:2.02-0.65.el7.centos.2                      grub2-tools-minimal.x86_64 1:2.02-0.65.el7.centos.2                     
  kernel.x86_64 0:3.10.0-693.21.1.el7                                   

Aktualisiert:
  NetworkManager.x86_64 1:1.8.0-11.el7_4                              NetworkManager-libnm.x86_64 1:1.8.0-11.el7_4                               
  NetworkManager-team.x86_64 1:1.8.0-11.el7_4                         NetworkManager-tui.x86_64 1:1.8.0-11.el7_4                                 
  NetworkManager-wifi.x86_64 1:1.8.0-11.el7_4                         bash.x86_64 0:4.2.46-29.el7_4                                              
  bind-libs-lite.x86_64 32:9.9.4-51.el7_4.2                           bind-license.noarch 32:9.9.4-51.el7_4.2                                    
  binutils.x86_64 0:2.25.1-32.base.el7_4.2                            cpio.x86_64 0:2.11-25.el7_4                                                
  cryptsetup-libs.x86_64 0:1.7.4-3.el7_4.1                            device-mapper-persistent-data.x86_64 0:0.7.0-0.1.rc6.el7_4.1               
  dhclient.x86_64 12:4.2.5-58.el7.centos.3                            dhcp-common.x86_64 12:4.2.5-58.el7.centos.3                                
  dhcp-libs.x86_64 12:4.2.5-58.el7.centos.3                           dracut.x86_64 0:033-502.el7_4.1                                            
  dracut-config-rescue.x86_64 0:033-502.el7_4.1                       dracut-network.x86_64 0:033-502.el7_4.1                                    
  glibc.x86_64 0:2.17-196.el7_4.2                                     glibc-common.x86_64 0:2.17-196.el7_4.2                                     
  grub2-common.noarch 1:2.02-0.65.el7.centos.2                        grub2-pc.x86_64 1:2.02-0.65.el7.centos.2                                   
  grub2-pc-modules.noarch 1:2.02-0.65.el7.centos.2                    initscripts.x86_64 0:9.49.39-1.el7_4.1                                     
  iptables.x86_64 0:1.4.21-18.3.el7_4                                 iwl100-firmware.noarch 0:39.31.5.1-58.el7_4                                
  iwl1000-firmware.noarch 1:39.31.5.1-58.el7_4                        iwl105-firmware.noarch 0:18.168.6.1-58.el7_4                               
  iwl135-firmware.noarch 0:18.168.6.1-58.el7_4                        iwl2000-firmware.noarch 0:18.168.6.1-58.el7_4                              
  iwl2030-firmware.noarch 0:18.168.6.1-58.el7_4                       iwl3160-firmware.noarch 0:22.0.7.0-58.el7_4                                
  iwl3945-firmware.noarch 0:15.32.2.9-58.el7_4                        iwl4965-firmware.noarch 0:228.61.2.24-58.el7_4                             
  iwl5000-firmware.noarch 0:8.83.5.1_1-58.el7_4                       iwl5150-firmware.noarch 0:8.24.2.2-58.el7_4                                
  iwl6000-firmware.noarch 0:9.221.4.1-58.el7_4                        iwl6000g2a-firmware.noarch 0:17.168.5.3-58.el7_4                           
  iwl6000g2b-firmware.noarch 0:17.168.5.2-58.el7_4                    iwl6050-firmware.noarch 0:41.28.5.1-58.el7_4                               
  iwl7260-firmware.noarch 0:22.0.7.0-58.el7_4                         iwl7265-firmware.noarch 0:22.0.7.0-58.el7_4                                
  kernel-tools.x86_64 0:3.10.0-693.21.1.el7                           kernel-tools-libs.x86_64 0:3.10.0-693.21.1.el7                             
  kexec-tools.x86_64 0:2.0.14-17.2.el7                                kmod.x86_64 0:20-15.el7_4.7                                                
  kmod-libs.x86_64 0:20-15.el7_4.7                                    kpartx.x86_64 0:0.4.9-111.el7_4.2                                          
  libblkid.x86_64 0:2.23.2-43.el7_4.2                                 libdb.x86_64 0:5.3.21-21.el7_4                                             
  libdb-utils.x86_64 0:5.3.21-21.el7_4                                libgcc.x86_64 0:4.8.5-16.el7_4.2                                           
  libgomp.x86_64 0:4.8.5-16.el7_4.2                                   libmount.x86_64 0:2.23.2-43.el7_4.2                                        
  libpciaccess.x86_64 0:0.13.4-3.1.el7_4                              libstdc++.x86_64 0:4.8.5-16.el7_4.2                                        
  libteam.x86_64 0:1.25-6.el7_4.3                                     libuuid.x86_64 0:2.23.2-43.el7_4.2                                         
  linux-firmware.noarch 0:20170606-58.gitc990aae.el7_4                microcode_ctl.x86_64 2:2.1-22.5.el7_4                                      
  ncurses.x86_64 0:5.9-14.20130511.el7_4                              ncurses-base.noarch 0:5.9-14.20130511.el7_4                                
  ncurses-libs.x86_64 0:5.9-14.20130511.el7_4                         nss.x86_64 0:3.28.4-15.el7_4                                               
  nss-softokn.x86_64 0:3.28.3-8.el7_4                                 nss-softokn-freebl.x86_64 0:3.28.3-8.el7_4                                 
  nss-sysinit.x86_64 0:3.28.4-15.el7_4                                nss-tools.x86_64 0:3.28.4-15.el7_4                                         
  openssh.x86_64 0:7.4p1-13.el7_4                                     openssh-clients.x86_64 0:7.4p1-13.el7_4                                    
  openssh-server.x86_64 0:7.4p1-13.el7_4                              python-gobject-base.x86_64 0:3.22.0-1.el7_4.1                              
  python-perf.x86_64 0:3.10.0-693.21.1.el7                            selinux-policy.noarch 0:3.13.1-166.el7_4.9                                 
  selinux-policy-targeted.noarch 0:3.13.1-166.el7_4.9                 sudo.x86_64 0:1.8.19p2-11.el7_4                                            
  systemd.x86_64 0:219-42.el7_4.10                                    systemd-libs.x86_64 0:219-42.el7_4.10                                      
  systemd-sysv.x86_64 0:219-42.el7_4.10                               teamd.x86_64 0:1.25-6.el7_4.3                                              
  tuned.noarch 0:2.8.0-5.el7_4.2                                      tzdata.noarch 0:2018d-1.el7                                                
  util-linux.x86_64 0:2.23.2-43.el7_4.2                               wpa_supplicant.x86_64 1:2.6-5.el7_4.1                                      
  yum.noarch 0:3.4.3-154.el7.centos.1                                

Ersetzt       :
  grub2.x86_64 1:2.02-0.64.el7.centos                                  grub2-tools.x86_64 1:2.02-0.64.el7.centos                                 

Komplett!
➜  ~ sudo yum -y install httpd
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.dclux.com
 * extras: mirror.dclux.com
 * updates: mirror.dclux.com
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
--------> Paket httpd.x86_64 0:2.4.6-67.el7.centos.6 markiert, um installiert zu werden
--> Abhängigkeit httpd-tools = 2.4.6-67.el7.centos.6 wird für Paket httpd-2.4.6-67.el7.centos.6.x86_64 verarbeitet
--> Abhängigkeit /etc/mime.types wird für Paket httpd-2.4.6-67.el7.centos.6.x86_64 verarbeitet
--> Abhängigkeit libaprutil-1.so.0()(64bit) wird für Paket httpd-2.4.6-67.el7.centos.6.x86_64 verarbeitet
--> Abhängigkeit libapr-1.so.0()(64bit) wird für Paket httpd-2.4.6-67.el7.centos.6.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
--------> Paket apr.x86_64 0:1.4.8-3.el7_4.1 markiert, um installiert zu werden
--------> Paket apr-util.x86_64 0:1.5.2-6.el7 markiert, um installiert zu werden
--------> Paket httpd-tools.x86_64 0:2.4.6-67.el7.centos.6 markiert, um installiert zu werden
--------> Paket mailcap.noarch 0:2.1.41-2.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==================================================================================================================================================
 Package                           Arch                         Version                                       Paketquelle                   Größe
==================================================================================================================================================
Installieren:
 httpd                             x86_64                       2.4.6-67.el7.centos.6                         updates                       2.7 M
Als Abhängigkeiten installiert:
 apr                               x86_64                       1.4.8-3.el7_4.1                               updates                       103 k
 apr-util                          x86_64                       1.5.2-6.el7                                   base                           92 k
 httpd-tools                       x86_64                       2.4.6-67.el7.centos.6                         updates                        88 k
 mailcap                           noarch                       2.1.41-2.el7                                  base                           31 k

Transaktionsübersicht
==================================================================================================================================================
Installieren  1 Paket (+4 Abhängige Pakete)

Gesamte Downloadgröße: 3.0 M
Installationsgröße: 10 M
Downloading packages:
(1/5): httpd-tools-2.4.6-67.el7.centos.6.x86_64.rpm                                                                        |  88 kB  00:00:03     
(2/5): mailcap-2.1.41-2.el7.noarch.rpm                                                                                     |  31 kB  00:00:03     
(3/5): apr-1.4.8-3.el7_4.1.x86_64.rpm                                                                                      | 103 kB  00:00:03     
(4/5): apr-util-1.5.2-6.el7.x86_64.rpm                                                                                     |  92 kB  00:00:03     
(5/5): httpd-2.4.6-67.el7.centos.6.x86_64.rpm                                                                              | 2.7 MB  00:00:04     
-------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                            681 kB/s | 3.0 MB  00:00:04     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : apr-1.4.8-3.el7_4.1.x86_64                                                                                               1/5 
  Installieren     : apr-util-1.5.2-6.el7.x86_64                                                                                              2/5 
  Installieren     : httpd-tools-2.4.6-67.el7.centos.6.x86_64                                                                                 3/5 
  Installieren     : mailcap-2.1.41-2.el7.noarch                                                                                              4/5 
  Installieren     : httpd-2.4.6-67.el7.centos.6.x86_64                                                                                       5/5 
  Überprüfung läuft: mailcap-2.1.41-2.el7.noarch                                                                                              1/5 
  Überprüfung läuft: httpd-2.4.6-67.el7.centos.6.x86_64                                                                                       2/5 
  Überprüfung läuft: apr-util-1.5.2-6.el7.x86_64                                                                                              3/5 
  Überprüfung läuft: apr-1.4.8-3.el7_4.1.x86_64                                                                                               4/5 
  Überprüfung läuft: httpd-tools-2.4.6-67.el7.centos.6.x86_64                                                                                 5/5 

Installiert:
  httpd.x86_64 0:2.4.6-67.el7.centos.6                                                                                                            

Abhängigkeit installiert:
  apr.x86_64 0:1.4.8-3.el7_4.1    apr-util.x86_64 0:1.5.2-6.el7    httpd-tools.x86_64 0:2.4.6-67.el7.centos.6    mailcap.noarch 0:2.1.41-2.el7   

Komplett!
➜  ~ sudo firewall-cmd --permanent --add-port=80/tcp
success
➜  ~ sudo firewall-cmd --permanent --add-port=443/tcp
success
➜  ~ sudo firewall-cmd --reload
success
➜  ~ sudo systemctl start httpd
➜  ~ sudo systemctl enable httpd
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
➜  ~ sudo systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Mo 2018-04-09 19:22:40 CEST; 26s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 2320 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─2320 /usr/sbin/httpd -DFOREGROUND
           ├─2321 /usr/sbin/httpd -DFOREGROUND
           ├─2322 /usr/sbin/httpd -DFOREGROUND
           ├─2323 /usr/sbin/httpd -DFOREGROUND
           ├─2324 /usr/sbin/httpd -DFOREGROUND
           └─2325 /usr/sbin/httpd -DFOREGROUND

Apr 09 19:22:40 localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
Apr 09 19:22:40 localhost.localdomain httpd[2320]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name...message
Apr 09 19:22:40 localhost.localdomain systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode