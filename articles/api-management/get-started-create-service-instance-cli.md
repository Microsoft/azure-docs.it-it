---
title: "Guida introduttiva: creare un'istanza di gestione API di Azure con CLI"
description: Creare una nuova istanza del servizio Gestione API di Azure usando l'interfaccia della riga di comando di Azure.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 72d1faac02a21f23d46eb992af1d501bca89e71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688077"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Guida introduttiva: creare una nuova istanza del servizio gestione API di Azure usando l'interfaccia della riga di comando di Azure

Gestione API di Azure consente alle organizzazioni di pubblicare API per permettere a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei dati e dei servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

Questo argomento di avvio rapido descrive i passaggi per la creazione di una nuova istanza di Gestione API tramite i comandi [az apim](/cli/azure/apim) dell'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione2.11.1 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di Gestione API di Azure, analogamente a tutte le risorse di Azure, devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

Per iniziare, creare un gruppo di risorse denominato *myResourceGroup* nell'area Stati Uniti centrali (centralus) con il comando [az group create](/cli/azure/group#az-group-create) seguente:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Creare un nuovo servizio

Ora che si dispone di un gruppo di risorse, è possibile creare un'istanza del servizio Gestione API. Crearne una usando il comando [az apim create](/cli/azure/apim#az-apim-create) e specificare un nome di servizio e i dettagli del server di pubblicazione. Il nome del servizio deve essere univoco in Azure. 

Nell'esempio seguente viene usato come nome di servizio *myapim*. Aggiornarlo con un valore univoco. Aggiornare anche il nome dell'organizzazione del server di pubblicazione dell'API e l'indirizzo di posta elettronica per la ricezione delle notifiche. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Per impostazione predefinita, il comando crea l'istanza nel livello Developer, un'opzione economica per valutare Gestione API di Azure. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare). 

> [!TIP]
> Possono essere necessari da 30 a 40 minuti per creare e attivare un servizio Gestione API in questo livello. Il comando precedente usa l'opzione `--no-wait` in modo che il comando restituisca immediatamente il controllo mentre viene creato il servizio.

Controllare lo stato della distribuzione eseguendo il comando [az apim show](/cli/azure/apim#az-apim-show):

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Inizialmente l'output ha un aspetto simile a quanto al seguente e riporta lo stato `Activating`:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Dopo l'attivazione, lo stato diventa `Online` e l'istanza del servizio presenta un indirizzo gateway e un indirizzo IP pubblico. Per il momento, questi indirizzi non espongono alcun contenuto. Ad esempio:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Nel momento in cui l'istanza del servizio Gestione API è online, è possibile iniziare a usarla. Iniziare con l'esercitazione per l'[importazione e la pubblicazione della prima API](import-and-publish.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e l'istanza del servizio Gestione API non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group#az-group-delete) per rimuoverli.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare e pubblicare la prima API](import-and-publish.md)
