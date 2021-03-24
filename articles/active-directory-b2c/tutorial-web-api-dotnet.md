---
title: "Esercitazione: Concedere l'accesso a un'API Web ASP.NET"
titleSuffix: Azure AD B2C
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web ASP.NET e chiamarla da un'applicazione Web ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 84bf0ddd194cd59a7e728c0c50dbe3cbad2afa4a
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "100555640"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Esercitazione: Concedere l'accesso a un'API Web ASP.NET con Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web protetta in Active Directory B2C (Azure AD B2C) da un'applicazione Web ASP.NET.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'applicazione Web con Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapi1*.
1. In **URI di reindirizzamento**  selezionare **Web** e quindi immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:44332`.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni (legacy)** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. In **Includi app Web/API Web** selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:44332`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Crea**.
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

* * *

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Ad esempio, gli utenti dell'API Web possono avere accesso sia in lettura sia in scrittura oppure possono avere solo accesso in lettura. In questa esercitazione vengono usati gli ambiti per definire autorizzazioni di lettura e scrittura per l'API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *webapp1* in Azure AD B2C. Questa applicazione verrà usata per chiamare l'API Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

L'applicazione verrà registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione. L'applicazione ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, si configura l'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'API Web di esempio. L'API Web di esempio è inclusa nel progetto scaricato nell'esercitazione preliminare.

La soluzione di esempio contiene due progetti:

* **TaskWebApp** crea e modifica un elenco attività. L'esempio usa il flusso utente di **iscrizione o accesso** per l'iscrizione o l'accesso degli utenti.
* **TaskService** supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API è protetta da Azure AD B2C e viene chiamata da TaskWebApp.

### <a name="configure-the-web-application"></a>Configurare l'applicazione Web

1. Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.
1. Nel progetto **TaskWebApp** aprire **Web.config**.
1. Per eseguire l'API in locale, usare l'impostazione localhost per **api:TaskServiceUrl**. Modificare il file Web.config come segue:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configurare l'URI dell'API. È l'URI usato dall'applicazione Web per effettuare la richiesta API. Configurare anche le autorizzazioni richieste.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Nel progetto **TaskService** aprire **Web.config**.
1. Configurare l'API per l'uso del tenant.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Impostare l'ID client sull'ID applicazione dell'applicazione API Web registrata *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Aggiornare l'impostazione del flusso utente con il nome del flusso utente di iscrizione e accesso *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configurare l'impostazione degli ambiti in modo che corrisponda a quelli creati nel portale.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

È necessario eseguire entrambi i progetti, ovvero **TaskWebApp** e **TaskService**.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio...** .
1. Selezionare **Progetti di avvio multipli**.
1. Modificare il valore di **Azione** in entrambi i progetti in **Avvia**.
1. Fare clic su **OK** per salvare la configurazione.
1. Premere **F5** per eseguire entrambe le applicazioni. Ogni applicazione viene visualizzata in una finestra del browser separata.
    * `https://localhost:44316/` corrisponde all'applicazione Web.
    * `https://localhost:44332/` è l'API Web.

1. Nell'applicazione Web selezionare **sign-up / sign-in** per accedere all'applicazione Web. Usare l'account creato in precedenza.
1. Dopo l'accesso selezionare **To-do list** e creare un elemento elenco attività.

Quando si crea un elemento elenco attività, l'applicazione Web invia una richiesta all'API Web per la generazione dell'elemento. L'applicazione Web protetta chiama l'API Web protetta da Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

> [!div class="nextstepaction"]
> [Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](add-identity-provider.md)