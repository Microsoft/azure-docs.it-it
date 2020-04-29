---
title: Configurare LVM in una macchina virtuale che esegue Linux
description: Informazioni su come configurare LVM su Linux in Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066802"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurare LVM in una macchina virtuale Linux in Azure
Questo documento illustra come configurare il gestore dei volumi logici (Logical Volume Manager, LVM) nella macchina virtuale di Azure. È possibile usare LVM nel disco del sistema operativo o in dischi dati in macchine virtuali di Azure, ma per impostazione predefinita LVM non è configurato nel disco del sistema operativo nella maggior parte delle immagini cloud. La procedura seguente è incentrata sulla configurazione di LVM per i dischi dati.

## <a name="linear-vs-striped-logical-volumes"></a>Volumi lineari e volumi con striping logici
La LVM può essere usata per combinare un numero di dischi fisici in un unico volume di archiviazione. Per impostazione predefinita la LVM crea generalmente volumi logici lineari, il che significa che l'archiviazione fisica è concatenata. In questo caso, le operazioni di lettura/scrittura sono in genere inviate solo a un singolo disco. Al contrario, è anche possibile creare volumi logici con striping in cui le operazioni di lettura e scrittura sono distribuite in più dischi contenuti nel gruppo di volumi (analogamente a RAID0). Per motivi di prestazioni, è probabile che sia necessario eseguire lo striping dei volumi logici in modo che le letture e le scritture usino tutti i dischi dati associati.

In questo documento viene descritto come combinare più dischi dati in un singolo gruppo di volumi e quindi creare un volume con striping logici. La procedura seguente è generalizzata per l'uso con la maggior parte delle distribuzioni. Nella maggior parte dei casi le utilità e i flussi di lavoro per la gestione di LVM in Azure non sono fondamentalmente diversi da altri ambienti. Come sempre, rivolgersi anche al fornitore Linux per la documentazione e le procedure consigliate per l'uso di LVM con una distribuzione specifica.

## <a name="attaching-data-disks"></a>Collegamento di dischi dati
In genere si preferisce iniziare con due o più dischi dati vuoti quando si usano le LVM. In base alle esigenze di I/O, è possibile scegliere di collegare dischi che sono archiviati nell'archiviazione Standard con un massimo di 500 IO/ps per ogni disco o nell'archiviazione Premium con un massimo di 5.000 IO/ps per ogni disco. In questo articolo non verrà illustrato in dettaglio come eseguire il provisioning e collegare dischi dati a una macchina virtuale Linux. Per istruzioni dettagliate su come collegare un disco dati vuoto a una macchina virtuale Linux in Azure, vedere l'articolo di Microsoft Azure relativo al [collegamento di dischi](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="install-the-lvm-utilities"></a>Installare le utilità della LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS e Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 e openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    In SLES11 è anche necessario modificare `/etc/sysconfig/lvm` e impostare `LVM_ACTIVATED_ON_DISCOVERED` su "enable":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurare la LVM
In questa guida si presuppone che siano stati connessi tre dischi dati, che vengono indicati come `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Questi percorsi potrebbero non corrispondere ai nomi di percorso dei dischi nella macchina virtuale. È possibile eseguire`sudo fdisk -l`o un comando simile per elencare i dischi disponibili.

1. Preparare i volumi fisici:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Creare un gruppo di volumi. In questo esempio il nome del gruppo di volumi è `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Creare i volumi logici. Con il comando seguente si crea un singolo volume logico denominato `data-lv01` da estendere nell'intero gruppo, ma si noti che è anche possibile creare più volumi logici nel gruppo di volumi.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formattare il volume logico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Con l'uso di SLES11 `-t ext3` al posto di ext4. SLES11 supporta l'accesso in sola lettura per i file system ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Aggiungere il nuovo file a /etc/fstab
> [!IMPORTANT]
> Se il file `/etc/fstab` non viene modificato in modo corretto, il sistema potrebbe non essere disponibile per l'avvio. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file `/etc/fstab` prima della modifica.

1. Creare il punto di montaggio desiderato per il nuovo file system, ad esempio:

    ```bash  
    sudo mkdir /data
    ```

2. Individuare il percorso del volume logico

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Aprire `/etc/fstab` in un editor di testo e aggiungere una voce per il nuovo file system, ad esempio:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Salvare e chiudere `/etc/fstab`.

4. Verificare che la voce `/etc/fstab` sia corretta:

    ```bash    
    sudo mount -a
    ```

    Se questo comando restituisce un messaggio di errore, verificare la sintassi nel file `/etc/fstab`.
   
    Eseguire quindi il comando `mount` per assicurarsi che il file system venga montato:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Facoltativo) Parametri di avvio alternativo in `/etc/fstab`
   
    Molte distribuzioni includono i parametri di montaggio `nobootwait` o `nofail`, che è possibile aggiungere al file `/etc/fstab`. Tali parametri consentono di ignorare gli errori durante il montaggio di uno specifico file system. Consentono pertanto di proseguire l'avvio del sistema Linux anche se non è possibile montare correttamente il file system RAID. Per altre informazioni su questi parametri, fare riferimento alla documentazione della distribuzione.
   
    Esempio (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Supporto per TRIM/UNMAP
Alcuni kernel di Linux supportano operazioni TRIM/UNMAP allo scopo di rimuovere i blocchi inutilizzati sul disco. Nel servizio di archiviazione standard, queste operazioni sono particolarmente utili per informare Azure che le pagine eliminate non sono più valide e possono essere rimosse. L'eliminazione delle pagine consente di risparmiare sui costi quando si creano file di grandi dimensioni per poi eliminarli.

Esistono due modi per abilitare la funzione TRIM in una VM Linux. Come di consueto, consultare la documentazione della distribuzione per stabilire l'approccio consigliato:

- Usare l'opzione di montaggio `discard` in `/etc/fstab`, ad esempio:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- In alcuni casi l'opzione `discard` può avere implicazioni sulle prestazioni. In alternativa, è possibile eseguire il comando `fstrim` manualmente dalla riga di comando oppure aggiungerlo a crontab per eseguirlo a intervalli regolari:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS e Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
