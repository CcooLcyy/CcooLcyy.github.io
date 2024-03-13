---
title: Arch-Linux
date: 2023-12-10 23:00:48
tags:
---

# 安装流程

首先要保证是在UEFI下进行安装，这一点通过grub引导项就可以看出来，或者是

```shell
cat /sys/firmware/efi/fw_platform_size
```

应该会显示`64`如果没有显示则可能并不是通过UEFI启动。

## 联网

如果接驳网线的话会自动链接网络，但是这只是在live中，安装的系统中如何联网按照后面的步骤。

### WiFi

live中带有iwctl交互式命令，可以进行连接WiFi

## 更新系统时钟

应该默认的是UTC时钟，在这里先确定时间是准确的，后面可以通过切换时区调整时间。

```shell
timedatectl
```

## 磁盘分区

使用`cfdisk`交互式分区，如果是空磁盘的话主要使用GPT分区表，具体GPT分区还是MBR分区在[Arch Linux Wiki](https://wiki.archlinux.org/title/Partitioning#Choosing_between_GPT_and_MBR)有详细说明。

### 格式化

接下来就是进行格式化，根据需要的文件系统进行格式化

```shell
mkfs.vfat [/path/to/efi_device]
# mkfs.ext4 [/path/to/normal_device]
mkfs.btrfs [/path/to/device]
mkswap
```

### 挂载

分区之后首先挂在根目录，再根目录下创建文件夹，接着将其他分区挂载到对应文件夹下

|分区|路径|
|-|-|
|/|/mnt|
|home|/mnt/home|
|boot|/mnt/boot|
|EFI|/mnt/boot/EFI|

分区完成之后就是挂在文件系统到`/mnt`目录下

## 安装

### 选择镜像

只要是在国内就使用国内的镜像需要修改文件夹

```shell
vim /etc/pacman.d/mirrorlist
```

```shell
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

### 安装！

```shell
pacstrap -K [/path/to/sys] [pkg_name]
```

`-K`：生成一个空的pacman密钥环

基础的安装只需要三个包即可，同样也需要一些其他的包

|层级|包名||||
|-|-|-|-|-|
|基础|base|linux-firmware|linux|base-devel|
|开机启动|grub|efibootmgr|
|微码|amd-ucode|intel-ucode|
|GPG证书|archlinux-keyring|
|必要工具|sudoe|zsh|zsh-completions|
|文档|man-db|man-pages|texinfo|
|联网|dhcpcd|networkmanager|
|编辑器|vim|

## 导入文件系统表fstab

```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

`-U`：使用UUID

`-L`：使用label


## chroot系统并配置

### 时间

切换时区到上海时区

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

将更新好的时间同步到硬件

```shell
hwclock --systohc
```

### 本地化

```shell
vim /etc/locale.gen # 取消en_US.UTF-8和zh_CN.UTF-8
```

运行`locale-gen`

```shell
vim /etc/locale.conf 

LANG=en_US.UTF-8
```

### 主机名以及配置root密码

```shell
echo '[my_host_name]' >> /etc/hostname
```

### root密码

很简单，但也很容易忘记，导致需要重新通过U盘引导，繁琐。

通过`passwd root`配置密码

### 构建开机启动项

```shell
grub-instal --target=x86_64-efi --efi-directory=[/path/to/efi] --bootloader-id=GRUB
```
生成配置文件
```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

# 图形化

作为日常使用需要又图形化界面，同时日常使用不能使用root用户，因此需要创建普通用户以及做其他能够日常使用的设置。

## 配置普通用户

```shell
useradd -m -G wheel -s /bin/bash daniel

passwd daniel
```

修改`sudoers`取消如下注释`%wheel ALL=(ALL) ALL`

## 开启32位库 中文社区仓库

```shell
vim /etc/pacman.conf

# 取消注释中两行
[multilib]

[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

## 安装KDE Plasma

```shell
pacman -S plasma-meta konsole dolphin
```

## 启动桌面

```shell
systemctl enable --now sddm
```

## 安装基础包

### 解决wine应用方块字的问题。

安装文泉驿系列开源字体可解决

```shell
sudo pacman -S wqy-zenhei wqy-microhei wqy-microhei-lite wqy-bitmapfont
```

### 安装谷歌开源字体

但是感觉谷歌开源字体不是很好看
```shell
pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
```

重新签名archlinuxcn GPG和yay。

```shell
pacman -S archlinuxcn-keyring yay
```

在安装过程中可能无法导入证书的情况，可以先将证书禁用，然后导入证书

```shell
SigLevel = Never
LocalFileSigLevel = Never
```

## 安装输入法

```shell
pacman -S fcitx5-im fcitx5-chinese-addons
```

```shell
sudo vim /etc/environment

GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

# Btrfs

在安装的时候需要安装`btrfs-progs`

## 单设备文件系统

## 创建btrfs文件系统

使用`mkfs.btrfs`

```shell
mkfs.btrfs -L [label] -n 32k [/path/to/device]
```

- 通过`-L`指定文件系统的标签

- 通过`-n`指定节点大小，默认使用16k，允许使用更大的元数据节点尺寸，但是必须是扇区尺寸的整数倍，最大64k。

### 节点尺寸的影响

更小的节点尺寸：会造成更多的碎片，但是也会升高B树从而降低锁竞争。

更大的节点尺寸：当更新元数据块时，会更好打包以及更少的碎片代价时昂贵的内存操作。