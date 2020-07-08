---
title: Esperienza dell'utente finale per le applicazioni-Azure Active Directory
description: Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c2ec85af2c954c2189f0df1b407a2c40967f096
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763398"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Esperienze per gli utenti finali per le applicazioni in Azure Active Directory

Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione:

* Pannello di accesso di Azure AD
* Applicazione di avvio di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

I metodi che è possibile scegliere per la distribuzione nell'organizzazione sono a discrezione dell'utente.

## <a name="azure-ad-access-panel"></a>Pannello di accesso di Azure AD

Il pannello di accesso https://myapps.microsoft.com è un portale basato sul Web che consente a un utente finale con un account aziendale in Azure Active Directory di visualizzare e avviare applicazioni a cui è stato concesso l'accesso dall'amministratore Azure ad. Se si è un utente finale con [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), è anche possibile usare le funzionalità di gestione di gruppi in modalità self-service tramite il riquadro di accesso.

![Screenshot che mostra il portale del pannello di accesso Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in un'unica pagina. È tuttavia possibile usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendole più facili da trovare. È ad esempio possibile utilizzare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di processo, attività, progetti e così via specifici. Per informazioni, vedere [come usare le raccolte di app personali per personalizzare i pannelli di accesso utente](access-panel-collections.md). 

Il riquadro di accesso non fa parte del portale di Azure e non richiede agli utenti di avere una sottoscrizione per Azure o Office 365.

Per altre informazioni sul riquadro di accesso di Azure AD, vedere [Introduzione al riquadro di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Applicazione di avvio di Office 365

Per le organizzazioni che hanno distribuito Office 365, le applicazioni assegnate agli utenti tramite Azure AD verranno visualizzate anche nel portale di Office 365 all'indirizzo [https://portal.office.com/myapps](https://portal.office.com/myapps) . Questo rende facile e comodo per gli utenti all’interno di un'organizzazione avviare le proprie applicazioni senza dover utilizzare un secondo portale ed è la soluzione di avvio delle applicazioni consigliata per le organizzazioni che utilizzano Office 365.

![Screenshot che mostra il portale di Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Per ulteriori informazioni sull'avvio di applicazioni di Office 365, vedere [Visualizzazione dell'applicazione nell’applicazione di avvio di Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Accesso diretto alle applicazioni federate

La maggior parte delle applicazioni federate che supportano OpenID Connect, WS-Federation o SAML 2.0 supportano anche la possibilità per gli utenti di avviare l'applicazione e quindi ottenere l’accesso tramite Azure AD con il reindirizzamento automatico oppure facendo clic su un collegamento per accedere. Questo è noto come accesso avviato dal provider di servizi e la maggior parte delle applicazioni federate nella raccolta di applicazioni di Azure AD lo supporta (vedere la documentazione collegata dalla configurazione guidata di configurazione di Single Sign-On dell'app nel portale di Azure per i dettagli).

![Esempio di una pagina di accesso dell'app per dispositivi mobili](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Collegamenti diretti Single Sign-On

Azure AD supporta anche collegamenti diretti Single Sign-On alle singole applicazioni che supportano Single Sign-On basato su password, Single Sign-On collegato e qualunque forma di Single Sign-On federato.

Questi collegamenti sono URL realizzati appositamente che inviano un utente attraverso la procedura di accesso di Azure AD per un'applicazione specifica senza richiedere che la avvii dal pannello di accesso di Azure AD o Office 365. Questi **URL di accesso utente** si trovano nelle proprietà delle applicazioni aziendali disponibili. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali**. Selezionare l'applicazione, quindi selezionare **Proprietà**.

![Esempio di URL di accesso utente nelle proprietà di Twitter](media/end-user-experiences/direct-sign-on-link.png)

Questi collegamenti possono essere copiati e incollati ovunque per fornire un collegamento di accesso all'applicazione selezionata. Questo potrebbe essere all’interno di un messaggio di posta elettronica o in qualsiasi portale personalizzato basato sul Web configurato per l'accesso alle applicazioni dell’utente. Di seguito è riportato un esempio di URL Single Sign-On diretto di Azure AD per Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Analogamente agli URL specifici dell'organizzazione per il pannello di accesso, è possibile personalizzare ulteriormente questo URL mediante l'aggiunta di uno dei domini attivi o verificati per la directory dopo il dominio myapps.microsoft.com. Questo assicura che eventuali personalizzazioni dell’organizzazione vengano caricate immediatamente nella pagina di accesso senza che l'utente debba immettere prima l'ID utente:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando un utente autorizzato fa clic su uno di questi collegamenti specifici dell'applicazione, per prima cosa vede le pagina di accesso aziendale (presupponendo che non abbia già effettuato l’accesso) e dopo l’accesso viene reindirizzato alla propria applicazione senza passare prima dal pannello di accesso. Se l'utente non dispone dei prerequisiti per l'accesso all'applicazione, ad esempio l'estensione browser di accesso singolo basato su password, il collegamento richiederà all'utente di installare l'estensione mancante. L'URL del collegamento rimane costante se la configurazione Single Sign-On per l'applicazione viene modificata.

Questi collegamenti utilizzano gli stessi meccanismi di controllo dell'accesso come il pannello di accesso e Office 365 e solo gli utenti o i gruppi che sono stati assegnati all'applicazione nel portale di Azure potranno eseguire l'autenticazione. Tuttavia, gli utenti non autorizzati vedranno un messaggio che indica che non sono autorizzati all’accesso e verrà fornito loro un collegamento per caricare il pannello di accesso per visualizzare le applicazioni disponibili per cui dispongono dell’accesso.

## <a name="next-steps"></a>Passaggi successivi

Per i piani di distribuzione, vedere [Piani di distribuzione di Azure Active Directory](../fundamentals/active-directory-deployment-plans.md)
