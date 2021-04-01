---
title: 'Esercitazione: Autenticazione con Funzioni di Azure - Azure SignalR'
description: Questa esercitazione illustra come autenticare i client del servizio Azure SignalR per il binding di Funzioni di Azure
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150909"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Esercitazione: Autenticazione del servizio Azure SignalR con Funzioni di Azure

Un'esercitazione dettagliata per creare una chat room con autenticazione e messaggistica privata usando Funzioni di Azure, Autenticazione servizio app e il servizio SignalR.

## <a name="introduction"></a>Introduzione

### <a name="technologies-used"></a>Tecnologie usate

* [Funzioni di Azure](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): API di back-end per l'autenticazione degli utenti e l'invio di messaggi di chat
* [Servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): per la trasmissione di nuovi messaggi ai client di chat connessi
* [Archiviazione di Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): per l'hosting del sito Web statico per l'interfaccia utente del client di chat

### <a name="prerequisites"></a>Prerequisiti

Per la compilazione in questa esercitazione è richiesto il software seguente.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (versione 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (versione 2.x, richiesta per le estensioni di Funzioni)
* [Strumenti di base di Funzioni di Azure](https://github.com/Azure/azure-functions-core-tools) (versione 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) con le estensioni seguenti
  * [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per usare Funzioni di Azure in VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) per rendere disponibili le pagine Web in locale per il test

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Andare al [portale di Azure](https://portal.azure.com/) e accedere con le proprie credenziali.

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="create-an-azure-signalr-service-instance"></a>Creare un'istanza del servizio Azure SignalR

L'app Funzioni di Azure verrà compilata e testata in locale. L'app accederà a un'istanza del servizio SignalR in Azure che deve essere già stata creata.

1. Fare clic sul pulsante **Crea una risorsa** ( **+** ) per creare una nuova risorsa di Azure.

1. Cercare il **servizio SignalR** e selezionarlo. Scegliere **Crea**.

    ![Nuovo servizio SignalR](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Immettere le seguenti informazioni.

    | Nome | valore |
    |---|---|
    | Nome risorsa | Nome univoco per l'istanza del servizio SignalR |
    | Resource group | Creare un nuovo gruppo di risorse con un nome univoco |
    | Location | Selezionare una località vicina |
    | Piano tariffario | Livello gratuito |

1. Scegliere **Crea**.

1. Dopo che l'istanza è stata distribuita, aprirla nel portale e individuare la relativa pagina delle impostazioni. Modificare l'impostazione della modalità Servizio su *Serverless*.

    ![Modalità del servizio SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="initialize-the-function-app"></a>Inizializzare l'app per le funzioni

### <a name="create-a-new-azure-functions-project"></a>Creare un nuovo progetto Funzioni di Azure

1. In una nuova finestra di VS Code usare `File > Open Folder` nel menu per creare e aprire una cartella vuota in un percorso appropriato. Questa sarà la cartella di progetto principale per l'applicazione da compilare.

1. Usando l'estensione Funzioni di Azure in VS Code, inizializzare un'app per le funzioni nella cartella di progetto principale.
   1. Aprire il riquadro comandi in VS Code selezionando **Visualizza > Riquadro comandi** dal menu (scelta rapida `Ctrl-Shift-P`, macOS `Cmd-Shift-P`).
   1. Cercare il comando **Funzioni di Azure: Crea nuovo progetto** e selezionarlo.
   1. Dovrebbe essere visualizzata la cartella di progetto principale. Selezionarla o usare "Sfoglia" per individuarla.
   1. Nel prompt per la scelta di un linguaggio selezionare **JavaScript**.

      ![Creare un'app per le funzioni](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Installare le estensioni dell'app per le funzioni

Questa esercitazione usa le associazioni di Funzioni di Azure per interagire con il servizio Azure SignalR. Come la maggior parte delle altre associazioni, quelle del servizio SignalR sono disponibili come estensione da installare tramite l'interfaccia della riga di comando di Strumenti di base di Funzioni di Azure prima di poter essere usate.

1. Aprire un terminale in Visual Studio Code selezionando **Visualizza > Terminale** dal menu (Ctrl-\`).

1. Verificare che la cartella di progetto principale sia la directory corrente.

1. Installare l'estensione dell'app per le funzioni del servizio SignalR.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Quando si esegue e si sottopone a debug il runtime di Funzioni di Azure in locale, le impostazioni dell'applicazione vengono lette da **local.settings.json**. Aggiornare il file con la stringa di connessione dell'istanza del servizio SignalR creata in precedenza.

1. In VS Code selezionare **local.settings.json** nel riquadro Esplora per aprirlo.

1. Sostituire il contenuto del file con il seguente.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Immettere la stringa di connessione del servizio Azure SignalR in un'impostazione denominata `AzureSignalRConnectionString`. Ottenere il valore dalla pagina **Chiavi** nella risorsa del servizio Azure SignalR nel portale di Azure. Può essere usata la stringa di connessione primaria o secondaria.
   * L'impostazione `WEBSITE_NODE_DEFAULT_VERSION` non viene usata in locale, ma è obbligatoria per la distribuzione in Azure.
   * La sezione `Host` configura le impostazioni CORS e della porta per l'host di Funzioni locale (questa impostazione non ha effetto durante l'esecuzione in Azure).

       > [!NOTE]
       > Live Server è in genere configurato per rendere disponibili contenuti da `http://127.0.0.1:5500`. Se si rileva che Live Server usa un URL diverso oppure l'utente usa un server HTTP diverso, modificare l'impostazione `CORS` per riflettere l'origine corretta.

     ![Ottenere la chiave del servizio SignalR](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Salvare il file.

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Creare una funzione per autenticare gli utenti per il servizio SignalR

Quando l'app di chat viene aperta per la prima volta nel browser, richiede credenziali di connessione valide per connettersi al servizio Azure SignalR. Si creerà una funzione attivata tramite HTTP denominata *negotiate* nell'app per le funzioni per restituire queste informazioni di connessione.

> [!NOTE]
> Questa funzione deve essere denominata *negotiate* perché il client SignalR richiede un endpoint che termina con `/negotiate`.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Creare il comando Funzione** .

1. Quando richiesto, specificare le informazioni seguenti.

    | Nome | valore |
    |---|---|
    | Cartella dell'app per le funzioni | Selezionare la cartella di progetto principale |
    | Modello | Trigger HTTP |
    | Nome | negotiate |
    | Livello di autorizzazione | Anonima |

    Viene creata una cartella denominata **negotiate** che contiene la nuova funzione.

1. Aprire **negotiate/function.json** per configurare i binding per la funzione. Modificare il contenuto del file come segue. In questo modo verrà aggiunta un'associazione di input che genera credenziali valide per la connessione di un client a un hub del servizio Azure SignalR denominato `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    La proprietà `userId` nell'associazione `signalRConnectionInfo` viene usata per creare una connessione autenticata al servizio SignalR. Lasciare vuota la proprietà per lo sviluppo locale. Verrà usata quando si distribuirà l'app per le funzioni in Azure.

1. Aprire **negotiate/index.js** per visualizzare il corpo della funzione. Modificare il contenuto del file come segue.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Questa funzione accetta le informazioni di connessione a SignalR dall'associazione di input e le restituisce al client nel corpo della risposta HTTP. Il client SignalR userà queste informazioni per connettersi all'istanza del servizio SignalR.

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="create-a-function-to-send-chat-messages"></a>Creare una funzione per inviare messaggi di chat

L'app Web richiede anche un'API HTTP per inviare i messaggi di chat. Si creerà una funzione attivata tramite HTTP denominata *SendMessage* che invia messaggi a tutti i client connessi tramite il servizio SignalR.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Creare il comando Funzione** .

1. Quando richiesto, specificare le informazioni seguenti.

    | Nome | valore |
    |---|---|
    | Cartella dell'app per le funzioni | Selezionare la cartella di progetto principale |
    | Modello | Trigger HTTP |
    | Nome | SendMessage |
    | Livello di autorizzazione | Anonima |

    Viene creata una cartella denominata **SendMessage** contenente la nuova funzione.

1. Aprire **SendMessage/function.json** per configurare le associazioni per la funzione. Modificare il contenuto del file come segue.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Vengono apportate due modifiche alla funzione originale:
    * La route viene modificata in `messages` e il trigger HTTP viene limitato al metodo HTTP **POST**.
    * Viene aggiunto un binding di output per il servizio SignalR che invia un messaggio restituito dalla funzione a tutti i client connessi a un hub del servizio SignalR denominato `chat`.

1. Salvare il file.

1. Aprire **SendMessage/index.js** per visualizzare il corpo della funzione. Modificare il contenuto del file come segue.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Questa funzione accetta il corpo della richiesta HTTP e lo invia ai client connessi al servizio SignalR, richiamando una funzione denominata `newMessage` in ogni client.

    La funzione può leggere l'identità del mittente e può accettare un valore *recipient* nel corpo del messaggio per consentire di inviare un messaggio privatamente a un singolo utente. Queste funzionalità verranno usate più avanti nell'esercitazione.

1. Salvare il file.

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Creare ed eseguire l'interfaccia utente Web del client di chat

L'interfaccia utente dell'applicazione di chat è una semplice applicazione a pagina singola creata con il framework Vue JavaScript, che verrà ospitata separatamente dall'app per le funzioni. In locale, si eseguirà l'interfaccia Web usando l'estensione Live Server di VS Code.

1. In VS Code creare una nuova cartella denominata **content** alla radice della cartella di progetto principale.

1. Nella cartella **content** creare un nuovo file denominato **index.html**.

1. Copiare e incollare il contenuto di **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

1. Salvare il file.

1. Premere **F5** per eseguire l'app per le funzioni in locale e collegare un debugger.

1. Con **index.html** aperto, avviare Live Server aprendo il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`) e selezionando **Live Server: Apri con Live Server**. Live Server aprirà l'applicazione in un browser.

1. L'applicazione viene aperta. Immettere un messaggio nella casella di chat e premere INVIO. Aggiornare l'applicazione per vedere i nuovi messaggi. Non essendo stata configurata alcuna autenticazione, tutti i messaggi verranno inviati come "anonimi".

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="deploy-to-azure-and-enable-authentication"></a>Distribuire in Azure e abilitare l'autenticazione

L'app per le funzioni e l'applicazione di chat sono state eseguite in locale. Ora verranno distribuite in Azure e si abiliteranno l'autenticazione e la messaggistica privata nell'applicazione.

### <a name="log-into-azure-with-vs-code"></a>Accedere ad Azure con VS Code

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Azure: Accedi**.

1. Seguire le istruzioni per completare il processo di accesso nel browser.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione di Azure è richiesto da un'app per le funzioni in esecuzione in Azure. Verrà anche ospitata la pagina Web per l'interfaccia utente della chat usando la funzionalità dei siti Web statici di Archiviazione di Azure.

1. Nel portale di Azure fare clic sul pulsante **Crea una risorsa** ( **+** ) per creare una nuova risorsa di Azure.

1. Selezionare la categoria **Archiviazione**, quindi selezionare **Account di archiviazione**.

1. Immettere le seguenti informazioni.

    | Nome | Valore |
    |---|---|
    | Subscription | Selezionare la sottoscrizione contenente l'istanza del servizio SignalR |
    | Resource group | Selezionare lo stesso gruppo di risorse |
    | Nome risorsa | Un nome univoco per l'account di archiviazione |
    | Location | Selezionare la stessa posizione delle altre risorse |
    | Prestazioni | Standard |
    | Tipologia account | Archiviazione V2 (utilizzo generico V2) |
    | Replica | Archiviazione con ridondanza locale |
    | Livello di accesso | Accesso frequente |

1. Fare clic su **Rivedi e crea**, quindi su **Crea**.

### <a name="configure-static-websites"></a>Configurare i siti Web statici

1. Dopo aver creato l'account di archiviazione, aprirlo nel portale di Azure.

1. Selezionare **Sito Web statico**.

1. Selezionare **Abilitato** per abilitare la funzionalità dei siti Web statici.

1. In **Nome del documento di indice** immettere *index.html*.

1. Fare clic su **Save**.

1. Viene visualizzato un **endpoint primario**. Prendere nota di questo valore. Sarà necessario per configurare l'app per le funzioni.

### <a name="configure-function-app-for-authentication"></a>Configurare l'app per le funzioni per l'autenticazione

Finora, l'app di chat ha funzionato in modo anonimo. In Azure si userà [Autenticazione servizio app](../app-service/overview-authentication-authorization.md) per autenticare l'utente. L'ID utente o il nome utente dell'utente autenticato può essere passato all'associazione *SignalRConnectionInfo* per generare informazioni di connessione autenticate come utente.

Quando si invia un messaggio, l'app può decidere se inviarlo a tutti i client connessi o solo ai client che sono stati autenticati per un determinato utente.

1. In VS Code aprire **negotiate/function.json**.

1. Inserire un'[espressione di associazione](../azure-functions/functions-triggers-bindings.md) nella proprietà *userId* dell'associazione *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Questa operazione imposta il valore sul nome utente dell'utente autenticato. L'aspetto dell'attributo dovrebbe essere simile al seguente.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Salvare il file.


### <a name="deploy-function-app-to-azure"></a>Distribuire l'app per le funzioni in Azure

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`) e selezionare **Funzioni di Azure: Deploy to Function App** (Distribuisci nell'app per le funzioni).

1. Quando richiesto, specificare le informazioni seguenti.

    | Nome | valore |
    |---|---|
    | Cartella per la distribuzione | Selezionare la cartella di progetto principale |
    | Sottoscrizione | Selezionare la propria sottoscrizione |
    | App per le funzioni | Selezionare **Crea nuova app per le funzioni** |
    | Nome dell'app per le funzioni | Immettere un nome univoco |
    | Resource group | Selezionare lo stesso gruppo di risorse dell'istanza del servizio SignalR |
    | Account di archiviazione | Selezionare l'account di archiviazione creato in precedenza |

    Viene creata una nuova app per le funzioni in Azure e la distribuzione ha inizio. Attendere il completamento della distribuzione.

### <a name="upload-function-app-local-settings"></a>Caricare le impostazioni locali dell'app per le funzioni

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Carica impostazioni locali**.

1. Quando richiesto, specificare le informazioni seguenti.

    | Nome | valore |
    |---|---|
    | File di impostazioni locali | local.settings.json |
    | Sottoscrizione | Selezionare la propria sottoscrizione |
    | App per le funzioni | Selezionare l'app per le funzioni distribuita in precedenza |

Le impostazioni locali vengono caricate nell'app per le funzioni in Azure. Se viene richiesto di sovrascrivere le impostazioni esistenti, selezionare **Sì a tutti**.


### <a name="enable-app-service-authentication"></a>Abilitare l'autenticazione del servizio app

Autenticazione servizio app supporta l'autenticazione con Azure Active Directory, Facebook, Twitter, un account Microsoft e Google.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Apri nel portale**.

1. Selezionare la sottoscrizione e il nome dell'app per le funzioni per aprire l'app per le funzioni nel portale di Azure.

1. Nell'app per le funzioni che è stata aperta nel portale, individuare la scheda **Funzionalità della piattaforma** e selezionare **Autenticazione/autorizzazione**.

1. **Abilitare** Autenticazione servizio app.

1. In **Azione da eseguire quando la richiesta non è autenticata** selezionare "Accedi con {provider di autenticazione selezionato in precedenza}".

1. In **URL di reindirizzamento esterni consentiti** immettere l'URL dell'endpoint Web primario dell'account di archiviazione annotato in precedenza.

1. Seguire la documentazione per il proprio provider di accesso per completare la configurazione.

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [Account Microsoft](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>Aggiornare l'app Web

1. Nel portale di Azure passare alla pagina di panoramica dell'app per le funzioni.

1. Copiare l'URL dell'app per le funzioni.

    ![Ottenere l'URL](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. In VS Code aprire **index.html** e sostituire il valore di `apiBaseUrl` con l'URL dell'app per le funzioni.

1. L'applicazione può essere configurata con l'autenticazione usando Azure Active Directory, Facebook, Twitter, un account Microsoft o Google. Selezionare il provider di autenticazione da usare impostando il valore di `authProvider`.

1. Salvare il file.

### <a name="deploy-the-web-application-to-blob-storage"></a>Distribuire l'applicazione Web in Archiviazione BLOB

L'applicazione Web verrà ospitata usando la funzionalità dei siti Web statici di Archiviazione BLOB di Azure.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Archiviazione di Azure: Distribuisci in sito Web statico**.

1. Immettere i valori seguenti:

    | Nome | Valore |
    |---|---|
    | Subscription | Selezionare la propria sottoscrizione |
    | Account di archiviazione | Selezionare l'account di archiviazione creato in precedenza |
    | Cartella per la distribuzione | Selezionare **Sfoglia** e quindi la cartella *content* |

A questo punto, i file nella cartella *content* saranno stati distribuiti nel sito Web statico.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Abilitare la condivisione di risorse tra le origini (CORS) per l'app per le funzioni

Anche se è disponibile un'impostazione CORS in **local.settings.json**, non è propagata all'app per le funzioni in Azure. È necessario impostarla separatamente.

1. Aprire l'app per le funzioni nel portale di Azure.

1. Nella scheda **Funzionalità della piattaforma** selezionare **CORS**.

    ![Trovare CORS](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. Nella sezione *Origini consentite* aggiungere una voce con l'*endpoint primario* del sito Web statico come valore (rimuovere la */* finale).

1. Affinché JavaScript SDK di SignalR richiami l'app per le funzioni da un browser, è necessario abilitare il supporto per le credenziali in CORS. Selezionare la casella di controllo "Abilita Access-Control-Allow-Credentials".

    ![Abilita Access-Control-Allow-Credentials](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Per salvare le impostazioni CORS, fare clic su **Salva**.

### <a name="try-the-application"></a>Provare l'applicazione

1. In un browser passare all'endpoint Web primario dell'account di archiviazione.

1. Selezionare **Accesso** per eseguire l'autenticazione con il provider di autenticazione scelto.

1. Inviare messaggi pubblici digitandoli nella casella di chat principale.

1. Inviare messaggi privati facendo clic su un nome utente nella cronologia delle chat. Solo il destinatario selezionato riceverà i messaggi.

Congratulazioni! È stata distribuita un'app di chat serverless in tempo reale.

![Demo](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questa esercitazione, eliminare il gruppo di risorse usando il portale di Azure.

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Funzioni di Azure con il servizio Azure SignalR. Sono disponibili altre informazioni sulla creazione di applicazioni serverless in tempo reale con le associazioni del servizio SignalR per Funzioni di Azure.

> [!div class="nextstepaction"]
> [Creare app in tempo reale con Funzioni di Azure](signalr-concept-azure-functions.md)

[Problemi? Segnalarli](https://aka.ms/asrs/qsauth).