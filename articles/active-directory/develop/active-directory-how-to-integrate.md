---
title: Come integrare con Azure Active Directory
description: Scopri i vantaggi dell'integrazione dell'applicazione con Azure Active Directory e Ottieni risorse per funzionalità come l'accesso semplificato, la gestione delle identità, l'autenticazione a più fattori e il controllo degli accessi.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 4eaca7350ac006a2fcf2eed8d3eb0cb8ef274841
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885702"
---
# <a name="integrating-with-azure-active-directory"></a>Integrazione con Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In questo articolo vengono illustrati i vantaggi dell'integrazione dell'applicazione con Azure Active Directory (Azure AD) e le risorse per l'integrazione. Azure AD offre alle organizzazioni una gestione delle identità di livello aziendale per le applicazioni cloud. L'integrazione di Azure AD offre agli utenti un'esperienza di accesso semplificata e favorisce la conformità delle applicazioni ai criteri IT.

## <a name="how-to-integrate"></a>Modalità di integrazione

Per eseguire l'integrazione con Azure AD, sono disponibili diversi modi. Fare riferimento agli scenari seguenti a seconda dell'applicazione.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Supportare Azure AD come un modo per accedere all'applicazione

**Ridurre i problemi di accesso e i costi di supporto.**  Grazie all'uso di Azure AD per l'accesso, gli utenti non avranno un nome utente e una password in più da ricordare. Gli sviluppatori avranno invece una password in meno da archiviare e proteggere. Anche il solo fatto di non dover gestire la reimpostazione delle password dimenticate costituisce un risparmio significativo. Azure AD offre la tecnologia di accesso per alcune delle applicazioni cloud più diffuse al mondo, tra cui Office 365 e Microsoft Azure. Con centinaia di milioni di utenti da milioni di organizzazioni, la probabilità che un utente abbia già eseguito l'accesso ad Azure AD è molto elevata. Altre informazioni sull'[aggiunta del supporto per l'accesso ad Azure AD](authentication-scenarios.md).

**Semplificare l'iscrizione all'applicazione.**   Durante l'iscrizione all'applicazione, Azure AD può inviare le informazioni essenziali relative a un utente per precompilare il modulo di iscrizione o eliminarle completamente. Gli utenti possono effettuare l'iscrizione all'applicazione usando l'account Azure AD tramite un'esperienza di consenso nota simile a quelle dei social media e delle applicazioni per dispositivi mobili. Qualsiasi utente può iscriversi e accedere a un'applicazione integrata con Azure AD senza richiedere l'intervento del reparto IT. Altre informazioni sulla [registrazione dell'applicazione per l'accesso con l'account Azure AD](../../app-service/configure-authentication-provider-aad.md) .

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Cerca utenti, Gestisci il provisioning utenti e controlla l'accesso all'applicazione

**Esplorare la directory per cercare gli utenti.**  Usare l'API Microsoft Graph per consentire agli utenti di cercare altri utenti all'interno dell'organizzazione quando invitano altri utenti o di concedere l'accesso, anziché richiedere di digitare indirizzi di posta elettronica. Gli utenti possono esplorare la directory tramite un'interfaccia simile a una rubrica e possono visualizzare anche i dettagli della gerarchia all'interno dell'organizzazione. Altre informazioni sull' [API Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Riutilizzare i gruppi e le liste di distribuzione di Active Directory che il cliente gestisce già.**   In Azure AD sono già presenti i gruppi che il cliente usa per la distribuzione della posta elettronica e per la gestione degli accessi. Usando l'API Microsoft Graph, riusare questi gruppi invece di richiedere al cliente di creare e gestire un set separato di gruppi nell'applicazione. Le informazioni sui gruppi possono anche essere inviate all'applicazione nei token di accesso. Altre informazioni sull' [API Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Usare Azure AD per controllare gli utenti che hanno accesso all'applicazione.**   Gli amministratori e i proprietari di applicazioni in Azure AD possono assegnare l'accesso alle applicazioni a utenti e gruppi specifici. Usando l'API di Microsoft Graph, è possibile leggere questo elenco e usarlo per controllare il provisioning e il deprovisioning delle risorse e dell'accesso all'interno dell'applicazione.

**Usare Azure AD per il controllo degli accessi in base al ruolo.**   Gli amministratori e i proprietari di applicazioni possono assegnare utenti e gruppi ai ruoli definiti al momento della registrazione dell'applicazione in Azure AD. Le informazioni sui ruoli vengono inviate all'applicazione nei token di accesso e possono essere lette anche tramite l'API Microsoft Graph. Altre informazioni sull' [uso di Azure AD per l'autorizzazione](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Ottenere l'accesso al profilo utente, al calendario, alla posta elettronica, ai contatti, ai file e altro ancora

**Azure AD è il server di autorizzazione per Office 365 e altri servizi aziendali Microsoft.**   Se si supporta Azure AD per l'accesso all'applicazione o il collegamento degli account utente correnti agli account utente Azure AD tramite OAuth 2.0, è possibile richiedere l'accesso in lettura e scrittura al profilo, al calendario, alla posta elettronica, ai contatti, ai file dell'utente e ad altre informazioni. In questo modo, è possibile inserire eventi nel calendario dell'utente oppure accedere in lettura e scrittura ai file in OneDrive. Altre informazioni sull' [accesso alle API di Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promuovere l'applicazione nei Marketplace di Azure e Office 365

**Promuovere l'applicazione per farla conoscere a milioni di organizzazioni che usano già Azure AD.**   Gli utenti che esplorano questi Marketplace usano già uno o più servizi cloud, di conseguenza rappresentano il pubblico di destinazione ideale per l'applicazione. Altre informazioni sulla promozione dell'applicazione in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando gli utenti effettuano l'iscrizione all'applicazione, questa verrà visualizzata nel riquadro di accesso di Azure AD e nell'icona di avvio delle app di Office 365.**   Questo consente tornare all'applicazione in modo facile e veloce, migliorando così il coinvolgimento degli utenti. Altre informazioni sul [pannello di accesso di Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicazione da dispositivo a servizio e da servizio a servizio sicura

**L'uso di Azure AD per la gestione delle identità di servizi e dispositivi riduce le attività di scrittura di codice e consente al reparto IT di gestire gli accessi.**   I servizi e i dispositivi ricevono token tramite OAuth da Azure AD e li usano per accedere alle API Web. L'uso di Azure AD consente di evitare la scrittura di codice di autenticazione complesso. Poiché le identità di servizi e dispositivi sono archiviate in Azure AD, il reparto IT può gestire le chiavi e le revoche in una posizione centralizzata, anziché in una posizione separata all'interno dell'applicazione.

## <a name="benefits-of-integration"></a>Vantaggi dell'integrazione

L'integrazione con Azure AD non richiede di scrivere codice aggiuntivo.

### <a name="integration-with-enterprise-identity-management"></a>Integrazione con Enterprise Identity Management

**Conformità dell'applicazione ai criteri IT.**   Le organizzazioni possono integrare i sistemi di gestione delle identità aziendali con Azure AD, affinché quando un dipendente lascia l'azienda, perderà automaticamente l'accesso all'applicazione evitando così al reparto IT ulteriori attività. Il reparto IT può gestire gli utenti che possono accedere all'applicazione e determinare i criteri di accesso necessari, ad esempio l'autenticazione a più fattori, riducendo così la necessità di scrivere codice per garantire la conformità con criteri aziendali complessi. Azure AD fornisce agli amministratori un log di controllo dettagliato con l'elenco degli utenti che hanno eseguito l'accesso all'applicazione e monitorare così l'utilizzo.

**Azure AD estende Active Directory nel cloud per consentire l'integrazione dell'applicazione con Active Directory.**   Molte organizzazioni in tutto il mondo usano Active Directory come sistema di gestione degli accessi e delle identità principale e richiedono pertanto che le applicazioni funzionino con Active Directory. L'integrazione con Azure AD integra l'app con Active Directory.

### <a name="advanced-security-features"></a>Funzionalità di sicurezza avanzate

**Autenticazione a più fattori.**   Azure AD offre l'autenticazione a più fattori nativa. Gli amministratori IT possono richiedere l'autenticazione a più fattori per l'accesso all'applicazione, evitando così di dover scrivere il codice per garantire questo tipo di supporto. Altre informazioni su [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rilevamento di attività di accesso con anomalie.**   Azure AD elabora oltre un miliardo di accessi al giorno e si avvale di algoritmi di apprendimento automatico per rilevare attività sospette e notificare agli amministratori IT eventuali problemi. Grazie al supporto dell'accesso tramite Azure AD, l'applicazione può usufruire di questa ulteriore protezione. Altre informazioni sulla [visualizzazione del report di accesso di Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Accesso condizionale.**   Oltre all'autenticazione a più fattori, gli amministratori possono richiedere che vengano soddisfatte specifiche condizioni per consentire l'accesso degli utenti all'applicazione. Tra le condizioni che è possibile impostare, sono inclusi l'intervallo di indirizzi IP dei dispositivi client, l'appartenenza a gruppi specificati e lo stato del dispositivo usato per l'accesso. Altre informazioni su [Azure Active Directory l'accesso condizionale](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Facile sviluppo

**Protocolli standard del settore.**   Microsoft si impegna a supportare gli standard del settore. La piattaforma Microsoft Identity supporta i protocolli standard del settore OAuth 2,0 e OpenID Connect 1,0. Altre informazioni sui [protocolli di autenticazione della piattaforma Microsoft Identity](active-directory-v2-protocols.md).

**Librerie open source.**   Per accelerare lo sviluppo, Microsoft fornisce librerie open source completamente supportate per le piattaforme e i linguaggi più diffusi. Il codice sorgente è concesso in licenza secondo i termini della licenza Apache, versione 2.0 con la libertà di personalizzarlo e di contribuire ai progetti. Ulteriori informazioni su [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Presenza in tutto il mondo e disponibilità elevata

**Azure AD è distribuito nei data center di tutto il mondo ed è gestito e monitorato 24 ore al giorno.**   Azure AD è il sistema di gestione delle identità per Microsoft Azure e Office 365 ed è distribuito in 28 data center nel mondo. I dati della directory vengono replicati in almeno tre data center. I servizi di bilanciamento del carico globali garantiscono che l'utente acceda alla copia di Azure AD che contiene i dati dell'utente più vicina e reindirizza automaticamente le richieste ad altri data center nel caso in cui vengono rilevati problemi.

## <a name="next-steps"></a>Passaggi successivi

[Introduzione alla scrittura di codice](v2-overview.md#getting-started).

[Consentire agli utenti di accedere usando la piattaforma di identità Microsoft](authentication-scenarios.md)

