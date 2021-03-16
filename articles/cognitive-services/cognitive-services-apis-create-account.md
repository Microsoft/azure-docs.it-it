---
title: Creare una risorsa Servizi cognitivi nella portale di Azure
titleSuffix: Azure Cognitive Services
description: Per iniziare a usare servizi cognitivi di Azure, creare e sottoscrivere una risorsa nel portale di Azure.
services: cognitive-services
author: aahill
manager: nitinme
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 69c83e9172a8369b7ff31116ee4db74fc33d86bb
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472121"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Guida introduttiva: creare una risorsa di servizi cognitivi usando il portale di Azure

Usare questa Guida introduttiva per iniziare a usare servizi cognitivi di Azure. Dopo aver creato una risorsa di servizio cognitivo nella portale di Azure, si otterranno un endpoint e una chiave per autenticare le applicazioni.

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa Servizi cognitivi di Azure

1. Creare una risorsa.

### <a name="multi-service-resource"></a>[Risorsa multi-servizio](#tab/multiservice)

La risorsa multiservizio è denominata **Servizi cognitivi** nel portale. [Creare una risorsa Servizi cognitivi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

A questo punto, la risorsa multiservizio consente l'accesso ai servizi cognitivi seguenti:

* Visione artificiale
* Content Moderator
* Viso
* Language Understanding (LUIS)
* Text Analytics
* Funzione di conversione

### <a name="single-service-resource"></a>[Risorsa a servizio singolo](#tab/singleservice)

Usare i collegamenti seguenti per creare una risorsa per i servizi cognitivi disponibili:

| Visione                      | Sintesi vocale                  | Lingua                          | Decisione             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Visione artificiale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Servizi vocali](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Lettore immersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Rilevamento anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Servizio visione artificiale personalizzato](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Riconoscimento del parlante](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Viso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizza esperienze](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Riconoscimento input penna](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Advisor metriche](https://go.microsoft.com/fwlink/?linkid=2142156)                    |

---

2. Nella pagina **Crea** specificare le informazioni seguenti:
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Risorsa multi-servizio](#tab/multiservice)

|Dettagli del progetto| Descrizione   |
|--|--|
| **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
| **Gruppo di risorse** | Gruppo di risorse Azure che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |
| **Area** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *MyCognitiveServicesResource*. |
| **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.

![Schermata di creazione delle risorse per più servizi](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

Selezionare **Crea**.

### <a name="single-service-resource"></a>[Risorsa a servizio singolo](#tab/singleservice)

|Dettagli del progetto| Descrizione   |
|--|--|
| **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
| **Gruppo di risorse** | Gruppo di risorse Azure che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |
| **Area** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *MyCognitiveServicesResource*. |
| **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.

![Schermata di creazione di risorse a servizio singolo](media/cognitive-services-apis-create-account/resource_create_screen.png)

Selezionare **Crea**.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

1. Dopo che la risorsa è stata distribuita correttamente, fare clic su **Vai alla risorsa** in **passaggi successivi**.

    ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Dal riquadro avvio rapido visualizzato è possibile accedere alla chiave e all'endpoint.

    ![Ottieni chiave ed endpoint](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse al suo interno.

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse contenente la risorsa da eliminare
3. Fare clic con il pulsante destro del mouse sull'elenco dei gruppi di risorse. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="see-also"></a>Vedi anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cos'è la ricerca cognitiva di Azure?](./what-are-cognitive-services.md)
* [Creare una nuova risorsa usando la libreria client di gestione di Azure](.\cognitive-services-apis-create-account-client-library.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto di contenitori Docker](cognitive-services-container-support.md)
