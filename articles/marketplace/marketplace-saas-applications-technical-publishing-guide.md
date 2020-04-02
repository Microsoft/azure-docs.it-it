---
title: Guida tecnica alla pubblicazione di applicazioni SaaS in Azure Marketplace
description: Guida dettagliata ed elenchi di controllo per la pubblicazione di applicazioni SaaS in Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 641297ea74a08dea163cf768b8e9b245348824a4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544291"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guida alla pubblicazione dell'offerta di applicazioni SaaS

Le applicazioni SaaS possono essere pubblicate nel marketplace con tre diversi inviti all'azione: "Contattami", "Prova adesso" e "Scarica adesso". Questa guida illustra queste tre opzioni, inclusi i relativi requisiti. 

## <a name="offer-overview"></a>Panoramica dell'offerta  

Le applicazioni SaaS sono disponibili in entrambe le vetrine di Azure. La tabella seguente illustra le opzioni attualmente disponibili:

| Vetrina | Elenco | Versione di valutazione/Transazione |  
| --- | --- | --- |  
| AppSource | Sì | Sì |
| Azure Marketplace | No | Sì |   

**Elenco:** l’opzione di pubblicazione Elenco consiste in un'offerta di tipo Contatta e viene usata quando non è possibile una partecipazione a livello di valutazione o di transazione. Il vantaggio di questo approccio è dato dall'opportunità offerta agli editori di mettere subito in commercio una soluzione per iniziare a ricevere clienti potenziali che possono trasformarsi in ottimi affari per la crescita della loro azienda.  
**Versione di valutazione/Transazione**: il cliente ha la possibilità di acquistare direttamente o richiedere una versione di valutazione della soluzione. Un'esperienza di valutazione aumenta il livello di coinvolgimento offerto ai clienti e consente loro di esaminare la soluzione prima di acquistarla. Offrendo un'esperienza di valutazione, si hanno maggiori opportunità di promozione nelle vetrine e con molta probabilità si riesce a raggiungere un numero più ampio di clienti potenziali grazie a questa occasione di coinvolgimento. Le versioni di valutazione devono includere il supporto gratuito almeno per la durata del periodo di valutazione.  

| Offerta di app SaaS | Requisiti aziendali | Requisiti tecnici |  
| --- | --- | --- |  
| **contattaci** | Sì | No |  
| **PowerBI/Dynamics** | Sì | Sì (integrazione di Azure AD) |  
| **Applicazioni SaaS**| Sì | Sì (integrazione di Azure AD) |     

## <a name="saas-list"></a>Elenco SaaS

L'invito all'azione per un elenco SaaS senza versione di valutazione e funzionalità di fatturazione è "Contattami". 

Non è necessario configurare Azure Active Directory per elencare un'applicazione SaaS. 

|Requisiti  |Dettagli  |
|---------|---------|
|L'app è un'offerta SaaS  |   La soluzione è un’offerta SaaS e viene offerto un prodotto SaaS multi-tenant.      |


## <a name="saas-trial"></a>Versione di valutazione SaaS

Fornire una soluzione o l'app usando una versione di valutazione basata su SaaS (software-as-a-service) che è possibile provare gratuitamente. Le offerte di versione di valutazione gratuita possono essere presentate come un account di prova con durata o uso limitato. 


|Requisiti  |Dettagli  |
|---------|---------|
|L'app è un'offerta SaaS  |   La soluzione è un’offerta SaaS e viene offerto un prodotto SaaS multi-tenant.      |
|L'app è abilitata per AAD     |   Il cliente sarà reindirizzato al dominio e le transazioni verranno effettuate direttamente con il cliente       |


## <a name="saas-trial-technical-requirements"></a>Requisiti tecnici della versione di valutazione SaaS

I requisiti tecnici per le applicazioni SaaS sono semplici. Gli editori devono semplicemente garantire l'integrazione con Azure Active Directory (Azure AD) per la pubblicazione. L'integrazione di Azure AD con le applicazioni è documentata in modo dettagliato e Microsoft mette a disposizione una vasta gamma di risorse e SDK a tale scopo.  

Per iniziare, è consigliabile disporre di una sottoscrizione dedicata per la pubblicazione in Azure Marketplace, in modo da isolare questo tipo di attività da altre iniziative. Al termine dell'operazione, è possibile iniziare a distribuire l'applicazione SaaS nella sottoscrizione corrente per avviare il lavoro di sviluppo.  

La documentazione, gli esempi e le linee guida migliori per Azure Active Directory sono disponibili nei siti seguenti: 

* [Azure Active Directory Developer's Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrazione con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrazione di applicazioni con Azure Active DirectoryIntegrating Applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Roadmap per Azure - Sicurezza e identità](https://azure.microsoft.com/roadmap/?category=security-identity)

Per le esercitazioni video, vedere i collegamenti seguenti:

* [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticazione di Azure Active Directory con Vittorio Bertocci)

* [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 1 di 2)

* [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 2 di 2)

* [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Compilazione di app con Microsoft Azure Active Directory)

* [Video di Microsoft Azure incentrati su Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Per contenuti di formazione gratuiti per Azure Active Directory, vedere il collegamento seguente:  
* [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Azure Active Directory fornisce inoltre un sito in cui verificare la disponibilità di aggiornamenti del servizio.   
* [Aggiornamenti del servizio Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Uso di Azure Active Directory per abilitare l'accesso alle versioni di valutazione  

Microsoft autentica tutti gli utenti del Marketplace con Azure AD, pertanto quando un utente autenticato fa clic sull'elenco di versioni di valutazione in Marketplace e viene reindirizzato all'ambiente di valutazione, è possibile eseguire il provisioning dell'utente direttamente in una versione di valutazione senza richiedere un passaggio di accesso aggiuntivo. Il token che l'app riceve da Azure AD in fase di autenticazione include informazioni rilevanti sull'utente che l'editore può usare per creare un account utente nell'app, in modo da automatizzare l'esperienza di provisioning e aumentare così le probabilità che la versione di valutazione venga convertita in offerta a pagamento. Per altre informazioni sul token, vedere [Token di esempio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

L'uso di Azure AD per abilitare l'autenticazione con un solo clic all'app o alla versione di valutazione offre i vantaggi seguenti:  
* Consente di ottimizzare l'esperienza di accesso dei clienti da Marketplace alla versione di valutazione.  
* Mantiene l'aspetto di un'esperienza integrata nel prodotto, anche quando l'utente viene reindirizzato da Marketplace al dominio o all'ambiente di valutazione dell'editore.  
* Riduce la probabilità di abbandono al momento del reindirizzamento poiché non è necessario eseguire un altro passaggio per l'accesso.  
* Facilita la distribuzione per l'ampia popolazione di utenti di Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificazione dell'integrazione di Azure AD per Marketplace  

Certificare l'integrazione di Azure AD in diversi modi, a seconda che l'applicazione sia single-tenant o multi-tenant e che non si sia a che fare con Azure AD single sign-on (SSO) o che sia già presente.  

**Per le applicazioni multi-tenant:**  

Se Azure AD è già supportato, eseguire le operazioni seguenti:
1.  Registrare l'applicazione nel portale di Azure
2.  Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic. Altre informazioni specifiche sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se non si ha familiarità con l'accesso SSO federato di Azure AD, eseguire queste operazioni: 
1.  Registrare l'applicazione nel portale di Azure
2.  Sviluppare SSO con Azure AD usando [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) o [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Abilitare la funzionalità per il supporto multi-tenancy in AAD per ottenere un'esperienza di valutazione con un solo clic. Altre informazioni specifiche sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Per un'applicazione single-tenant, usare una delle opzioni seguenti:**  
* Aggiungere utenti alla directory come utenti guest tramite [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Effettuare manualmente il provisioning delle versioni di valutazione per i clienti tramite l'opzione Contatta
* Sviluppare una test drive per ogni cliente
* Creare un'app demo di esempio multi-tenant con SSO

## <a name="saas-subscriptions"></a>Sottoscrizioni SaaS

Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione. Per l'app SaaS è necessario soddisfare i requisiti seguenti:
- Prezzo e fatturazione del servizio presso un appartamento (mensile o annuale) o a una tariffa per utente.
- Fornire un metodo per aggiornare o annullare il servizio in qualsiasi momento.
Microsoft gestisce la transazione commerciale. Microsoft invia gli addebiti al cliente per conto dell'utente. Per offrire un'app SaaS come sottoscrizione, è necessario integrarsi con le API di evasione di SaaS.  Il servizio deve supportare il provisioning, l'aggiornamento e l'annullamento.

| Requisito | Dettagli |  
|:--- |:--- |  
|Fatturazione e misurazione | Il prezzo dell'offerta viene valutato in base al modello di determinazione dei prezzi selezionato prima della pubblicazione (tariffa fissa o per utente).  Se si utilizza il modello a tariffa forfettaria, è possibile includere facoltativamente dimensioni aggiuntive utilizzate per addebitare ai clienti l'utilizzo non incluso nella tariffa forfettaria. |  
|Annullamento | L'offerta può essere annullata dal cliente in qualsiasi momento. |  
|Pagina di destinazione delle transazioni | L'editore ospita una pagina di destinazione delle transazioni di Azure con co-branding in cui gli utenti possono creare e gestire il proprio account per il servizio SaaS. |   
| API della sottoscrizione | L'editore espone un servizio che può interagire con la sottoscrizione SaaS per creare, aggiornare ed eliminare un account utente e un piano di servizio. Le modifiche critiche all'API devono essere supportate entro 24 ore. Le modifiche non critiche all'API verranno rilasciate periodicamente. |  

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto,

- [Registrati](https://azuremarketplace.microsoft.com/sell) nel marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedi al portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare la tua offerta.
- Vedere [Offerta di applicazioni SaaS di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) per altre informazioni.
