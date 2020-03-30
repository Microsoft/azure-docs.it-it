---
title: Panoramica della sicurezza di Azure Active Directory Identity ProtectionAzure Active Directory Identity Protection security overview
description: Scopri in che modo la panoramica sulla sicurezza offre informazioni dettagliate sul livello di sicurezza della tua organizzazione.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382189"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - Panoramica della sicurezza

La [panoramica sulla sicurezza](https://aka.ms/IdentityProtectionRefresh) nel portale di Azure offre informazioni dettagliate sul livello di sicurezza dell'organizzazione. Consente di identificare gli attacchi potenziali e comprendere l'efficacia dei criteri.

"Panoramica della sicurezza" è diviso in linea di massima in due sezioni:

- Tendenze, a sinistra, fornisce una sequenza temporale dei rischi nell'organizzazione.
- Riquadri, a destra, evidenzia i principali problemi in corso nell'organizzazione e suggerisce come intervenire rapidamente.

![Panoramica della sicurezza](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Tendenze

### <a name="new-risky-users-detected"></a>Nuovi utenti a rischio rilevati

Questo grafico mostra il numero dei nuovi utenti a rischio rilevati nel periodo di tempo selezionato. È possibile filtrare la visualizzazione di questo grafico per livello di rischio utente (basso, medio, elevato). Passare il mouse sugli incrementi delle date in UTC per visualizzare il numero di utenti a rischio rilevati per quel giorno. Facendo clic su questo grafico si accede al report "Utenti a rischio". Per salvaguardare gli utenti a rischio, provare a modificarne la password.

### <a name="new-risky-sign-ins-detected"></a>Nuovi accessi a rischio rilevati

Questo grafico mostra il numero dei nuovi accessi a rischio rilevati nel periodo di tempo selezionato. È possibile filtrare la visualizzazione di questo grafico in base al tipo di rischio di accesso (in tempo reale o aggregato) e al livello di rischio di accesso (basso, medio, elevato). Gli accessi non protetti sono accessi a rischio con esito positivo in tempo reale a cui non è stata inviata una richiesta di autenticazione a più fattori. (Nota: gli invii rischiosi a causa dei rilevamenti offline non possono essere protetti in tempo reale dai criteri di rischio di accesso). Passare il mouse sugli incrementi delle date in UTC per visualizzare il numero di accessi a rischio rilevati per quel giorno. Facendo clic su questo grafico si accede al report "Accessi a rischio".

## <a name="tiles"></a>Riquadri
 
### <a name="high-risk-users"></a>Utenti ad alto rischio

Il riquadro "Utenti ad alto rischio", mostra il numero più recente di utenti con elevata probabilità di identità compromessa. Questi ultimi dovrebbero rappresentare una priorità per l'analisi. Facendo clic sul riquadro "Utenti ad alto rischio" si viene reindirizzati a una vista filtrata del report "Utenti a rischio" che mostra solo gli utenti con un alto livello di rischio. Usando questo report, è possibile acquisire altre informazioni e salvaguardare questi utenti tramite la reimpostazione della password.

![Panoramica della sicurezza](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Utenti a medio rischio
Il riquadro "Utenti a medio rischio", mostra il numero più recente di utenti con media probabilità di identità compromessa. Facendo clic sul riquadro "Utenti a medio rischio" si viene reindirizzati a una vista filtrata del report "Utenti a rischio" che mostra solo gli utenti con un livello di rischio medio. Usando questo report, è possibile effettuare un'ulteriore analisi e salvaguardare questi utenti.

### <a name="unprotected-risky-sign-ins"></a>Accessi a rischio non protetti

Il riquadro 'Accessi rischiosi non protetti' mostra il numero dell'ultima settimana di accessi rischiosi riusciti in tempo reale che non sono stati bloccati o MFA contestati da un criterio di accesso condizionale, criteri di rischio di protezione dell'identità o MFA per utente. Si tratta di accessi con esito positivo potenzialmente compromessi a cui non è stata inviata una richiesta di autenticazione a più fattori. Per proteggere tali accessi in futuro, applicare un criterio di rischio di accesso. Facendo clic sul riquadro "Accessi a rischio non protetti" si viene reindirizzati al pannello di configurazione dei criteri di rischio di accesso in cui configurare il criterio per richiedere l'autenticazione a più fattori per un accesso che presenta un livello di rischio specificato.

### <a name="legacy-authentication"></a>Autenticazione legacy

Il riquadro "Autenticazione legacy" mostra il numero di autenticazioni legacy dell'ultima settimana nell'organizzazione. I protocolli di autenticazione legacy non supportano i metodi di sicurezza moderni, ad esempio l'autenticazione a più fattori. Per evitare l'autenticazione legacy, è possibile applicare un criterio di accesso condizionale. Facendo clic sul riquadro "Autenticazione Legacy" si viene reindirizzati al riquadro "Identity Secure Score".

### <a name="identity-secure-score"></a>Identity Secure Score

Identity Secure Score valuta e confronta il comportamento di sicurezza con i modelli del settore. Se si fa clic sul riquadro 'Identity Secure Score (Anteprima)', verrà reindirizzato al pannello 'Identity Secure Score' dove è possibile ottenere ulteriori informazioni su come migliorare la posizione di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- [Che cosa sono i rischi?](concept-identity-protection-risks.md)

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
