---
title: Interoperabilità delle riunioni di Teams
titleSuffix: An Azure Communication Services concept document
description: Partecipare alle riunioni di Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cf6553cd7c59febd19f9654e31188f127b8eb065
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276757"
---
# <a name="teams-interoperability"></a>Interoperabilità di Teams

[!INCLUDE [Public Preview](../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Per abilitare o disabilitare l' [interoperabilità del tenant teams](../concepts/teams-interop.md), completare [il modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

È possibile usare Servizi di comunicazione di Azure per creare esperienze personalizzate di riunioni che interagiscono con Microsoft Teams. Gli utenti delle soluzioni per i servizi di comunicazione possono interagire con i team partecipanti attraverso la voce, il video, la chat e la condivisione dello schermo.

L'interoperabilità del team consente di creare applicazioni personalizzate che connettono gli utenti alle riunioni del team. Gli utenti delle applicazioni personalizzate non devono necessariamente avere identità di Azure Active Directory o licenze di Teams per provare questa funzionalità. Questa soluzione è ideale per coinvolgere i dipendenti (che possono avere familiarità con Teams) e gli utenti esterni (che usano un'esperienza di applicazione personalizzata) in un'esperienza di riunione senza soluzione di continuità. Ad esempio:

1. I dipendenti usano Teams per pianificare una riunione 
1. I dettagli della riunione sono condivisi con utenti esterni tramite l'applicazione personalizzata.
   * **Utilizzo di API Graph** L'applicazione Servizi di comunicazione personalizzata usa le API Microsoft Graph per accedere ai dettagli della riunione da condividere. 
   * **Utilizzo di altre opzioni** Il collegamento alla riunione, ad esempio, può essere copiato dal calendario in Microsoft teams.
1. Gli utenti esterni usano l'applicazione personalizzata per partecipare alla riunione dei team (tramite gli SDK per la chiamata e la chat dei servizi di comunicazione)

L'architettura generale per questo caso d'uso è simile alla seguente: 

![Architettura per l'interoperabilità di Teams](./media/call-flows/teams-interop.png)

Sebbene alcuni team soddisfino le funzionalità, ad esempio la modalità di generazione, la modalità insieme e le chat room, saranno disponibili solo per gli utenti del team, l'applicazione personalizzata avrà accesso alle funzionalità di base audio, video, chat e condivisione dello schermo della riunione. La chat della riunione sarà accessibile all'utente dell'applicazione personalizzata mentre si trova nella chiamata. Non saranno in grado di inviare o ricevere messaggi prima di partecipare o dopo aver lasciato la chiamata. 

Quando un utente di servizi di comunicazione partecipa alla riunione dei team, il nome visualizzato fornito tramite l'SDK chiamante verrà visualizzato agli utenti del team. L'utente di Servizi di comunicazione verrà altrimenti trattato come un utente anonimo in Teams.  Per l'applicazione personalizzata è necessario considerare l'autenticazione degli utenti e altre misure di sicurezza, in modo da proteggere le riunioni di Teams. Tenere presenti le implicazioni per la sicurezza derivanti dalla partecipazione di utenti anonimi alle riunioni e seguire la [guida alla sicurezza di Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) per configurare le funzionalità disponibili per utenti anonimi.

L'interoperabilità tra i team di servizi di comunicazione è attualmente in anteprima privata. Se disponibile a livello generale, gli utenti di servizi di comunicazione verranno considerati come "utenti di accesso esterno". Scopri di più sull'accesso esterno in [Call, chat e collabora con persone esterne all'organizzazione in Microsoft teams](/microsoftteams/communicate-with-users-from-other-organizations).

Gli utenti di Servizi di comunicazione possono partecipare a riunioni pianificate di Teams purché la partecipazione anonima sia abilitata nelle [impostazioni](/microsoftteams/meeting-settings-in-teams). Se la riunione è pianificata per un canale, gli utenti di servizi di comunicazione non saranno in grado di partecipare alla chat o di inviare e ricevere messaggi.

## <a name="teams-in-government-clouds-gcc"></a>Teams in Government Community Cloud (GCC)
L'interoperabilità dei servizi di comunicazione di Azure non è compatibile con le distribuzioni di team che usano i [cloud di Microsoft 365 Government (GCC)](/MicrosoftTeams/plan-for-government-gcc) al momento. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere l'app di chiamata a una riunione di Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
