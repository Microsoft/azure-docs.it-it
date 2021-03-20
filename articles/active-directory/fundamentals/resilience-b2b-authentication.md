---
title: Resilienza della compilazione nell'autenticazione utente esterna con Azure Active Directory
description: Guida per amministratori IT e architetti per la creazione di un'autenticazione resiliente per utenti esterni
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724960"
---
# <a name="build-resilience-in-external-user-authentication"></a>Resilienza di compilazione nell'autenticazione utente esterna

[Azure Active Directory collaborazione B2B](../external-identities/what-is-b2b.md) (Azure ad B2B) è una funzionalità di [identità esterne](../external-identities/delegate-invitations.md) che consente la collaborazione con altre organizzazioni e singoli utenti. Consente l'onboarding sicuro degli utenti guest nel tenant di Azure AD senza dover gestire le proprie credenziali. Gli utenti esterni portano la propria identità e le proprie credenziali da un provider di identità esterno (IdP), in modo che non debbano ricordare una nuova credenziale. 

## <a name="ways-to-authenticate-external-users"></a>Modalità di autenticazione degli utenti esterni

È possibile scegliere i metodi di autenticazione utente esterno per la directory. È possibile usare gli IDP Microsoft o altri IDP.

Ogni IdP esterno prende una dipendenza dalla disponibilità di tale IdP. Con alcuni metodi di connessione a IDP, è possibile eseguire alcune operazioni per aumentare la resilienza.

> [!NOTE] 
> Azure AD B2B offre la capacità incorporata di autenticare qualsiasi utente da qualsiasi tenant [Azure Active Directory](../index.yml) o con un [account Microsoft](https://account.microsoft.com/account)personale. Non è necessario eseguire alcuna configurazione con queste opzioni predefinite.

### <a name="considerations-for-resilience-with-other-idps"></a>Considerazioni sulla resilienza con altri IDP

Quando si usa IDP esterni per l'autenticazione degli utenti guest, è necessario assicurarsi di mantenere le configurazioni necessarie per evitare le rotture.

| Metodo di autenticazione| Considerazioni sulla resilienza |
| - | - |
| Federazione con IDP di social networking come [Facebook](../external-identities/facebook-federation.md) o [Google](../external-identities/google-federation.md).| È necessario gestire l'account con l'IdP e configurare l'ID client e il segreto client. |
| [Federazione diretta con provider di identità SAML e WS-Federation](../external-identities/direct-federation.md)| È necessario collaborare con il proprietario del provider di identità per accedere ai propri endpoint, su cui si è dipendenti. <br>È necessario mantenere i metadati che contengono certificati ed endpoint. |
| [Indirizzo di posta elettronica monouso](../external-identities/one-time-passcode.md)| Questo metodo dipende dal sistema di posta elettronica di Microsoft, dal sistema di posta elettronica dell'utente e dal client di posta elettronica dell'utente. |


 

## <a name="self-service-sign-up-preview"></a>Iscrizione Self-Service (anteprima)

In alternativa all'invio di inviti o collegamenti, è possibile abilitare l' [iscrizione self-service](../external-identities/self-service-sign-up-overview.md).  Ciò consente agli utenti esterni di richiedere l'accesso a un'applicazione. È necessario creare un [connettore API](../external-identities/self-service-sign-up-add-api-connector.md) e associarlo a un flusso utente. Si associano i flussi utente che definiscono l'esperienza utente con una o più applicazioni. 

È possibile usare i [connettori API](../external-identities/api-connectors-overview.md) per integrare il flusso utente di iscrizione self-service con le API dei sistemi esterni. Questa integrazione dell'API può essere usata per i [flussi di lavoro di approvazione personalizzati](../external-identities/self-service-sign-up-add-approvals.md), per [eseguire la verifica dell'identità](../external-identities/code-samples-self-service-sign-up.md)e altre attività, ad esempio la sovrascrittura degli attributi utente. L'uso delle API richiede la gestione delle dipendenze seguenti.

* **Autenticazione del connettore API**: la configurazione di un connettore richiede un URL dell'endpoint, un nome utente e una password. Configurare un processo in base al quale vengono mantenute le credenziali e collaborare con il proprietario dell'API per assicurarsi di avere familiarità con la pianificazione della scadenza.

* **Risposta del connettore API**: progettare i connettori dell'API nel flusso di iscrizione per eseguire correttamente l'operazione se l'API non è disponibile. Esaminare e fornire agli sviluppatori di API queste [risposte API di esempio](../external-identities/self-service-sign-up-add-api-connector.md) e le [procedure consigliate per la risoluzione dei problemi](../external-identities/self-service-sign-up-add-api-connector.md). Collaborare con il team di sviluppo di API per testare tutti i possibili scenari di risposta, tra cui continuazione, errore di convalida e risposte di blocco. 

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
