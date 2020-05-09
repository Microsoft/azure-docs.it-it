---
title: Ridimensionare un disco del sistema operativo con una partizione GPT | Microsoft Docs
description: Questo articolo fornisce istruzioni per il ridimensionamento di un disco del sistema operativo con partizione GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: f863233f0a34271841cc8e973f9aa3ca9416ceeb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858990"
---
# <a name="resize-an-os-disk-with-a-gpt-partition"></a>Ridimensionare un disco del sistema operativo con una partizione GPT

> [!NOTE]
> Questo scenario si applica solo al disco del sistema operativo con una partizione GPT.

Questo articolo descrive come aumentare le dimensioni del disco del sistema operativo con una partizione GPT in Linux.

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Determinare se il disco del sistema operativo ha una partizione MBR o GPT

Usare il comando **parted** per verificare se la partizione disco è stata creata con una partizione MBR (master boot record) o GPT (GUID Partition Table).

### <a name="mbr-partition"></a>Partizione MBR

Nell'output seguente la tabella delle **partizioni** Mostra un valore di **MSDOS**, che identifica una partizione **MBR** .

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partizione GPT

Nell'output seguente la tabella delle **partizioni** Mostra un valore di **GPT**, che identifica una partizione GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Se la macchina virtuale (VM) ha una partizione GPT sul disco del sistema operativo, aumentare le dimensioni del disco del sistema operativo.

## <a name="increase-the-size-of-the-os-disk"></a>Aumentare le dimensioni del disco del sistema operativo

Le istruzioni seguenti si applicano alle distribuzioni approvate da Linux.

> [!NOTE]
> Prima di continuare, creare una copia di backup della macchina virtuale o eseguire uno snapshot del disco del sistema operativo.

### <a name="ubuntu-16x-and-18x"></a>Ubuntu 16. x e 18. x

Per aumentare le dimensioni del disco del sistema operativo in Ubuntu 16. x e 18. x:

1. Arrestare la VM.
1. Aumentare le dimensioni del OSDisk dal portale.
1. Riavviare la macchina virtuale e quindi accedere alla macchina virtuale come utente **root** .
1. OSDisk visualizzerà ora un aumento delle dimensioni del file system.

Come illustrato nell'esempio seguente, il disco del sistema operativo è stato ridimensionato dal portale a 100 GB, perché il **/dev/sda1** file system montato in **/** ora visualizza 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse-12-sp4suse-sles-12-for-sap-suse-sles-15-and-suse-sles-15-for-sap"></a>SUSE 12 SP4, SUSE SLES 12 per SAP, SUSE SLES 15 e SUSE SLES 15 per SAP

Per aumentare le dimensioni del disco del sistema operativo in SUSE 12 SP4, SUSE SLES 15 e SUSE SLES 15 per SAP:

1. Arrestare la VM.
1. Aumentare le dimensioni del OSDisk dal portale.
1. Riavviare la VM.

Quando la macchina virtuale è stata riavviata, seguire questa procedura:

   1. Accedere alla macchina virtuale come **utente root** usando il comando seguente:
   
      `#sudo su`

   1. Usare il comando seguente per installare il pacchetto **gptfdisk** , che è necessario per aumentare le dimensioni del disco del sistema operativo:

      `#zypper install gptfdisk -y`

   1. Per visualizzare il settore più grande disponibile sul disco, eseguire il comando seguente:

      `#sgdisk -e /dev/sda`

   1. Ridimensionare la partizione senza eliminarla usando il comando seguente. Il comando **parted** dispone di un'opzione denominata **resizepart** per ridimensionare la partizione senza eliminarla. Il numero 4 dopo resizepart indica il ridimensionamento della quarta (quarta) partizione.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Eseguire il `#lsblk` comando per verificare se la partizione è stata aumentata.

      L'output seguente mostra che la partizione **/dev/sda4** è stata ridimensionata a 98,5 GB.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Identificare il tipo di file system in OSDisk usando il comando seguente:

      `blkid`

      Output di esempio:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. In base al tipo di file system, usare i comandi appropriati per ridimensionare l'file system.

      Per **XFS**, usare il comando seguente:

      ` #xfs_growfs /`

      Output di esempio:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Per **ext4**, usare il comando seguente:

      ```#resize2fs /dev/sda4```

   1. Verificare la dimensione file system aumentata per **DF-TH**, usando il comando seguente:

      `#df -Th`

      Output di esempio:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

Come illustrato nell'esempio precedente, è possibile vedere che le dimensioni file system per OSDisk sono state aumentate.

### <a name="rhel-7x-with-lvm"></a>RHEL 7. x con LVM

1. Arrestare la VM.
1. Aumentare le dimensioni del OSDisk dal portale.
1. Avviare la VM.

Quando la macchina virtuale è stata riavviata, seguire questa procedura:

   1. Accedere alla macchina virtuale come **utente root** usando il comando seguente:
   
      `#sudo su`

   1. Installare il pacchetto **gptfdisk** , che è necessario per aumentare le dimensioni del disco del sistema operativo.

      `#yum install gdisk -y`

   1. Per visualizzare il settore più grande disponibile sul disco, eseguire il comando seguente:

      `#sgdisk -e /dev/sda`

   1. Ridimensionare la partizione senza eliminarla usando il comando seguente. Il comando **parted** dispone di un'opzione denominata **resizepart** per ridimensionare la partizione senza eliminarla. Il numero 4 dopo resizepart indica il ridimensionamento della quarta (quarta) partizione.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Eseguire il comando seguente per verificare che la partizione sia stata aumentata:

      `#lsblk`

      L'output seguente mostra che la partizione **/dev/sda4** è stata ridimensionata a 99 GB.

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. Usare il comando seguente per ridimensionare il **volume fisico (PV)**:

      `#pvresize /dev/sda4`

      L'output seguente mostra che il PV è stato ridimensionato a 99,02 GB.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. Nell'esempio seguente, `/dev/mapper/rootvg-rootlv` viene ridimensionato da 2 GB a 12 GB (un aumento di 10 GB) usando il comando seguente, che consente anche di ridimensionare la file System:

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      Output di esempio:

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. Verificare se `/dev/mapper/rootvg-rootlv` ha aumentato le dimensioni del file System o meno usando il comando seguente:

      `#df -Th /`

      Output di esempio:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

      > [!NOTE]
      > Per usare la stessa procedura per ridimensionare qualsiasi altro volume logico, modificare il nome **LV** nel passaggio 7

## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare il disco](expand-disks.md)