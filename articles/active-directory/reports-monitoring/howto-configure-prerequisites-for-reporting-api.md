---
title: Prerequisiti per l'API per la creazione di report di Azure Active Directory Documenti Microsoft
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991263"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare queste API da strumenti e linguaggi di programmazione.

L'API di creazione report usa l'autenticazione [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) per autorizzare l'accesso alle API Web.

Per preparare l'accesso all'API di creazione report, è necessario:

1. [Assegnare ruoli](#assign-roles)
2. [Requisiti di licenza](#license-requirements)
3. [Registrare un'applicazione](#register-an-application)
4. [Concedere autorizzazioni](#grant-permissions)
5. [Ottenere le impostazioni di configurazione](#gather-configuration-settings)

## <a name="assign-roles"></a>Assegnare ruoli

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza

- Amministratore della sicurezza

- Amministratore globale

## <a name="license-requirements"></a>Requisiti di licenza

Per accedere ai report di accesso per un tenant, un tenant di Azure AD deve avere una licenza di Azure AD Premium associata. La licenza di Azure AD Premium P1 (o versione successiva) è necessaria per accedere ai report di accesso per qualsiasi tenant di Azure AD. In alternativa, se il tipo di directory è Azure AD B2C , i report di accesso sono accessibili tramite l'API senza alcun requisito di licenza aggiuntivo. 


## <a name="register-an-application"></a>Registrare un'applicazione

La registrazione è necessaria anche se si accede all'API di creazione di report tramite uno script. La registrazione fornisce un **ID applicazione**, necessario per le chiamate di autorizzazione e consente al codice di ricevere i token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi di amministratore possono essere molto potenti, quindi assicurarsi di conservare l'ID e il segreto dell'applicazione in un luogo sicuro.
> 

**Per registrare un'applicazione Azure AD:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Registrazioni per l'app**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Nella pagina **Registrazioni app** selezionare **Nuova registrazione**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. La pagina **Registrazione di un'applicazione:**

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **Tipo di account supportati**, selezionare Account solo **nell'organizzazione**.

    c. Nella casella di testo Url `https://localhost`di **reindirizzamento** selezionare **Web** digitare .

    d. Selezionare **Registra**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

A seconda delle API a cui si desidera accedere, è necessario concedere all'app le autorizzazioni seguenti:  

| API | Autorizzazione |
| --- | --- |
| Microsoft Azure Active Directory | Leggi i dati della directory |
| Microsoft Graph | Leggere tutti i dati dei log di controllo |


![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Nella sezione seguente viene elencata la procedura per entrambe le API. Se non si desidera accedere a una delle API, è possibile ignorare le procedure relative.

**Per concedere l'autorizzazione dell'applicazione per l'uso delle API:**


1. Selezionare **Autorizzazioni API,** quindi **Aggiungi un'autorizzazione**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Nella **pagina Autorizzazioni API richiesta**individuare Supporta **grafico di Azure Active Directory**di Azure Active Directory per **API legacy.** 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Nella pagina **Autorizzazioni necessarie** selezionare **Autorizzazioni applicazione**, espandere Casella di controllo **Directory** **Directory.ReadAll**.  Selezionare **Aggiungi autorizzazioni**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Nella pagina **Applicazione API di report - Autorizzazioni API** selezionare **Concedi consenso amministrativo**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: **Microsoft Graph** viene aggiunto per impostazione predefinita durante la registrazione dell'API.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 

Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Nomi di dominio personalizzati**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3. Nella pagina dell'applicazione, passare a **ID applicazione** e selezionare **Fare clic per copiare**.

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
 Evitare errori durante il tentativo di accedere ai log di controllo o all'accesso tramite l'API.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3.  Selezionare **Certificati e segreti** nella pagina **dell'applicazione API,** nella sezione **Segreti client,** fare clic su **Nuovo segreto client**. 

    ![Registrare l'applicazione](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Nella pagina **Aggiungi segreto client aggiungere:On** the Add a client secret page, add:

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Salva**.

    d. Copiare il valore della chiave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Risolvere gli errori relativi all'API di creazione report

In questa sezione sono elencati i messaggi di errore comuni che possono verificarsi durante l'accesso ai report attività utilizzando l'API Microsoft Graph e i passaggi per la loro risoluzione.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Errore: impossibile ottenere i ruoli utente da Microsoft Graph

 Accedi al tuo account usando entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer per evitare di ricevere un errore quando tenti di accedere usando Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Errore: impossibile eseguire il controllo della licenza premium da Microsoft Graph 

Se compare questo messaggio di errore durante il tentativo di accedere agli accessi tramite Graph Explorer, scegliere **Autorizzazioni di modifica** sotto l'account sulla barra di spostamento a sinistra e selezionare **Tasks.ReadWrite** e **Directory.Read.All**. 

![Interfaccia utente di Autorizzazioni di modifica](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: il tenant non è B2C o il tenant non dispone di una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Errore: i ruoli consentiti non includono Utente. 

 Evitare errori durante il tentativo di accedere ai log di controllo o all'accesso tramite l'API. Assicurarsi che l'account faccia parte del ruolo **Lettore sicurezza** o **Lettore report** nel tenant di Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Errore: applicazione mancante Microsoft Graph API 'Leggi tutti i dati del log di controllo' autorizzazione

Seguire i passaggi descritti nei [prerequisiti per accedere all'API](howto-configure-prerequisites-for-reporting-api.md) di creazione di report di Azure Active Directory per verificare che l'applicazione sia in esecuzione con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
