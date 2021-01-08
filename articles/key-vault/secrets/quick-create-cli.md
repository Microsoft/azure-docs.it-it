---
title: 'Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault'
description: Guida introduttiva che illustra come impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 01741540d94d906fc6d41693392f48c17dbae5ce
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934816"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure

In questo argomento di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con l'interfaccia della riga di comando di Azure. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviato un segreto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *ContosoResourceGroup* nella posizione *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Verrà quindi creata un'istanza di Key Vault nel gruppo di risorse creato nel passaggio precedente. Sarà necessario specificare alcune informazioni:

- Per questa guida di avvio rapido si usa **Contoso-vault2**. È necessario specificare un nome univoco nel test.
- Nome del gruppo di risorse **ContosoResourceGroup**.
- Località **Stati Uniti orientali**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

- **Vault Name**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri comandi di Key Vault.
- **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://contoso-vault2.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi aggiuntivi. Questa password può essere usata da un'applicazione. La password sarà denominata **ExamplePassword** e al suo interno verrà archiviato il valore di **hVFkk965BuUv**.

Digitare i comandi seguenti per creare in Key Vault un segreto denominato **ExamplePassword** in cui verrà archiviato il valore **hVFkk965BuUv**:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

È ora possibile fare riferimento a questa password aggiunta ad Azure Key Vault usando il relativo URI. Usare **'https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword '** per ottenere la versione corrente. 

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stato archiviato e recuperato un segreto.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group). È possibile eliminare le risorse in questo modo:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [comandi az keyvault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault?view=azure-cli-latest)
- Vedere [Panoramica della sicurezza di Key Vault](../general/security-overview.md)
