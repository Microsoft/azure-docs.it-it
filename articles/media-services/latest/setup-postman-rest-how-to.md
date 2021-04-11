---
title: Configurare il post per l'API REST di servizi multimediali di Azure V3
description: In questo articolo viene illustrato come configurare Postman in modo da usarlo per chiamare le API REST di Servizi multimediali di Azure (AMS).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1266e10f6d8bf69c6e72a236ecde27623ad1cf12
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282043"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Configurare il post per le chiamate all'API REST di servizi multimediali V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In questo articolo viene illustrato come configurare **Postman** in modo da usarlo per chiamare le API REST di Servizi multimediali di Azure (AMS). L'articolo illustra come importare i file di ambiente e di raccolta in **Postman**. La raccolta contiene definizioni raggruppate delle richieste HTTP che chiamano le API REST di Servizi multimediali di Azure (AMS). Il file di ambiente contiene le variabili usate dalla raccolta.

Prima di iniziare lo sviluppo, vedere [sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](./account-create-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali. 
- Ottenere le informazioni necessarie per [accedere alle API](./access-api-howto.md)
- Installare il client REST di [Postman](https://www.getpostman.com/) per eseguire le API REST mostrate in alcune delle esercitazioni REST di AMS. 

    Si sta usando **Postman** ma si può usare qualsiasi strumento REST. Altre alternative possibili: **Visual Studio Code** con il plug-in REST o **Telerik Fiddler**. 

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Scaricare i file Postman

Clonare un repository di GitHub che contiene i file di raccolta e ambiente Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurare Postman

### <a name="configure-the-environment"></a>Configurare l'ambiente 

1. Aprire l'app **Postman**.
2. A destra dello schermo, selezionare l'opzione **Manage environment** (Gestisci ambiente).

    ![Gestire l'ambiente](./media/develop-with-postman/postman-import-env.png)
4. Dalla finestra di dialogo **Manage environment** (Gestisci ambiente), fare clic su **Import** (Importa).
2. Individuare il file `Azure Media Service v3 Environment.postman_environment.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Viene aggiunto l'ambiente **Azure Media Service v3 Environment**.

    > [!Note]
    > Aggiornare le variabili di accesso con i valori ottenuti dalla sezione **Accedere all'API di Servizi multimediali** precedente.

7. Fare doppio clic sul file selezionato e immettere i valori ottenuti seguendo la procedura per l'accesso all'API.
8. Chiudere la finestra di dialogo.
9. Selezionare l'ambiente **Azure Media Service v3 Environment** nell'elenco a discesa.

    ![Scegliere l'ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurare la raccolta

1. Fare clic su **Import** (Importa) per importare il file di raccolta.
1. Individuare il file `Media Services v3.postman_collection.json` scaricato quando è stato clonato `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Scegliere il file **Media Services v3.postman_collection.json**.

    ![Importare un file](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Ottenere il token di Azure AD 

Prima di iniziare la modifica delle risorse in servizi multimediali di Microsoft Azure v3 è necessario ottenere e impostare token di Azure AD per l'autenticazione dell'entità servizio.

1. Nella finestra sinistra dell'app Postman selezionare "Step 1: Get AAD Auth token" (Passaggio 1: Ottenere un token di autenticazione AAD).
2. Selezionare quindi "Get Azure AD Token for Service Principal Authentication" (Ottieni token Azure AD per autenticazione basata su entità servizio).
3. Fare clic su **Invia**.

    Viene inviata l'operazione **POST** seguente.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La risposta viene restituita con il token e imposta la variabile di ambiente "AccessToken" sul valore del token.  

    ![Ottenere il token AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi 

* Se l'applicazione ha esito negativo con "HTTP 504: Timeout gateway", assicurarsi che la variabile di percorso non sia stata impostata in modo esplicito su un valore diverso da quello previsto per l'account di servizi multimediali. 
* Se viene ricevuto un errore "account non trovato", verificare anche che la proprietà location nel messaggio JSON Body sia impostata sul percorso in cui si trova l'account di servizi multimediali. 

## <a name="see-also"></a>Vedi anche

- [Creare filtri con Servizi multimediali - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST basate su Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Passaggi successivi

- [Streaming di file con REST](stream-files-tutorial-with-rest.md).  
- [Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - REST](stream-files-tutorial-with-rest.md)
