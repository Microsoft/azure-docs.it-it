---
title: 'Guida introduttiva: pubblicare la Knowledge base, REST, Java-QnA Maker'
description: In questo avvio rapido basato su Java REST viene pubblicata la knowledge base e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: e8df1fcc61d6b838c08f8daca9298e73820590f5
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342698"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Guida introduttiva: Pubblicare una knowledge base in QnA Maker con Java

Questa guida introduttiva basata su REST illustra la procedura di pubblicazione della knowledge base (KB) a livello di codice. Con la pubblicazione viene eseguito il push dell'ultima versione della knowledge base in un indice dedicato di Ricerca cognitiva di Azure e viene creato un endpoint che può essere chiamato nell'applicazione o nel chatbot.

In questa guida introduttiva viene chiamata l'API QnA Maker seguente:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Pubblicazione): con questa API non sono richieste informazioni nel corpo della richiesta.

## <a name="prerequisites"></a>Prerequisiti

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* In questo esempio si usa il [client HTTP](https://hc.apache.org/httpcomponents-client-ga/) Apache di HTTP Components. È necessario aggiungere le librerie client HTTP Apache seguenti al progetto:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Per recuperare la chiave e l'endpoint (che include il nome della risorsa), selezionare **Avvio rapido** per la risorsa nel portale di Azure.
* ID della knowledge base (KB) di QnA Maker trovato nell'URL nel parametro della stringa di query `kbid` come mostrato di seguito.

    ![ID della knowledge base di QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se non si ha ancora una knowledge base, è possibile crearne una di esempio da usare per questa guida introduttiva: [Creare una nuova knowledge base](create-new-kb-csharp.md).

> [!NOTE]
> I file della soluzione completa sono disponibili nel [repository GitHub **Azure-Samples/cognitive-Services-qnamaker-Java** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Creare un file Java

Aprire VSCode e creare un nuovo file denominato `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Aggiungere le dipendenze richieste

All'inizio di `PublishKB.java`, sopra la classe, inserire le righe seguenti per aggiungere le dipendenze necessarie al progetto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Creare la classe PublishKB con il metodo main

Dopo le dipendenze aggiungere la classe seguente:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Aggiungere le costanti obbligatorie

Nel metodo **Main** aggiungere le costanti obbligatorie per accedere QnA Maker. Sostituire i valori con quelli personalizzati.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Aggiungere la richiesta POST per pubblicare la knowledge base

Dopo le costanti richieste, aggiungere il codice seguente, che effettua una richiesta HTTPS all'API di QnA Maker per pubblicare una knowledge base e riceve la risposta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

In caso di pubblicazione riuscita la chiamata API restituisce uno stato 204 senza alcun contenuto nel corpo della risposta. Il codice aggiunge il contenuto per le risposte con stato 204.

Per qualsiasi altra risposta, tale risposta viene restituita senza variazioni.

## <a name="build-and-run-the-program"></a>Compilare ed eseguire il programma

Compilare ed eseguire il programma dalla riga di comando. Verrà inviata automaticamente la richiesta all'API di QnA Maker, quindi verrà visualizzato l'output nella finestra della console.

1. Compilare il file:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Eseguire il file:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo la pubblicazione della knowledge base, è necessario l'[URL dell'endpoint per generare una risposta](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)