---
title: Gestione e monitoraggio delle funzionalità di sicurezza - Microsoft Azure Documenti Microsoft
description: Questo articolo fornisce una panoramica dei servizi e delle funzionalità di sicurezza forniti da Azure per facilitare la gestione e il monitoraggio delle macchine virtuali e dei servizi cloud di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162757"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Panoramica su gestione e monitoraggio della sicurezza di Azure
Questo articolo fornisce una panoramica dei servizi e delle funzionalità di sicurezza forniti da Azure per facilitare la gestione e il monitoraggio delle macchine virtuali e dei servizi cloud di Azure.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo consente la gestione specifica degli accessi per le risorse di Azure. L'uso del controllo degli accessi in base al ruolo consente di concedere agli utenti solo il livello di accesso necessario per le attività che devono svolgere. Il controllo degli accessi in base al ruolo consente anche di assicurarsi che gli utenti non abbiano più accesso alle risorse nel cloud quando lasciano l'organizzazione.

Altre informazioni:

* [Blog del team di Active Directory sul controllo degli accessi in base al ruolo](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Con Azure è possibile usare software antimalware a cura dei principali fornitori di soluzioni di sicurezza, come Microsoft, Symantec, Trend Micro, McAfee e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce.

Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure offre la possibilità di installare un agente antimalware per le macchine virtuali e i ruoli PaaS. Questa funzionalità, basata su System Center Endpoint Protection, applica al cloud una comprovata tecnologia di sicurezza locale.

Offriamo anche una profonda integrazione per i prodotti [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) di Trend nella piattaforma Azure. DeepSecurity è una soluzione antivirus e SecureCloud è una soluzione di crittografia. La soluzione DeepSecurity viene distribuita nelle macchine virtuali con un modello di estensione. Usando l'interfaccia utente del portale di Azure e PowerShell si può scegliere di usare DeepSecurity all'interno delle nuove macchine virtuali attivate o di macchine virtuali esistenti già distribuite.

Symantec Endpoint Protection (SEP) è supportato anche in Azure. Tramite l'integrazione nel portale è possibile specificare che si intende usare SEP in una macchina virtuale. È possibile installare SEP in una nuova macchina virtuale tramite il portale di Azure oppure in una macchina virtuale esistente usando PowerShell.

Altre informazioni:

* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](antimalware.md)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nuove opzioni antimalware per la protezione di macchine virtuali di Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication è un metodo di autenticazione che richiede l'uso di più di un metodo di verifica per autenticare un utente. Fornisce un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti.

Multi-Factor Authentication permette di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre autenticazione avanzata tramite diverse opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app per dispositivi mobili o un codice di verifica e token OATH di terze parti.

Altre informazioni:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Informazioni su Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)
* [Come funziona Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute consente di estendere le reti locali in Microsoft Cloud tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Azure, Office 365 e CRM Online. La connettività può essere stabilita da:

* Una rete (IP VPN) any-to-any.
* Una rete Ethernet punto a punto.
* Una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso.

Le connessioni ExpressRoute non vengono instradate attraverso la rete Internet pubblica. Possono offrire maggiore affidabilità, velocità più elevate, latenze minori e sicurezza superiore rispetto alle tipiche connessioni tramite Internet.

Altre informazioni:

* [Panoramica tecnica relativa a ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateway di rete virtuale

I gateway VPN, anche detti gateway di rete virtuale di Azure, vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali. Vengono inoltre usati per inviare il traffico tra più reti virtuali in Azure (da rete a rete). I gateway VPN offrono connettività cross-premise sicura tra Azure e l'infrastruttura locale.

Altre informazioni:

* [Informazioni sui gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Panoramica della sicurezza di rete di Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

In alcuni casi gli utenti hanno la necessità di eseguire operazioni con privilegi nelle risorse di Azure o in altre applicazioni SaaS. Per questa ragione, le organizzazioni spesso concedono agli utenti l'accesso con privilegi permanente in Azure Active Directory (Azure AD).

Questo rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud poiché le organizzazioni non sono in grado di monitorare completamente le operazioni eseguite dagli utenti con l'accesso con privilegi. Inoltre, se un account utente con accesso privilegiato è compromesso, tale singola violazione può compromettere la sicurezza dell'intero cloud di un'organizzazione. Azure AD Privileged Identity Management consente di evitare questo rischio riducendo il tempo di esposizione dei privilegi e aumentando la visibilità sull'utilizzo.  

Privileged Identity Management introduce il concetto di amministratore temporaneo per un ruolo o accesso amministratore "just in time". Questo tipo di amministratore è un utente che deve completare un processo di attivazione per quel ruolo assegnato. Il processo di attivazione cambia l'assegnazione dell'utente a un ruolo in Azure AD da inattiva ad attiva per un periodo di tempo specificato.

Altre informazioni:

* [Gestione identità con privilegi di Azure AD](../../active-directory/privileged-identity-management/pim-configure.md)
* [Introduzione ad Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection offre una visualizzazione consolidata delle attività di accesso sospette e delle potenziali vulnerabilità per garantire la protezione dell'organizzazione. Identity Protection rileva attività sospette per utenti e identità privilegiate (admin), sulla base di segnali come:

* Attacchi di forza bruta.
* Credenziali perse.
* Accessi da posizioni non note e dispositivi infetti.

Tramite l'invio di notifiche e la visualizzazione delle correzioni consigliate, Identity Protection consente di ridurre i rischi in tempo reale. Calcola la gravità dei rischi per l'utente. Consente di configurare criteri basati sul rischio per proteggere automaticamente l'accesso all'applicazione da future minacce.

Altre informazioni:

* [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro sicurezza

Il Centro sicurezza di Azure consente di prevenire, rilevare e gestire le minacce. Il Centro sicurezza offre maggiore visibilità e controllo sulla sicurezza delle risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate e opera con un ampio ecosistema di soluzioni per la sicurezza.

Il Centro sicurezza di Azure consente di ottimizzare e monitorare la sicurezza di delle risorse di Azure tramite le funzionalità seguenti:

* Possibilità per l'utente di definire i criteri per le risorse di sottoscrizione di Azure in base a:
  * Esigenze di sicurezza dell'azienda.
  * Tipo di applicazioni o sensibilità dei dati in ogni sottoscrizione.
* Il monitoraggio dello stato della rete, delle applicazioni e delle macchine virtuali di Azure.
* Fornitura di un elenco di avvisi di sicurezza con priorità, inclusi gli avvisi di soluzioni partner integrate. Fornisce inoltre le informazioni necessarie per analizzare rapidamente un attacco e gli elementi consigliati su come risolverlo.

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](../../security-center/security-center-intro.md)
* [Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph offre protezione dalle minacce in tempo reale nei prodotti e servizi Microsoft. Questo sistema è basato su tecnologia di analisi avanzata che collega una notevole quantità di dati di sicurezza e intelligence sulle minacce per fornire informazioni dettagliate che possono rafforzare la sicurezza dell'organizzazione. Microsoft usa l'analisi avanzata, elaborando più di 450 miliardi di autenticazioni al mese, analizzando 400 miliardi di messaggi di posta elettronica alla ricerca di malware e phishing e aggiornando un miliardo di dispositivi, per fornire informazioni più dettagliate. Queste informazioni possono essere utili all'organizzazione per rilevare e rispondere rapidamente agli attacchi.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Passaggi successivi
Informazioni sul modello di [responsabilità condivisa](shared-responsibility.md) e sulle attività di sicurezza gestite da Microsoft e sulle attività gestite dall'utente.

Per altre informazioni sulla gestione della sicurezza, vedere [Gestione della sicurezza in Azure](management.md).
