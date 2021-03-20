---
title: Configurare l'attestazione del ruolo per le app Azure AD aziendali | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali in Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 3c1b4b3d57bdce4a66adb35717f46d8989ee2453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648816"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali

Usando Azure Active Directory (Azure AD), è possibile personalizzare il tipo di attestazione per l'attestazione basata su ruolo nel token di risposta ricevuto dopo aver autorizzato un'app.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure AD con la directory installata.
- Sottoscrizione con Single Sign-On (SSO) abilitato. È necessario configurare SSO con l'applicazione.

> [!NOTE]
> Questo articolo illustra come creare/aggiornare/eliminare i ruoli applicazione nell'entità servizio usando le API in Azure AD. Se si vuole usare la nuova interfaccia utente per i ruoli dell'app, vedere [qui](./howto-add-app-roles-in-azure-ad-apps.md)i dettagli.

## <a name="when-to-use-this-feature"></a>Quando usare questa funzionalità

Usare questa funzionalità se l'applicazione prevede ruoli personalizzati nella risposta SAML restituita da Azure AD. È possibile creare tutti i ruoli necessari.

## <a name="create-roles-for-an-application"></a>Creare ruoli per un'applicazione

1. Nel <a href="https://portal.azure.com/" target="_blank">portale di Azure</a> fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Icona Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali**. Selezionare quindi **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare il nome dell'applicazione e quindi selezionare l'applicazione nel pannello dei risultati. Selezionare quindi il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Applicazione nell'elenco dei risultati](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Dopo avere aggiunto l'applicazione, passare alla pagina **Proprietà** e copiare l'ID oggetto.

    ![Pagina Proprietà](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Aprire [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra e seguire questa procedura:

    1. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    1. Sono necessarie autorizzazioni sufficienti per creare i ruoli. Selezionare **Autorizzazioni di modifica** per ottenere le autorizzazioni.

        ![Pulsante Autorizzazioni di modifica](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > Il ruolo di amministratore di App cloud e App non funzionerà in questo scenario perché sono necessarie le autorizzazioni di amministratore globale per le directory in lettura e scrittura.

    1. Selezionare le autorizzazioni seguenti nell'elenco (se non sono già disponibili) e quindi **Autorizzazioni di modifica**.

        ![Elenco delle autorizzazioni e pulsante Autorizzazioni di modifica](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. Accettare la richiesta di consenso. Si verrà riconnessi al sistema.

    1. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:

        `https://graph.microsoft.com/beta/servicePrincipals`

        Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![Finestra di dialogo Graph explorer, con la query per recuperare le entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. Estrarre la proprietà **appRoles** dall'oggetto entità servizio.

        ![Dettagli della proprietà appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        Se si usa l'app personalizzata e non l'app Azure Marketplace, verranno visualizzati due ruoli predefiniti: user e msiam_access. Per l'app Marketplace, msiam_access è l'unico ruolo predefinito. Non è necessario apportare modifiche ai ruoli predefiniti.

    1. Generare nuovi ruoli per l'applicazione.

        Il codice JSON seguente è un esempio dell'oggetto **appRoles**. Creare un oggetto simile per aggiungere i ruoli desiderati per l'applicazione.

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        È possibile aggiungere nuovi ruoli solo dopo msiam_access per l'operazione patch. Inoltre, è possibile aggiungere tutti i ruoli necessari per l'organizzazione. Azure AD invierà il valore di questi ruoli come valore dell'attestazione nella risposta SAML. Per generare i valori GUID per l'ID dei nuovi ruoli, usare strumenti Web come [questo](https://www.guidgenerator.com/)

    1. Tornare a Graph explorer e modificare il metodo da **GET** a **PATCH**. Applicare la patch all'oggetto entità servizio in modo da avere i ruoli desiderati aggiornando la proprietà **appRoles** perché sia simile a quella mostrata nell'esempio precedente. Selezionare **Esegui query** per eseguire l'operazione patch. Un messaggio di operazione completata conferma la creazione del ruolo.

        ![Operazione patch con messaggio di operazione completata](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. Dopo che all'entità servizio è stata applicata la patch con più ruoli, è possibile assegnare gli utenti ai rispettivi ruoli. Per assegnare gli utenti, è possibile accedere al portale e passare all'applicazione. Selezionare la scheda **utenti e gruppi** . Questa scheda elenca tutti gli utenti e i gruppi già assegnati all'app. È possibile aggiungere nuovi utenti nei nuovi ruoli. È anche possibile selezionare un utente esistente e fare clic su **Modifica** per modificare il ruolo.

    ![Scheda Utenti e gruppi](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Per assegnare il ruolo a un utente, selezionare il nuovo ruolo e fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Riquadro Modifica assegnazione e riquadro Selezionare un ruolo](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    Aggiornare la sessione nell'portale di Azure per visualizzare i nuovi ruoli.

1. Aggiornare la tabella **Attributi** per definire un mapping personalizzato dell'attestazione basata su ruolo.

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome attributo | Valore di attributo |
    | -------------- | ----------------|
    | Nome del ruolo  | user.assignedroles |

    Se il valore dell'attestazione del ruolo è null, Azure AD non invierà questo valore nel token e questa impostazione è predefinita in base alla progettazione.

    1. Fare clic sull'icona **modifica** per aprire **attributi utente &** finestra di dialogo attestazioni.

        ![Screenshot che evidenzia l'icona di modifica utilizzata per aprire la finestra di dialogo attributi utente & attestazioni.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. Nella finestra di dialogo **Gestisci attestazioni utente** aggiungere l'attributo token SAML facendo clic su **Aggiungi nuova attestazione**.

        ![Pulsante Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        ![Riquadro Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. Nella casella di testo **Nome** digitare il nome dell'attributo necessario. Questo esempio usa **Nome ruolo** come nome di attestazione.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Selezionare **Salva**.

10. Per testare l'applicazione in modalità Single Sign-On avviata da un provider di identità, accedere a [Riquadro di accesso ](https://myapps.microsoft.com) e selezionare il riquadro dell'applicazione. Nel token SAML verranno visualizzati tutti i ruoli assegnati per l'utente con il nome di attestazione specificato.

## <a name="update-an-existing-role"></a>Aggiornare un ruolo esistente

Per aggiornare un ruolo esistente, completare questi passaggi:

1. Aprire [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

1. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Finestra di dialogo Graph explorer, con la query per recuperare le entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Estrarre la proprietà **appRoles** dall'oggetto entità servizio.

    ![Dettagli della proprietà appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. Per aggiornare un ruolo esistente, completare i passaggi seguenti.

    ![Corpo della richiesta per "PATCH," con "description" e "displayname" evidenziati](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. Modificare il metodo da **GET** a **PATCH**.

    1. Copiare i ruoli esistenti e incollarli in **Corpo della richiesta**.

    1. Aggiornare il valore di un ruolo aggiornando la descrizione del ruolo, il valore del ruolo o il nome visualizzato del ruolo nel modo necessario.

    1. Dopo avere aggiornato tutti i ruoli necessari, fare clic su **Esegui query**.

## <a name="delete-an-existing-role"></a>Eliminare un ruolo esistente

Per eliminare un ruolo esistente, completare questi passaggi:

1. Aprire [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

1. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

1. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Finestra di dialogo Graph explorer, con la query per recuperare l'elenco delle entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. Estrarre la proprietà **appRoles** dall'oggetto entità servizio.

    ![Dettagli della proprietà appRoles dall'oggetto entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. Per eliminare il ruolo esistente, completare i passaggi seguenti.

    ![Corpo della richiesta per "PATCH," con IsEnabled impostato su false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. Modificare il metodo da **GET** a **PATCH**.

    1. Copiare i ruoli esistenti dall'applicazione e incollarli in **Corpo della richiesta**.

    1. Impostare il valore di **IsEnabled** su **false** per il ruolo che si vuole eliminare.

    1. Selezionare **Esegui query**.

    Assicurarsi di avere il ruolo msiam_access e che l'ID corrisponda nel ruolo generato.

1. Dopo che il ruolo è stato disabilitato, eliminare il blocco del ruolo dalla sezione **appRoles**. Mantenere il metodo come **PATCH** e selezionare **Esegui query**.

1. Al termine dell'esecuzione della query, il ruolo viene eliminato.

    Il ruolo deve essere disabilitato prima che possa essere rimosso.

## <a name="next-steps"></a>Passaggi successivi

Per i passaggi aggiuntivi, vedere la [documentazione dell'app](../saas-apps/tutorial-list.md).

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png