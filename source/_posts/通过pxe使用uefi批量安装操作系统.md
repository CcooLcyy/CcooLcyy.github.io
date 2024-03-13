---
title: 通过pxe使用uefi批量安装操作系统
date: 2023-02-03 17:10:32
tags:
---

# 配置说明

拥有系统安装镜像的被称为服务器，需要安装系统的称为客户机。网络中有一台服务器和一台客户机，服务器需要搭建DHCP服务以便客户机能够在没有系统的情况下获得IP，由于客户机没有操作系统，我们需要先在服务器上搭建一个tftp服务将必要启动镜像传输过去。之后再建立一个稳定的http服务向客户机传输完整镜像。

# 配置dhcp服务器

需要注意的是在进行实验的时候是使用VMware虚拟机进行实验的，由于需要网络链接，我们就选择NAT网卡，但是需要把NAT网卡的DHCP关闭，避免产生冲突，使用服务器配置的dhcp服务。

通过`/etc/dhcp/dhcpd.conf`文件配置dhcp。

```conf
allow booting;
allow bootp;
allow unknow-clients;

subnet 192.168.10.10 netmask 255.255.255.0 {
    # 分配范围
    range 192.168.10.100 192.168.10.200;
    # DNS服务器地址
    option domain-name-server 192.168.10.10;
    # 默认租约
    default-lease-time 21600;
    # 最大租约
    max-lease-time 43200;
    # 服务器地址
    next-server 192.168.10.10;
    查找文件
    filename "uefi/BOOTX64.EFI";

}
```

## 启动服务并将服务添加到开机启动

```sh
systemctl start dhcpd && systemctl enable dhcpd
```

由于我们需要持久化，因此以后的所有服务如果没有特殊说明均需要重启服务并且添加开机启动。

这样可以保证当有新的设备连接到网络中可以自动为其分配IP。

# 传输vmlinuz和initrd.img

我们先将这两个镜像从服务器传输到客户机，需要搭建一个tftp服务。应当使用xinetd来管理tftp服务。（需要注意的是，某些系统并没有这个程序，需要自己下载。比如我在操作的时候使用rocky Linux 9就没有这个程序，但是rocky Linux 8却有。我通过rpm包安装的）安装tftp和xinetd之后修改xinetd配置文件使其能够管理tftp服务。（当然也可以使用systemctl来管理，但是推荐使用xinetd）

在`/etc/xinetd.d/`下新建一个名为`tftp`的文件。修改为以下内容。

```conf
service tftp
{
    socket_type     = dgram
    protocol        = udp
    wait            = yes
    user            = root
    server          = /usr/sbin/in.tftpd
    server_args     = -s /var/lib/tftpboot
    disable         = no                    # 需要注意这里设置为no来管理
    per_source      = 11
    cps             = 100 2
    flags           = IPv4
}
```

由于xinetd这个服务是自动启动的因此我们需要重启一下这个服务。

到这一步我们已经跑起来tftp服务，我们需要将`vmlinuz initrd.img`这两个文件复制到`/var/lib/tftpboot`下，这两个文件可以在安装镜像中找到。

![启动镜像](../image/%E9%80%9A%E8%BF%87pxe%E4%BD%BF%E7%94%A8uefi%E6%89%B9%E9%87%8F%E5%AE%89%E8%A3%85%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E5%90%AF%E5%8A%A8%E9%95%9C%E5%83%8F.png)

# 配置http服务传输完整镜像

到了这个时候裸机在接入网络开机之后就可以找到我们的服务器，并且通过服务器上的tftp服务接受两个镜像，此时我们的客户机已经具备了能够建立可靠连接的能力，我们就应该通过http服务（其他服务比如vsftp均可，但是如果客户机要安装的是Windows需要配置Windows支持的服务）传输完整镜像。

## 安装http服务

只要启动http服务并且将完整镜像复制到`/var/www/html`下就可以，此时我们可以在能够ping通的情况下打开网页测试。

# 使用kickstart应答文件自动安装

到这里我们就可以实现从网络获取安装镜像，但是如果需要实现全无人值守安装需要使用kickstart应答文件，在安装过程中的所有设置都可以在这个文件中进行设置

这个文件可以配置安装前和安装后执行的脚本，通过配置安装后脚本（%post）可以设置安装系统之后的操作，包括更换安装源或者安装其他程序。

# 后续更新

经过多次实验之后发现安装系统的时候并不需要整张光盘镜像，并且我再大多数情况下都是用的是最小化安装，因此我将镜像替换为最小化安装镜像并且将其中的包文件进行替换，体积减小300M（主要是取决于最小化安装包和全量安装包的差距）简化后的文件树如下

![文件树](../image/%E9%80%9A%E8%BF%87pxe%E4%BD%BF%E7%94%A8uefi%E6%89%B9%E9%87%8F%E5%AE%89%E8%A3%85%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%96%87%E4%BB%B6%E6%A0%91.png)

在BaseOS下包含了最小化安装的软件包，其实只要包含Packages和repodata两个文件夹的内容就可以，这里其实就是在图形化安装的时候选择安装源的那个地方。（这里我用rocky linux 9的安装光盘安装9.1也能顺利安装）