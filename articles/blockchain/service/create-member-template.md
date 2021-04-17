---
title: Creare un membro del servizio Azure Blockchain un modello di Azure Resource Manager
description: Informazioni su come creare un membro del servizio Azure Blockchain usando un modello di Azure Resource Manager.
services: azure-resource-manager
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: c49e45ae84d58d62460d493887e2a4e78e40ba32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536303"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Avvio rapido: Creare un membro del servizio Azure Blockchain con un modello di Resource Manager

In questo argomento di avvio rapido si distribuisce un nuovo membro e un consorzio blockchain nel servizio Azure Blockchain usando un modello di Azure Resource Manager (modello ARM). Un membro del servizio Azure Blockchain è un nodo blockchain in una rete blockchain del consorzio privato. Quando si effettua il provisioning di un membro, è possibile creare o partecipare alla rete di un consorzio. Per una rete del consorzio è necessario almeno un membro. Il numero di membri di blockchain necessari per i partecipanti dipende dallo scenario. I partecipanti del consorzio possono avere uno o più membri di blockchain oppure possono condividere i membri con altri partecipanti. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

Risorse di Azure definite nel modello:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare il collegamento seguente per accedere ad Azure e aprire un modello.

    [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Specificare le impostazioni per il membro del servizio Azure Blockchain.

    Impostazione | Descrizione
    --------|------------
    Subscription | Selezionare la sottoscrizione di Azure da usare per il servizio. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Resource group | Creare un nuovo nome di gruppo di risorse o sceglierne uno esistente nella sottoscrizione.
    Region | Scegliere un'area per creare il gruppo di risorse. La località deve essere la stessa per tutti i membri del consorzio. Le località disponibili per la distribuzione sono *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* e *japaneast*. Alcune funzionalità potrebbero non essere disponibili in alcune aree. Azure Blockchain Data Manager è disponibile nelle aree di Azure seguenti: ovvero Stati Uniti orientali ed Europa occidentale.
    Nome membro blockchain | Scegliere un nome univoco per il membro del servizio Azure Blockchain. Il nome del membro della blockchain può contenere solo lettere minuscole e numeri. Il primo carattere deve essere una lettera. Il valore deve avere una lunghezza compresa tra 2 e 20 caratteri.
    Nome del consorzio | Immettere un nome univoco. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md).
    Password del membro | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain.
    Password dell'account di gestione del consorzio | La password dell'account del consorzio viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. Viene usata per la gestione del consorzio.
    Livello SKU | Piano tariffario del nuovo servizio. È possibile scegliere tra **Standard** e **Basic**. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Usare il livello *Standard* per le distribuzioni di produzione. Usare il livello *Standard* anche se si usa Blockchain Data Manager o si invia un volume elevato di transazioni private. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.
    Nome SKU | Configurazione del nodo e costo del nuovo servizio. Usare **B0** per il livello Basic e **S0** per il livello Standard.
    Location | Scegliere una località per creare il membro. Per impostazione predefinita, la località del gruppo di risorse usata è `[resourceGroup().location]`. La località deve essere la stessa per tutti i membri del consorzio. Le località disponibili per la distribuzione sono *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* e *japaneast*. Alcune funzionalità potrebbero non essere disponibili in alcune aree. Azure Blockchain Data Manager è disponibile nelle aree di Azure seguenti: ovvero Stati Uniti orientali ed Europa occidentale.

1. Selezionare **Rivedi e crea** per verificare e distribuire il modello.

  In questo caso per distribuire il modello si usa il portale di Azure. È anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e un'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare il portale di Azure per visualizzare i dettagli del membro del servizio Azure Blockchain distribuito. Nel portale passare al gruppo di risorse contenente il membro del servizio Azure Blockchain. Selezionare il membro della blockchain creato.

![Dettagli della panoramica dei membri di Azure Blockchain distribuiti nel portale di Azure](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il membro della blockchain creato per l'esercitazione o la guida di avvio rapido successiva. Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse creato per l'avvio rapido.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
2. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati distribuiti un membro del servizio Azure Blockchain e un nuovo consorzio. Provare l'avvio rapido successivo per usare Azure Blockchain Development Kit per Ethereum per collegarsi a un membro del servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per connettersi al servizio Azure Blockchain](connect-vscode.md)
