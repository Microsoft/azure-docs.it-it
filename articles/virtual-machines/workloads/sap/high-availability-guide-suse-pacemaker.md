---
title: Configurazione di Pacemaker su SLES in Azure Documenti Microsoft
description: Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/17/2020
ms.author: radeltch
ms.openlocfilehash: 9d3d0ddbd1282827f17cd82228fcf0f3fba3a60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471983"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Per configurare un cluster Pacemaker in Azure sono disponibili due opzioni. È possibile usare un agente di isolamento, che si occupa di riavviare un nodo bloccato tramite le API di Azure, oppure un dispositivo SBD.

Per il dispositivo SBD è necessaria almeno una macchina virtuale supplementare che possa fungere da server di destinazione iSCSI e fornire un dispositivo SBD. Tali server di destinazione iSCSI possono tuttavia essere condivisi con altri cluster Pacemaker. Il vantaggio dell'utilizzo di un dispositivo SBD è un tempo di failover più rapido e, se si utilizzano dispositivi SBD in locale, non richiede alcuna modifica sulla modalità di funzionamento del cluster pacemaker. È possibile usare fino a tre dispositivi SBD per un cluster Pacemaker per consentire a un dispositivo SBD di diventare non disponibile, ad esempio durante l'applicazione di patch del sistema operativo del server di destinazione iSCSI. Per usare più di un dispositivo SBD per Pacemaker, assicurarsi di distribuire più server di destinazione iSCSI e connettere un solo SBD da ogni server di destinazione iSCSI. È consigliabile usare uno o tre dispositivi SBD. Pacemaker non sarà in grado isolare automaticamente un nodo del cluster se si configurano solo due dispositivi SBD e uno di essi non è disponibile. Per porre un limite quando un server di destinazione iSCSI è inattivo, è necessario usare tre dispositivi SBD e pertanto tre server di destinazione iSCSI.

Se non si vuole investire in una macchina virtuale aggiuntiva, è anche possibile usare l'agente di Azure Fence.If you don't want to invest in one additional virtual machine, you can also use the Azure Fence agent. Lo svantaggio è che un failover può richiedere dai 10 ai 15 minuti se si verifica un errore durante l'arresto di una risorsa o se i nodi del cluster non riescono più a comunicare tra loro.

![Panoramica di Pacemaker su SLES](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Quando si pianificano e distribuiscono nodi del cluster e dispositivi SBD di Linux Pacemaker, per avere un'affidabilità completa della configurazione di tutto il cluster è fondamentale che il routing tra le macchine virtuali coinvolte e le macchine virtuali che ospitano i dispositivi SBD non a passi attraverso alcun dispositivo, ad esempio [appliance virtuali di rete](https://azure.microsoft.com/solutions/network-appliances/). In caso contrario, i problemi e gli eventi di manutenzione con appliance virtuali di rete possono avere un impatto negativo sulla stabilità e l'affidabilità della configurazione di tutto il cluster. Per evitare tali ostacoli, non definire regole di routing delle nVA o delle regole di [routing definito dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) che instradano il traffico tra nodi cluster e dispositivi SBD tramite nVA e dispositivi simili durante la pianificazione e la distribuzione di nodi cluster Linux Pacemaker e dispositivi SBD. 
>

## <a name="sbd-fencing"></a>Isolamento tramite SBD

Seguire questa procedura se si vuole usare un dispositivo SBD per l'isolamento.

### <a name="set-up-iscsi-target-servers"></a>Configurare un server di destinazione iSCSI

Prima di tutto è necessario creare le macchine virtuali per la destinazione iSCSI. I server di destinazione iSCSI possono essere condivisi con più cluster Pacemaker.

1. Distribuire una nuova macchina virtuale SLES 12 SP1 o versione successiva e connettersi alla macchina tramite ssh. Non è necessario che le macchine siano grandi. È sufficiente una macchina virtuale di dimensioni Standard_E2s_v3 o Standard_D2s_v3. Assicurarsi di usare Archiviazione Premium sul disco sistema operativo.

Eseguire i comandi seguenti in tutte le **macchine virtuali di destinazione iSCSI**.

1. Aggiornare SLES.

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Potrebbe essere necessario riavviare il sistema operativo dopo l'aggiornamento o l'aggiornamento del sistema operativo. 

1. Rimuovere i pacchetti.

   Per evitare un problema noto con targetcli e SLES 12 SP3, disinstallare i pacchetti seguenti. È possibile ignorare gli errori relativi ai pacchetti non trovati.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installare i pacchetti di destinazione iSCSI.

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Abilitare il servizio di destinazione iSCSI.

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Creare il dispositivo iSCSI nel server di destinazione iSCSI

Eseguire i comandi seguenti in tutte le **macchine virtuali di destinazione iSCSI** per creare i dischi iSCSI per i cluster usati dai sistemi SAP. Nell'esempio seguente vengono creati dispositivi SBD per più cluster. Viene illustrato come usare un solo server di destinazione iSCSI per più cluster. I dispositivi SBD vengono posizionati nel disco del sistema operativo. Assicurarsi di avere spazio sufficiente.

**`nfs`** viene utilizzato per identificare il cluster NFS, **ascsnw1** viene utilizzato per identificare il cluster ASCS di **NW1**, **dbnw1** viene utilizzato per identificare il cluster di database di **NW1**, **nfs-0** e **nfs-1** sono i nomi host dei nodi del cluster NFS, **nw1-xscs-0** e **nw1-xscs-1** sono i nomi host dei nodi del cluster **ASCS NW1** e **nw1-db-0** e **nw1-db-1** sono i nomi host dei nodi del cluster di database. Sostituirli con i nomi host dei nodi del cluster e l'ID di sicurezza del sistema SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

È possibile verificare se tutto è stato configurato correttamente con

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Configurare il dispositivo SBD

Connettersi al dispositivo iSCSI creato nell'ultimo passaggio del cluster.
Eseguire i comandi seguenti nei nodi del nuovo cluster che si intende creare.
Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A] ** Connettersi ai dispositivi iSCSI

   Abilitare prima i servizi iSCSI e SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1] ** Modificare il nome dell'iniziatore nel primo nodo

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modificare il contenuto del file in modo che corrisponda agli ACL usati durante la creazione del dispositivo iSCSI nel server di destinazione iSCSI, ad esempio per il server NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2] ** Modificare il nome dell'iniziatore nel secondo nodo

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Modificare il contenuto del file in modo che corrisponda agli ACL usati durante la creazione del dispositivo iSCSI nel server di destinazione iSCSI.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Riavviare il servizio iSCSI

   Riavviare ora il servizio iSCSI per applicare la modifica.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Connettersi ai dispositivi iSCSI. Nell'esempio seguente, 10.0.0.17 è l'indirizzo IP del server di destinazione iSCSI e 3260 è la porta predefinita. <b>iqn.2006 04.nfs.local:nfs</b> è uno dei nomi di destinazione elencati quando si esegue il primo comando seguente (iscsiadm-m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Assicurarsi che i dispositivi iSCSI siano disponibili e annotare il nome dei dispositivi (nell'esempio seguente /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   A questo punto, recuperare l'ID dei dispositivi iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Vengono elencati tre ID di dispositivo per ogni dispositivo SBD. È consigliabile usare l'ID che inizia con scsi-3. Nell'esempio precedente si tratta dell'ID seguente:

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Creare il dispositivo SBD

   Usare l'ID del dispositivo iSCSI per creare un nuovo dispositivo SBD nel primo nodo del cluster.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Adattare la configurazione di SBD

   Aprire il file di configurazione SBD.

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Modificare la proprietà del dispositivo SBD, abilitare l'integrazione di Pacemaker e modificare la modalità di avvio di SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Creare `softdog` il file di configurazioneCreate the configuration file

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Caricare ora il modulo.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Installazione del cluster

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]** Aggiorna SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Componente di installazione, necessario per le risorse cluster

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Installare il componente azure-lb, necessario per le risorse cluster

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Controllare la versione degli agenti di risorse del pacchetto e assicurarsi che siano soddisfatti i requisiti minimi di versione:Check the version of package resource-agents and make sure the minimum version requirements are met:  
   > - Per SLES 12 SP4/SP5, la versione deve essere almeno resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Per SLES 15/15 SP1, la versione deve essere almeno resource-agents-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Configurare il sistema operativo

   In alcuni casi, Pacemaker crea molti processi, esaurendo così il numero di processi consentito. In tal caso, un heartbeat tra i nodi del cluster potrebbe avere esito negativo e richiedere il failover delle risorse. È consigliabile aumentare il numero massimo di processi consentito impostando il parametro seguente.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Ridurre le dimensioni della cache dirty. Per altre informazioni, vedere [Prestazioni di scrittura ridotte sui server SLES 11 12 con RAM di grandi dimensioni](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configurare cloud-netconfig-azure per il cluster HA

   Modificare il file di configurazione per l'interfaccia di rete come illustrato di seguito per impedire al plug-in di rete cloud di rimuovere l'indirizzo IP virtuale (Pacemaker deve controllare l'assegnazione VIP). Per ulteriori informazioni, vedere [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Abilita l'accesso ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Abilita l'accesso ssh

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Abilita l'accesso ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Installare l'agente di isolamento
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Se si usa Suse Linux Enterprise Server per SAP 15, tenere presente che è necessario attivare un modulo aggiuntivo e installare componenti aggiuntivi, che è prerequisito per l'utilizzo di Azure Fence Agent. Per ulteriori informazioni sui moduli e le estensioni SUSE, vedere [Moduli ed estensioni spiegati](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Seguire le istruzioni per installare Azure Python SDK. 

   Le istruzioni seguenti su come installare Azure Python SDK sono applicabili solo per Suse Enterprise Server per SAP **15.**  

    - Se si utilizza Bring-Your-Own-Subscription, seguire queste istruzioni  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Se si utilizza l'abbonamento con pagamento in base al numero di utilizzi, seguire queste istruzioni  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti. Il vantaggio di usare /etc/hosts è che il cluster diventa indipendente dal DNS, che potrebbe essere un singolo punto di guasto.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Installare il cluster

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Aggiungere un nodo al cluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Modificare la password hacluster con la stessa password

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Regolare le impostazioni di corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Aggiungere il contenuto in grassetto seguente al file se i valori non sono presenti o sono diversi. Assicurarsi di modificare il token in modo da ottenere 30000 per consentire la manutenzione con mantenimento della memoria. Per altre informazioni, vedere [questo articolo per Linux][virtual-machines-linux-maintenance] o [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Riavviare quindi il servizio corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Creare il dispositivo STONITH dell'agente di isolamento di Azure

Il dispositivo STONITH usa un'entità servizio per l'autorizzazione in Microsoft Azure. Per creare un'entità servizio, seguire questa procedura.

1. Passare a <https://portal.azure.com>.
1. Aprire il pannello Azure Active Directory  
   Passare a Proprietà e annotare l'ID directory. Si tratta dell'**ID tenant**.
1. Fare clic su Registrazioni per l'app
1. Fare clic su Nuova registrazione
1. Immettere un Nome, selezionare "Solo account nella directory dell'organizzazione" 
2. Selezionare Tipo di applicazione "Web", immettere un\/URL di accesso (ad esempio http: /localhost) e fare clic su Aggiungi  
   L'URL di accesso non viene usato e può essere qualsiasi URL valido
1. Selezionare Certificati e segreti, quindi fare clic su Nuovo segreto client
1. Immettere una descrizione per una nuova chiave, selezionare "Non scade mai" e fare clic su Aggiungi
1. Annotare il valore. Viene utilizzato come **password** per l'entità servizio
1. Selezionare Panoramica. Annotare l'ID applicazione. Viene utilizzato come nome utente (**ID di accesso** nei passaggi seguenti) dell'entità servizio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1] ** Creare un ruolo personalizzato per l'agente di isolamento

L'entità servizio non dispone delle autorizzazioni per accedere alle risorse di Azure per impostazione predefinita. È necessario concedere all'entità servizio le autorizzazioni per avviare e arrestare (deallocare) tutte le macchine virtuali del cluster. Se il ruolo personalizzato non è già stato creato, è possibile farlo usando [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli).

Per il file di input usare il contenuto seguente. Tale contenuto deve essere adattato alle sottoscrizioni, ovvero è necessario sostituire c276fc76-9cd4-44c9-99a7-4fd71546436e ed e91d47c4-76f3-4271-a796-21b4ecfe3624 con gli ID delle sottoscrizioni. Se si ha una sola sottoscrizione, rimuovere la seconda voce in AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Assegnare il ruolo personalizzato all'entità servizio

Assegnare all'entità servizio il ruolo personalizzato "Linux Fence Agent Role" creato nel capitolo precedente. Non usare più il ruolo Proprietario!

1. Vai a[https://portal.azure.com](https://portal.azure.com)
1. Aprire il pannello Tutte le risorse
1. Selezionare la macchina virtuale del primo nodo del cluster.
1. Fare clic su Controllo di accesso (IAM)
1. Fare clic su Aggiungi assegnazione ruolo
1. Selezionare il ruolo "Linux Fence Agent Role".
1. Immettere il nome dell'applicazione creata in precedenza
1. Fare clic su Salva.

Ripetere la procedura precedente per il secondo nodo del cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Creare il dispositivo STONITH

Dopo aver modificato le autorizzazioni per le macchine virtuali, è possibile configurare i dispositivi STONITH nel cluster.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Configurazione di Pacemaker predefinita per SBD

1. **[1]** Abilitare l'utilizzo di un dispositivo STONITH e impostare il ritardo di isolamento

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Configurazione pacemaker per gli eventi pianificati di AzurePacemaker configuration for Azure scheduled events

Azure offre [eventi pianificati](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Gli eventi pianificati vengono forniti tramite il servizio meta-dati e consentono all'applicazione di prepararsi per eventi come l'arresto della macchina virtuale, la ridistribuzione della macchina virtuale e così via. Monitoraggio degli **[eventi azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** dell'agente di risorse per gli eventi pianificati di Azure.Resource agent azure-events monitors for scheduled Azure events. Se vengono rilevati eventi, l'agente tenterà di arrestare tutte le risorse sulla macchina virtuale interessata e di spostarle in un altro nodo del cluster. Per ottenere questo numero aggiuntivo di risorse Pacemaker, è necessario configurare le risorse Pacemaker aggiuntive. 

1. **[A]** Assicurarsi che il pacchetto per l'agente **azure-events** sia già installato e aggiornato. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Configurare le risorse in Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Dopo aver configurato le risorse Pacemaker per l'agente eventi di azure, quando si inserisce il cluster in modalità di manutenzione o in uscita, è possibile che vengano inviati messaggi di avviso come:After you configure the Pacemaker resources for azure-events agent, when you place the cluster in or out of maintenance mode, you may get warning messages like:  
     AVVISO: cib-bootstrap-options: attributo sconosciuto 'hostName_ <strong>nomehost'</strong>  
     AVVISO: cib-bootstrap-options: attributo sconosciuto 'azure-events_globalPullState'  
     AVVISO: cib-bootstrap-options: attributo sconosciuto 'hostName_ <strong>nomehost'</strong>  
   > Questi messaggi di avviso possono essere ignorati.

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server][sles-nfs-guide]
* [Disponibilità elevata per SAP NetWeaver su macchina virtuali di Azure in SUSE Linux Enterprise Server for SAP applications][sles-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha].
