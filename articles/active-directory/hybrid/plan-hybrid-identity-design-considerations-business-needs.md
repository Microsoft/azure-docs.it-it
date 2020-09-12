---
title: Requisiti delle identità per la progettazione di una soluzione cloud ibrida per la gestione delle identità in Azure | Microsoft Docs
description: Identificare le esigenze aziendali che consentiranno di definire i requisiti per la progettazione della soluzione ibrida di gestione delle identità.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660573"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinare i requisiti per la soluzione ibrida di gestione delle identità
Il primo passaggio della progettazione di una soluzione ibrida di gestione delle identità consiste nel determinare i requisiti per l'organizzazione che userà la soluzione.  La gestione ibrida delle identità nasce come un ruolo di supporto (supporta tutte le altre soluzioni cloud fornendo l'autenticazione) che può in seguito offrire funzionalità nuove e interessanti per i carichi di lavoro degli utenti.  I carichi di lavoro o i servizi che verranno adottati per gli utenti determinano i requisiti per la progettazione della soluzione ibrida di gestione delle identità.  Questi servizi e carichi di lavoro dovranno usare la soluzione ibrida di gestione delle identità sia in locale che nel cloud.  

È necessario esaminare questi aspetti dell'attività per stabilire ciò che costituisce un requisito ora e ciò che l'azienda ha pianificato per il futuro. Se non si ha visibilità sulla strategia a lungo termine per la progettazione della soluzione ibrida di gestione delle identità, è possibile che la soluzione non garantisca la scalabilità necessaria in grado di sostenere la crescita e il cambiamento dell'azienda. Il diagramma seguente illustra un esempio di architettura di identità ibrida e i carichi di lavoro che vengono sbloccati per gli utenti. Si tratta solo di un esempio di tutte le nuove funzionalità che possono essere rese disponibili e distribuite grazie a una solida strategia di gestione ibrida delle identità. 

Alcuni componenti che fanno parte dell'architettura ibrida di identità ibrida ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinare le esigenze aziendali
Ogni azienda ha requisiti specifici e anche le aziende che operano nello stesso settore possono manifestare esigenze diverse. È comunque utile vedere le procedure consigliate per un determinato settore, ma sarà necessario valutare le esigenze specifiche dell'azienda per definire i requisiti per la progettazione della soluzione ibrida di gestione delle identità. 

Per identificare le esigenze aziendali, rispondere alle domande seguenti:

* L'azienda intende operare tagli sui costi operativi per l'IT?
* L'azienda prevede di proteggere gli asset cloud (app SaaS, infrastruttura e così via)?
* L'azienda desidera modernizzare l'infrastruttura IT?
  * La mobilità degli utenti è in crescita e aumentano le richieste al reparto IT per la creazione di eccezioni nella rete perimetrale per consentire a un tipo di traffico diverso di accedere a risorse differenti?
  * La società dispone di app legacy che devono essere pubblicate e rese disponibili per questi utenti moderni ma che non sono facili da riscrivere?
  * L'azienda deve eseguire tutte queste attività e tenerle sotto controllo allo stesso tempo?
* L'azienda vuole proteggere le identità degli utenti e ridurre i rischi adottando nuovi strumenti basati sulla tecnologia di sicurezza di Microsoft Azure in locale?
* L'azienda sta tentando di eliminare i temuti account "esterni" locali spostandoli nel cloud dove non rappresentano più una minaccia dormiente all'interno dell'ambiente locale?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizzare l'infrastruttura di gestione delle identità locale
Ora che ci si è fatti un'idea sui requisiti aziendali, è necessario valutare l'infrastruttura di gestione delle identità locale. Questa valutazione è importante per definire i requisiti tecnici per integrare la soluzione di gestione delle identità corrente nel sistema di gestione delle identità cloud. Rispondere alle domande seguenti:

* Qual è la soluzione di autenticazione e autorizzazione locale usata dall'azienda? 
* Al momento l'azienda usa servizi di sincronizzazione locali?
* L'azienda usa provider di identità di terze parti (IdP)?

È anche necessario valutare quali sono i servizi cloud usati dall'azienda. È molto importante effettuare una valutazione per acquisire informazioni sull'integrazione corrente con modelli SaaS, IaaS o PaaS nell'ambiente in uso. Rispondere alle domande seguenti in fase di valutazione:

* L'azienda usa un qualsiasi tipo d'integrazione con un provider di servizi cloud?
* In caso affermativo, quali sono i servizi in uso?
* Questa integrazione è già presente nell'ambiente di produzione o si tratta di un progetto pilota?

> [!NOTE]
> Cloud Discovery analizza i log del traffico a fronte del catalogo di app cloud di Microsoft Cloud App Security, contenente oltre 16.000 app cloud classificate e valutate in base a più di 70 fattori di rischio, per fornire visibilità continua sull'uso del cloud, su Shadow IT e sul rischio a cui Shadow IT può esporre l'organizzazione. Per iniziare consultare [Configurare Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Valutare i requisiti di integrazione della soluzione di gestione delle identità
A questo punto, è necessario valutare i requisiti di integrazione della soluzione di gestione delle identità. Questa valutazione è importante per definire i requisiti tecnici relativi alla modalità di autenticazione degli utenti, all'aspetto che assumerà l'organizzazione nel cloud, alla modalità in cui l'organizzazione consentirà l'autorizzazione e alla definizione dell'esperienza utente. Rispondere alle domande seguenti:

* L'organizzazione prevede di usare la federazione, l'autenticazione standard o entrambe?
* La federazione è un requisito?  Motivo:
  * SSO basato su Kerberos
  * L'azienda dispone di applicazioni locali (sviluppate internamente o da terze parti) che usano il formato SAML o funzionalità federative simili.
  * Multi-Factor Authentication tramite smart card. RSA SecurID e così via.
  * Regole di accesso client che risolvono le problematiche evidenziate dalle domande seguenti:
    1. È possibile bloccare tutto l'accesso esterno a Microsoft 365 in base all'indirizzo IP del client?
    2. È possibile bloccare tutto l'accesso esterno a Microsoft 365, ad eccezione di Exchange ActiveSync?
    3. È possibile bloccare tutto l'accesso esterno ai Microsoft 365, ad eccezione delle app basate su browser (OWA, SPO)
    4. È possibile bloccare tutto l'accesso esterno ai Microsoft 365 per i membri dei gruppi AD designati
* Preoccupazioni relative a sicurezza e controllo
* Si dispone già di un investimento esistente in una soluzione di autenticazione federata
* Quale sarà il nome usato dall'organizzazione per il dominio nel cloud?
* L'organizzazione ha un dominio personalizzato?
  1. Questo dominio è pubblico e facile da verificare tramite DNS?
  2. In caso contrario, è disponibile un dominio pubblico che si può usare per registrare un nome dell'entità utente alternativo in Active Directory?
* Gli ID utente sono coerenti per la rappresentazione nel cloud? 
* L'organizzazione dispone di app che richiedono l'integrazione con servizi cloud?
* Se l'organizzazione ha più domini, useranno tutti l'autenticazione federata o standard?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Valutare le applicazioni in esecuzione nell'ambiente
Ora che ci si è fatti un'idea sull'infrastruttura locale e cloud, è necessario valutare le applicazioni in esecuzione in questi ambienti. Questa valutazione è importante per definire i requisiti tecnici per integrare queste applicazioni nel sistema di gestione delle identità cloud. Rispondere alle domande seguenti:

* Dove risiederanno le applicazioni?
* Gli utenti accederanno alle applicazioni locali?  Alle applicazioni nel cloud? A entrambe?
* È previsto un piano per spostare i carichi di lavoro delle applicazioni esistenti nel cloud?
* È previsto un piano per sviluppare nuove applicazioni che risiederanno in locale o nel cloud e che useranno l'autenticazione cloud?

## <a name="evaluate-user-requirements"></a>Valutare i requisiti degli utenti
A questo punto, è necessario valutare i requisiti degli utenti. Questa valutazione è importante per definire i passaggi necessari per la formazione e l'assistenza degli utenti per la transizione al cloud. Rispondere alle domande seguenti:

* Gli utenti accederanno alle applicazioni in locale?
* Gli utenti accederanno alle applicazioni nel cloud?
* Qual è la modalità di accesso tipica all'ambiente locale?
* In che modo gli utenti accederanno al cloud?

> [!NOTE]
> Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. [Determinare i requisiti di risposta agli eventi imprevisti](plan-hybrid-identity-design-considerations-incident-response-requirements.md) illustra le opzioni disponibili, oltre ai vantaggi e agli svantaggi di ogni opzione.  Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
[Determinare i requisiti di sincronizzazione della directory](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](plan-hybrid-identity-design-considerations-overview.md)

