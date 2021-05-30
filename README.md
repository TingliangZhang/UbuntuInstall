# UbuntuInstall
Ubuntu安装



## ROS安装

http://wiki.ros.org/noetic/Installation/Ubuntu

注意改成清华源

## 安装Windows + Ubuntu 20.04 双系统

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

使用apt-get update会导致自动重启，估计是WiFi驱动不支持导致的

## AMD Radeon Graphics显卡驱动安装

启动后，从Grub界面进入普通Ubuntu会卡死在开机Logo界面，显卡驱动问题

使用GPU-Z可以看到内部代号为Cezanne-C4 2100MHz，类似于

## RTX 3070驱动和Cuda安装

使用GPU-Z可以看到内部代号为GA104-A1 1560MHz
