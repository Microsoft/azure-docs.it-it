---
title: Più indirizzi IP per le macchine virtuali di Azure - Portale | Documentazione Microsoft
description: Informazioni su come assegnare più indirizzi IP a una macchina virtuale usando il portale di Azure | Resource Manager.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 97d78b5bc77fef30bf7e3ad082a70f16a2ec74d1
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146607"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Assegnare più indirizzi IP alle macchine virtuali usando il portale di Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Questo articolo spiega come creare una macchina virtuale (VM) tramite il modello di distribuzione Azure Resource Manager usando il portale di Azure. Non è possibile a assegnare più indirizzi IP alle risorse create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

Se si vuole creare una macchina virtuale con più indirizzi IP, o un indirizzo IP privato statico, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure. Per informazioni su come procedere, fare clic sulle opzioni relative a PowerShell o all'interfaccia della riga di comando nella parte superiore di questo articolo. È possibile creare una macchina virtuale con un singolo indirizzo IP privato dinamico e (facoltativamente) un singolo indirizzo IP pubblico. Usare il portale seguendo i passaggi riportati negli articoli [Creare una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [creare una macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md). Dopo aver creato la VM, è possibile cambiare il tipo di indirizzo IP da dinamico a statico e aggiungere altri indirizzi IP tramite il portale seguendo i passaggi indicati nella sezione [Aggiungere indirizzi IP a una macchina virtuale](#add) di questo articolo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale

È possibile aggiungere indirizzi IP privati e pubblici a un'interfaccia di rete di Azure completando la procedura seguente. Gli esempi delle sezioni seguenti presuppongono che si disponga già di una VM con le tre configurazioni IP descritte nello [scenario](#scenario), ma non è indispensabile.

### <a name="core-steps"></a><a name="coreadd"></a>Passaggi di base

1. Passare al portale di Azure all'indirizzo https://portal.azure.com e, se necessario, eseguire l'accesso.
2. Nel portale fare clic su **Altri servizi** > digitare *macchine virtuali* nella casella del filtro e quindi fare clic su **Macchine virtuali**.
3. Nel riquadro **Macchine virtuali** scegliere la macchina virtuale a cui aggiungere indirizzi IP. Passare alla scheda **rete** . fare clic sull' **interfaccia di rete** nella pagina. Come illustrato nell'immagine seguente: 


    ![Aggiungere un indirizzo IP pubblico a una macchina virtuale](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. Nel riquadro **interfaccia di rete** fare clic su **configurazioni IP**.

5. Nel riquadro visualizzato per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP**. Fare clic su **Aggiungi**, completare i passaggi in una delle sezioni seguenti, in base al tipo di indirizzo IP che si desidera aggiungere e quindi fare clic su **OK**. 

### <a name="add-a-private-ip-address"></a>**Aggiungere un indirizzo IP privato**

Completare i passaggi seguenti per aggiungere un nuovo indirizzo IP privato:

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Fare clic su **Aggiungi**. Nel riquadro **Aggiungi configurazione IP** che viene visualizzato creare una configurazione IP denominata *IPConfig-4* con *10.0.0.7* come indirizzo IP privato *Statico* quindi fare clic su **OK**.

    > [!NOTE]
    > Quando si aggiunge un indirizzo IP statico, è necessario specificare un indirizzo valido e inutilizzato nella subnet a cui la scheda di rete è connessa. Se l'indirizzo selezionato non è disponibile, il portale visualizza una X per l'indirizzo IP ed è necessario selezionare un altro indirizzo.

3. Dopo aver fatto clic su OK, il riquadro si chiude e la nuova configurazione IP compare nell'elenco. Fare clic su **OK** per chiudere il riquadro **Aggiungi configurazione IP**.
4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP.
5. Aggiungere gli indirizzi IP privati al sistema operativo della macchina virtuale seguendo la procedura riportata nella sezione [Aggiungere indirizzi IP a una macchina virtuale](#os-config) di questo articolo.

### <a name="add-a-public-ip-address"></a>Aggiungere un indirizzo IP pubblico

Per aggiungere un indirizzo IP pubblico è necessario associare una risorsa indirizzo IP pubblico a una configurazione IP nuova o esistente.

> [!NOTE]
> Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Creare una risorsa indirizzo IP pubblico

Un indirizzo IP pubblico consiste in una singola impostazione per una risorsa indirizzo IP pubblico. Se si dispone di una risorsa indirizzo IP pubblico che non è attualmente associata a una configurazione IP e la si vuole associare a una configurazione IP, ignorare i passaggi seguenti e completare quelli della sezione appropriata fra quelle riportate di seguito. Se non si dispone di una risorsa indirizzo IP pubblico, completare la procedura seguente per crearne una:

1. Passare al portale di Azure all'indirizzo https://portal.azure.com e, se necessario, eseguire l'accesso.
3. Nel portale fare clic su **Crea una risorsa** > **rete** > **indirizzo IP pubblico**.
4. Nel riquadro **Crea indirizzo IP pubblico** che viene visualizzato immettere un **Nome**, selezionare un tipo di **Assegnazione indirizzi IP**, una **Sottoscrizione**, un **Gruppo di risorse** e un **Percorso**, quindi fare clic su **Crea**, come illustrato nell'immagine seguente:

    ![Creare una risorsa indirizzo IP pubblico](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Completare i passaggi in una delle sezioni seguenti per associare la risorsa indirizzo IP pubblico a una configurazione IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associare la risorsa indirizzo IP pubblico a una nuova configurazione IP

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Fare clic su **Aggiungi**. Nel riquadro **Aggiungi configurazione IP** che viene visualizzato creare una configurazione IP denominata *IPConfig-4*. Attivare l'opzione **Indirizzo IP pubblico** e selezionare una risorsa indirizzo IP pubblico esistente e disponibile dal riquadro **Scegli indirizzo IP pubblico**.

    Dopo aver selezionato la risorsa indirizzo IP pubblico, fare clic su **OK** e il riquadro verrà chiuso. Se non si dispone di un indirizzo IP pubblico esistente, è possibile crearne uno completando la procedura descritta nella sezione [Creare una risorsa indirizzo IP pubblico](#create-public-ip) di questo articolo. 

3. Rivedere la nuova configurazione IP. Anche se non è stato fatto in modo esplicito, un indirizzo IP privato è stato comunque assegnato alla configurazione IP, in quanto tutte le configurazioni IP devono avere un indirizzo IP privato.
4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP.
5. Aggiungere l'indirizzo IP privato al sistema operativo della VM completando i passaggi relativi al sistema operativo indicati nella sezione [Aggiungere indirizzi IP al sistema operativo di una VM](#os-config) di questo articolo. Non aggiungere l'indirizzo IP pubblico al sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associare la risorsa indirizzo IP pubblico a una configurazione IP esistente

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Fare clic sulla configurazione IP che si vuole aggiungere alla risorsa indirizzo IP pubblico.
3. Nel riquadro IPConfig visualizzato, fare clic su **Indirizzo IP**.
4. Nel riquadro **Scegli indirizzo IP pubblico** visualizzato, selezionare un indirizzo IP pubblico.
5. Fare clic su **Salva** e chiudere i riquadri. Se non si dispone di un indirizzo IP pubblico esistente, è possibile crearne uno completando la procedura descritta nella sezione [Creare una risorsa indirizzo IP pubblico](#create-public-ip) di questo articolo.
3. Rivedere la nuova configurazione IP.
4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP. Non aggiungere l'indirizzo IP pubblico al sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
