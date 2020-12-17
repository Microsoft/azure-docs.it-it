---
title: "Esercitazione: Personalizzare l'interfaccia utente"
titleSuffix: Azure AD B2C
description: Seguire questa esercitazione per informazioni su come personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C mediante il portale di Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 036e9e6bf58d837c698a6092770ef581f77bc20c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108247"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Esercitazione: Personalizzare l'interfaccia delle esperienze utente in Azure Active Directory B2C

Per esperienze utente più comuni, come l'iscrizione, l'accesso e la modifica del profilo, è possibile usare i [flussi utente](user-flow-overview.md) di Azure Active Directory B2C (Azure AD B2C). Le informazioni fornite in questa esercitazione spiegano come [personalizzare l'interfaccia utente](customize-ui-with-html.md) di queste esperienze usando i propri file HTML e CSS.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare file di personalizzazione dell'interfaccia utente
> * Aggiornare il flusso utente in modo da usare i file
> * Testare l'interfaccia utente personalizzata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="create-customization-files"></a>Creare i file di personalizzazione

Occorre creare un account di archiviazione e un contenitore di Azure e quindi inserire i file HTML e CSS di base nel contenitore.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

I file possono essere archiviati in diversi modi, ma per questa esercitazione si userà l'[archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene la sottoscrizione. Questa directory è diversa da quella che contiene il tenant Azure B2C.
3. Scegliere Tutti i servizi nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Account di archiviazione**.
4. Selezionare **Aggiungi**.
5. In **Gruppo di risorse** selezionare **Crea nuovo**, immettere un nome per il nuovo gruppo di risorse e quindi fare clic su **OK**.
6. Immettere il nome dell'account di archiviazione. Il nome deve essere univoco in Azure, deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
7. Selezionare il percorso dell'account di archiviazione o accettare quello predefinito.
8. Accettare tutti gli altri valori predefiniti, selezionare **Rivedi e crea** e fare clic su **Crea**.
9. Dopo aver creato l'account di archiviazione, selezionare **Vai alla risorsa**.

### <a name="create-a-container"></a>Creare un contenitore

1. Nella pagina Panoramica dell'account di archiviazione selezionare **BLOB**.
2. Selezionare **Contenitore**, immettere un nome per il contenitore, selezionare **BLOB (accesso in lettura anonimo solo per i BLOB)** e quindi fare clic su **OK**.

### <a name="enable-cors"></a>Abilitare CORS

 Il codice Azure AD B2C in un browser usa un approccio moderno e standard per caricare il contenuto personalizzato da un URL specificato in un flusso utente. La funzionalità Condivisione di risorse tra le origini (CORS) abilita la richiesta di un numero limitato di risorse in una pagina Web da parte di altri domini.

1. Nel menu selezionare **CORS**.
2. Per **Origini consentite** immettere `https://your-tenant-name.b2clogin.com`. Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio: `https://fabrikam.b2clogin.com`. È necessario usare solo lettere minuscole quando si immette il nome del tenant.
3. Per **Metodi consentiti** selezionare `GET`, `PUT` e `OPTIONS`.
4. Per **Intestazioni consentite** immettere un asterisco (*).
5. Per **Intestazioni esposte** immettere un asterisco (*).
6. Per **Età massima** immettere 200.

    ![Pagina di configurazione di CORS nell'archiviazione BLOB di Azure nel portale di Azure](./media/tutorial-customize-ui/enable-cors.png)

5. Fare clic su **Salva**.

### <a name="create-the-customization-files"></a>Creare i file di personalizzazione

Per personalizzare l'interfaccia utente dell'esperienza di iscrizione, creare innanzitutto un file HTML e un file CSS semplici. È possibile configurare l'HTML nel modo che si preferisce, purché abbia un elemento **div** con un identificatore `api`. Ad esempio: `<div id="api"></div>`. Azure AD B2C inserisce gli elementi nel contenitore `api` quando viene visualizzata la pagina.

1. In una cartella locale creare il file seguente e sostituire `your-storage-account` con il nome dell'account di archiviazione e `your-container` con il nome del contenitore creato. Ad esempio: `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    La pagina può essere progettata nel modo che si preferisce, ma l'elemento div **api** è obbligatorio per qualsiasi file di personalizzazione HTML creato.

3. Salvare il file con il nome *custom-ui.html*.
4. Creare il seguente codice CSS semplice, che allinea tutti gli elementi al centro della pagina di iscrizione o accesso, inclusi gli elementi inseriti da Azure AD B2C.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Salvare il file con il nome *style.css*.

### <a name="upload-the-customization-files"></a>Caricare i file di personalizzazione

In questa esercitazione i file creati devono essere archiviati nell'account di archiviazione, in modo che Azure AD B2C possa accedervi.

1. Scegliere **Tutti i servizi** nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Account di archiviazione**.
2. Selezionare l'account di archiviazione creato, selezionare **BLOB** e quindi selezionare il contenitore creato.
3. Selezionare **Carica**, individuare e selezionare il file *custom-ui.html* e quindi fare clic su **Carica**.

    ![Pagina Carica BLOB nel portale con il pulsante Carica e File evidenziati](./media/tutorial-customize-ui/upload-blob.png)

4. Copiare l'URL del file che era stato caricato per usarlo successivamente nell'esercitazione.
5. Ripetere i passaggi 3 e 4 per il file *style.css*.

## <a name="update-the-user-flow"></a>Aggiornare il flusso utente

1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
2. Selezionare **Flussi utente** e quindi il flusso utente *B2C_1_signupsignin1*.
3. Selezionare **Layout di pagina** e quindi in **Pagina unificata per l'iscrizione o l'accesso** fare clic su **Sì** per **Usa contenuto di pagina personalizzato**.
4. In **URI della pagina personalizzata** immettere l'URI del file *custom-ui.html* registrato in precedenza.
5. Nella parte superiore della pagina selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Dal tenant di Azure AD B2C selezionare **Flussi utente** e quindi il flusso utente *B2C_1_signupsignin1*.
2. Nella parte superiore della pagina, fare clic su **Esegui il flusso utente**.
3. Fare clic sul pulsante **Esegui il flusso utente**.

    ![Pagina Esegui il flusso utente per il flusso utente di iscrizione o accesso](./media/tutorial-customize-ui/run-user-flow.png)

    Dovrebbe essere visualizzata una pagina simile all'esempio seguente, con gli elementi allineati al centro in base al file CSS creato:

    ![Web browser che visualizza la pagina di iscrizione o di accesso con elementi dell'interfaccia utente personalizzati](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare file di personalizzazione dell'interfaccia utente
> * Aggiornare il flusso utente in modo da usare i file
> * Testare l'interfaccia utente personalizzata

> [!div class="nextstepaction"]
> [Personalizzare l'interfaccia utente in Azure Active Directory B2C](customize-ui-with-html.md)
