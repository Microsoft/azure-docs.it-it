---
title: Creazione e caricamento di un VHD SUSE Linux in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo SUSE Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: cf50ee847bd1542a3e024cb88cf7bbc8bc283f91
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83643422"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparare una macchina virtuale SLES o openSUSE per Azure


In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Linux SUSE od openSUSE in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>Note di installazione di SLES/openSUSE
* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse.  Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Usare SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) consente di creare e gestire facilmente le immagini SLES e openSUSE per Azure e Hyper-V. Questo è l'approccio consigliato per personalizzare le proprie immagini SLES e openSUSE.

In alternativa alla creazione di un disco rigido virtuale, SUSE pubblica anche immagini BYOS (portare la propria sottoscrizione) per SLES in [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparare SUSE Linux Enterprise Server 11 SP4
1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.
3. Registrare il sistema SUSE Linux Enterprise per scaricare gli aggiornamenti e installare i pacchetti.
4. Aggiornare il sistema con tutte le patch più recenti:
   
        # sudo zypper update
5. Installare l'agente Linux di Azure dal repository SLES (SLE11-Public-Cloud-Module):
   
        # sudo zypper install python-azure-agent
6. Controllare se l'agente waagent è impostato su "on" in chkconfig e, in caso contrario, abilitarlo per l'avvio automatico:
   
        # sudo chkconfig waagent on
7. Controllare se il servizio waagent è in esecuzione e, in caso contrario, avviarlo: 
   
        # sudo service waagent start
8. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
9. Verificare che /boot/grub/menu.lst e /etc/fstab facciano riferimento al disco tramite il relativo UUID (by-uuid) anziché l'ID disco (by-id). 
   
    Ottenere l'UUID disco
   
        # ls /dev/disk/by-uuid/
   
    Se si usa /dev/disk/by-id/, aggiornare sia /boot/grub/menu.lst sia /etc/fstab con il valore by-uuid corretto
   
    Prima della modifica
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Dopo la modifica
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:
    
     DHCLIENT_SET_HOSTNAME="no"
12. In "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
14. Non creare l'area di swap sul disco del sistema operativo.
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTA: da impostare laddove richiesto.
15. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

---
## <a name="prepare-opensuse-131"></a>Preparare openSUSE 13.1+
1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.
3. Nella shell eseguire il comando '`zypper lr`'. Se questo comando restituisce un output simile al seguente, i repository vengono configurati come previsto e non sono necessarie modifiche. Si noti che i numeri di versione possono variare:
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se il comando restituisce un messaggio simile a "Nessun archivio definito..." usare i comandi seguenti per aggiungere gli archivi:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Sarà quindi possibile verificare che i repository siano stati aggiunti eseguendo nuovamente il comando '`zypper lr`' . Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aggiornare il kernel all'ultima versione disponibile:
   
        # sudo zypper up kernel-default
   
    Oppure per aggiornare il sistema con tutte le patch più recenti:
   
        # sudo zypper update
5. Installare l'agente Linux di Azure.
   
        # sudo zypper install WALinuxAgent
6. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Rimuovere inoltre i messaggi seguenti dalla riga di avvio del kernel, se presenti:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Importante:** In "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
10. Non creare l'area di swap sul disco del sistema operativo.
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTA: da impostare laddove richiesto.
11. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Verificare che l'agente Linux di Azure venga eseguito all'avvio:
    
        # sudo systemctl enable waagent.service
13. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il disco rigido virtuale SUSE Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
