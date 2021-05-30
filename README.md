# UbuntuInstall
新笔记本（枪神5P）的Ubuntu安装

ROG枪神5Plus 17.3英寸300Hz高色域液金导热游戏本笔记本电脑(超频版8核锐龙R9 5900HX 32G 1TSSD RTX3070)

## ROS安装

http://wiki.ros.org/noetic/Installation/Ubuntu

注意改成清华源



## 安装Windows + Ubuntu 20.04 双系统

F2进入BIOS，调整Boot顺序，F10保存

https://www.zybuluo.com/yanbo01haomiao/note/1704990

`efi`：引导分区，分区类型为逻辑分区，大小200M，分区格式ext4;

`挂载点/`：安装系统和软件的主分区，分区类型为主分区，大小100G（可根据实际情况增加），分区格式ext4;

https://blog.csdn.net/tjuyanming/article/details/79267984

因为是AMD核显+Nvidia独显，会有问题

https://www.cnblogs.com/xia-Autumn/p/6294042.html

acpi_osi=linux xforcevesa

安装完之后在GRUB界面禁用英伟达独显，按e进入编辑模式，添加

acpi_osi=linux nomodeset

https://www.dell.com/support/kbdoc/zh-hk/000123893

https://carlchen.icu/ubuntu20_dual_GPU/

https://amdgpu-install.readthedocs.io/en/latest/

https://www.amd.com/en/support/graphics/amd-radeon-2nd-generation-vega/amd-radeon-2nd-generation-vega/amd-radeon-vii

https://www.reddit.com/r/linuxquestions/comments/lca33g/5900hx_igpu_driver/

http://ridgecrop.co.uk/index.htm?guiformat.htm



## AX200驱动安装

使用apt-get update会导致自动重启，不知道为啥，联网能连上



## AMD Radeon Graphics显卡驱动安装

启动后，从Grub界面进入普通Ubuntu会卡死在开机Logo界面，显卡驱动问题

使用GPU-Z可以看到内部代号为Cezanne-C4 2100MHz



注意到安装完系统初次启动的时候有一个nouveau错误

查看驱动安装情况：lshw -c video

https://blog.csdn.net/weixin_53242716/article/details/110948529

https://askubuntu.com/questions/1244376/cant-get-internal-and-external-monitor-working-simultaneously-with-20-04-on-lap

https://blog.csdn.net/catscanner/article/details/108897993



## RTX 3070驱动和Cuda安装

使用GPU-Z可以看到内部代号为GA104-A1 1560MHz



## 更新内核版本

以上所有问题都能通过更新内核解决。

首先下载合适的内核，可以离线安装：

https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.12.8/

根据电脑64位处理器 选择 AMD64

这里里面还分了 `generic` 和 `lowlatency`

所以这里面的 `*_all.deb` 是必选，其他都是2选1

推荐选 `generic` ，那么最终下载的就是下面这4个

```
linux-headers-5.12.8-051208-generic_5.12.8-051208.202105281232_amd64.deb
linux-headers-5.12.8-051208_5.12.8-051208.202105281232_all.deb
linux-image-unsigned-5.12.8-051208-generic_5.12.8-051208.202105281232_amd64.deb
linux-modules-5.12.8-051208-generic_5.12.8-051208.202105281232_amd64.deb
```

然后新建一个文件夹，把这4个文件放进去，拷到FAT32的小U盘（小于32G）里面，挂载并拷贝

```shell
fdisk -l
mkdir ~/u
mkfs.vfat -F 32 /dev/sda1 #需要格式化的话用这个
mount /dev/sda1 ~/u
cd /mnt
ls
```

执行安装命令

```sh
sudo dpkg -i *.deb
```

装完重启，然后再试试内核版本有没有变

```
uname -a
```

返回这样就正确了

```
Linux ubuntu 5.10.9-051009-generic
```

安完报错：

initramfs unpacking failed: Decoding failed

以及安装时命令行报错

[w: possible missing firmware /lib/firmware/rtl_nic/rtl8125a-3.fw for module r8169](https://askubuntu.com/questions/1287896/w-possible-missing-firmware-lib-firmware-rtl-nic-rtl8125a-3-fw-for-module-r816)



https://forums.linuxmint.com/viewtopic.php?t=323152

`/etc/initramfs-tools/initramfs.conf` Change `COMPRESS=lz4` to `COMPRESS=gzip`

Save the changes then:

```shell
sudo update-initramfs -u -k all
```

and reboot.



然而这个好像不是关键，依然卡在这个地方

/dev/nvme0n1p3: clean, xx/xx files, xx/xx blocks
