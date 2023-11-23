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

