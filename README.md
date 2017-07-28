# TCP_BBR_Modified

## How to compile tcp_bbr_modified algorithm on CentOS7?

- Install the latest version of Linux kernel, headers and devel tools.

```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install kernel-ml -y
yum -y remove kernel-headers
yum --enablerepo=elrepo-kernel install kernel-ml-headers -y
yum --enablerepo=elrepo-kernel install kernel-ml-devel -y
```

Then do`egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'`to see the sequence of alreadly installed kernels and choose the one you've installed just now.

```
grub2-set-default 0  //For instance, if you want choose the first one to boot by dedault then do 'grub2-set-default 0`
reboot
```

- Install GCC with version number grater than 4.9 via CentOS Software Collections

```
yum -y install centos-release-scl
yum -y install devtoolset-4-gcc*
scl enable devtoolset-4 bash
```
- Download source code of tcp_bbr_modified and generate the Makefile

```
https://github.com/GEM7/TCP_BBR_Modified.git
echo "obj-m:=tcp_bbr_Modified.o" > Makefile
```

- Compile it and initialization

```
make -s -C /lib/modules/$(uname -r)/build M=`pwd` modules CC=`which gcc`
cp -rf ./tcp_bbr_modified.ko /lib/modules/$(uname -r)/kernel/net/ipv4
depmod -a 
odprobe tcp_bbr_modified
```

- Set it as the default TCP congestion control algorithm

```
echo "net.core.default_qdisc = fq" >>/etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control = bbr_modified" >>/etc/sysctl.conf
```

- Check if bbr_modified works

```
lsmod | grrp bbr
```

if you see words including bbr_modified, then it works.
