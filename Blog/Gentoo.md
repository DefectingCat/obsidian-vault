## 磁盘与分区

根据手册中描述的对应磁盘类型的设备名称，可以更方便的找到对应的设备。

|Type of device|Default device handle|Editorial notes and considerations|
|---|---|---|
|IDE, SATA, SAS, SCSI, or USB flash|/dev/sda|Found on hardware from roughly 2007 until the present, this device handle is perhaps the most commonly used in Linux. These types of devices can be connected via the [SATA bus](https://en.wikipedia.org/wiki/Serial_ATA "wikipedia:Serial ATA"), [SCSI](https://en.wikipedia.org/wiki/SCSI "wikipedia:SCSI"), [USB](https://en.wikipedia.org/wiki/USB "wikipedia:USB") bus as block storage. As example, the first partition on the first SATA device is called /dev/sda1.|
|NVM Express (NVMe)|/dev/nvme0n1|The latest in solid state technology, [NVMe](https://en.wikipedia.org/wiki/NVM_Express "wikipedia:NVM Express") drives are connected to the PCI Express bus and have the fastest transfer block speeds on the market. Systems from around 2014 and newer may have support for NVMe hardware. The first partition on the first NVMe device is called /dev/nvme0n1p1.|
|MMC, eMMC, and SD|/dev/mmcblk0|[embedded MMC](https://en.wikipedia.org/wiki/MultiMediaCard#eMMC "wikipedia:MultiMediaCard") devices, SD cards, and other types of memory cards can be useful for data storage. That said, many systems may not permit booting from these types of devices. It is suggested to **not** use these devices for active Linux installations; rather consider using them to transfer files, which is their typical design intention. Alternatively this storage type could be useful for short-term file backups or snapshots.|

磁盘的分区是一个老生常谈的问题了，分区的主要痛点不是在于给哪个区分多大的空间，而是要注意对应当前设备的启动方式选择好合适的分区表以及分区格式。毕竟当所有东西都编译完成之后发现分区表错了才是最痛苦的。

常见的分区表选择有：

- GPT（GUID Partition Table）：GPT 是可以为传统 BIOS 启动做分区表的，但是就无法与 Windows 做多个系统了。
- MBR（Master boot record）：传统的分区表，支持 BIOS，但对最大硬盘只能支持到 2 TiB。

EFI 的配置会稍微多一点，BIOS 启动则会简单一点。SWAP 则看个人喜好。

GPT for UEFI 的常见分区：

|Device path (sysfs)|Mount point|File system|DPS UUID (PARTUUID)|Description|
|---|---|---|---|---|
|/dev/sda1|/efi|vfat|c12a7328-f81f-11d2-ba4b-00a0c93ec93b|EFI system partition (ESP) details.|
|/dev/sda2|N/A. Swap is not mounted to the filesystem like a device file.|swap|0657fd6d-a4ab-43c4-84e5-0933c84b4f4f|Swap partition details.|
|/dev/sda3|/|xfs|4f68bce3-e8cd-4db1-96e7-fbcaf984b709|Root partition details.|

MBR for BIOS 的常见分区

|Device path (sysfs)|Mount point|File system|DPS UUID (PARTUUID)|Description|
|---|---|---|---|---|
|/dev/sda1|/boot|xfs|N/A|MBR DOS / legacy BIOS boot partition details.|
|/dev/sda2|N/A. Swap is not mounted to the filesystem like a device file.|swap|0657fd6d-a4ab-43c4-84e5-0933c84b4f4f|Swap partition details.|
|/dev/sda3|/|xfs|4f68bce3-e8cd-4db1-96e7-fbcaf984b709|Root partition details.|

## 安装前的其他准备

在分区完成，并挂载到 `/mnt/gentoo` 后，可以先做点准备工作。例如启动 `ssh` 实例，方便后续的一些复制粘贴操作。

```bash
rc-service start sshd
```

### 时间

安装时的系统时间非常重要，C 的某些编译环节会需要正确的系统时间。如果在安装时没有设置好时间，则会导致进入系统后某些文件的创建时间不正确，可能会遇到例如：

```
ninja: error: manifest 'build.ninja' still dirty after 100 tries
```

## Kernel

Gentoo 为我们提供了三种内核的选择：

- Distribution Kernels：与其他多数发行版类似，Distribution Kernel 是已经配置过的，直接编译即可。除此之外还能够选择已经编译好的 `sys-kernel/gentoo-kernel-bin`。
- Genkernel：Gentoo 提供的一种配置内核的方式，能够提供比完全手动配置更方便的选择。
- Manual configuration：完全的手动配置。

### Manual configuration

## Hyprland

作为新时代的系统窗口协议，Wayland 是良好的 X11 替代品。

> Wayland is a replacement for the X11 window system protocol and architecture with the aim to be easier to develop, extend, and maintain.

不同于 Sway 的目标是代替在 X11 上的 i3，Hyprland 则完全是全新的体验。得益于 GPU 的渲染，使得其能够拥有流畅的动画。
## 为其他机器编译内核

参考这个帖子：[Compiling kernel from another pc can I use it to my pc?](https://forums.gentoo.org/viewtopic-t-1056998-start-0.html#top)。在为其他硬件编译内核只需要选择好目标所需要的驱动，并且别一不小心安装到当前机器上就行了。

配置完后，编译的步骤还是和以前一样：

```bash
sudo make -j16 -l16
```

不同的是，`modules_install` 可以通过指定环境变量来将所有的 modules 安装到指定位置，方便待会拷贝到指定的机器上。

```bash
sudo make modules_install INSTALL_MOD_PATH=/home/xfy/gentoo-mini
```

同理，整个内核也是可以通过环境变量来指定安装的位置。

```bash
sudo make install INSTALL_PATH=/home/xfy/gentoo-mini/
```

之后整个目录看起来就应该类似于

```
.
|-- lib
|-- config-6.1.57-gentoo-gentoo-xfy
|-- System.map-6.1.57-gentoo-gentoo-xfy
`-- vmlinuz-6.1.57-gentoo-gentoo-xfy
```

拷贝到目标机器之后，只需要删除旧的 modules（留着也可以），并将内核复制到 `/boot`。再更新下启动配置即可。

```bash
sudo rm -rf /lib/modules/gentoo.x.x
sudo cp -aR lib/modules/6.1.57-gentoo-gentoo-xfy/ /lib/modules/
sudo cp config-6.1.57-gentoo-gentoo-xfy /boot/
sudo cp System.map-6.1.57-gentoo-gentoo-xfy /boot/
sudo cp vmlinuz-6.1.57-gentoo-gentoo-xfy /boot/
```

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```