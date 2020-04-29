---
title: 'Guida introduttiva: Creare una knowledge base in REST, Java - QnA Maker'
description: Questa guida introduttiva basata su Java REST illustra come creare a livello di codice una knowledge base di esempio per QnA Maker, che verrà visualizzata nel dashboard di Azure relativo all'account delle API Servizi cognitivi.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 90ab36389ceac2e8aad12332db433732525c62f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851828"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Guida introduttiva: Creare una knowledge base in QnA Maker con Java

Questa guida introduttiva illustra come creare a livello di codice una knowledge base QnA Maker di esempio. QnA Maker estrae automaticamente domande e risposte da contenuto semistrutturato, come le domande frequenti, delle [origini dati](../Concepts/knowledge-base.md). Il modello per la knowledge base è definito nel codice JSON inviato nel corpo della richiesta API.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Creare la knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Ottenere i dettagli dell'operazione](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Reference documentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Esempio](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) di documentazione di riferimento per Java

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Go 1.10.1](https://golang.org/dl/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.

Il [codice di esempio](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) è disponibile nel repository GitHub per QnA Maker con Java.

## <a name="create-a-knowledge-base-file"></a>Creare un file di knowledge base

Creare un file denominato `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `CreateKB.java` inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Aggiungere le costanti obbligatorie
Dopo le dipendenze obbligatorie precedenti, aggiungere alla classe `CreateKB` le costanti obbligatorie per accedere a QnA Maker.

È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e il nome della risorsa, selezionare **Avvio rapido** nel portale di Azure per la risorsa QnA Maker.

Impostare i valori seguenti:

* `<your-qna-maker-subscription-key>` - La **chiave** è una stringa di 32 caratteri ed è disponibile nella risorsa QnA Maker, nella pagina Avvio rapido del portale di Azure. Non è la stessa chiave dell'endpoint di previsione.
* `<your-resource-name>` - Il **nome della risorsa** viene usato per creare l'URL dell'endpoint di creazione nel formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Non è lo stesso URL usato per eseguire query sull'endpoint di previsione.

Non è necessario aggiungere la parentesi graffa finale per terminare la classe; si trova nel frammento di codice finale alla fine di questa guida introduttiva.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Aggiungere le classi di definizione del modello di knowledge base
Dopo le costanti, aggiungere le classi e le funzioni seguenti all'interno della classe `CreateKB` per serializzare l'oggetto di definizione del modello in JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Aggiungere funzioni di supporto

A questo punto, aggiungere le funzioni di supporto seguenti nella classe `CreateKB`.

1. Aggiungere la funzione seguente per stampare JSON in un formato leggibile:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Aggiungere la classe seguente per gestire la risposta HTTP:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Aggiungere il metodo seguente per effettuare una richiesta POST alle API QnA Maker. `Ocp-Apim-Subscription-Key` è la chiave del servizio QnA Maker usata per l'autenticazione.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Aggiungere il metodo seguente per effettuare una richiesta GET alle API QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Aggiungere un metodo per creare la knowledge base
Aggiungere il metodo seguente per creare la knowledge base chiamando il metodo POST.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Questa chiamata API restituisce una risposta JSON che include l'ID operazione. Usare l'ID operazione per determinare se la knowledge base è stata creata.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Aggiungere un metodo per ottenere lo stato
Aggiungere il metodo seguente per verificare lo stato di creazione.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Ripetere la chiamata fino a quando l'esito non è positivo o negativo:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Aggiungere un metodo Main
Il metodo Main crea la knowledge base e quindi esegue il polling dello stato. L'ID operazione viene restituito nel campo di intestazione della risposta POST **Location** e quindi viene usato come parte della route nella richiesta GET. Il ciclo `while` ritenta il polling dello stato se non è completo.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Compilare ed eseguire il programma

1. Verificare che la libreria gson si trovi nella directory `./libs`. Alla riga di comando compilare il file `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Per eseguire il programma, immettere il comando seguente a una riga di comando. La richiesta per creare la knowledge base verrà inviata all'API QnA Maker e verrà eseguito il polling dei risultati ogni 30 secondi. Ogni risposta viene stampata nella finestra della console.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Dopo aver creato la knowledge base, è possibile visualizzarla nella pagina [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Knowledge base personali) del portale di QnA Maker.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)