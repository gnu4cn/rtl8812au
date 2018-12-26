# `0bda:0811 Realtek Semiconductor Corp.` USB无线网卡Linux驱动

针对 Ubuntu 18.04(LTS) 的安装，亲测可用，在此贡献出来。

1. `git clone git@github.com:gnu4cn/rtl8812au`

2. `cd rtl8812au`

3. `sudo ./dkms-install.sh`

4. `reboot`

# RTL8811AU无线网卡linux驱动安装笔记（Fedora、Ubuntu）

> 原文：[https://blog.csdn.net/zzzzllll2006/article/details/82024679](https://blog.csdn.net/zzzzllll2006/article/details/82024679)


最近公司重新布网络，采购了一批无线网卡都是是USB接口的，卡王（Card-king）KW-AC8012 600M双频增强型外置天线USB无线网卡（[https://item.jd.com/7216632.html](https://item.jd.com/7216632.html)），芯片型号是Realtek RTL8811AU的。这款无线网卡要是放在Windows环境下的话，即插即用，不用安装驱动。但是Linux就悲剧了，只能自己编译安装驱动。

USB网卡自带了一张光盘，光盘里有Linux下的驱动源码，还有安装脚本。但经过尝试，可能是因为编译器版本问题，无法编译出驱动文件，只能再想其他办法。

于是网上找了不少第三方的驱动，但无一例外，编译失败或者是无法驱动。最后找到了几篇比较有参考意义的文章，折腾了一下，重要可以用了。参考文章如下：

参考1：[https://blog.csdn.net/jerry_sc/article/details/76407673](https://blog.csdn.net/jerry_sc/article/details/76407673)


参考2：[https://dustymabe.com/2016/01/24/802.11ac-on-linux-with-netgear-a6100-rtl8811au-usb-adapter/](https://dustymabe.com/2016/01/24/802.11ac-on-linux-with-netgear-a6100-rtl8811au-usb-adapter/)


参考3：[https://github.com/paspro/rtl8812au](https://github.com/paspro/rtl8812au)

我的编译、安装步骤主要参考 [https://blog.csdn.net/jerry_sc/article/details/76407673](https://blog.csdn.net/jerry_sc/article/details/76407673)，并做部分修改。

1. 首先，升级系统，安装内核头文件

Ubuntu下命令行如下（我用的Elementary，基于Ubuntu，所以操作一样）：

```
$ sudo -i
# apt-get update
# apt-get upgrade
# apt-get install dkms linux-headers-$(uname -r)
```

Fedora下相应的命令是：

```
# dnf install -y dkms kernel-devel-$(uname -r)
# systemctl enable dkms
```

2. 去GitHub下载驱动源码。下载 [https://github.com/paspro/rtl8812au](https://github.com/paspro/rtl8812au)  （已失效）提供的源码 `v5.1.5` 版本，只有这个版本亲测可用，其他版本都有问题！！！你可以安装 `git` 命令，也可以在网页上下载，都行。虽然源码显示的是 `RTL8812AU` 型号无线网卡的驱动，但 `RTL8811AU` 也一样可以用。CSDN 上我也放了一份： [https://download.csdn.net/download/zzzzllll2006/10625455](https://download.csdn.net/download/zzzzllll2006/10625455)。


3. 编译安装驱动。下载下来的源码里有个文件 `dkms-install.sh`，本以为直接执行脚本，就完成了，可是却出现了多个奇怪的错误，仔细检查发现，原来是脚本作者在拷贝驱动源文件的时候写错了路径，修改一下就OK了。

```
cp -r ../${DRV_DIR} /usr/src/${DRV_NAME}-${DRV_VERSION}
```

这一句修改成下面的，这样进入源码目录，`sudo ./dkms-install.sh` 即可。

```
mkdir /usr/src/${DRV_NAME}-${DRV_VERSION}
cp -r . /usr/src/${DRV_NAME}-${DRV_VERSION}
```
 

经过测试，GitHub上的牛人给出的驱动兼容性挺好，Ubuntu、Elementary、Fedora 都可以用，还挺稳定的。
--------------------- 
作者：zzzzllll2006 
来源：CSDN 
原文：https://blog.csdn.net/zzzzllll2006/article/details/82024679 
版权声明：本文为博主原创文章，转载请附上博文链接！

# Realtek RTL8812AU Driver

## DKMS
This driver can be installed using [DKMS](http://linux.dell.com/dkms/). This is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the `dkms` package, which on Debian (based) systems is done like this:
```
sudo apt install dkms
```

## Installation of Driver
In order to install the driver open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-install.sh
```

## Removal of Driver
In order to remove the driver from your system open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-remove.sh
```

## Note
For Ubuntu 17.04 add the following lines
```
[device]
wifi.scan-rand-mac-address=no
```
at the end of file /etc/NetworkManager/NetworkManager.conf and restart NetworkManager with the command:
```
sudo service NetworkManager restart
```

