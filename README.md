# Notes


Linux系统之升级内核版本方法（Centos）
 
一、检查本地系统环境
1.检查系统版本
[root@jeven ~]# cat /etc/os-release 

2.检查系统内核版本
[root@jeven ~]# uname -r 
3.10.0-957.el7.x86_64
二、小版本升级内核
1.列出yum仓库的内核包版本
确保yum仓库的正常，本次实践用的是阿里的镜像源。
[root@jeven yum.repos.d]# yum list kernel
2.升级内核
直接更新内核版本，此方法适用于更新内核补丁。

[root@jeven yum.repos.d]# yum update -y kernel

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

3.载入elrepo-kernel元数据
[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel repolist

4.列出rpm包
列出elrepo-kernel的rpm包

[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel list kernel*

5.安装最新版本kernel
[root@jeven ~]# yum --disablerepo=\* --enablerepo=elrepo-kernel install -y kernel-ml.x86_64
6.查看内核是否载入到grub2
查看系统上的所有可用内核

[root@jeven ~]# awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
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
重启
reboot
8.检查重启后的系统内核版本
重启系统后，发现系统内核已经成功升级。

[root@jeven ~]# uname -r 
6.1.8-1.el7.elrepo.x86_64
