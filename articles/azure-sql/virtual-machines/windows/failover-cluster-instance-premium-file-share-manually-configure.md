---
title: Creare un'istanza di FCI con una condivisione file Premium
description: Usare una condivisione file Premium (PFS) per creare un'istanza del cluster di failover con SQL Server nelle macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482195"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Creare un'istanza FCI con una condivisione file Premium (SQL Server nelle macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come creare un'istanza del cluster di failover con SQL Server in macchine virtuali di Azure usando una [condivisione file Premium.](../../../storage/files/storage-how-to-create-file-share.md)

Le condivisioni file Premium sono condivisioni file con supporto di Spazi di archiviazione diretta (SSD), costantemente a bassa latenza, completamente supportate per l'uso con istanze del cluster di failover per SQL Server 2012 o versioni successive in Windows Server 2012 o versioni successive. Le condivisioni file Premium offrono maggiore flessibilità, consentendo di ridimensionare una condivisione file senza tempi di inattività.

Per altre informazioni, vedere una panoramica dell'istanza di Failover con [SQL Server sulle macchine virtuali di Azure](failover-cluster-instance-overview.md) e sulle procedure consigliate per i [cluster.](hadr-cluster-best-practices.md) 

## <a name="prerequisites"></a>Prerequisiti

Prima di completare le istruzioni riportate in questo articolo, è necessario avere già:

- Una sottoscrizione di Azure.
- Un account con autorizzazioni per creare oggetti sia nelle macchine virtuali di Azure che in Active Directory.
- [Due o più macchine virtuali di Windows Azure preparate](failover-cluster-instance-prepare-vm.md) in un [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) o in zone di disponibilità [diverse.](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)
- Una [condivisione file Premium](../../../storage/files/storage-how-to-create-file-share.md) da usare come unità del cluster, in base alla quota di archiviazione del database per i file di dati.
- La versione più recente di [PowerShell.](/powershell/azure/install-az-ps) 

## <a name="mount-premium-file-share"></a>Montare la condivisione file Premium

1. Accedere al [portale di Azure](https://portal.azure.com). e passare all'account di archiviazione.
1. Passare a **Condivisioni file** in **Servizio file** e quindi selezionare la condivisione file Premium da usare per l'archiviazione SQL.
1. Selezionare **Connetti** per visualizzare la stringa di connessione per la condivisione file.
1. Nell'elenco a discesa selezionare la lettera di unità da usare e quindi copiare entrambi i blocchi di codice nel Blocco note.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Copiare entrambi i comandi di PowerShell dal portale di connessione della condivisione file":::

1. Usare Remote Desktop Protocol (RDP) per connettersi alla macchina virtuale SQL Server con l'account che l'SQL Server FCI userà per l'account del servizio.
1. Aprire una console di comandi di PowerShell come amministratore.
1. Eseguire i comandi salvati in precedenza durante il lavoro nel portale.
1. Passare alla condivisione usando Esplora file o la **finestra di** dialogo Esegui (selezionare Windows + R). Usare il percorso di rete `\\storageaccountname.file.core.windows.net\filesharename`. Ad esempio, usare `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Creare almeno una cartella nella condivisione file appena connessa in cui inserire i file di dati SQL.
1. Ripetere questi passaggi in ogni macchina virtuale di SQL Server che parteciperà al cluster.

  > [!IMPORTANT]
  > - Prendere in considerazione l'uso di una condivisione file separata per i file di backup per salvare le operazioni di input/output al secondo e la capacità di spazio di questa condivisione per i file di dati e di log. È possibile usare una condivisione file Premium o Standard per i file di backup.
  > - Se si usa Windows 2012 R2 o versioni precedenti, seguire questi stessi passaggi per montare la condivisione file che si userà come server di controllo della condivisione file. 
  > 


## <a name="add-windows-cluster-feature"></a>Aggiungere la funzionalità cluster di Windows

1. Connettersi alla prima macchina virtuale con RDP usando un account di dominio che è membro del gruppo degli amministratori locali e ha l'autorizzazione necessarie per creare oggetti in Active Directory. Usare questo account per il resto della configurazione.

1. [Aggiungere il clustering di failover a ogni macchina virtuale.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

   Per installare il clustering di failover dall'interfaccia utente, eseguire le operazioni seguenti in entrambe le macchine virtuali:
   1. In **Server Manager** selezionare **Gestione** e quindi **Aggiungi ruoli e funzionalità**.
   1. **Nell'Aggiunta guidata ruoli e funzionalità** selezionare **Avanti** fino a quando non si arriva a **Seleziona funzionalità.**
   1. In **Seleziona funzionalità** selezionare **Clustering di failover**. Includere tutte le funzionalità necessarie e gli strumenti di gestione. 
   1. Selezionare **Aggiungi funzionalità**.
   1. Selezionare **Avanti** e quindi selezionare **Fine** per installare le funzionalità.

   Per installare il clustering di failover tramite PowerShell, eseguire lo script seguente da una sessione di PowerShell di amministratore in una delle macchine virtuali:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Convalidare il cluster

Convalidare il cluster nell'interfaccia utente o usando PowerShell.

Per convalidare il cluster usando l'interfaccia utente, eseguire le operazioni seguenti in una delle macchine virtuali:

1. In **Server Manager** selezionare **Strumenti** e quindi selezionare **Gestione cluster di failover**.
1. In **Gestione cluster di failover** selezionare **Azione** e quindi selezionare **Convalida configurazione**.
1. Selezionare **Avanti**.
1. In **Selezione di server o di un cluster** immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di testing** selezionare **Esegui solo test selezionati**. 
1. Selezionare **Avanti**.
1. In **Selezione dei test** selezionare tutti i test tranne **Archiviazione** e **Spazi di archiviazione diretta**, come illustrato di seguito:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Selezionare i test di convalida del cluster":::

1. Selezionare **Avanti**.
1. In **Conferma** selezionare **Avanti**.

La **Convalida guidata configurazione** esegue i test di convalida.

Per convalidare il cluster usando PowerShell, eseguire lo script seguente da una sessione di PowerShell in modalità amministratore in una delle macchine virtuali:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Dopo aver convalidato il cluster, creare il cluster di failover.


## <a name="create-failover-cluster"></a>Creare un cluster di failover

Per creare il cluster di failover è necessario:

- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- Nome del cluster di failover.
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2012 tramite Windows Server 2016. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Lo script di PowerShell seguente crea un cluster di failover per Windows Server 2019.  Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Per altre informazioni, vedere [Cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configurare il quorum

Configurare la soluzione quorum più adatta alle esigenze aziendali. È possibile configurare un disco [di controllo,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)un [cloud di controllo](/windows-server/failover-clustering/deploy-cloud-witness)o un controllo di condivisione [file.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) Per altre informazioni, vedere [Quorum con SQL Server virtuali.](hadr-cluster-best-practices.md#quorum) 


## <a name="test-cluster-failover"></a>Testare il failover del cluster

Testare il failover del cluster. In Gestione cluster di failover fare clic con il pulsante destro del mouse sul cluster, scegliere Altre azioni Sposta risorsa cluster principale Selezionare **il** nodo e quindi selezionare  >    >  l'altro nodo del cluster. Spostare le risorse principali del cluster in ogni nodo del cluster, quindi spostarle di nuovo nel nodo primario. Se lo spostamento del cluster in ogni nodo avviene in modo corretto, è possibile installare SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando le risorse principali negli altri nodi":::


## <a name="create-sql-server-fci"></a>Creare l'istanza del cluster di failover di SQL Server

Dopo aver configurato il cluster di failover, è possibile creare l'istanza del cluster di failover di SQL Server.

1. Connettersi alla prima macchina virtuale con RDP.

1. In **Gestione cluster di failover** verificare che tutte le risorse principali del cluster siano nella prima macchina virtuale. Se necessario, spostare tutte le risorse in questa macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. 

1. Selezionare **Imposta**.

1. Nel **Centro installazione SQL Server** selezionare **Installazione**.

1. Selezionare **Nuovo SQL Server'installazione del cluster** di failover di e quindi seguire le istruzioni della procedura guidata per installare l'istanza del SQL Server cluster di failover.

   Le directory di dati dell'istanza del cluster di failover devono trovarsi nella condivisione file Premium. Immettere il percorso completo della condivisione, nel formato seguente: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Verrà visualizzato un avviso che informa che è stato specificato un file server come directory di dati. Si tratta di un avviso previsto. Assicurarsi che l'account utente usato per accedere alla macchina virtuale tramite RDP quando è stata resa persistente la condivisione file sia lo stesso account usato dal servizio SQL Server per evitare possibili errori.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Usare la condivisione file come directory dati SQL":::

1. Dopo aver completato i passaggi della procedura guidata, verrà installata un'istanza del cluster di failover di SQL Server nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo usando RDP.

1. Aprire il **SQL Server installazione di** e quindi selezionare **Installazione**.

1. Selezionare **Aggiungi nodo a cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace con SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Per altre informazioni, vedere [Scaricare SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Ripetere questi passaggi in tutti gli altri nodi da aggiungere all'istanza del cluster SQL Server failover. 

## <a name="register-with-the-sql-vm-rp"></a>Eseguire la registrazione con la macchina virtuale SQL RP

Per gestire la macchina virtuale SQL Server dal portale, registrarla con l'estensione sql IaaS Agent (RP) in modalità [di](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)gestione leggera, attualmente l'unica modalità supportata con FCI e SQL Server nelle macchine virtuali di Azure. 

Registrare SQL Server macchina virtuale in modalità leggera con PowerShell (-LicenseType può `PAYG` essere o `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurare la connettività 

Per instradare il traffico in modo appropriato al nodo primario corrente, configurare l'opzione di connettività adatta all'ambiente. È possibile creare un servizio di bilanciamento del carico di [Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oppure, se si usa SQL Server 2019 CU2 (o [](failover-cluster-instance-distributed-network-name-dnn-configure.md) versione successiva) e Windows Server 2016 (o versione successiva), è possibile usare la funzionalità del nome di rete distribuita. 

Per altre informazioni sulle opzioni di connettività del cluster, vedere [Instradare connessioni a disponibilità elevata e ripristino di emergenza a SQL Server in macchine virtuali di Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitazioni

- Microsoft Distributed Transaction Coordinator (MSDTC) non è supportato in Windows Server 2016 e versioni precedenti. 
- FileStream non è supportato per un cluster di failover con una condivisione file Premium. Per usare filestream, distribuire il cluster usando [Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md) o dischi condivisi [di Azure.](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- È supportata solo la registrazione con l'estensione SQL IaaS Agent in [modalità di gestione](sql-server-iaas-agent-extension-automate-management.md#management-modes) leggera. 
- Gli snapshot del database non sono attualmente supportati con [File di Azure a causa di limitazioni dei file di tipo sparse.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)  

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, configurare la connettività all'istanza di FCI con un nome di rete virtuale e un servizio di bilanciamento del carico di [Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) o [DNN (Distributed Network Name).](failover-cluster-instance-distributed-network-name-dnn-configure.md) 


Se le condivisioni file Premium non sono la soluzione di archiviazione FCI appropriata, è consigliabile creare l'istanza FCI usando dischi condivisi di [Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) [o](failover-cluster-instance-storage-spaces-direct-manually-configure.md) Spazi di archiviazione diretta. 

Per altre informazioni, vedere una panoramica dell'istanza di Failover con [SQL Server sulle macchine virtuali](failover-cluster-instance-overview.md) di Azure e sulle procedure consigliate per la configurazione del [cluster.](hadr-cluster-best-practices.md) 

Per altre informazioni, vedere: 
- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
