# Notes


Linux系统之升级内核版本方法
 江湖有缘 发表于 2023/01/28 21:57:14  212  0  1
【摘要】 Linux系统之升级内核版本方法
一、检查本地系统环境
1.检查系统版本
[root@jeven ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
2.检查系统内核版本
[root@jeven ~]# uname -r 
3.10.0-957.el7.x86_64
二、小版本升级内核
1.列出yum仓库的内核包版本
确保yum仓库的正常，本次实践用的是阿里的镜像源。

[root@jeven yum.repos.d]# yum list kernel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * epel: mirrors.bfsu.edu.cn
Installed Packages
kernel.x86_64                                                              3.10.0-957.el7                                                                    @anaconda
Available Packages
kernel.x86_64                                                              3.10.0-1160.81.1.el7                                                              updates
2.升级内核
直接更新内核版本，此方法适用于更新内核补丁。

[root@jeven yum.repos.d]# yum update -y kernel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                                                                                           | 8.5 kB  00:00:00     
 * epel: mirrors.bfsu.edu.cn
base                                                                                                                                           | 3.6 kB  00:00:00     
docker-ce-stable                                                                                                                               | 3.5 kB  00:00:00     
epel                                                                                                                                           | 4.7 kB  00:00:00     
extras                                                                                                                                         | 2.9 kB  00:00:00     
updates                                                                                                                                        | 2.9 kB  00:00:00     
(1/3): epel/x86_64/updateinfo                                                                                                                  | 1.0 MB  00:00:00     
(2/3): epel/x86_64/primary_db                                                                                                                  | 7.0 MB  00:00:01     
(3/3): updates/7/x86_64/primary_db                                                                                                             |  19 MB  00:00:09     
Resolving Dependencies
--> Running transaction check
---> Package kernel.x86_64 0:3.10.0-1160.83.1.el7 will be installed
--> Processing Dependency: linux-firmware >= 20190429-72 for package: kernel-3.10.0-1160.83.1.el7.x86_64
--> Running transaction check
---> Package linux-firmware.noarch 0:20180911-69.git85c5d90.el7 will be updated
---> Package linux-firmware.noarch 0:20200421-80.git78c0348.el7_9 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================================
 Package                                 Arch                            Version                                               Repository                        Size
======================================================================================================================================================================
Installing:
 kernel                                  x86_64                          3.10.0-1160.83.1.el7                                  updates                           52 M
Updating for dependencies:
 linux-firmware                          noarch                          20200421-80.git78c0348.el7_9                          updates                           80 M

Transaction Summary
======================================================================================================================================================================
Install  1 Package
Upgrade             ( 1 Dependent package)

Total download size: 132 M
Downloading packages:
No Presto metadata available for updates
(1/2): kernel-3.10.0-1160.83.1.el7.x86_64.rpm                                                                                                  |  52 MB  00:00:31     
(2/2): linux-firmware-20200421-80.git78c0348.el7_9.noarch.rpm                                                                                  |  80 MB  00:00:48     
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                 2.7 MB/s | 132 MB  00:00:48     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : linux-firmware-20200421-80.git78c0348.el7_9.noarch                                                                                                 1/3 
  Installing : kernel-3.10.0-1160.83.1.el7.x86_64                                                                                                                 2/3 
  Cleanup    : linux-firmware-20180911-69.git85c5d90.el7.noarch                                                                                                   3/3 
  Verifying  : kernel-3.10.0-1160.83.1.el7.x86_64                                                                                                                 1/3 
  Verifying  : linux-firmware-20200421-80.git78c0348.el7_9.noarch                                                                                                 2/3 
  Verifying  : linux-firmware-20180911-69.git85c5d90.el7.noarch                                                                                                   3/3 

Installed:
  kernel.x86_64 0:3.10.0-1160.83.1.el7                                                                                                                                

Dependency Updated:
  linux-firmware.noarch 0:20200421-80.git78c0348.el7_9                                                                                                                

Complete!
3.重启并检查内核版本
重启服务器
reboot
检查内核版本
重启完成，发现内核版本已经升级为【3.10.0-1160.83.1.el7.x86_64】

[root@jeven ~]# uname -r 
3.10.0-1160.83.1.el7.x86_64
三、大版本升级内核
1.导入公钥
ELRepo是一个为Linux提供驱动程序和内核映像的存储库，这里的内核大版本升级方案就是采用ELRepo提供的内核通道。

[root@jeven ~]# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
[root@jeven ~]#
2.下载并安装elrepo仓库
ELRepo内核下载地址：https://elrepo.org/linux/kernel/el7/x86_64/RPMS/，如果服务器不能联网，可离线下载。

[root@jeven ~]# yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
Loaded plugins: fastestmirror, langpacks
elrepo-release-7.el7.elrepo.noarch.rpm                                                                                                         | 8.7 kB  00:00:00     
Examining /var/tmp/yum-root-RRC6Uo/elrepo-release-7.el7.elrepo.noarch.rpm: elrepo-release-7.0-6.el7.elrepo.noarch
Marking /var/tmp/yum-root-RRC6Uo/elrepo-release-7.el7.elrepo.noarch.rpm to be installed
Resolving Dependencies
--> Running transaction check
---> Package elrepo-release.noarch 0:7.0-6.el7.elrepo will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================================
 Package                             Arch                        Version                               Repository                                                Size
======================================================================================================================================================================
Installing:
 elrepo-release                      noarch                      7.0-6.el7.elrepo                      /elrepo-release-7.el7.elrepo.noarch                      5.0 k

Transaction Summary
======================================================================================================================================================================
Install  1 Package

Total size: 5.0 k
Installed size: 5.0 k
Is this ok [y/d/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : elrepo-release-7.0-6.el7.elrepo.noarch                                                                                                             1/1 
  Verifying  : elrepo-release-7.0-6.el7.elrepo.noarch                                                                                                             1/1 

Installed:
  elrepo-release.noarch 0:7.0-6.el7.elrepo                                                                                                                            

Complete!
3.载入elrepo-kernel元数据
[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * elrepo-kernel: mirrors.tuna.tsinghua.edu.cn
elrepo-kernel                                                                                                                                  | 3.0 kB  00:00:00     
elrepo-kernel/primary_db                                                                                                                       | 2.1 MB  00:00:00     
repo id                                                 repo name                                                                                               status
elrepo-kernel                                           ELRepo.org Community Enterprise Linux Kernel Repository - el7                                           34
repolist: 34
4.列出rpm包
列出elrepo-kernel的rpm包

[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel list kernel*
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * elrepo-kernel: mirrors.tuna.tsinghua.edu.cn
Installed Packages
kernel.x86_64                                                                      3.10.0-957.el7                                                        @anaconda    
kernel.x86_64                                                                      3.10.0-1160.83.1.el7                                                  @updates     
kernel-tools.x86_64                                                                3.10.0-957.el7                                                        @anaconda    
kernel-tools-libs.x86_64                                                           3.10.0-957.el7                                                        @anaconda    
Available Packages
kernel-lt.x86_64                                                                   5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-devel.x86_64                                                             5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-doc.noarch                                                               5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-headers.x86_64                                                           5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-tools.x86_64                                                             5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-tools-libs.x86_64                                                        5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-lt-tools-libs-devel.x86_64                                                  5.4.230-1.el7.elrepo                                                  elrepo-kernel
kernel-ml.x86_64                                                                   6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-devel.x86_64                                                             6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-doc.noarch                                                               6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-headers.x86_64                                                           6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-tools.x86_64                                                             6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-tools-libs.x86_64                                                        6.1.8-1.el7.elrepo                                                    elrepo-kernel
kernel-ml-tools-libs-devel.x86_64                                                  6.1.8-1.el7.elrepo                                                    elrepo-kerne

5.安装最新版本kernel
[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel install -y kernel-ml.x86_64
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * elrepo-kernel: mirrors.tuna.tsinghua.edu.cn
Resolving Dependencies
--> Running transaction check
---> Package kernel-ml.x86_64 0:6.1.8-1.el7.elrepo will be installed
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================================
 Package                              Arch                              Version                                        Repository                                Size
======================================================================================================================================================================
Installing:
 kernel-ml                            x86_64                            6.1.8-1.el7.elrepo                             elrepo-kernel                             60 M

Transaction Summary
======================================================================================================================================================================
Install  1 Package

Total download size: 60 M
Installed size: 284 M
Downloading packages:
kernel-ml-6.1.8-1.el7.elrepo.x86_64.rpm                                                                                                        |  60 MB  00:00:02     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : kernel-ml-6.1.8-1.el7.elrepo.x86_64                                                                                                                1/1 
  Verifying  : kernel-ml-6.1.8-1.el7.elrepo.x86_64                                                                                                                1/1 

Installed:
  kernel-ml.x86_64 0:6.1.8-1.el7.elrepo                                                                                                                               

Complete!
6.查看内核是否载入到grub2
查看系统上的所有可用内核

[root@jeven ~]# awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
0 : CentOS Linux (6.1.8-1.el7.elrepo.x86_64) 7 (Core)
1 : CentOS Linux (3.10.0-1160.83.1.el7.x86_64) 7 (Core)
2 : CentOS Linux (3.10.0-957.el7.x86_64) 7 (Core)
3 : CentOS Linux (0-rescue-626024fee7b34ca3acd67183d6f1ca99) 7 (Core)
7.设置新的内核为grub2的默认版本
通过 grub2-set-default命令设置默认启动版本

[root@jeven ~]# grub2-set-default 0
[root@jeven ~]# 

通过编辑 /etc/default/grub 文件设置默认启动版本
[root@jeven ~]# cat /etc/default/grub 
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=0
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
生成 grub 配置文件并重启
[root@jeven ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.1.8-1.el7.elrepo.x86_64
Found initrd image: /boot/initramfs-6.1.8-1.el7.elrepo.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1160.83.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.83.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-957.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-957.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-626024fee7b34ca3acd67183d6f1ca99
Found initrd image: /boot/initramfs-0-rescue-626024fee7b34ca3acd67183d6f1ca99.img
done

重启
reboot
8.检查重启后的系统内核版本
重启系统后，发现系统内核已经成功升级。

[root@jeven ~]# uname -r 
6.1.8-1.el7.elrepo.x86_64
