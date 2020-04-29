---
title: Creare un peering di rete virtuale di Azure - Modelli di distribuzione diversa - Stessa sottoscrizione | Microsoft Docs
description: Informazioni su come creare un peering di rete virtuale tra reti virtuali distribuite con modelli di distribuzione di Azure diversi e incluse nella stessa sottoscrizione di Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023260"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Creare un peering di rete virtuale: diversi modelli di distribuzione, stessa sottoscrizione

In questa esercitazione si apprenderà a creare un peering di rete virtuale tra reti virtuali create con modelli di distribuzione diversi. Le reti virtuali devono trovarsi nella stessa sottoscrizione. Il peering di due reti virtuali consente alle risorse che si trovano in reti virtuali diverse di comunicare tra loro con la stessa larghezza di banda e la stessa latenza come se fossero nella stessa rete virtuale. Altre informazioni sul [Peering di rete virtuale](virtual-network-peering-overview.md).

I passaggi per creare un peering di rete virtuale sono diversi a seconda che le reti virtuali siano incluse nella stessa sottoscrizione o in sottoscrizioni diverse e in base al [modello di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con il quale sono state create le reti virtuali. Per informazioni su come creare un peering di rete virtuale in altri scenari, fare clic sullo scenario nella tabella seguente:

|Modello di distribuzione di Azure  | Sottoscrizione di Azure  |
|--------- |---------|
|[Entrambi Resource Manager](tutorial-connect-virtual-networks-portal.md) |Uguale|
|[Entrambi Resource Manager](create-peering-different-subscriptions.md) |Diversa|
|[Uno di Resource Manager, uno della versione classica](create-peering-different-deployment-models-subscriptions.md) |Diversa|

Non è possibile creare un peering di rete virtuale tra due reti virtuali distribuite tramite il modello di distribuzione classica. Se è necessario connettere due reti virtuali, entrambe create tramite il modello di distribuzione classica, è possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure.

Questa esercitazione consente di eseguire il peering di due reti virtuali nella stessa area. È anche possibile eseguire il peering di reti virtuali in diverse [aree supportate](virtual-network-manage-peering.md#cross-region). È consigliabile acquisire familiarità con [i requisiti e i vincoli del peering](virtual-network-manage-peering.md#requirements-and-constraints) prima di eseguire il peering di reti virtuali.

Per creare un peering di rete virtuale, è possibile usare la portale di Azure, l' [interfaccia della riga di comando](#cli) di Azure, Azure [PowerShell](#powershell)o un modello di Azure Resource Manager. Facendo clic sui collegamenti degli strumenti precedenti, si passa direttamente alle procedure per la creazione di un peering di rete virtuale con il determinato strumento.

## <a name="create-peering---azure-portal"></a>Creare un peering: portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#requirements-and-constraints).
2. Fare clic su **+ Nuovo**, **Rete** e quindi **Rete virtuale**.
3. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnet1*
    - **Spazio di indirizzi**: *10.0.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.0.0.0/24*
    - **Sottoscrizione**: selezionare la sottoscrizione
    - **Gruppo di risorse**: selezionare **Crea nuovo** e immettere *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
4. Fare clic su **+ nuovo**. Nella casella **Cerca nel Marketplace** digitare *Rete virtuale*. Fare clic su **Rete virtuale** quando viene visualizzato nei risultati della ricerca.
5. Nel pannello **Rete virtuale** selezionare **Classico** nella casella **Selezionare un modello di distribuzione** e fare clic su **Crea**.
6. Nel pannello **Crea rete virtuale** immettere o selezionare i valori necessari per le impostazioni seguenti e quindi fare clic su **Crea**:
    - **Nome**: *myVnet2*
    - **Spazio di indirizzi**: *10.1.0.0/16*
    - **Nome subnet**: *predefinito*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*
    - **Sottoscrizione**: selezionare la sottoscrizione
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*
    - **Località**: *Stati Uniti orientali*
7. Nella casella **Cerca risorse** nella parte superiore del portale digitare *myResourceGroup*. Fare clic su **MyResourceGroup** quando viene visualizzato nei risultati della ricerca. Viene visualizzato un pannello per il gruppo di risorse **myresourcegroup**. Il gruppo di risorse contiene le due reti virtuali create nei passaggi precedenti.
8. Fare clic su **myVNet1**.
9. Nel pannello **myVnet1** visualizzato fare clic su **Peer** nell'elenco verticale di opzioni sul lato sinistro del pannello.
10. Nel pannello **myVnet1 - Peer** visualizzato fare clic su **+ Aggiungi**
11. Nel pannello **Aggiungi peering** visualizzato immettere o selezionare le opzioni seguenti e quindi fare clic su **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modello di distribuzione della rete virtuale**: selezionare **Classica**.
     - **Sottoscrizione**: selezionare la sottoscrizione
     - **Rete virtuale**: fare clic su **Scegliere una rete virtuale** e quindi scegliere **myVnet2**.
     - **Consenti accesso alla rete virtuale:** assicurarsi che sia selezionato **Abilitato**.
    Questa esercitazione non prevede l'uso di altre impostazioni. Per informazioni su tutte le impostazioni per il peering, vedere [Gestire i peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
12. Dopo aver fatto clic su **OK** nel passaggio precedente, il pannello **Aggiungi peering** si chiude e viene visualizzato di nuovo il pannello **myVnet1 - Peer**. Dopo alcuni secondi, il peering creato viene visualizzato nel pannello. Nella colonna **STATO PEERING** relativa al peering **myVnet1ToMyVnet2** creato è riportato **Connesso**.

    Il peering viene quindi stabilito. Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
14. **Facoltativo**: per eliminare le risorse create in questa esercitazione, completare i passaggi della sezione [Eliminare risorse](#delete-portal) di questo articolo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Creare un peering - Interfaccia della riga di comando di Azure

Completare i passaggi seguenti con l'interfaccia della riga di comando classica di Azure e l'interfaccia della riga di comando di Azure. È possibile completare i passaggi da Azure Cloud Shell selezionando il pulsante **Prova** in uno dei passaggi seguenti oppure installando l'[interfaccia della riga di comando classica](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) e l'[interfaccia della riga di comando](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) ed eseguendo i comandi nel computer locale.

1. Se si usa Cloud Shell, andare al passaggio 2, poiché Cloud Shell esegue automaticamente l'accesso ad Azure. Aprire una sessione di comando e accedere ad Azure usando il comando `azure login`.
2. Eseguire l'interfaccia della riga di comando in modalità di gestione del servizio immettendo il comando `azure config mode asm`.
3. Per creare una rete virtuale con modello di distribuzione classico, immettere il comando seguente:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Eseguire il seguente script dell'interfaccia della riga di comando bash usando l'interfaccia della riga di comando, non l'interfaccia della riga di comando classica. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando bash nel computer Windows, vedere [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Creare un peering di rete virtuale tra due reti virtuali create con modelli di distribuzione diversi mediante l'interfaccia della riga di comando. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<subscription id>` con l'ID della sottoscrizione. Se non si conosce l'ID della sottoscrizione, immettere il comando `az account show`. Il valore per **id** nell'output è l'ID della sottoscrizione. Incollare lo script modificato nella sessione dell'interfaccia della riga di comando e quindi premere `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale creata con Resource Manager. Copiare il comando seguente, incollarlo nella sessione dell'interfaccia della riga di comando e quindi premere `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   L'output mostra **Connesso** nella colonna **PeeringState**.

   Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
8. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-cli) in questo articolo.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Creare un peering - PowerShell

1. Installare la versione più recente dei moduli [Azure](https://www.powershellgallery.com/packages/Azure) e [AZ](https://www.powershellgallery.com/packages/Az/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
2. Avviare una sessione di PowerShell.
3. In PowerShell accedere ad Azure immettendo il comando `Add-AzureAccount`. L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#requirements-and-constraints).
4. Per creare una rete virtuale con distribuzione classica con PowerShell, è necessario creare un nuovo file di configurazione di rete o modificarne uno esistente. Informazioni su come [esportare, aggiornare e importare il file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Il file deve includere l'elemento **VirtualNetworkSite** seguente per la rete virtuale usata in questa esercitazione:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L'importazione di un file di configurazione di rete modificato può modificare le reti virtuali esistenti create con la distribuzione classica nella sottoscrizione. Assicurarsi di aggiungere solo la rete virtuale precedente e di non modificare o rimuovere le reti virtuali esistenti dalla sottoscrizione.
5. Accedere ad Azure per creare la rete virtuale con Resource Manager immettendo il comando `Connect-AzAccount`. L'account con cui che si esegue l'accesso deve disporre delle autorizzazioni necessarie per creare un peering di rete virtuale. Per un elenco di autorizzazioni, vedere [Autorizzazioni di peering di reti virtuali](virtual-network-manage-peering.md#requirements-and-constraints).
6. Creare un gruppo di risorse e una rete virtuale con Resource Manager. Copiare lo script, incollarlo in PowerShell e quindi premere `Enter`.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Creare un peering di rete virtuale tra due reti virtuali create con modelli di distribuzione diversi. Copiare lo script seguente in un editor di testo nel PC. Sostituire `<subscription id>` con l'ID della sottoscrizione. Se non si conosce l'ID della sottoscrizione, immettere il comando `Get-AzSubscription` per visualizzarlo. Il valore di **Id** nell'output restituito è l'ID della sottoscrizione. Per eseguire lo script, copiare lo script modificato dall'editor di testo, quindi fare clic con il pulsante destro del mouse nella sessione di PowerShell e premere `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Dopo aver eseguito lo script, rivedere i peering di ciascuna rete virtuale creata con Resource Manager. Copiare il comando seguente, incollarlo nella sessione di PowerShell e quindi premere `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    L'output mostra **Connesso** nella colonna **PeeringState**.

    Tutte le risorse di Azure create in una delle reti virtuali possono ora comunicare tra loro tramite i relativi indirizzi IP. Se si usa la risoluzione dei nomi di Azure predefinita per le reti virtuali, le risorse contenute nelle reti virtuali non sono in grado di risolvere i nomi tra le reti virtuali. Se si intende risolvere i nomi tra le reti virtuali in peering, è necessario creare un proprio server DNS. Per informazioni sulla configurazione, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Facoltativo**: anche se la creazione delle macchine virtuali non è illustrata in questa esercitazione, è possibile creare una macchina virtuale in ogni rete virtuale ed eseguire la connessione da una macchina virtuale all'altra per convalidare la connettività.
10. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare risorse](#delete-powershell) in questo articolo.

## <a name="delete-resources"></a><a name="delete"></a>Elimina risorse

Al termine di questa esercitazione, è necessario eliminare le risorse create, per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina** .
3. Per confermare l'eliminazione, nella casella **digitare il nome del gruppo di risorse** immettere **myResourceGroup**e quindi fare clic su **Elimina**.

### <a name="azure-cli"></a><a name="delete-cli"></a>INTERFACCIA della riga di comando di Azure

1. Usare l'interfaccia della riga di comando di Azure per eliminare la rete virtuale (Resource Manager) con il seguente comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Usare l'interfaccia della riga di comando classica per eliminare la rete virtuale (classica) con i comandi seguenti:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Immettere il comando seguente per eliminare la rete virtuale creata con Resource Manager:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Per eliminare una rete virtuale creata con distribuzione classica con PowerShell, è necessario modificare un file di configurazione di rete esistente. Informazioni su come [esportare, aggiornare e importare il file di configurazione di rete](virtual-networks-using-network-configuration-file.md). Rimuovere l'elemento VirtualNetworkSite seguente dalla rete virtuale usata in questa esercitazione:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L'importazione di un file di configurazione di rete modificato può modificare le reti virtuali esistenti create con la distribuzione classica nella sottoscrizione. Assicurarsi di rimuovere solo la rete virtuale precedente e di non modificare o rimuovere altre reti virtuali esistenti dalla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con importanti [vincoli e comportamenti del peering di rete virtuale](virtual-network-manage-peering.md#requirements-and-constraints) prima di creare un peering di rete virtuale per l'uso in produzione.
- Acquisire informazioni più dettagliate su tutte le [impostazioni per il peering di rete virtuale](virtual-network-manage-peering.md#create-a-peering).
- Acquisire informazioni su come [Creare una topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) con il peering di rete virtuale.
