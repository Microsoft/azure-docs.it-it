---
title: Creare e caricare un VHD di Red Hat Enterprise Linux per l'utilizzo in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure contenente un sistema operativo Linux RedHat.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: 4140f9f07a0fd653c8e0370d017cbae7effd0a07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084312"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparare una macchina virtuale basata su RedHat per Azure
In questo articolo verrà descritto come preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'utilizzo in Azure. Le versioni di RHEL trattate in questo articolo sono la 6.7+ e la 7.1+. Gli hypervisor per la preparazione illustrati in questo articolo sono Hyper-V, KVM (Kernel-based Virtual Machine) e VMware. Per altre informazioni sui requisiti di idoneità per partecipare al programma di accesso al cloud di Red Hat, vedere gli articoli relativi al [sito web di accesso al cloud di Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e all'[esecuzione di RHEL in Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Per informazioni su come automatizzare la creazione di immagini RHEL, vedere il [Generatore di immagini di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale basata su Red Hat dalla console di gestione di Hyper-V

### <a name="prerequisites"></a>Prerequisiti
In questa sezione si presuppone che si sia già ottenuto un file ISO dal sito Web Red Hat e che sia già stata installata un'immagine RHEL in un disco rigido virtuale (VHD). Per altri dettagli su come usare la console di gestione di Hyper-V per installare un'immagine del sistema operativo, vedere l'articolo su come [installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di RHEL**

* Azure non supporta il formato VHDX. Azure supporta solo dischi rigidi virtuali a dimensione fissa. È possibile usare la console di gestione di Hyper-V o il cmdlet convert-vhd per convertire il disco in formato VHD. Se si usa VirtualBox, durante la creazione del disco selezionare **Fixed size** (A dimensione fissa) anziché l'opzione predefinita di allocazione dinamica.
* Azure supporta le macchine virtuali Gen1 (BIOS boot) & Gen2 (UEFI Boot).
* La dimensione massima consentita per il disco rigido virtuale è 1.023 GB.
* LVM (Logical Volume Manager) è supportato e può essere usato nel disco del sistema operativo o nei dischi dati in macchine virtuali di Azure. Tuttavia, in genere è consigliabile usare partizioni standard sul disco del sistema operativo anziché LVM. Questa procedura consentirà di evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse eventualmente necessario collegare un disco del sistema operativo a un'altra macchina virtuale identica per la risoluzione dei problemi. Vedere anche la documentazione di [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per montare file system UDF (Universal Disk Format) è necessario il supporto del kernel. Al primo avvio in Azure, i supporti con formattazione UDF collegati al guest passano la configurazione di provisioning alla macchina virtuale Linux. L'agente Linux di Azure deve poter montare il file system UDF per leggerne la configurazione ed effettuare il provisioning della macchina virtuale.
* Non configurare una partizione di swapping sul disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file di scambio sul disco risorse temporaneo.  Altre informazioni su questo argomento sono disponibili nei passaggi riportati di seguito.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Altri dettagli sono disponibili nelle procedure seguenti. Per altre informazioni, vedere anche [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale RHEL 6 dalla console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. In RHEL 6, NetworkManager può interferire con l'agente Linux di Azure. Disinstallare il pacchetto eseguendo questo comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # sudo chkconfig network on

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
    
    È consigliabile anche rimuovere i parametri seguenti:
    
        rhgb quiet crashkernel=auto
    
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.  È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB. Questa configurazione potrebbe causare problemi in macchine virtuali di dimensioni inferiori.


1. Verificare che il server SSH (Secure Shell) sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare /etc/ssh/sshd_config per includere la riga seguente:

        ClientAliveInterval 180

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    L'installazione del pacchetto WALinuxAgent determina la rimozione dei pacchetti NetworkManager e NetworkManager-gnome, se non sono già stati rimossi nel passaggio 3.

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

        # sudo subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Fare clic su **azione**  >  **Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = sì NM_CONTROLLED = Sì

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # sudo systemctl enable network

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Questa configurazione disattiva anche le nuove convenzioni di denominazione di RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:
   
        rhgb quiet crashkernel=auto
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

        ClientAliveInterval 180

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Fare clic su **azione**  >  **Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparare una macchina virtuale basata su Red Hat da KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparare una macchina virtuale RHEL 6 da KVM

1. Scaricare l'immagine KVM di RHEL 6 dal sito Web Red Hat.

1. Impostare una password radice.

    Generare una password crittografata e copiare l'output del comando:

        # openssl passwd -1 changeme

    Impostare una password radice con guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Modificare il secondo campo dell'utente radice da "!!" con la password crittografata.

1. Creare una macchina virtuale in KVM dall'immagine qcow2. Impostare il tipo di disco su **qcow2** e il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Avviare quindi la macchina virtuale e accedere come utente ROOT.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti causano problemi quando si clona una macchina virtuale in Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # chkconfig network on

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa configurazione, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
    
    È consigliabile anche rimuovere i parametri seguenti:
    
        rhgb quiet crashkernel=auto
    
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.


1. Aggiungere i moduli Hyper-V a initramfs:  

    Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ricompilare initramfs:

        # dracut -f -v

1. Disinstallare cloud-init:

        # yum remove cloud-init

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

        # chkconfig sshd on

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare i parametri seguenti in **/etc/waagent.conf** in modo appropriato:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

        # subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Arrestare la macchina virtuale in KVM.

1. Convertire l'immagine qcow2 nel formato VHD.

> [!NOTE]
> Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparare una macchina virtuale RHEL 7 da KVM

1. Scaricare l'immagine KVM di RHEL 7 dal sito Web di Red Hat. Questa procedura usa come esempio RHEL 7.

1. Impostare una password radice.

    Generare una password crittografata e copiare l'output del comando:

        # openssl passwd -1 changeme

    Impostare una password radice con guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Modificare il secondo campo dell'utente radice da "!!" con la password crittografata.

1. Creare una macchina virtuale in KVM dall'immagine qcow2. Impostare il tipo di disco su **qcow2** e il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Avviare quindi la macchina virtuale e accedere come utente ROOT.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = sì NM_CONTROLLED = Sì

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # sudo systemctl enable network

1. Registrare la propria sottoscrizione Red Hat per abilitare l’installazione dei pacchetti dall’archivio RHEL eseguendo il seguente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa configurazione, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Questo comando, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Il comando disattiva anche le nuove convenzioni di denominazione di RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:
   
        rhgb quiet crashkernel=auto
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Aggiungere i moduli Hyper-V in initramfs.

    Modificare `/etc/dracut.conf` e aggiungere il contenuto:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ricompilare initramfs:

        # dracut -f -v

1. Disinstallare cloud-init:

        # yum remove cloud-init

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

        # systemctl enable sshd

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # yum install WALinuxAgent

    Abilitare il servizio waagent:

        # systemctl enable waagent.service

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

        # subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Arrestare la macchina virtuale in KVM.

1. Convertire l'immagine qcow2 nel formato VHD.

> [!NOTE]
> Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparare una macchina virtuale basata su Red Hat da VMware
### <a name="prerequisites"></a>Prerequisiti
In questa sezione si presuppone che una macchina virtuale RHEL sia già stata installata in VMware. Per informazioni dettagliate su come installare un sistema operativo in VMware, vedere la [guida all'installazione del sistema operativo guest VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Quando si installa il sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che è spesso l'impostazione predefinita per numerose installazioni. Questo consentirà di evitare conflitti di nome LVM con la macchina virtuale clonata, in particolare se fosse eventualmente necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
* Non configurare una partizione di swapping sul disco del sistema operativo. È possibile configurare l'agente Linux per poter creare un file di scambio sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi riportati di seguito.
* Quando si crea il disco rigido virtuale, selezionare **Store virtual disk as a single file**(Archivia disco virtuale come singolo file).

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparare una macchina virtuale RHEL 6 da VMware
1. In RHEL 6, NetworkManager può interferire con l'agente Linux di Azure. Disinstallare il pacchetto eseguendo questo comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti causano problemi quando si clona una macchina virtuale in Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # sudo chkconfig network on

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È consigliabile anche rimuovere i parametri seguenti:
   
        rhgb quiet crashkernel=auto
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Aggiungere i moduli Hyper-V a initramfs:

    Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ricompilare initramfs:

        # dracut -f -v

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ClientAliveInterval 180

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

        # sudo subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Arrestare la macchina virtuale e convertire il file VMDK in un file con estensione vhd.

> [!NOTE]
> Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparare una macchina virtuale RHEL 7 da VMware
1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = sì NM_CONTROLLED = Sì

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

        # sudo systemctl enable network

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Questa configurazione, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva anche le nuove convenzioni di denominazione RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:
   
        rhgb quiet crashkernel=auto
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Aggiungere i moduli Hyper-V a initramfs.

    Modificare `/etc/dracut.conf`e aggiungere il contenuto:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ricompilare initramfs:

        # dracut -f -v

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questa è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

        ClientAliveInterval 180

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

        # sudo subscription-manager unregister

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Arrestare la macchina virtuale e convertire il file VMDK nel formato VHD.

> [!NOTE]
> Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparare una macchina virtuale basata su Red Hat da un'immagine ISO usando un file kickstart automatico
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparare una macchina virtuale RHEL 7 da un file kickstart

1.  Creare e salvare un file kickstart con il contenuto seguente. Per informazioni dettagliate sull'installazione di kickstart, vedere la [guida all'installazione di kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT = Yes NM_CONTROLLED = Yes EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Posizionare il file kickstart in un percorso in cui sia accessibile per il sistema di installazione.

1. Creare una nuova macchina virtuale nella console di gestione di Hyper-V. Nella pagina **Connessione disco rigido virtuale** selezionare **Connetti un disco rigido virtuale successivamente** e completare la creazione guidata della macchina virtuale.

1. Aprire le impostazioni della macchina virtuale:

    a.  Collegare un nuovo disco rigido virtuale alla macchina virtuale. Accertarsi di selezionare **Formato VHD** e **A dimensione fissa**.

    b.  Collegare l'ISO di installazione all'unità DVD.

    c.  Impostare il BIOS per l'avvio da CD.

1. Avviare la macchina virtuale. Quando viene visualizzata la guida all'installazione, premere **Tab** per configurare le opzioni di avvio.

1. Inserire `inst.ks=<the location of the kickstart file>` alla fine di opzioni di avvio e premere **Invio**.

1. Attendere la fine dell'installazione. Al termine, la macchina virtuale verrà arrestata automaticamente. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="known-issues"></a>Problemi noti
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Non è possibile includere il driver Hyper-V nel disco RAM iniziale quando si usa un hypervisor non Hyper-V

In alcuni casi, i programmi di installazione di Linux potrebbero non includere i driver per Hyper-V nel disco RAM iniziale (initrd o initramfs), a meno che Linux non rilevi di essere in esecuzione in un ambiente Hyper-V.

Quando si usa un sistema di virtualizzazione diverso (ovvero VirtualBox, Xen e così via) per preparare l'immagine Linux, potrebbe essere necessario ricompilare initrd per assicurarsi che almeno i moduli kernel hv_vmbus e hv_storvsc siano disponibili sul disco RAM iniziale. Questo è un problema noto, almeno nei sistemi basati sulla distribuzione upstream di Red Hat.

Per risolvere questo problema, aggiungere i moduli Hyper-V a initramfs e ricompilarlo:

Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Ricompilare initramfs:

        # dracut -f -v

Per altri dettagli, vedere le informazioni sulla [ricompilazione di initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passaggi successivi
* È ora possibile usare il disco rigido virtuale Red Hat Enterprise Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
* Per altre informazioni sugli hypervisor certificati per l'esecuzione di Red Hat Enterprise Linux, visitare [il sito Web di Red Hat](https://access.redhat.com/certified-hypervisors).
* Per altre informazioni sull'uso di immagini RHEL BYOS pronte per la produzione, vedere la pagina della documentazione relativa a [BYOS](../workloads/redhat/byos.md).
