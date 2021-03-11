---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b2b405e7f6babf84ef8297310a5a7a34c3f2dc11
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623223"
---
Servizi cognitivi offre due servizi di elaborazione del linguaggio naturale, [Language Understanding](../luis/what-is-luis.md) e [QnA Maker](../qnamaker/overview/overview.md), ciascuno con uno scopo diverso. Comprendere quando utilizzare ogni servizio e il modo in cui si complimentano tra loro.

L'elaborazione del linguaggio naturale (PNL) consente all'applicazione client, ad esempio un bot di chat, di lavorare con gli utenti, usando il linguaggio naturale. Un utente immette una frase o una frase. Il testo dell'utente può avere una grammatica, un'ortografia e una punteggiatura insoddisfacenti. Il servizio cognitivo può comunque utilizzare la frase dell'utente, restituendo le informazioni necessarie al bot della chat per aiutare l'utente.

## <a name="cognitive-services-with-nlp"></a>Servizi cognitivi con PNL

Language Understanding (LUIS) e QnA Maker forniscono la PNL. L'applicazione client Invia testo in linguaggio naturale. Il servizio accetta il testo, lo elabora e restituisce un risultato.

## <a name="when-to-use-each-service"></a>Quando usare ogni servizio

Language Understanding (LUIS) e QnA Maker risolvere diversi problemi. LUIS determina lo scopo del testo di un utente (noto come enunciato), mentre QnA Maker determina la risposta al testo di un utente (noto come query).

Per selezionare il servizio corretto, è necessario comprendere il testo dell'utente proveniente dall'applicazione client e le informazioni che l'applicazione client deve ottenere dal servizio cognitivo.

Se la chat bot riceve il testo `How do I get to the Human Resources building on the Seattle North campus?` , usare il grafico seguente per comprendere il funzionamento di ogni servizio con il testo.

|Servizio|L'applicazione client determina|
|--|--|
|LUIS|**Determina l'intenzione dell'utente** del testo. il servizio non restituisce la risposta alla domanda. Questo testo, ad esempio, viene classificato come corrispondente allo `FindLocation` scopo.<br>|
|QnA Maker|**Restituisce la risposta alla domanda** da una Knowledge base personalizzata. Questo testo, ad esempio, viene determinato come una domanda con la risposta del testo statico di  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Infografica per determinare quando utilizzare LUIS e quando utilizzare QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Quando si usa LUIS?

Usare LUIS quando è necessario conoscere l'intenzione dell'espressione come parte di un processo nel chatbot. Continuando con il testo di esempio, `How do I get to the Human Resources building on the Seattle North campus?` , quando si è a conoscenza dell'intenzione dell'utente di trovare un percorso, è possibile passare i dettagli sull'espressione (estratti con le entità) a un altro servizio, ad esempio un server di trasporto, per ottenere la risposta.

Non è necessario combinare LUIS e QnA Maker per determinare lo scopo.

È possibile combinare i due servizi per questa espressione, se il bot della chat deve elaborare il testo in base alle intenzioni e alle entità (tramite LUIS), oltre a trovare la risposta specifica del testo statico (usando QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Quando si usa QnA Maker?

Usare QnA Maker quando si dispone di una knowledge base di risposte statica. Questa knowledge base è personalizzata in base alle proprie esigenze create con documenti quali PDF e URL.

Continuando con l'espressione di esempio, `How do I get to the Human Resources building on the Seattle North campus?` , inviare il testo, come query, al servizio QnA Maker pubblicato e ricevere la risposta migliore.

Non è necessario combinare LUIS e QnA Maker per determinare la risposta alla domanda.

È possibile combinare i due servizi per questa espressione, se il bot della chat deve elaborare il testo in base a intenzioni ed entità (con LUIS), nonché trovare la risposta (usando QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Utilizzare entrambi i servizi quando la Knowledge base non è completa

Se si compila la Knowledge base QnA Maker ma si conosce il dominio soggetto che sta cambiando, ad esempio le informazioni tempestive, è possibile combinare LUIS e QnA Maker Services. Questo consente di usare le informazioni nella Knowledge base, ma anche di usare LUIS per determinare l'intenzione di un utente. Una volta che l'applicazione client ha intenzione, può richiedere informazioni rilevanti da un'altra origine.

È necessario che l'applicazione client monitori le risposte LUIS e QnA Maker per i punteggi. Se il punteggio da QnA Maker è inferiore a una soglia arbitraria, usare le informazioni sull'entità e sullo scopo restituite da LUIS per passare le informazioni a un servizio di terze parti.

Continuando con il testo di esempio, si `How do I get to the Human Resources building on the Seattle North campus?` supponga che QnA Maker restituisca un punteggio di confidenza basso. Usare lo scopo restituito da LUIS `FindLocation` ed eventuali entità estratte, ad esempio `Human Resources building` e `Seattle North campus` , per inviare queste informazioni a un servizio di ricerca o di mapping per un'altra risposta.

È possibile presentare questa risposta di terze parti all'utente per la convalida. Una volta ottenuto l'approvazione dell'utente, è possibile tornare a QnA Maker per aggiungere le informazioni per accrescere le proprie conoscenze.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Usare entrambi i servizi quando la chat bot necessita di ulteriori informazioni

Se la chat bot richiede più informazioni rispetto a quelle fornite da uno dei servizi, per continuare con un albero delle decisioni, usare entrambi i servizi ed elaborare entrambe le risposte nell'applicazione client.

Usare lo strumento dell' **[interfaccia](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** della riga di comando per la distribuzione di bot Framework per creare un processo per lavorare con entrambi i servizi. Questo strumento crea un'app LUIS principale di Intents che invia tra LUIS e QnA Maker come app figlio. [Scopri di più](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs) sull'integrazione con LUIS, QnA Maker e bot Framework.

Per implementare questo tipo di chat bot, usare l'esempio Bot Builder, **NLP con dispatch**, in [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) o [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch).

## <a name="best-practices"></a>Procedure consigliate

Implementare le procedure consigliate per ogni servizio:

* Procedure consigliate di [Luis](../luis/luis-concept-best-practices.md)
* Procedure consigliate [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Vedi anche

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [INTERFACCIA della riga di comando dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Esempi di bot Framework](https://github.com/Microsoft/BotBuilder-Samples)
* [Servizio Azure bot](/azure/bot-service/bot-service-overview-introduction)
* [Emulatore di Azure bot](https://github.com/Microsoft/BotFramework-Emulator)
* [Chat Web di bot Framework](https://github.com/microsoft/BotFramework-WebChat)