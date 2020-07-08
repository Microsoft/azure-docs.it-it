---
title: Esercitazione-creare flussi utente-Azure Active Directory B2C
description: Informazioni su come creare flussi utente nel portale di Azure per abilitare l'iscrizione, l'accesso e la modifica del profilo utente per le applicazioni in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 63c15b13134f17cd2e1d0bf7b8d11a168dca5b2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829958"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Esercitazione: creare flussi utente in Azure Active Directory B2C

Nelle applicazioni è possibile che siano presenti [flussi utente](user-flow-overview.md) che consentono agli utenti di iscriversi, accedere o gestire il proprio profilo. È possibile creare più flussi utente di tipi diversi nel tenant di Azure Active Directory B2C (Azure AD B2C) e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Questa esercitazione illustra come creare alcuni flussi utente consigliati tramite il portale di Azure. Per informazioni su come configurare un flusso di credenziali password del proprietario della risorsa (ROPC) nell'applicazione, vedere [configurare il flusso di credenziali password del proprietario della risorsa in Azure ad B2C](configure-ropc.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Registrare le applicazioni](tutorial-register-applications.md) che fanno parte dei flussi utente da creare.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

    ![Riquadro tenant, directory e sottoscrizione B2C portale di Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **criteri**selezionare **flussi utente**, quindi selezionare **nuovo flusso utente**.

    ![Pagina flussi utente nel portale con il pulsante nuovo flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Nella scheda **consigliata** selezionare il flusso di **iscrizione e accesso** dell'utente.

    ![Selezionare una pagina flusso utente con il flusso di iscrizione e accesso evidenziato](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per **Provider di identità** selezionare **Iscrizione posta elettronica**.

    ![Creazione della pagina flusso utente in portale di Azure con le proprietà evidenziate](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

    ![Pagina attributi e selezione attestazioni con tre attestazioni selezionate](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso di *B2C_1* viene anteposto automaticamente al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina Panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Pagina Esegui flusso utente nel portale con il pulsante Esegui flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Immettere un indirizzo di posta elettronica valido, fare clic su **Invia codice di verifica**, immettere il codice di verifica ricevuto, quindi selezionare **Verifica codice**.
1. Immettere una nuova password e confermarla.
1. Selezionare il paese e l'area geografica, immettere il nome che si desidera visualizzare, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate per paese/area geografica, nome e Cap.

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Nel menu della pagina Panoramica del tenant Azure AD B2C selezionare **flussi utente**e quindi selezionare **nuovo flusso utente**.
1. Selezionare il flusso utente per la **modifica del profilo** nella scheda **consigliata** .
1. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
1. Per **Provider di identità** selezionare **Accesso all'account locale**.
1. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra altro**, quindi scegliere sia attributi che attestazioni per **nome visualizzato** e **titolo processo**. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina Panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
1. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continue**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per consentire agli utenti dell'applicazione di reimpostare la password, è possibile usare un flusso utente per la reimpostazione della password.

1. Nel menu Panoramica del tenant Azure AD B2C selezionare **flussi utente**, quindi selezionare **nuovo flusso utente**.
1. Selezionare il flusso utente per la **reimpostazione della password** nella scheda **consigliata** .
1. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
1. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
1. In Attestazioni dell'applicazione fare clic su **Mostra altro** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
1. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina Panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**, verificare l'indirizzo di posta elettronica dell'account creato in precedenza e selezionare **continua**.
1. Ora è possibile modificare la password per l'utente. Modificare la password e selezionare **continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Successivamente, informazioni sull'aggiunta di provider di identità alle applicazioni per consentire l'accesso utente con provider come Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft o Twitter.

> [!div class="nextstepaction"]
> [Aggiungere provider di identità alle applicazioni >](tutorial-add-identity-providers.md)
