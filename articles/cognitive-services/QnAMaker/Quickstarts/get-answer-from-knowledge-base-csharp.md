---
title: 'Guida introduttiva: Ottenere una risposta dalla knowledge base in REST, C# - QnA Maker'
description: Questa Guida introduttiva C# basata su REST assiste nell'ottenimento di una risposta da una knowledge base a livello di codice.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: d8a5a576ee3f7be099374b2294b35ec8c1c87f89
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934192"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Guida introduttiva: ottenere le risposte a una domanda da una Knowledge base con C #

Questa guida introduttiva illustra come ottenere, a livello di codice, una risposta da una knowledge base QnA Maker pubblicata. La knowledge base include domande e risposte da [origini dati](../Concepts/knowledge-base.md), ad esempio domande frequenti. La [domanda](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) viene inviata al servizio QnA Maker. La [risposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) include la risposta stimata più attendibile.

[Documentazione di riferimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Esempio](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Prerequisiti

* Versione più recente di [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave, selezionare **Chiavi** in **Gestione risorse** nel dashboard di Azure per la risorsa di QnA Maker.
* Impostazioni pagina di **Pubblicazione**. Se non si dispone di una knowledge base pubblicata, creare una knowledge base vuota, importare una knowledge base nella pagina **Impostazioni** e quindi pubblicarla. È possibile scaricare e usare [questa knowledge base di base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    Le impostazioni della pagina di pubblicazione includono il valore di route POST, il valore Host e il valore EndpointKey.

    ![Impostazioni di pubblicazione](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Creare un progetto per la knowledge base

1. Aprire Visual Studio 2019 Community Edition.
1. Creare un nuovo progetto App console (.NET Core) e assegnare al progetto il nome QnaMakerQuickstart. Accettare le impostazioni predefinite per le altre impostazioni.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio del file Program.cs sostituire la singola istruzione using con le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie

Nella parte superiore della classe `Program`, all'interno di `Main`, aggiungere le costanti seguenti per accedere a QnA Maker. Questi dati sono contenuti nella pagina **Pubblica** dopo la pubblicazione della knowledge base.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Aggiungere una richiesta POST per inviare domande e ottenere una risposta

Nel codice seguente viene effettuata una richiesta HTTPS all'API di QnA Maker per inviare la domanda alla knowledge base e ricevere la risposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

Il valore dell'intestazione di `Authorization` include la stringa `EndpointKey`.

Altre informazioni su [richiesta](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [risposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma da Visual Studio. Verrà inviata automaticamente la richiesta all'API di QnA Maker, quindi verrà visualizzato l'output nella finestra della console.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)