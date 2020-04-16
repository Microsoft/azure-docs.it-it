---
title: 'Guida introduttiva: Creare una chiave LUIS'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come creare un'applicazione LUIS e ottenere una chiave.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400841"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Guida introduttiva: Ottenere una chiave dell'endpoint LUIS

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre degli elementi seguenti:

* Account LUIS. È possibile ottenerne uno gratuitamente tramite il [portale di LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS e riconoscimento vocale

LUIS si integra con il servizio di riconoscimento vocale per distinguere le finalità dai contenuti vocali. Non è necessaria una sottoscrizione al servizio di riconoscimento vocale, LUIS è sufficiente.

LUIS usa tre tipi di chiave:

|Tipo di chiave|Scopo|
|--------|-------|
|Creazione|Consente di creare e modificare a livello di codice app LUIS|
|Starter|Testa l'applicazione LUIS usando solo testo|
|Endpoint |Autorizza l'accesso a una particolare app LUIS|

Per questa esercitazione è necessario il tipo di chiave dell'endpoint. L'esercitazione usa l'esempio dell'app di domotica LUIS, che è possibile creare seguendo l'avvio rapido [Usare l'app di domotica predefinita](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Se è stata creata un'app LUIS personalizzata, è possibile invece usare quest'ultima.

Quando si crea un'app LUIS, viene generata automaticamente una chiave di avvio in modo da testare l'app usando le query di testo. Questa chiave non abilita l'integrazione del servizio Voce e non funziona con questa esercitazione. Creare una risorsa LUIS nel dashboard di Azure e assegnarla all'app LUIS. È possibile usare il livello di sottoscrizione gratuita per questa esercitazione.

Dopo aver creato la risorsa LUIS nel dashboard di Azure, accedere al [portale LUIS](https://www.luis.ai/home), scegliere l'applicazione nella pagina **App personali**, quindi passare alla pagina **Gestione** dell'app. Infine, selezionare **Chiavi ed endpoint** nella barra laterale.

![Chiavi del portale LUIS e impostazioni di endpoint](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Nella pagina **Impostazioni chiavi ed endpoint**:

1. Scorrere verso il basso fino alla sezione **Risorse e chiavi** e selezionare **Assegnare risorsa**.
1. Nella finestra di dialogo **Assegnare una chiave all'app** apportare le modifiche seguenti:

   * In **Tenant** scegliere **Microsoft**.
   * In **Nome della sottoscrizione** scegliere la sottoscrizione di Azure che contiene la risorsa LUIS che si vuole usare.
   * In **Chiave** scegliere la risorsa LUIS che si vuole usare con l'app.

   A breve, la nuova sottoscrizione viene visualizzata nella tabella nella parte inferiore della pagina.

1. Selezionare l'icona accanto a una chiave per copiarla negli Appunti. (È possibile usare una qualsiasi chiave.)

![Chiavi di sottoscrizione dell'app LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscimento delle finalità](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
