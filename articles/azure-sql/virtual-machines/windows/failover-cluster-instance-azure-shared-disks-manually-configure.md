---
title: Creare un'istanza FCI con i dischi condivisi di Azure
description: Usare i dischi condivisi di Azure per creare un'istanza del cluster di failover (FCI) con SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: ce77021e74507ead6d225081debc7024cb89a15a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042403"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Creare un'istanza FCI con i dischi condivisi di Azure (SQL Server in macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come creare un'istanza del cluster di failover usando i dischi condivisi di Azure con SQL Server in macchine virtuali (VM) di Azure. 

Per altre informazioni, vedere Panoramica di [FCI con SQL Server nelle macchine virtuali di Azure](failover-cluster-instance-overview.md) e [procedure consigliate per cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Prerequisiti 

Prima di completare le istruzioni riportate in questo articolo, è necessario avere già:

- Una sottoscrizione di Azure. Inizia subito [gratuitamente](https://azure.microsoft.com/free/). 
- [Due o più macchine virtuali di Windows Azure](failover-cluster-instance-prepare-vm.md). I [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md) e i gruppi di posizionamento di [prossimità](../../../virtual-machines/co-location.md#proximity-placement-groups) (PPGs) supportati per le zone SSD Premium e di [disponibilità](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) sono supportati per i dischi Ultra. Tutti i nodi devono esistere nello stesso [gruppo di posizionamento di prossimità](../../../virtual-machines/co-location.md#proximity-placement-groups).
- Un account con autorizzazioni per creare oggetti sia nelle macchine virtuali di Azure che in Active Directory.
- La versione più recente di [PowerShell](/powershell/azure/install-az-ps). 

## <a name="add-azure-shared-disk"></a>Aggiungi disco condiviso di Azure
Distribuire un disco SSD Premium gestito con la funzionalità disco condiviso abilitata. Impostare `maxShares` l' **allineamento con il numero di nodi del cluster** per rendere il disco condivisibile tra tutti i nodi dell'istanza FCI. 

Aggiungere un disco condiviso di Azure seguendo questa procedura: 

1. Salvare lo script seguente come *SharedDiskConfig.jsin*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```

2. Eseguire *SharedDiskConfig.jsin* usando PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Per ogni macchina virtuale, inizializzare i dischi condivisi collegati come GPT (tabella di partizione GUID) e formattarli come nuovo file System di tecnologia (NTFS) eseguendo questo comando: 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>Crea cluster di failover

Per creare il cluster di failover è necessario:

- I nomi delle macchine virtuali che diventeranno i nodi del cluster.
- Nome del cluster di failover.
- un indirizzo IP per il cluster di failover. È possibile usare un indirizzo IP non usato nella stessa rete virtuale di Azure e nella stessa subnet dei nodi del cluster.

# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Lo script di PowerShell seguente crea un cluster di failover. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Lo script di PowerShell seguente crea un cluster di failover. Aggiornare lo script con i nomi dei nodi (ossia i nomi delle macchine virtuali) e un indirizzo IP disponibile della rete virtuale di Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Per altre informazioni, vedere [Cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---

## <a name="configure-quorum"></a>Configurare il quorum

Configurare la soluzione quorum più adatta alle proprie esigenze aziendali. È possibile configurare un [disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo, un [cloud](/windows-server/failover-clustering/deploy-cloud-witness)di controllo o una [condivisione file](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo. Per altre informazioni, vedere [quorum con SQL Server VM](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Convalida cluster
Convalidare il cluster nell'interfaccia utente o usando PowerShell.

Per convalidare il cluster usando l'interfaccia utente, eseguire le operazioni seguenti in una delle macchine virtuali:

1. In **Server Manager** selezionare **Strumenti** e quindi selezionare **Gestione cluster di failover**.
1. In **Gestione cluster di failover** selezionare **Azione** e quindi selezionare **Convalida configurazione**.
1. Selezionare **Avanti**.
1. In **Selezione di server o di un cluster** immettere i nomi di entrambe le macchine virtuali.
1. In **Opzioni di testing** selezionare **Esegui solo test selezionati**. 
1. Selezionare **Avanti**.
1. In **selezione test** selezionare tutti i test *eccetto* **archiviazione**

## <a name="test-cluster-failover"></a>Test del failover del cluster

Testare il failover del cluster. In **Gestione cluster di failover** fare clic con il pulsante destro del mouse sul cluster, scegliere **altre azioni**  >  **Sposta Core Cluster Resource**  >  **selezionare nodo**, quindi selezionare l'altro nodo del cluster. Spostare le risorse principali del cluster in ogni nodo del cluster, quindi spostarle di nuovo nel nodo primario. Se lo spostamento del cluster in ogni nodo avviene in modo corretto, è possibile installare SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testare il failover del cluster spostando le risorse principali negli altri nodi":::

## <a name="create-sql-server-fci"></a>Creare l'istanza del cluster di failover di SQL Server

Dopo aver configurato il cluster di failover e tutti i componenti del cluster, inclusa la risorsa di archiviazione, è possibile creare l'istanza del cluster di failover di SQL Server.

1. Connettersi alla prima macchina virtuale usando Remote Desktop Protocol (RDP).

1. In **Gestione cluster di failover** assicurarsi che tutte le risorse cluster principali siano nella prima macchina virtuale. Se necessario, spostare tutte le risorse in tale macchina virtuale.

1. Individuare i supporti di installazione. Se la macchina virtuale usa una delle immagini di Azure Marketplace, i supporti si trovano in `C:\SQLServer_<version number>_Full`. 

1. Selezionare **Imposta**.

1. In **Centro installazione SQL Server** selezionare **Installazione**.

1. Selezionare **Installazione di un nuovo cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare l'istanza del cluster di failover di SQL Server.

Le directory dei dati FCI devono trovarsi nei dischi condivisi di Azure. 

1. Dopo aver completato le istruzioni della procedura guidata, verrà installata un'istanza del cluster di failover di SQL Server nel primo nodo.

1. Al termine dell'installazione dell'istanza del cluster di failover nel primo nodo, connettersi al secondo nodo usando RDP.

1. Aprire **Centro installazione SQL Server**, quindi selezionare **installazione**.

1. Selezionare **Aggiungi nodo a cluster di failover di SQL Server**. Seguire le istruzioni della procedura guidata per installare SQL Server e aggiungere il server all'istanza del cluster di failover.

   >[!NOTE]
   >Se è stata usata un'immagine della raccolta di Azure Marketplace che contiene SQL Server, gli strumenti di SQL Server sono stati inclusi con l'immagine. In caso contrario, installare gli strumenti di SQL Server separatamente. Per altre informazioni, vedere [Scaricare SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >

## <a name="register-with-the-sql-vm-rp"></a>Eseguire la registrazione con la macchina virtuale SQL RP

Per gestire la macchina virtuale SQL Server dal portale, registrarla con l'estensione SQL IaaS Agent (RP) in [modalità di gestione leggera](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), attualmente l'unica modalità supportata con FCI e SQL Server in macchine virtuali di Azure. 

Registrare una macchina virtuale SQL Server in modalità Lightweight con PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurare la connettività 

Per instradare il traffico in modo appropriato al nodo primario corrente, configurare l'opzione di connettività adatta per l'ambiente. È possibile creare un servizio di [bilanciamento del carico di Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) o, se si usa SQL Server 2019 Cu2 (o versione successiva) e Windows Server 2016 (o versione successiva), è invece possibile usare la funzionalità nome di [rete distribuita](failover-cluster-instance-distributed-network-name-dnn-configure.md) .  

Per altre informazioni sulle opzioni di connettività del cluster, vedere [Instradare connessioni a disponibilità elevata e ripristino di emergenza a SQL Server in macchine virtuali di Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitazioni

- È supportata solo la registrazione con l'estensione SQL IaaS Agent in [modalità di gestione Lightweight](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, configurare la connettività all'istanza del cluster di failover con un [nome di rete virtuale e un](failover-cluster-instance-vnn-azure-load-balancer-configure.md) servizio di bilanciamento del carico di Azure o un nome di [rete distribuita (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Se i dischi condivisi di Azure non sono la soluzione di archiviazione FCI appropriata, provare a creare un'istanza FCI usando [condivisioni file Premium](failover-cluster-instance-premium-file-share-manually-configure.md) o [spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md) . 

Per altre informazioni, vedere Panoramica di [FCI con SQL Server nelle macchine virtuali di Azure](failover-cluster-instance-overview.md) e [procedure consigliate](hadr-cluster-best-practices.md)per la configurazione del cluster.

Per altre informazioni, vedere: 
- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
