---
title: Disponibilità elevata per NFS in macchine virtuali di Azure in SLES | Microsoft Docs
description: Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 993baa521530ffa6a702f8324a1691850687c366
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668691"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare un server NFS a disponibilità elevata che può essere usato per archiviare i dati condivisi di un sistema SAP a disponibilità elevata.
Questa guida descrive come configurare un server NFS a disponibilità elevata usato da due sistemi SAP, NW1 e NW2. I nomi delle risorse, ad esempio delle macchine virtuali e delle reti virtuali, nell'esempio presuppongono che sia stato usato il [modello di file server SAP][template-file-server] con il prefisso di risorsa **prod**.


> [!NOTE]
> Questo articolo contiene riferimenti ai termini *slave* e *Master*, termini che Microsoft non usa più. Quando i termini vengono rimossi dal software, verranno rimossi da questo articolo.

Leggere prima di tutto le note e i documenti seguenti relativi a SAP

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure

* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2205917] contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [1944799] contiene linee guida per SAP HANA per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Microsoft Azure per SAP in Linux (questo articolo)][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [Guide alle procedure consigliate per SUSE Linux Enterprise High Availability Extension 12 SP3][sles-hae-guides]
  * Archiviazione NFS a disponibilità elevata con DRBD e Pacemaker
* [Guide alle procedure consigliate per SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp]
* [Note sulla versione di SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Panoramica

Per ottenere la disponibilità elevata, SAP NetWeaver richiede un server NFS. Il server NFS viene configurato in un cluster separato e può essere usato da più sistemi SAP.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Il server NFS usa un nome host virtuale dedicato e indirizzi IP virtuali per ogni sistema SAP che usa questo server NFS. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del bilanciamento del carico.        

* Configurazione front-end
  * Indirizzo IP 10.0.0.4 per NW1
  * Indirizzo IP 10.0.0.5 per NW2
* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster NFS
* Porta probe
  * Porta 61000 per NW1
  * Porta 61001 per NW2
* Regole di bilanciamento del carico (se si usa il servizio di bilanciamento del carico Basic)
  * TCP 2049 per NW1
  * UDP 2049 per NW1
  * TCP 2049 per NW2
  * UDP 2049 per NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Configurare un server NFS a disponibilità elevata

È possibile usare un modello di Azure da GitHub per distribuire tutte le risorse di Azure necessarie, tra cui le macchine virtuali, il set di disponibilità e il bilanciamento del carico, oppure è possibile distribuire le risorse manualmente.

### <a name="deploy-linux-via-azure-template"></a>Distribuire Linux tramite un modello di Azure

Azure Marketplace contiene un'immagine per SUSE Linux Enterprise Server for SAP Applications 12 che è possibile usare per distribuire nuove macchine virtuali.
È possibile usare uno dei modelli di avvio rapido di GitHub per distribuire tutte le risorse necessarie. Il modello distribuisce le macchine virtuali, il servizio di bilanciamento del carico, il set di disponibilità e così via. Per distribuire il modello, attenersi alla procedura seguente:

1. Aprire il [modello di file server di SAP][template-file-server] nel portale di Azure   
1. Immettere i parametri seguenti
   1. Prefisso della risorsa  
      Immettere il prefisso che si vuole usare. Il valore viene usato come prefisso per le risorse distribuite.
   2. Numero sistema SAP  
      Immettere il numero di sistemi SAP che useranno il file server. Verrà distribuita la quantità richiesta di configurazioni front-end, le regole di bilanciamento del carico, le porte Probe, i dischi e così via.
   3. Tipo di sistema operativo  
      Selezionare una delle distribuzioni Linux. Per questo esempio, selezionare SLES 12
   4. Nome utente e password amministratore  
      Verrà creato un nuovo utente con cui è possibile accedere alla macchina
   5. Subnet ID  
      Se si vuole distribuire la macchina virtuale in una rete virtuale esistente in cui è stata definita la subnet a cui assegnare la macchina virtuale, specificare l'ID di tale subnet. L'ID si presenta in genere come **&lt; ID &gt; sottoscrizione**/subscriptions//resourceGroups/nome **&lt; gruppo &gt; di risorse****&lt; &gt;** /Providers/Microsoft.Network/virtualNetworks/nome **&lt; rete &gt; virtuale**/Subnets/nome subnet

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuire Linux manualmente tramite il portale di Azure

Prima di tutto è necessario creare le macchine virtuali per il cluster NFS. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un gruppo di risorse
1. Creare una rete virtuale
1. Creare un set di disponibilità  
   Impostare il numero massimo di domini di aggiornamento
1. Creare la macchina virtuale 1 Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3 BYOS SLES For SAP Applications 12 SP3 (BYOS)  
   Selezionare il set di disponibilità creato in precedenza  
1. Creare la macchina virtuale 2 Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine BYOS SLES4SAP 12 SP3  
   SLES For SAP Applications 12 SP3 (BYOS)  
   Selezionare il set di disponibilità creato in precedenza  
1. Aggiungere un disco dati per ogni sistema SAP a entrambe le macchine virtuali.
1. Creare una Load Balancer (interna). Si consiglia di usare [Load Balancer Standard](../../../load-balancer/load-balancer-overview.md).  
   1. Seguire queste istruzioni per creare un servizio di bilanciamento del carico standard:
      1. Creare gli indirizzi IP front-end
         1. Indirizzo IP 10.0.0.4 per NW1
            1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
            1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **nw1-frontend**
            1. Impostare Assegnazione su Statico e immettere l'indirizzo IP, ad esempio **10.0.0.4**
            1. Scegliere OK
         1. Indirizzo IP 10.0.0.5 per NW2
            * Ripetere i passaggi precedenti per NW2
      1. Creare i pool back-end
         1. Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster NFS
            1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
            1. Immettere il nome del nuovo pool back-end (ad esempio **, NW-back-end**)
            1. Seleziona rete virtuale
            1. Fare clic su Aggiungi una macchina virtuale
            1. Selezionare le macchine virtuali del cluster NFS e i relativi indirizzi IP.
            1. Fare clic su Aggiungi.
      1. Creare i probe di integrità
         1. Porta 61000 per NW1
            1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
            1. Immettere il nome del nuovo probe di integrità, ad esempio **nw1-hp**
            1. Selezionare TCP come protocollo, la porta 610 **00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
            1. Scegliere OK
         1. Porta 61001 per NW2
            * Ripetere i passaggi precedenti per creare un probe di integrità per NW2
      1. Regole di bilanciamento del carico
         1. Aprire il servizio di bilanciamento del carico, selezionare regole di bilanciamento del carico e fare clic su Aggiungi.
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **NW1-lb**
         1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza, ad esempio **NW1-frontend**. **NW-backend** e **NW1-HP**)
         1. Selezionare **Porte a disponibilità elevata**.
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
         * Ripetere i passaggi precedenti per creare una regola di bilanciamento del carico per NW2
   1. In alternativa, se lo scenario richiede il servizio di bilanciamento del carico di base, seguire queste istruzioni:
      1. Creare gli indirizzi IP front-end
         1. Indirizzo IP 10.0.0.4 per NW1
            1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
            1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **nw1-frontend**
            1. Impostare Assegnazione su Statico e immettere l'indirizzo IP, ad esempio **10.0.0.4**
            1. Scegliere OK
         1. Indirizzo IP 10.0.0.5 per NW2
            * Ripetere i passaggi precedenti per NW2
      1. Creare i pool back-end
         1. Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster NFS
            1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
            1. Immettere il nome del nuovo pool back-end (ad esempio **, NW-back-end**)
            1. Fare clic su Aggiungi una macchina virtuale
            1. Selezionare il set di disponibilità creato in precedenza
            1. Selezionare le macchine virtuali del cluster NFS
            1. Fare clic su OK.
      1. Creare i probe di integrità
         1. Porta 61000 per NW1
            1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
            1. Immettere il nome del nuovo probe di integrità, ad esempio **nw1-hp**
            1. Selezionare TCP come protocollo, la porta 610 **00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
            1. Scegliere OK
         1. Porta 61001 per NW2
            * Ripetere i passaggi precedenti per creare un probe di integrità per NW2
      1. Regole di bilanciamento del carico
         1. TCP 2049 per NW1
            1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
            1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **nw1-lb-2049**
            1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza, ad esempio **nw1-frontend**
            1. Mantenere il protocollo **TCP** e immettere la porta **2049**
            1. Aumentare il timeout di inattività a 30 minuti
            1. **Assicurarsi di abilitare l'indirizzo IP mobile**
            1. Fare clic su OK.
         1. UDP 2049 per NW1
            * Ripetere i passaggi precedenti per la porta 2049 e UDP per NW1
         1. TCP 2049 per NW2
            * Ripetere i passaggi precedenti per la porta 2049 e TCP per NW2
         1. UDP 2049 per NW2
            * Ripetere i passaggi precedenti per la porta 2049 e UDP per NW2

> [!IMPORTANT]
> L'IP mobile non è supportato in una configurazione IP secondaria di NIC negli scenari di bilanciamento del carico. Per informazioni dettagliate, vedere limitazioni del servizio di [bilanciamento del carico di Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se è necessario un indirizzo IP aggiuntivo per la macchina virtuale, distribuire una seconda scheda di interfaccia di rete.  

> [!Note]
> Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure) per creare un cluster Pacemaker di base per questo server NFS.

### <a name="configure-nfs-server"></a>Configurare il server NFS

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Abilitare il server NFS

   Creare la voce di esportazione NFS radice

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Installare i componenti drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Creare una partizione per i dispositivi drbd

   Elencare tutti i dischi dati disponibili

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Output di esempio
   
   ```
   lun0  lun1
   ```

   Creare partizioni per ogni disco dati

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Creare le configurazioni LVM

   Elencare tutte le partizioni disponibili

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Output di esempio
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Creare volumi LVM per ogni partizione

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Configurare drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Assicurarsi che il file drbd.conf contenga le due righe seguenti

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Modificare la configurazione globale di drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Aggiungere le voci seguenti al gestore e alla sezione net.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** Creare i dispositivi drbd NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Inserire la configurazione per il nuovo dispositivo drbd e uscire

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Inserire la configurazione per il nuovo dispositivo drbd e uscire

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Creare il dispositivo drbd e avviarlo

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Saltare la sincronizzazione iniziale

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Impostare il nodo primario

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Attendere il completamento della sincronizzazione dei nuovi dispositivi drbd

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Creare i file system nei dispositivi drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** Configurare il rilevamento di scenari split brain per drbd

   Quando si usa drbd per sincronizzare i dati da un host a un altro, può verificarsi un fenomeno denominato split brain. Uno Split Brain è uno scenario in cui entrambi i nodi del cluster hanno innalzato di livello il dispositivo DRBD come primario e non sono più sincronizzati. Potrebbe trattarsi di una situazione rara, ma si vuole comunque gestire e risolvere un cervello diviso il più velocemente possibile. Di conseguenza, è importante ricevere una notifica quando si verifica uno scenario di split brain.

   Leggere la [documentazione ufficiale di drbd](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) su come configurare una notifica di split brain.

   È anche possibile ripristinare automaticamente il sistema da uno scenario di split brain. Per altre informazioni, vedere [Automatic split brain recovery policies](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration) (Criteri di ripristino automatico da scenari di split brain)
   
### <a name="configure-cluster-framework"></a>Configurare il framework del cluster

1. **[1]** Aggiungere i dispositivi drbd NFS per il sistema SAP NW1 alla configurazione del cluster

   > [!IMPORTANT]
   > Sono state rilevate situazioni di test recenti, in cui netcat smette di rispondere alle richieste dovute al backlog e alla limitazione della gestione di una sola connessione. La risorsa netcat smette di essere in ascolto delle richieste del servizio di bilanciamento del carico di Azure e l'IP mobile diventa non disponibile.  
   > Per i cluster Pacemaker esistenti, in passato era consigliabile sostituire netcat con socat. Attualmente è consigliabile usare l'agente di risorse azure-lb, che fa parte degli agenti di risorse del pacchetto, con i requisiti di versione del pacchetto seguenti:
   > - Per SLES 12 SP4/SP5 la versione deve essere almeno resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Per SLES 15/15 SP1 la versione deve essere almeno resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Si noti che la modifica richiederà un breve tempo di inattività.  
   > Per i cluster Pacemaker esistenti, se la configurazione è stata già modificata per usare socat, come descritto in [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128) (Protezione avanzata dei rilevamenti del servizio di bilanciamento del carico di Azure), non è necessario passare immediatamente all'agente delle risorse azure-lb.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Aggiungere i dispositivi drbd NFS per il sistema SAP NW2 alla configurazione del cluster

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   L' `crossmnt` opzione nelle `exportfs` risorse cluster è presente nella documentazione per la compatibilità con le versioni precedenti di SLES.  

1. **[1]** Disabilitare la modalità manutenzione
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [Installare SAP ASCS e il database](high-availability-guide-suse.md)
* [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha]
