---
title: Macchine virtuali di Azure a disponibilità elevata per SAP NW in Windows con Azure NetApp Files (SMB) | Microsoft Docs
description: Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: b04dacfdedded417e2557d1568e01bc9fa8f5745
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590119"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Questo articolo descrive come distribuire, configurare le macchine virtuali, installare il Framework del cluster e installare un sistema SAP NetWeaver 7,50 a disponibilità elevata nelle VM Windows usando [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) in [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

Il livello del database non è descritto in dettaglio in questo articolo. Si presuppone che la [rete virtuale](../../../virtual-network/virtual-networks-overview.md) di Azure sia già stata creata.  

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533][1928533], che contiene:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows su Microsoft Azure
* La nota SAP [2015553][2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2178632][2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [1999351][1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* Nota SAP [2287140](https://launchpad.support.sap.com/#/notes/2287140) elenca i prerequisiti per la funzionalità CA supportata da SAP del protocollo SMB 3. x.
* La nota SAP [2802770](https://launchpad.support.sap.com/#/notes/2802770) contiene informazioni sulla risoluzione dei problemi per l'esecuzione lenta di SAP Transaction AL11 su Windows 2012 e 2016.
* La nota SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) contiene informazioni sulla funzionalità di failover trasparente per una condivisione file in Windows Server con il protocollo SMB 3,0.
* La nota SAP [662452](https://launchpad.support.sap.com/#/notes/662452) presenta una raccomandazione (disattivazione della generazione di nomi di 8,3) per risolvere i problemi di prestazioni/errori di file System durante gli accessi ai dati.
* [Installare la disponibilità elevata di SAP NetWeaver in un cluster di failover Windows e condivisione file per le istanze di SAP ASCS/SCS in Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Scenari e architettura di disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Aggiungere la porta probe nella configurazione del cluster ASC](sap-high-availability-installation-wsfc-file-share.md)
* [Installazione di un'istanza di (A) SCS in un cluster di failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Creare un volume SMB per Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

SAP ha sviluppato un nuovo approccio e un'alternativa per i dischi condivisi del cluster per il clustering di un'istanza ASCS/SCS di SAP in un cluster di failover di Windows. Invece di usare i dischi condivisi del cluster, è possibile usare una condivisione file SMB per distribuire i file dell'host globale SAP. Azure NetApp Files supporta SMBv3 (insieme a NFS) con ACL NTFS usando Active Directory. Azure NetApp Files è automaticamente a disponibilità elevata (in quanto si tratta di un servizio PaaS). Queste funzionalità rendono Azure NetApp Files ottima opzione per ospitare la condivisione file SMB per SAP Global.  
Sono supportati sia [servizi di dominio Azure Active Directory (ad)](../../../active-directory-domain-services/overview.md) che [Active Directory Domain Services (ad DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Con Azure NetApp Files è possibile usare controller di dominio Active Directory esistenti. I controller di dominio possono essere in Azure come macchine virtuali o in locale tramite la VPN ExpressRoute o S2S. In questo articolo verrà usato il controller di dominio in una macchina virtuale di Azure.  
La disponibilità elevata per SAP NetWeaver Central Services richiede l'archiviazione condivisa. Per ottenere questo risultato in Windows, fino a questo momento era necessario compilare il cluster SOFS o usare il disco condiviso del cluster s/w come il valore di base. È ora possibile ottenere la disponibilità elevata di SAP NetWeaver usando l'archiviazione condivisa, distribuita in Azure NetApp Files. L'uso di Azure NetApp Files per l'archiviazione condivisa Elimina la necessità di SOFS o di un valore di tipo.  

> [!NOTE]
> Il clustering di istanze ASCS/SCS di SAP tramite con condivisione file è supportato per SAP NetWeaver 7.40 (e versioni successive) con kernel SAP versione 7.49 o versione successiva.  

![Architettura a disponibilità elevata di SAP ASC/SCS con condivisione SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

I prerequisiti per una condivisione file SMB sono i seguenti:
* Protocollo SMB 3.0 (o versione successiva).
* Possibilità di impostare Active Directory elenchi di controllo di accesso (ACL) per Active Directory gruppi di utenti e l'oggetto computer $ computer.
* La condivisione file deve essere abilitata per la disponibilità elevata.

La condivisione per i servizi SAP Central in questa architettura di riferimento è offerta da Azure NetApp Files:

![Architettura a disponibilità elevata di SAP ASC/SCS con i dettagli della condivisione SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Creare e montare un volume SMB per Azure NetApp Files

Eseguire i passaggi seguenti, come preparazione per l'uso di Azure NetApp Files.  

1. Seguire i passaggi per [registrare Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Creare un account di Azure NetApp seguendo i passaggi descritti in  [creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Configurare il pool di capacità seguendo le istruzioni riportate in [configurare un pool di capacità](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Azure NetApp Files risorse devono trovarsi nella subnet delegata. Seguire le istruzioni in [delegare una subnet per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) per creare una subnet delegata.  

   > [!IMPORTANT]
   > È necessario creare le connessioni Active Directory prima di creare un volume SMB. Esaminare i [requisiti per le connessioni Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Creare Active Directory connessione, come descritto in [creare una connessione Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
6. Creare un volume SMB Azure NetApp Files SMB seguendo le istruzioni in [aggiungere un volume SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Montare il volume SMB nella macchina virtuale Windows.

> [!TIP]
> È possibile trovare le istruzioni su come montare il volume Azure NetApp Files. Se si passa al [portale di Azure](https://portal.azure.com/#home) all'oggetto Azure NetApp files, fare clic sul pannello **volumi** e quindi su **istruzioni di montaggio**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Preparare l'infrastruttura per la disponibilità elevata di SAP usando un cluster di failover di Windows 

1. [Impostare le regole di bilanciamento del carico di ASC/SCS per il servizio di bilanciamento del carico interno di Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Aggiungere macchine virtuali Windows al dominio](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Aggiungere voci del registro di sistema in entrambi i nodi del cluster dell'istanza di SAP ASC/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Configurare un cluster di failover di Windows Server per un'istanza di SAP ASC/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Se si usa Windows Server 2016, è consigliabile configurare [Azure cloud Witness](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Installare l'istanza di SAP ASC in entrambi i nodi

Per SAP è necessario il seguente software:
   * Strumento di installazione di SAP software Provisioning Manager (SWPM) versione SPS25 o successiva.
   * Kernel SAP 7,49 o versione successiva
   * Creare un nome host virtuale (nome di rete cluster) per l'istanza di SAP ASC/SCS in cluster, come descritto in [creare un nome host virtuale per l'istanza di SAP ASC/SCS in cluster](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Il clustering di istanze ASCS/SCS di SAP tramite con condivisione file è supportato per SAP NetWeaver 7.40 (e versioni successive) con kernel SAP versione 7.49 o versione successiva.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Installare un'istanza di ASC/SCS nel primo nodo del cluster ASC/SCS

1. Installare un'istanza di SAP ASC/SCS nel primo nodo del cluster. Avviare lo strumento di installazione di SAP SWPM, quindi passare a: **prodotto**  >  **DBMS** > installazione > server applicazioni ABAP (o Java) > High-Availability System > istanza di ASC/SCS > primo nodo del cluster.  

2. Selezionare il **cluster di condivisione file** come configurazione della condivisione del cluster in SWPM.  
3. Quando vengono richiesti i **parametri del cluster del sistema SAP**, immettere il nome host per la condivisione SMB Azure NetApp files già creata come **nome host della condivisione file**.  In questo esempio, il nome host della condivisione SMB è **anfsmb-9562**. 

   > [!IMPORTANT]
   > Se il controllo dei prerequisiti in SWPM Mostra che la condizione della funzionalità disponibilità continua non è soddisfatta, può essere risolta seguendo le istruzioni riportate nel [messaggio di errore quando si tenta di accedere a una cartella condivisa che non esiste più in Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Se il controllo dei prerequisiti in SWPM indica che la condizione di dimensioni di swapping non è stata soddisfatta, è possibile modificare le dimensioni di swapping passando a Computer locale>proprietà di sistema>impostazioni prestazioni>> memoria virtuale avanzata>.  

4. Configurare una risorsa cluster SAP, ovvero la `SAP-SID-IP` porta Probe, usando PowerShell. Eseguire questa configurazione in uno dei nodi del cluster SAP ASC/SCS, come descritto in [configurare la porta Probe](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Installare un'istanza di ASC/SCS nel secondo nodo del cluster ASC/SCS

1. Installare un'istanza di SAP ASC/SCS nel secondo nodo del cluster. Avviare lo strumento di installazione di SAP SWPM, quindi passare a **prodotto**  >  **DBMS** > installazione > server applicazioni ABAP (o Java) > High-Availability System > istanza di ASC/SCS > nodo del cluster aggiuntivo.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Installare un'istanza di DBMS e i server applicazioni SAP

Per completare l'installazione di SAP, installare:

   * Un'istanza DBMS  
   * Un server applicazioni SAP primario  
   * Un server applicazioni SAP aggiuntivo  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testare il failover dell'istanza di SAP ASC/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Eseguire il failover dal nodo A al nodo B e viceversa del cluster
In questo scenario di test verrà fatto riferimento al nodo del cluster sapascs1 come nodo A e al nodo del cluster sapascs2 come nodo B.

1. Verificare che le risorse del cluster siano in esecuzione nel nodo A. ![ Figura 1: risorse del cluster di failover di Windows Server in esecuzione nel nodo A prima del test di failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Riavviare il nodo A del cluster. Le risorse del cluster SAP vengono spostate nel nodo B del cluster. ![ Figura 2: risorse del cluster di failover di Windows Server in esecuzione sul nodo B dopo il test di failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test voce di blocco

1. Verificare che il server di replica di Accodamento SAP (ERS) sia attivo  
2. Accedere al sistema SAP, eseguire Transaction SU01 e aprire un ID utente in modalità di modifica. Questa operazione genererà una voce di blocco SAP.  
3. Quando si è connessi al sistema SAP, visualizzare la voce di blocco passando alla transazione ST12.  
4. Eseguire il failover delle risorse ASC dal nodo A al nodo B del cluster.  
5. Verificare che venga mantenuta la voce di blocco, generata prima del failover delle risorse del cluster SAP ASC/SCS.  

![Figura 3: la voce di blocco viene mantenuta dopo il test di failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Per altre informazioni, vedere [risoluzione dei problemi relativi al failover di Accodamento in ASC con ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP 
* HANA in Azure (istanze di grandi dimensioni), vedere [disponibilità elevata e ripristino di emergenza di SAP Hana (istanze large) in Azure](hana-overview-high-availability-disaster-recovery.md).
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha]
