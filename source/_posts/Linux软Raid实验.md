---
title: Linux软Raid实验
date: 2023-01-16 17:02:45
tags:
---

# 问题

为了实现能够动态的扩充硬盘并且能够充分利用带宽，将硬盘使用Linux自带的mdadm组成软Raid。为日后不断扩充的硬盘积累经验。

# Raid 5

Raid 5最少应该使用三块硬盘才能组成，能够使用总硬盘数减1的总共容量，使用一块硬盘作为冗余，其他的硬盘会并联起来，提高硬盘的速度。当剩下的硬盘中如果容量不相同会按照最小的容量设置每块盘的容量。

例如：有三块硬盘，分别是5G 6G 7G，这三块盘中最小的是5G，因此组成Raid之后相当于使用三块5G的硬盘组建了Raid，总容量也就是（3-1）* 5G。

# 硬盘选择

使用mdadm不仅可以使用物理硬盘，同时可以使用逻辑卷，由于没有阵列卡，不知道阵列卡是否能够使用逻辑卷。

我们可以将一块硬盘分为三个逻辑卷将这三个逻辑卷组成Raid 5，但是这样就没有Raid 5的优势而且还会损失一点容量。不过这样的好处是我们可以动态的添加硬盘。

例如：我们一开始由于经费问题无法购置很多硬盘，我们可以先买一个18T的硬盘，将其分为三个逻辑卷然后组成Raid 5，等后续能够再购入的时候我们将新硬盘直接连接，重新规划一下就可以使用两块硬盘的容量（每个硬盘分为两个逻辑卷，用四个逻辑卷组成阵列）。直到能够每块盘单独充当一个卷的时候就可以充分发挥阵列的优点。

实验会按照上述的描述来模拟。

# 环境介绍

1. 虚拟机环境

![vm环境](../image/Linux%E8%BD%AFRaid%E5%AE%9E%E9%AA%8C/vm%E7%8E%AF%E5%A2%83.png)

2. 系统环境：
    kernel: 5.14.0-70.13.1.el9_0.x86_64
    Linux: RockyLinux 9.0
    mdadm: mdadm - v4.2 - 2021-12-30 - 6

# 实验过程

## 硬盘分区

对于只有一块硬盘的情况我们可以通过将硬盘分为三个逻辑卷组成我们可以使用任何顺手的工具将硬盘进行分区。

```sh
cfdisk /dev/sdb
```

这是一个图形化分区工具，我们一开始可以一块硬盘分为三块组成Raid 5。

![分区](../image/Linux%E8%BD%AFRaid%E5%AE%9E%E9%AA%8C/%E5%88%86%E5%8C%BA.png)

## 创建Raid 5

```sh
mdadm -C /dev/md5 -ayes -l5 -n3 /dev/sdb[1,2,3]

# -C 创建的设备名
# -l5 创建的等级
# -n3 阵列中实际使用的设备
# 后跟使用哪些设备创建
```

创建好阵列之后我们查看阵列信息

```sh
mdadm -D /dev/md5
```

我们需要将阵列信息放到`/etc/mdadm.conf`中保存，用来统一管理。

```sh
echo DEVICE /dev/sdb[1,2,3] > /etc/mdadm.conf
mdadm -Ds /dev/md5 >> /etc/mdadm.conf
```

![信息](../image/Linux%E8%BD%AFRaid%E5%AE%9E%E9%AA%8C/Raid_5%E4%BF%A1%E6%81%AF.png)

我们可以看到第三块硬盘正在重建镜像，现在就可以正常使用了，但是直到镜像重建完成之前，阵列都没有处在保护之中。

## 添加新硬盘

我们这里使用的Raid 5是由三块硬盘组成的，在购入新硬盘的时候并不会将阵列的活动硬盘数量增加，在这里我们涉及到将硬盘的手动下线以及扩充硬盘。为了我们能够更好的划分硬盘容量我们将三块活动硬盘扩充为四块。

我们先将另外两块硬盘分区并且添加到阵列中，因为Raid 5在某个时间段只能有一块硬盘损坏，所以硬盘要一块一块下线，下线之后当阵列中有空闲硬盘的时候会自动重建，这个过程需要重建阵列，因此需要一定时间。

```sh
mdadm -a /dev/sdb[1,2]  # 将硬盘添加到阵列中
mdadm /dev/md5 -f /dev/sda1 # 将sda1下线
```

需要注意的是我们更改了组成阵列的硬盘之后要更改`/etc/mdadm.conf`中的设备列表，如果不修改在重新开机之后md5会改变，这时如果将阵列自动挂载了Linux开机会出现错误。

![下线硬盘](../image/Linux%E8%BD%AFRaid%E5%AE%9E%E9%AA%8C/%E4%B8%8B%E7%BA%BF%E7%A1%AC%E7%9B%98.png)

下线硬盘之后会在阵列中将硬盘标记为错误盘，我们可以将硬盘移除重新将他们格式化。需要注意的是我们可以将硬盘下线到只有两块，虽然这个时候硬盘没有冗余，但是可以正常运行。

最后我们处理好之后会得到一个使用3块硬盘的阵列和一个空闲硬盘，由于我们现在使用的是逻辑卷，因此如果出现问题至少是两个逻辑卷，因此现在的情况并不具备任何冗余性，此时我们可以将空闲的硬盘上线将阵列中的硬盘数量变成4块。

```sh
mdadm -G -l5 -n4 /dev/md5 
```

不管是保持盘数不变增加容量还是增加硬盘增加容量都需要手动更改。

```sh
mdadm -G -z max /dev/md5  # 修改容量到最大支持的容量
mdadm -G -z 1024 /dev/md5 # 修改容量到指定大小单位KB
# 注意无法指定单位
```

![扩展硬盘](../image/Linux%E8%BD%AFRaid%E5%AE%9E%E9%AA%8C/%E6%89%A9%E5%B1%95%E7%A1%AC%E7%9B%98.png)

## 文件系统扩展

```sh
resize2fs /dev/md5
df -TH # 查看文件系统容量
```

后续根据命令提示输入。

至此情况复现结束。

# 删除硬盘

```sh
mdadm --misc --zero-superblock /dev/to/delete
```