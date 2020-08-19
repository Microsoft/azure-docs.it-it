---
title: "Guida introduttiva: Configurare l'accesso per un'app Web ASP.NET"
titleSuffix: Azure AD B2C
description: In questo Avvio rapido eseguire un'app Web ASP.NET di esempio che usa Azure Active Directory B2C per consentire l'accesso all'account.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 379ea9a0c2a49e92fa1c4882ea6e783b3b91e32f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163918"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Guida introduttiva: Configurare l'accesso per un'applicazione ASP.NET tramite Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) consente la gestione delle identità del cloud per garantire la protezione costante dell'applicazione, delle attività aziendali e dei clienti. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social networking e account aziendali usando protocolli standard aperti. In questa guida introduttiva si usa un'applicazione ASP.NET per eseguire l'accesso con un provider di identità basato su social network e chiamare un'API Web protetta da Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.
- Un account di social networking di Facebook, Google o Microsoft.
- [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clonare l'applicazione Web di esempio da GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    La soluzione di esempio contiene due progetti:

    - **TaskWebApp**: applicazione Web che crea e modifica un elenco attività. L'applicazione Web usa il flusso utente di **iscrizione o accesso** per l'iscrizione o l'accesso degli utenti.
    - **TaskService**: API Web che supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API Web è protetta da Azure AD B2C e viene chiamata dall'applicazione Web.

## <a name="run-the-application-in-visual-studio"></a>Eseguire l'applicazione in Visual Studio

1. Nella cartella del progetto dell'applicazione di esempio aprire la soluzione **B2C-WebAPI-DotNet.sln** in Visual Studio.
2. Per questa guida introduttiva si eseguono contemporaneamente entrambi i progetti **TaskWebApp** e **TaskService**. Fare clic con il pulsante destro del mouse sulla soluzione **B2C-WebAPI-DotNet** in Esplora soluzioni e quindi scegliere **Imposta progetti di avvio**.
3. Selezionare **Progetti di avvio multipli** e modificare l'impostazione di **Azione** in **Avvia** per entrambi i progetti.
4. Fare clic su **OK**.
5. Premere **F5** per eseguire il debug di entrambe le applicazioni. Ogni applicazione viene visualizzata in una scheda del browser separata:

    - `https://localhost:44316/`: applicazione Web ASP.NET. Nella guida introduttiva si interagisce direttamente con l'applicazione.
    - `https://localhost:44332/`: API Web chiamata dall'applicazione Web ASP.NET.

## <a name="sign-in-using-your-account"></a>Eseguire l'accesso con il proprio account

1. Fare clic su **Sign up / Sign in** (Iscrizione/Accesso) nell'applicazione Web ASP.NET per avviare il flusso di lavoro.

    ![App Web ASP.NET di esempio nel browser con collegamento per iscrizione/accesso evidenziato](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    L'esempio supporta alcune opzioni di iscrizione, tra cui l'uso di un provider di identità di social networking o la creazione di un account locale tramite un indirizzo di posta elettronica. Per questo argomento di avvio rapido, usare un account di provider di identità basato su social network, ovvero un account di Facebook, Google o Microsoft.

2. Azure AD B2C presenta una pagina di accesso per un'azienda fittizia denominata Fabrikam per l'applicazione Web di esempio. Per iscriversi usando un provider di identità basato su social network, fare clic sul pulsante del provider di identità che si vuole usare.

    ![Pagina di accesso o di iscrizione che mostra i pulsanti del provider di identità](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    È necessario eseguire l'autenticazione (accesso) tramite le credenziali dell'account di social networking e autorizzare l'applicazione a leggere informazioni da questo account. Dopo la concessione dell'accesso, l'applicazione può recuperare le informazioni sul profilo dall'account, ad esempio il nome e la città dell'utente.

3. Completare il processo di accesso per il provider di identità.

## <a name="edit-your-profile"></a>Modificare il profilo

Azure Active Directory B2C offre funzionalità che consentono agli utenti di aggiornare il profilo. L'app Web di esempio usa un flusso utente di modifica dei profili di Azure AD B2C per il flusso di lavoro.

1. Sulla barra dei menu dell'applicazione fare clic sul nome del profilo e selezionare **Edit profile** (Modifica profilo) per modificare il profilo creato.

    ![App Web di esempio nel browser con il collegamento Modifica profilo evidenziato](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Modificare **Display name** (Nome visualizzato) o **City** (Città) e quindi fare clic su **Continue** (Continua) per aggiornare il profilo.

    La modifica viene visualizzata in alto a destra nella home page dell'applicazione Web.

## <a name="access-a-protected-api-resource"></a>Accedere a una risorsa API protetta

1. Fare clic su **Elenco attività** per immettere e modificare gli elementi elenco attività.

2. Immettere il testo nella casella di testo **Nuovo elemento**. Fare clic su **Aggiungi** per chiamare l'API Web protetta di Azure AD B2C che aggiunge un elemento elenco attività.

    ![App Web di esempio nel browser con l'opzione per aggiungere un elemento elenco attività](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    L'applicazione Web ASP.NET include un token di accesso di Azure AD nella richiesta alla risorsa API Web protetta per l'esecuzione di operazioni sugli elementi elenco attività dell'utente.

È stato usato l'account utente Azure AD B2C per eseguire una chiamata autorizzata a un'API Web protetta da Azure AD B2C.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre guide introduttive o esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata un'applicazione ASP.NET di esempio per:

* Accedere con una pagina di accesso personalizzata
* Accedere con un provider di identità basato su social network
* Creare un account Azure AD B2C
* Chiamare un'API Web protetta da Azure AD B2C

Iniziare ora a creare un tenant di Azure AD B2C.

> [!div class="nextstepaction"]
> [Creare un tenant di Azure Active Directory B2C nel portale di Azure](tutorial-create-tenant.md)
