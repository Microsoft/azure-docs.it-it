---
title: Creare la prima funzione nel portale di Azure
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite il portale di Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: e37a14b2634395d7f31565d6d5a6f6ec96a4a2f1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954525"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creare la prima funzione nel portale di Azure

Funzioni di Azure consente di eseguire il codice in un ambiente serverless senza dover prima creare una macchina virtuale (VM) o pubblicare un'applicazione Web. Questo articolo illustra come usare funzioni di Azure per creare una funzione trigger HTTP "Hello World" nel portale di Azure.

Si consiglia di [sviluppare le funzioni in locale](functions-develop-local.md) e di pubblicarle in un'app per le funzioni in Azure.  
Usare uno dei collegamenti seguenti per iniziare a usare l'ambiente di sviluppo locale e la lingua scelti:

| Visual Studio Code | Terminale/prompt dei comandi | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Introduzione a C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Introduzione a Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Introduzione a JavaScript](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Introduzione a PowerShell](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Introduzione a Python](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Introduzione a C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Introduzione a Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Introduzione a JavaScript](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Introduzione a PowerShell](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Introduzione a Python](./create-first-function-cli-python.md) | [Introduzione a C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Successivamente, creare una funzione nella nuova app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione Trigger HTTP

1. Selezionare **Funzioni** nel menu a sinistra della finestra **Funzioni** e quindi **Aggiungi** nel menu in alto. 
 
1. Nella finestra **Nuova funzione**, selezionare **Trigger HTTP**.

    ![Scegliere la funzione Trigger HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. Nella finestra **Nuova funzione**, accettare il nome predefinito per la **nuova funzione** oppure immettere un nuovo nome. 

1. Scegliere **Anonimo** nell'elenco a discesa **Livello di autorizzazione**, quindi selezionare **Crea funzione**.

    A questo punto Azure crea la funzione Trigger HTTP. Ora è possibile eseguire la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

1. Nella nuova funzione Trigger HTTP, selezionare **Codice + test** dal menu a sinistra, quindi selezionare **Ottieni URL funzione** dal menu in alto.

    ![Selezionare Ottieni URL funzione](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. Nella finestra di dialogo **Ottieni URL funzione**, selezionare **Predefinito** dall'elenco a discesa, quindi l'icona **Copia negli Appunti**. 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Incollare l'URL della funzione nella barra degli indirizzi del browser. Aggiungere il valore della stringa di query `&name=<your_name>` alla fine dell'URL e premere INVIO per eseguire la richiesta. 

    L'esempio seguente mostra la risposta nel browser:

    ![Risposta della funzione nel browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    L'URL della richiesta include una chiave necessaria per impostazione predefinita per accedere a una funzione tramite HTTP.

1. Quando viene eseguita la funzione, vengono scritte nei log informazioni di traccia. Per visualizzare l'output di traccia, tornare alla pagina **Codice + test** nel portale ed espandere la freccia **Log** nella parte inferiore della pagina.

   ![Visualizzatore log di Funzioni nel portale di Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
