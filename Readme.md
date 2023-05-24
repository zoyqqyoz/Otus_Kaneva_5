ДЗ 5. Стенд Vagrant с NFS

1. Создаём Vagrantfile по Методичке
```
#ruby
# -*- mode: ruby -*-
# vi: set ft=ruby : vsa
Vagrant.configure(2) do |config|
 config.vm.box = "centos/7"
 config.vm.box_version = "2004.01"
 config.vm.provider "virtualbox" do |v|
 v.memory = 256
 v.cpus = 1
 end
 config.vm.define "nfss" do |nfss|
 nfss.vm.network "private_network", ip: "192.168.56.10",  virtualbox__intnet: "net1"
 nfss.vm.hostname = "nfss"
 end
 config.vm.define "nfsc" do |nfsc|
 nfsc.vm.network "private_network", ip: "192.168.56.11",  virtualbox__intnet: "net1"
 nfsc.vm.hostname = "nfsc"
 config.vm.synced_folder ".", "/vagrant", disabled: true
 end
end
```

2. Поднимаем 2 ВМ из Vagrantfile
```
neva@Uneva:~$ vagrant up
Bringing machine 'nfss' up with 'virtualbox' provider...
Bringing machine 'nfsc' up with 'virtualbox' provider...
==> nfss: Checking if box 'centos/7' version '2004.01' is up to date...
==> nfss: Clearing any previously set forwarded ports...
==> nfss: Clearing any previously set network interfaces...
==> nfss: Preparing network interfaces based on configuration...
    nfss: Adapter 1: nat
    nfss: Adapter 2: intnet
==> nfss: Forwarding ports...
    nfss: 22 (guest) => 2222 (host) (adapter 1)
==> nfss: Running 'pre-boot' VM customizations...
==> nfss: Booting VM...
==> nfss: Waiting for machine to boot. This may take a few minutes...
    nfss: SSH address: 127.0.0.1:2222
    nfss: SSH username: vagrant
    nfss: SSH auth method: private key
==> nfss: Machine booted and ready!
==> nfss: Checking for guest additions in VM...
    nfss: No guest additions were detected on the base box for this VM! Guest
    nfss: additions are required for forwarded ports, shared folders, host only
    nfss: networking, and more. If SSH fails on this machine, please install
    nfss: the guest additions and repackage the box to continue.
    nfss:
    nfss: This is not an error message; everything may continue to work properly,
    nfss: in which case you may ignore this message.
==> nfss: Setting hostname...
==> nfss: Configuring and enabling network interfaces...
==> nfsc: Importing base box 'centos/7'...
==> nfsc: Matching MAC address for NAT networking...
==> nfsc: Checking if box 'centos/7' version '2004.01' is up to date...
==> nfsc: Setting the name of the VM: neva_nfsc_1684912517555_27348
==> nfsc: Fixed port collision for 22 => 2222. Now on port 2200.
==> nfsc: Clearing any previously set network interfaces...
==> nfsc: Preparing network interfaces based on configuration...
    nfsc: Adapter 1: nat
    nfsc: Adapter 2: intnet
==> nfsc: Forwarding ports...
    nfsc: 22 (guest) => 2200 (host) (adapter 1)
==> nfsc: Running 'pre-boot' VM customizations...
==> nfsc: Booting VM...
==> nfsc: Waiting for machine to boot. This may take a few minutes...
    nfsc: SSH address: 127.0.0.1:2200
    nfsc: SSH username: vagrant
    nfsc: SSH auth method: private key
    nfsc:
    nfsc: Vagrant insecure key detected. Vagrant will automatically replace
    nfsc: this with a newly generated keypair for better security.
    nfsc:
    nfsc: Inserting generated public key within guest...
    nfsc: Removing insecure key from the guest if it's present...
    nfsc: Key inserted! Disconnecting and reconnecting using new SSH key...
==> nfsc: Machine booted and ready!
==> nfsc: Checking for guest additions in VM...
    nfsc: No guest additions were detected on the base box for this VM! Guest
    nfsc: additions are required for forwarded ports, shared folders, host only
    nfsc: networking, and more. If SSH fails on this machine, please install
    nfsc: the guest additions and repackage the box to continue.
    nfsc:
    nfsc: This is not an error message; everything may continue to work properly,
    nfsc: in which case you may ignore this message.
==> nfsc: Setting hostname...
==> nfsc: Configuring and enabling network interfaces...
```

3. Подключаемся по ssh к серверу nfss и переключаемся на рута
```
neva@Uneva:~$ vagrant ssh nfss
[vagrant@nfss ~]$ sudo -i
```

4. Устанавливаем nfs-utils
```
[root@nfss ~]# yum install nfs-utils
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: de.mirrors.clouvider.net
 * extras: ftp.halifax.rwth-aachen.de
 * updates: ftp.halifax.rwth-aachen.de
base                                                                                                                                                                                                                  | 3.6 kB  00:00:00
extras                                                                                                                                                                                                                | 2.9 kB  00:00:00
updates                                                                                                                                                                                                               | 2.9 kB  00:00:00
(1/4): base/7/x86_64/group_gz                                                                                                                                                                                         | 153 kB  00:00:00
(2/4): extras/7/x86_64/primary_db                                                                                                                                                                                     | 249 kB  00:00:00
(3/4): base/7/x86_64/primary_db                                                                                                                                                                                       | 6.1 MB  00:00:02
(4/4): updates/7/x86_64/primary_db                                                                                                                                                                                    |  21 MB  00:00:04
Resolving Dependencies
--> Running transaction check
---> Package nfs-utils.x86_64 1:1.3.0-0.66.el7 will be updated
---> Package nfs-utils.x86_64 1:1.3.0-0.68.el7.2 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

=============================================================================================================================================================================================================================================
 Package                                                 Arch                                                 Version                                                            Repository                                             Size
=============================================================================================================================================================================================================================================
Updating:
 nfs-utils                                               x86_64                                               1:1.3.0-0.68.el7.2                                                 updates                                               413 k

Transaction Summary
=============================================================================================================================================================================================================================================
Upgrade  1 Package

Total download size: 413 k
Is this ok [y/d/N]: y
Downloading packages:
No Presto metadata available for updates
warning: /var/cache/yum/x86_64/7/updates/packages/nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY                                                            ]  0.0 B/s |    0 B  --:--:-- ETA
Public key for nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm is not installed
nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm                                                                                                                                                                                 | 413 kB  00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-8.2003.0.el7.centos.x86_64 (@anaconda)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 1:nfs-utils-1.3.0-0.68.el7.2.x86_64                                                                                                                                                                                       1/2
  Cleanup    : 1:nfs-utils-1.3.0-0.66.el7.x86_64                                                                                                                                                                                         2/2
  Verifying  : 1:nfs-utils-1.3.0-0.68.el7.2.x86_64                                                                                                                                                                                       1/2
  Verifying  : 1:nfs-utils-1.3.0-0.66.el7.x86_64                                                                                                                                                                                         2/2

Updated:
  nfs-utils.x86_64 1:1.3.0-0.68.el7.2
```

5. Включаем Фаейрволл и настраиваем доступ к сервисам NFS
```
[root@nfss ~]# systemctl enable firewalld --now
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.

[root@nfss ~]# firewall-cmd --add-service="nfs3" \
> --add-service="rpc-bind" \
> --add-service="mountd" \
> --permanent
success
[root@nfss ~]# firewall-cmd --reload
success
```

6. Включаем сервер NFS
```
[root@nfss ~]# systemctl enable nfs --now
Created symlink from /etc/systemd/system/multi-user.target.wants/nfs-server.service to /usr/lib/systemd/system/nfs-server.service.
```

7. Проверяем наличие слушаемых портов 2049/udp, 2049/tcp, 20048/udp,  20048/tcp, 111/udp, 111/tcp
```
[root@nfss ~]# ss -tnplu
Netid State      Recv-Q Send-Q                                                                       Local Address:Port                                                                                      Peer Address:Port
udp   UNCONN     0      0                                                                                        *:50902                                                                                                *:*
udp   UNCONN     0      0                                                                                        *:45811                                                                                                *:*                   users:(("rpc.statd",pid=2637,fd=7))
udp   UNCONN     0      0                                                                                        *:2049                                                                                                 *:*
udp   UNCONN     0      0                                                                                127.0.0.1:323                                                                                                  *:*                   users:(("chronyd",pid=375,fd=5))
udp   UNCONN     0      0                                                                                        *:68                                                                                                   *:*                   users:(("dhclient",pid=1910,fd=6))
udp   UNCONN     0      0                                                                                        *:20048                                                                                                *:*                   users:(("rpc.mountd",pid=2646,fd=7))
udp   UNCONN     0      0                                                                                        *:111                                                                                                  *:*                   users:(("rpcbind",pid=373,fd=6))
udp   UNCONN     0      0                                                                                        *:957                                                                                                  *:*                   users:(("rpcbind",pid=373,fd=7))
udp   UNCONN     0      0                                                                                127.0.0.1:703                                                                                                  *:*                   users:(("rpc.statd",pid=2637,fd=16))
udp   UNCONN     0      0                                                                                     [::]:60624                                                                                             [::]:*                   users:(("rpc.statd",pid=2637,fd=9))
udp   UNCONN     0      0                                                                                     [::]:2049                                                                                              [::]:*
udp   UNCONN     0      0                                                                                    [::1]:323                                                                                               [::]:*                   users:(("chronyd",pid=375,fd=6))
udp   UNCONN     0      0                                                                                     [::]:47952                                                                                             [::]:*
udp   UNCONN     0      0                                                                                     [::]:20048                                                                                             [::]:*                   users:(("rpc.mountd",pid=2646,fd=9))
udp   UNCONN     0      0                                                                                     [::]:111                                                                                               [::]:*                   users:(("rpcbind",pid=373,fd=9))
udp   UNCONN     0      0                                                                                     [::]:957                                                                                               [::]:*                   users:(("rpcbind",pid=373,fd=10))
tcp   LISTEN     0      128                                                                                      *:20048                                                                                                *:*                   users:(("rpc.mountd",pid=2646,fd=8))
tcp   LISTEN     0      128                                                                                      *:59923                                                                                                *:*                   users:(("rpc.statd",pid=2637,fd=8))
tcp   LISTEN     0      128                                                                                      *:22                                                                                                   *:*                   users:(("sshd",pid=692,fd=3))
tcp   LISTEN     0      100                                                                              127.0.0.1:25                                                                                                   *:*                   users:(("master",pid=774,fd=13))
tcp   LISTEN     0      64                                                                                       *:2049                                                                                                 *:*
tcp   LISTEN     0      64                                                                                       *:33642                                                                                                *:*
tcp   LISTEN     0      128                                                                                      *:111                                                                                                  *:*                   users:(("rpcbind",pid=373,fd=8))
tcp   LISTEN     0      128                                                                                   [::]:20048                                                                                             [::]:*                   users:(("rpc.mountd",pid=2646,fd=10))
tcp   LISTEN     0      128                                                                                   [::]:22                                                                                                [::]:*                   users:(("sshd",pid=692,fd=4))
tcp   LISTEN     0      64                                                                                    [::]:35321                                                                                             [::]:*
tcp   LISTEN     0      100                                                                                  [::1]:25                                                                                                [::]:*                   users:(("master",pid=774,fd=14))
tcp   LISTEN     0      64                                                                                    [::]:2049                                                                                              [::]:*
tcp   LISTEN     0      128                                                                                   [::]:51403                                                                                             [::]:*                   users:(("rpc.statd",pid=2637,fd=10))
tcp   LISTEN     0      128                                                                                   [::]:111                                                                                               [::]:*                   users:(("rpcbind",pid=373,fd=11))
```

8. Создаём и настраиваем дректорию для шары
```
[root@nfss ~]# mkdir -p /srv/share/upload
[root@nfss ~]# chown -R nfsnobody:nfsnobody /srv/share
[root@nfss ~]# chmod 0777 /srv/share/upload
```

9. Создаём в файле __/etc/exports__ структуру, которая позволит экспортировать директорию /srv/share/
```
[root@nfss ~]# cat << EOF > /etc/exports
> /srv/share 192.168.56.11/32(rw,sync,root_squash)
> EOF
[root@nfss ~]# cat /etc/exports
/srv/share 192.168.56.11/32(rw,sync,root_squash)
[root@nfss ~]#
```

10. Экспортируем директорию /srv/share/
```
[root@nfss ~]# exportfs -r
```

11. Проверяем
```
[root@nfss ~]# exportfs -s
/srv/share  192.168.56.11/32(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
```

12. Настраиваем клиент NFS на сервере nfsc, устанавливаем вспомогательные утилиты
```
[root@nfss ~]# exit
logout
[vagrant@nfss ~]$ exit
logout
neva@Uneva:~$ vagrant ssh nfsc
[vagrant@nfsc ~]$ sudo -i
[root@nfsc ~]#
[root@nfsc ~]# yum install nfs-utils
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: de.mirrors.clouvider.net
 * extras: de.mirrors.clouvider.net
 * updates: ftp.rz.uni-frankfurt.de
base                                                                                                                                                                                                                  | 3.6 kB  00:00:00
extras                                                                                                                                                                                                                | 2.9 kB  00:00:00
updates                                                                                                                                                                                                               | 2.9 kB  00:00:00
(1/4): base/7/x86_64/group_gz                                                                                                                                                                                         | 153 kB  00:00:00
(2/4): extras/7/x86_64/primary_db                                                                                                                                                                                     | 249 kB  00:00:00
(3/4): base/7/x86_64/primary_db                                                                                                                                                                                       | 6.1 MB  00:00:01
(4/4): updates/7/x86_64/primary_db                                                                                                                                                                                    |  21 MB  00:00:06
Resolving Dependencies
--> Running transaction check
---> Package nfs-utils.x86_64 1:1.3.0-0.66.el7 will be updated
---> Package nfs-utils.x86_64 1:1.3.0-0.68.el7.2 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

=============================================================================================================================================================================================================================================
 Package                                                 Arch                                                 Version                                                            Repository                                             Size
=============================================================================================================================================================================================================================================
Updating:
 nfs-utils                                               x86_64                                               1:1.3.0-0.68.el7.2                                                 updates                                               413 k

Transaction Summary
=============================================================================================================================================================================================================================================
Upgrade  1 Package

Total download size: 413 k
Is this ok [y/d/N]: y
Downloading packages:
No Presto metadata available for updates
warning: /var/cache/yum/x86_64/7/updates/packages/nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY                                                            ]  0.0 B/s |    0 B  --:--:-- ETA
Public key for nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm is not installed
nfs-utils-1.3.0-0.68.el7.2.x86_64.rpm                                                                                                                                                                                 | 413 kB  00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-8.2003.0.el7.centos.x86_64 (@anaconda)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 1:nfs-utils-1.3.0-0.68.el7.2.x86_64                                                                                                                                                                                       1/2
  Cleanup    : 1:nfs-utils-1.3.0-0.66.el7.x86_64                                                                                                                                                                                         2/2
  Verifying  : 1:nfs-utils-1.3.0-0.68.el7.2.x86_64                                                                                                                                                                                       1/2
  Verifying  : 1:nfs-utils-1.3.0-0.66.el7.x86_64                                                                                                                                                                                         2/2

Updated:
  nfs-utils.x86_64 1:1.3.0-0.68.el7.2

Complete!
```

13. Включаем firewall и проверяем, что он работает
```
[root@nfsc ~]# systemctl enable firewalld --now
Created symlink from /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service to /usr/lib/systemd/system/firewalld.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/firewalld.service to /usr/lib/systemd/system/firewalld.service.
[root@nfsc ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2023-05-24 08:42:29 UTC; 38s ago
     Docs: man:firewalld(1)
 Main PID: 21820 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─21820 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

May 24 08:42:29 nfsc systemd[1]: Starting firewalld - dynamic firewall daemon...
May 24 08:42:29 nfsc systemd[1]: Started firewalld - dynamic firewall daemon.
May 24 08:42:30 nfsc firewalld[21820]: WARNING: AllowZoneDrifting is enabled. This is considered an insecure configuration option. It will be removed in a future release. Please consider disabling it now.
```

14. Добавим автомонтирование шары при загрузке на клиенте
```
[root@nfsc ~]# echo "192.168.56.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0" >> /etc/fstab
[root@nfsc ~]# cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Apr 30 22:04:55 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=1c419d6c-5064-4a2b-953c-05b2c67edb15 /                       xfs     defaults        0 0
/swapfile none swap defaults 0 0
#VAGRANT-BEGIN
# The contents below are automatically generated by Vagrant. Do not modify.
#VAGRANT-END
192.168.56.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0
[root@nfsc ~]#
```

15. Перечитываем параметры, также в данном случае происходит автоматическая генерация systemd units в каталоге `/run/systemd/generator/`, которые производят монтирование при первом обращении к катаmcлогу `/mnt/`
```
[root@nfsc ~]# systemctl daemon-reload
[root@nfsc ~]# systemctl restart remote-fs.target
```

16. Заходим в директорию `/mnt/` и проверяем успешность монтирования
```
[root@nfsc ~]# cd /mnt
[root@nfsc mnt]# ls
upload
[root@nfsc mnt]# mount | grep mnt
systemd-1 on /mnt type autofs (rw,relatime,fd=46,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=45055)
192.168.56.10:/srv/share/ on /mnt type nfs (rw,relatime,vers=3,rsize=32768,wsize=32768,namlen=255,hard,proto=udp,timeo=11,retrans=3,sec=sys,mountaddr=192.168.56.10,mountvers=3,mountport=20048,mountproto=udp,local_lock=none,addr=192.168.56.10)
```

17. Проверка работоспособности: заходим на сервер, создаем в директории /srv/share/upload файл check_file
```
neva@Uneva:~$ vagrant ssh nfss
Last login: Wed May 24 07:18:54 2023 from 10.0.2.2
[vagrant@nfss ~]$ sudo -i
[root@nfss ~]# cd /srv/share/upload
[root@nfss upload]# touch check_file
```
Заходим на клиент, проверяем наличие файла  chek_file и создаём файл  client_file
```
[root@nfsc mnt]# cd /mnt/upload
[root@nfsc upload]# ls
check_file
[root@nfsc upload]# touch client_file
[root@nfsc upload]# ls
check_file  client_file
```
Перезгружаем сервер, подключаемся к нему, проверяем наличие файлов в каталоге `/srv/share/upload/` 
проверяем статус сервера NFS `systemctl status nfs
проверяем статус firewall
проверяем экспорты `exportfs -s`
проверяем работу RPC
```
[root@nfss upload]# reboot
Connection to 127.0.0.1 closed by remote host.
neva@Uneva:~$ vagrant ssh nfss
Last login: Wed May 24 08:58:02 2023 from 10.0.2.2
[vagrant@nfss ~]$ sudo -i
[root@nfss ~]# cd /srv/share/upload
[root@nfss upload]# ls -l
total 0
-rw-r--r--. 1 root      root      0 May 24 09:00 check_file
-rw-r--r--. 1 nfsnobody nfsnobody 0 May 24 09:04 client_file
[root@nfss upload]# systemctl status nfs
● nfs-server.service - NFS server and services
   Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; enabled; vendor preset: disabled)
  Drop-In: /run/systemd/generator/nfs-server.service.d
           └─order-with-mounts.conf
   Active: active (exited) since Wed 2023-05-24 09:12:55 UTC; 4min 5s ago
  Process: 813 ExecStartPost=/bin/sh -c if systemctl -q is-active gssproxy; then systemctl reload gssproxy ; fi (code=exited, status=0/SUCCESS)
  Process: 787 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
  Process: 784 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
 Main PID: 787 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/nfs-server.service

May 24 09:12:55 nfss systemd[1]: Starting NFS server and services...
May 24 09:12:55 nfss systemd[1]: Started NFS server and services.

[root@nfss upload]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2023-05-24 09:12:50 UTC; 4min 43s ago
     Docs: man:firewalld(1)
 Main PID: 401 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─401 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

May 24 09:12:48 nfss systemd[1]: Starting firewalld - dynamic firewall daemon...
May 24 09:12:50 nfss systemd[1]: Started firewalld - dynamic firewall daemon.
May 24 09:12:50 nfss firewalld[401]: WARNING: AllowZoneDrifting is enabled. This is considered an i...now.
Hint: Some lines were ellipsized, use -l to show in full.
[root@nfss upload]#
[root@nfss upload]# exportfs -s
/srv/share  192.168.56.11/32(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
[root@nfss upload]# showmount -a 192.168.56.10
All mount points on 192.168.56.10:
192.168.56.11:/srv/share
```

Перезагружаем клиент, подключаемся к нему, проверяем работу RPC,
проверяем статус монтирования,
наличие ранее созданных файлов,
создаём тестовый файл final_check,
```
[vagrant@nfsc mnt]$ showmount -a 192.168.56.10
All mount points on 192.168.56.10:
192.168.56.11:/srv/share
[vagrant@nfsc mnt]$ mount | grep mnt
systemd-1 on /mnt type autofs (rw,relatime,fd=33,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=11124)
192.168.56.10:/srv/share/ on /mnt type nfs (rw,relatime,vers=3,rsize=32768,wsize=32768,namlen=255,hard,proto=udp,timeo=11,retrans=3,sec=sys,mountaddr=192.168.56.10,mountvers=3,mountport=20048,mountproto=udp,local_lock=none,addr=192.168.56.10)
[vagrant@nfsc mnt]$ cd upload
[vagrant@nfsc upload]$ ls -l
total 0
-rw-r--r--. 1 root      root      0 May 24 09:00 check_file
-rw-r--r--. 1 nfsnobody nfsnobody 0 May 24 09:04 client_file
[vagrant@nfsc upload]$ touch final_check
[vagrant@nfsc upload]$ ls -l
total 0
-rw-r--r--. 1 root      root      0 May 24 09:00 check_file
-rw-r--r--. 1 nfsnobody nfsnobody 0 May 24 09:04 client_file
-rw-rw-r--. 1 vagrant   vagrant   0 May 24 09:29 final_check
```

18. Далее настраиваем автоматизацию через Vagrantfile и 2 скрипта для настройки сервера и клиента: nfss_script.sh, nfsc_script.sh и проверяем работоспособность.
Скрипты выложены в гите, измененный Vagrantfile в гите приложен под именем Vagrantfile1

19. Проверяем результаты автоматизации
Сервер

```
neva@Uneva:~$ vagrant ssh nfss
[vagrant@nfss ~]$ systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2023-05-24 10:07:18 UTC; 3min 35s ago
     Docs: man:firewalld(1)
 Main PID: 3189 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─3189 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid
[vagrant@nfss ~]$ ss -tnplu
Netid State      Recv-Q Send-Q                                                                       Local Address:Port                                                                                      Peer Address:Port
udp   UNCONN     0      0                                                                                        *:59044                                                                                                *:*
udp   UNCONN     0      0                                                                                127.0.0.1:968                                                                                                  *:*
udp   UNCONN     0      0                                                                                        *:970                                                                                                  *:*
udp   UNCONN     0      0                                                                                        *:2049                                                                                                 *:*
udp   UNCONN     0      0                                                                                        *:38443                                                                                                *:*
udp   UNCONN     0      0                                                                                127.0.0.1:323                                                                                                  *:*
udp   UNCONN     0      0                                                                                        *:68                                                                                                   *:*
udp   UNCONN     0      0                                                                                        *:20048                                                                                                *:*
udp   UNCONN     0      0                                                                                        *:111                                                                                                  *:*
udp   UNCONN     0      0                                                                                     [::]:970                                                                                               [::]:*
udp   UNCONN     0      0                                                                                     [::]:2049                                                                                              [::]:*
udp   UNCONN     0      0                                                                                     [::]:53819                                                                                             [::]:*
udp   UNCONN     0      0                                                                                    [::1]:323                                                                                               [::]:*
udp   UNCONN     0      0                                                                                     [::]:20048                                                                                             [::]:*
udp   UNCONN     0      0                                                                                     [::]:111                                                                                               [::]:*
udp   UNCONN     0      0                                                                                     [::]:51844                                                                                             [::]:*
tcp   LISTEN     0      64                                                                                       *:41148                                                                                                *:*
tcp   LISTEN     0      64                                                                                       *:2049                                                                                                 *:*
tcp   LISTEN     0      128                                                                                      *:54633                                                                                                *:*
tcp   LISTEN     0      128                                                                                      *:111                                                                                                  *:*
tcp   LISTEN     0      128                                                                                      *:20048                                                                                                *:*
tcp   LISTEN     0      128                                                                                      *:22                                                                                                   *:*
tcp   LISTEN     0      100                                                                              127.0.0.1:25                                                                                                   *:*
tcp   LISTEN     0      64                                                                                    [::]:38175                                                                                             [::]:*
tcp   LISTEN     0      64                                                                                    [::]:2049                                                                                              [::]:*
tcp   LISTEN     0      128                                                                                   [::]:45960                                                                                             [::]:*
tcp   LISTEN     0      128                                                                                   [::]:111                                                                                               [::]:*
tcp   LISTEN     0      128                                                                                   [::]:20048                                                                                             [::]:*
tcp   LISTEN     0      128                                                                                   [::]:22                                                                                                [::]:*
tcp   LISTEN     0      100                                                                                  [::1]:25                                                                                                [::]:*

[root@nfss ~]# exportfs -s
/srv/share  192.168.56.11/32(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
```

Проверяем на клиенте
```
neva@Uneva:~$ vagrant ssh nfsc
[vagrant@nfsc ~]$ systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2023-05-24 10:08:29 UTC; 16min ago
     Docs: man:firewalld(1)
 Main PID: 3193 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─3193 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid

[vagrant@nfsc ~]$ cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Thu Apr 30 22:04:55 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=1c419d6c-5064-4a2b-953c-05b2c67edb15 /                       xfs     defaults        0 0
/swapfile none swap defaults 0 0
#VAGRANT-BEGIN
# The contents below are automatically generated by Vagrant. Do not modify.
#VAGRANT-END
192.168.56.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0
[vagrant@nfsc ~]$ cd /mnt
[vagrant@nfsc mnt]$ ls
upload
[vagrant@nfsc mnt]$ cd upload
[vagrant@nfsc upload]$ touch file1
[vagrant@nfsc upload]$ ls
file  file1
[vagrant@nfsc upload]$






