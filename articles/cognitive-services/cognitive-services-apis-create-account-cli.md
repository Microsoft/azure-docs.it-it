---
title: Creare una risorsa di Servizi cognitivi con l'interfaccia della riga di comando di Azure
titleSuffix: Azure Cognitive Services
description: Introduzione ai servizi cognitivi di Azure tramite la creazione e la sottoscrizione di una risorsa tramite l'interfaccia della riga di comando di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 08ff2f416a00002cde5767111ba5a6824a721324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868172"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Guida introduttiva: creare una risorsa di servizi cognitivi usando l'interfaccia della Command-Line di Azure

Usare questa Guida introduttiva per iniziare a usare servizi cognitivi di Azure tramite l' [interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

I servizi cognitivi sono rappresentati da [risorse](../azure-resource-manager/management/manage-resources-portal.md) di Azure create nella sottoscrizione di Azure. Dopo aver creato la risorsa, usare le chiavi e l'endpoint generati per autenticare le applicazioni.

In questa Guida introduttiva si apprenderà come iscriversi ai servizi cognitivi di Azure e creare un account con una sottoscrizione a servizio singolo o multiservizio usando l' [interfaccia della riga di comando (CLI) di Azure](/cli/azure/install-azure-cli). Questi servizi sono rappresentati da [risorse](../azure-resource-manager/management/manage-resources-portal.md)di Azure, che consentono di connettersi a una o più API servizi cognitivi di Azure.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida- [crearne una](https://azure.microsoft.com/free/cognitive-services) gratuita.
* [Interfaccia della riga di comando di Azure (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>Installare l'interfaccia della riga di comando di Azure e accedere

Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il comando [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

È anche possibile usare il pulsante **prova** in verde per eseguire questi comandi nel browser.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Creare un nuovo gruppo di risorse servizi cognitivi di Azure

Prima di creare una risorsa Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o utilizzarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

### <a name="choose-your-resource-group-location"></a>Scegliere il percorso del gruppo di risorse

Per creare una risorsa, è necessario disporre di una delle località di Azure disponibili per la sottoscrizione. È possibile recuperare un elenco di percorsi disponibili con il comando [AZ account list-locations](/cli/azure/account#az-account-list-locations) . È possibile accedere alla maggior parte dei servizi cognitivi da diverse posizioni. Scegliere quella più vicina o vedere quali sono le località disponibili per il servizio.

> [!IMPORTANT]
> * Ricordare la località di Azure, perché sarà necessaria quando si chiamano i servizi cognitivi di Azure.
> * La disponibilità di alcuni servizi cognitivi può variare in base all'area. Per altre informazioni, vedere [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Dopo aver creato la località di Azure, creare un nuovo gruppo di risorse nell'interfaccia della riga di comando di Azure usando il comando [AZ Group create](/cli/azure/group#az-group-create) .

Nell'esempio seguente sostituire la località di Azure `westus2` con una delle località di Azure disponibili per la sottoscrizione.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Creare una risorsa per Servizi cognitivi

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Scegliere un servizio cognitivo e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscerne il tipo di servizio che si vuole usare, oltre al piano [tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni vengono usate come parametri durante la creazione della risorsa.

### <a name="multi-service"></a>Multi-servizio

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Più servizi. Per ulteriori informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


> [!NOTE]
> Molti dei servizi cognitivi di seguito hanno un livello gratuito che è possibile usare per provare il servizio. Per usare il livello gratuito, usare `F0` come SKU per la risorsa.

### <a name="vision"></a>Visione

| Servizio                    | Tipo                      |
|----------------------------|---------------------------|
| Visione artificiale            | `ComputerVision`          |
| Visione personalizzata - Previsione | `CustomVision.Prediction` |
| Visione personalizzata - Training   | `CustomVision.Training`   |
| Viso                       | `Face`                    |
| Riconoscimento modulo            | `FormRecognizer`          |
| Riconoscimento input penna             | `InkRecognizer`           |

### <a name="speech"></a>Voce

| Servizio            | Tipo                 |
|--------------------|----------------------|
| Servizi Voce    | `SpeechServices`     |
| Riconoscimento vocale | `SpeakerRecognition` |

### <a name="language"></a>Linguaggio

| Servizio            | Tipo                |
|--------------------|---------------------|
| Comprensione modulo | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Text Analytics     | `TextAnalytics`     |
| Traduzione testuale   | `TextTranslation`   |

### <a name="decision"></a>Decisione

| Servizio           | Tipo               |
|-------------------|--------------------|
| Rilevamento anomalie  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizza esperienze      | `Personalizer`     |

È possibile trovare un elenco di "tipi" di servizi cognitivi disponibili con il comando [AZ cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Aggiungere una nuova risorsa al gruppo di risorse

Per creare e sottoscrivere una nuova risorsa di servizi cognitivi, usare il comando [AZ cognitiveservices account create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . Questo comando aggiunge una nuova risorsa fatturabile al gruppo di risorse creato in precedenza. Quando si crea la nuova risorsa, è necessario conoscerne il "tipo" di servizio che si vuole usare, oltre al piano tariffario (o SKU) e a una località di Azure:

È possibile creare una risorsa F0 (gratuita) per il rilevatore di anomalie, denominato `anomaly-detector-resource` con il comando seguente.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

Per accedere all'installazione locale dell'interfaccia Command-Line (CLI), usare il comando [AZ login](/cli/azure/reference-index#az-login) .

```azurecli-interactive
az login
```

Usare il comando [AZ cognitiveservices account keys List](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) per ottenere le chiavi per la risorsa del servizio cognitivo.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* Il numero massimo di transazioni consentite al secondo.
* Le funzionalità del servizio abilitate all'interno del piano tariffario.
* Costo di un numero predefinito di transazioni. Il superamento di questo importo comporterà un addebito aggiuntivo come specificato nei dettagli relativi ai [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

## <a name="get-current-quota-usage-for-your-resource"></a>Ottenere l'utilizzo della quota corrente per la risorsa

Usare il comando [AZ cognitiveservices account list-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) per ottenere l'utilizzo della risorsa del servizio cognitivo.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole eseguire la pulizia e la rimozione di una risorsa di servizi cognitivi, è possibile eliminarla o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse al suo interno.

Per rimuovere il gruppo di risorse e le risorse associate, usare il comando AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Vedi anche

* Vedere **[autenticare le richieste ai servizi cognitivi di Azure](authentication.md)** per informazioni su come lavorare in modo sicuro con servizi cognitivi.
* Vedere **[che cosa sono i servizi cognitivi di Azure?](./what-are-cognitive-services.md)** per ottenere un elenco di categorie diverse all'interno di servizi cognitivi.
* Vedere **[supporto del linguaggio naturale](language-support.md)** per visualizzare l'elenco dei linguaggi naturali supportati da servizi cognitivi.
* Vedere **[usare servizi cognitivi come contenitori](cognitive-services-container-support.md)** per comprendere come usare i servizi cognitivi locali.
* Vedere **[pianificare e gestire i costi per i servizi cognitivi](plan-manage-costs.md)** per stimare i costi dell'uso di servizi cognitivi.
