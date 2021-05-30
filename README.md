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

查看驱动安装情况：

```shell
lshw -c video
```

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



好消息是更新内核之后，apt-get没问题了，提示执行

apt --fix-broken install

这一步应该是当初断网安装内核导致的。

```shell
apt-get update
apt --fix-broken install
apt-get full-upgrade
```

upgrade到最后跳回了Recovery Menu

```
lshw -c video
```

发现两个显卡都是UNCLAIM状态，说明驱动都掉了



## AMD 核显驱动支持

编辑 grub 启动目录

```
sudo vi /etc/default/grub
```

在 `GRUB_CMDLINE_LINUX_DEFAULT=`一行双引号内的末尾，添加 `amdgpu.exp_hw_support=1`，修改后如下

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amdgpu.exp_hw_support=1"
```

保存退出，并更新 grub

```
sudo update-grub
```

重启

https://carlchen.icu/ubuntu20_dual_GPU/



终于成功的进入了GUI

！！！



总结：思路是先升级内核到5.12.8，能apt-get之后更新软件，再编辑 grub加入amdgpu.exp_hw_support=1，使GUI支持amd核显。

进入GUI之后再装Nvidia的驱动以及其他的。



## 常用软件安装

```
sudo apt-get install vim htop
```

Chrome和VS Code下载deb文件后：

```shell
sudo -s
apt-get install ./name.deb
```

VLC等用的使snap商店，安装snap，可使用以下命令或图形界面的store通过鼠标点击操作：

```
sudo snap install vlc //安装vlc snap
```

## OpenVPN

https://community.openvpn.net/openvpn/wiki/OpenvpnSoftwareRepos

We maintain several OpenVPN (OSS) software repositories. To setup the repositories you need to change to the root user. Typically this is done using *sudo*:

```
$ sudo -s
```

Then import the public GPG key that is used to sign the packages:

```
$ wget -O - https://swupdate.openvpn.net/repos/repo-public.gpg|apt-key add -
```

Next you need to create a sources.list fragment (as root) so that apt can find the new OpenVPN packages. One way to do it is this:

```
$ echo "deb http://build.openvpn.net/debian/openvpn/stable focal main" > /etc/apt/sources.list.d/openvpn-aptrepo.list
```

On Debian/Ubuntu use

```
$ apt-get update && apt-get install openvpn
```

运行即使用

```shell
sudo openvpn xxx.conf 
```

但是好像DNS有点问题。。。



还是用OpenVPN3试试吧：

https://community.openvpn.net/openvpn/wiki/OpenVPN3Linux

First ensure that your apt supports the https transport:

```
   # apt install apt-transport-https
```

Install the OpenVPN repository key used by the OpenVPN 3 Linux packages

```
   # wget https://swupdate.openvpn.net/repos/openvpn-repo-pkg-key.pub
   # apt-key add openvpn-repo-pkg-key.pub
```

Then you need to install the proper repository. Replace $DISTRO with the release name depending on your Debian/Ubuntu distribution.

```
   # wget -O /etc/apt/sources.list.d/openvpn3.list https://swupdate.openvpn.net/community/openvpn3/repos/openvpn3-$DISTRO.list
   # apt update
```

And finally the openvpn3 package can be installed

```
   # apt install openvpn3
```



Import the configuration file:

```
$ openvpn3 config-import --config my-vpn-config.conf
```

Start a new VPN session

```
$ openvpn3 session-start --config my-vpn-config.conf
```

Listing established sessions

```
   $ openvpn3 sessions-list
```

这下连上了，但是为啥翻不了墙，bzd

还是断掉吧：

```shell
openvpn3 session-manage --disconnect --config skyworks-campus-ipv4-ubuntu.conf
```



## Github使用

https://cli.github.com/manual/

https://github.com/cli/cli

https://docs.github.com/en/github/getting-started-with-github/quickstart/set-up-git





## Python 3

系统自带python3，安一下pip3

```
sudo apt install python3-pip
```

