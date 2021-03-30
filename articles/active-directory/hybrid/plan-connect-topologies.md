---
title: 'Azure AD Connect: topologie supportate | Microsoft Docs'
description: Questo argomento illustra in modo dettagliato le topologie supportate e non supportate per Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359833"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie per Azure AD Connect
Questo articolo descrive diverse topologie locali e di Azure Active Directory (Azure AD) che usano il Servizio di sincronizzazione Azure AD Connect come soluzione di integrazione chiave. Questo articolo include le configurazioni supportate e non supportate.


Ecco la legenda delle immagini usate nell'articolo:

| Descrizione | Simbolo |
| --- | --- |
| Foresta locale di Active Directory |![Foresta locale di Active Directory](./media/plan-connect-topologies/legendad1.png) |
| Active Directory locale con importazione con filtri |![Active Directory con importazione con filtri](./media/plan-connect-topologies/legendad2.png) |
| Server di sincronizzazione di Azure AD Connect |![Server di sincronizzazione di Azure AD Connect](./media/plan-connect-topologies/legendsync1.png) |
| Server di sincronizzazione di Azure AD Connect in "modalità di staging" |![Server di sincronizzazione di Azure AD Connect in "modalità di staging"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync con Forefront Identity Manager (FIM) 2010 o Microsoft Identity Manager (MIM) 2016 |![GALSync con FIM 2010 o MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Dettagli relativi al server di sincronizzazione di Azure AD Connect |![Dettagli relativi al server di sincronizzazione di Azure AD Connect](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Scenario non supportato |![Scenario non supportato](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> Microsoft non supporta la modifica o l'uso del servizio di sincronizzazione Azure AD Connect al di fuori delle configurazioni e delle azioni descritte in modo formale. Una di queste configurazioni o azioni può causare uno stato incoerente o non supportato della sincronizzazione Azure AD Connect. Di conseguenza, Microsoft non è in grado di fornire supporto tecnico per tali distribuzioni.


## <a name="single-forest-single-azure-ad-tenant"></a>Foresta singola, tenant singolo di Azure AD
![Topologia per una foresta singola e un tenant singolo](./media/plan-connect-topologies/singleforestsingledirectory.png)

La topologia più comune è costituita da una singola foresta locale, con uno o più domini, e un tenant singolo di Azure AD. Per l'autenticazione di Azure AD viene usata la sincronizzazione dell'hash delle password. L'installazione rapida di Azure AD Connect supporta unicamente questa topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Foresta singola, più server di sincronizzazione a un tenant di Microsoft Azure
![Topologia con filtri non supportata per una foresta singola](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Non è supportato lo scenario che prevede più server di sincronizzazione di Azure AD Connect connessi allo stesso tenant di Azure AD, a eccezione di un [server di staging](#staging-server). Non è supportato neanche se i server sono configurati per la sincronizzazione con un set di oggetti che si escludono a vicenda. Questa topologia potrebbe essere stata presa in considerazione se non è possibile raggiungere tutti i domini della foresta da un unico server o se si vuole distribuire il carico tra server diversi.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Più foreste, tenant singolo di Azure AD
![Topologia per più foreste e un tenant singolo](./media/plan-connect-topologies/multiforestsingledirectory.png)

In molte organizzazioni sono presenti ambienti con più foreste Active Directory locali. La presenza di più foreste Active Directory locali può essere dovuta a vari motivi. Esempi tipici sono costituiti da progettazioni con foreste di account-risorse e dai risultati di fusioni o acquisizioni.

Quando sono presenti più foreste, devono essere tutte raggiungibili da un singolo server di sincronizzazione di Azure AD Connect. Il server deve essere aggiunto a un dominio. Se è necessario raggiungere tutte le foreste, è possibile inserire il server in una rete perimetrale.

L'installazione guidata di Azure AD Connect offre diverse opzioni per consolidare gli utenti rappresentati in più foreste. L'obiettivo è fare in modo che un utente sia rappresentato una sola volta in Azure AD. Nell'installazione guidata sono disponibili alcune topologie comuni che è possibile configurare nel percorso di installazione personalizzato. Nella pagina **Identificazione univoca per gli utenti** selezionare l'opzione corrispondente che rappresenta la topologia. Il consolidamento viene configurato solo per gli utenti. I gruppi duplicati non vengono consolidati con la configurazione predefinita.

Le topologie comuni sono illustrate nelle sezioni relative a topologie separate, a [mesh completo](#multiple-forests-full-mesh-with-optional-galsync)e [alla topologia di risorse di account](#multiple-forests-account-resource-forest).

La configurazione predefinita del servizio di sincronizzazione Azure AD Connect presuppone quanto segue:

* Ogni utente ha solo un account abilitato e la foresta in cui si trova l'account viene usata per l'autenticazione dell'utente. Questo presupposto riguarda la sincronizzazione dell'hash delle password, l'autenticazione pass-through e la federazione. userPrincipalName e sourceAnchor o immutableID provengono da questa foresta.
* Ogni utente ha solo una cassetta postale.
* La foresta che ospita la cassetta postale di un utente garantisce la migliore qualità dei dati per gli attributi visibili nell'Elenco indirizzi globale di Exchange. Se non è presente una cassetta postale per l'utente, è possibile usare qualsiasi foresta per recuperare questi valori di attributi.
* Se è disponibile una cassetta postale collegata, è presente anche un account in una foresta diversa usato per l'accesso.

Se l'ambiente non soddisfa questi presupposti, si verifica quanto segue:

* Se sono presenti più account attivi o più cassette postali, il motore di sincronizzazione ne seleziona uno e ignora gli altri.
* Una cassetta postale collegata priva di account attivi non viene esportata in Azure AD. L'account utente non è rappresentato come membro in alcun gruppo. Una cassetta postale collegata in DirSync viene sempre rappresentata come cassetta postale normale. Questa modifica introduce un comportamento diverso per migliorare il supporto degli scenari a più foreste.

Per altre informazioni, vedere [Informazioni sulla configurazione predefinita](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Più foreste, più server di sincronizzazione a un tenant di Microsoft Azure
![Topologia non supportata per più foreste e più server di sincronizzazione](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Non è consentito connettere più server di sincronizzazione di Azure AD Connect a un tenant singolo di Azure AD. Fa eccezione l'uso di un [server di gestione temporanea](#staging-server).

Questa topologia è diversa da quella seguente perché non supporta la connessione di **più server di sincronizzazione** a un singolo tenant di Azure AD.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Più foreste, un unico server di sincronizzazione, utenti rappresentati in una sola directory
![Opzione per rappresentare gli utenti solo una volta in tutte le directory](./media/plan-connect-topologies/multiforestusersonce.png)

![Immagine di più foreste e topologie separate](./media/plan-connect-topologies/multiforestseparatetopologies.png)

In questo ambiente tutte le foreste locali vengono considerate entità separate. Nessun utente è presente in nessuna altra foresta. Ogni foresta presenta un'organizzazione Exchange dedicata e non viene effettuata alcuna sincronizzazione dell'elenco di indirizzi globale (GALSync) tra le foreste. Questa topologia può presentarsi dopo una fusione o acquisizione o in un'organizzazione in cui ogni business unit opera in modo indipendente. In Azure AD queste foreste si trovano nella stessa organizzazione e vengono visualizzate con un elenco di indirizzi globale unificato. Nell'immagine precedente ogni oggetto di ogni foresta viene rappresentato una sola volta nel metaverse e aggregato nel tenant di Azure AD di destinazione.

### <a name="multiple-forests-match-users"></a>Più foreste: corrispondenze con utenti
Tutti gli scenari hanno in comune il fatto che i gruppi di distribuzione e di sicurezza possono contenere una combinazione di utenti, contatti ed entità di sicurezza esterne. Le entità di sicurezza esterne vengono usate in Active Directory Domain Services (AD DS) per rappresentare i membri di altre foreste in un gruppo di sicurezza. Tutte le entità di sicurezza esterne vengono risolte nell'oggetto reale in Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Più foreste: a maglia completa con GALSync facoltativo
![Opzione per usare l'attributo Mail per trovare la corrispondenza quando le identità utenti sono presenti in più directory](./media/plan-connect-topologies/multiforestusersmail.png)

![Topologia a maglia completa per più foreste](./media/plan-connect-topologies/multiforestfullmesh.png)

Una topologia a maglia completa consente di individuare utenti e risorse in qualsiasi foresta. In genere esistono trust bidirezionali tra le foreste.

Se Exchange è presente in più di una foresta, potrebbe essere disponibile (facoltativamente) una soluzione GALSync locale. Ogni utente viene quindi rappresentato come un contatto in tutte le altre foreste. GALSync viene in genere implementato tramite FIM 2010 o MIM 2016. Azure AD Connect non può essere usato per la soluzione GALSync locale.

In questo scenario, gli oggetti relativi all'identità vengono aggiunti tramite l'attributo Mail. Un utente che ha una cassetta postale in una foresta viene aggiunto ai contatti nelle altre foreste.

### <a name="multiple-forests-account-resource-forest"></a>Più foreste, foresta di tipo account-risorse
![Opzione per usare gli attributi ObjectSID e msExchMasterAccountSID per trovare la corrispondenza quando le identità sono presenti in più directory](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Topologia di foresta di tipo account-risorse per più foreste](./media/plan-connect-topologies/multiforestaccountresource.png)

In una topologia di foresta account-risorse sono presenti una o più foreste di *account* con account utente attivi. Sono presenti anche una o più foreste di *risorse* con account disabilitati.

In questo scenario una o più foreste risorse considerano attendibili tutte le foreste account. La foresta risorse presenta in genere uno schema di Active Directory esteso con Exchange e Lync. Tutti i servizi Exchange e Lync, con altri servizi condivisi, si trovano in questa foresta. Gli utenti hanno un account utente disabilitato in questa foresta e la cassetta postale è collegata alla foresta account.

## <a name="microsoft-365-and-topology-considerations"></a>Considerazioni su Microsoft 365 e topologia
Alcuni carichi di lavoro Microsoft 365 presentano alcune restrizioni sulle topologie supportate:

| Carico di lavoro | Restrizioni |
| --------- | --------- |
| Exchange Online | Per altre informazioni sulle topologie ibride supportate da Exchange Online, vedere [Distribuzioni ibride con più insiemi di strutture di Active Directory](/Exchange/hybrid-deployment/hybrid-with-multiple-forests). |
| Skype for Business | Quando si usano più foreste locali, sarà supportata solo la topologia di tipo foresta account-risorse. Per altre informazioni, vedere [Requisiti ambientali di Skype for Business Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Se si è un'organizzazione più grande, è consigliabile usare la funzionalità [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) . che consente di definire l'area del data center in cui si trovano le risorse dell'utente.

## <a name="staging-server"></a>server di gestione temporanea
![Server di staging in una topologia](./media/plan-connect-topologies/multiforeststaging.png)

Azure AD Connect supporta l'installazione di un secondo server in *modalità di staging*. Un server in questa modalità legge dati da tutte le directory connesse, ma non vi esegue operazioni di scrittura. Usa il normale ciclo di sincronizzazione e ha quindi a disposizione una copia aggiornata dei dati di identità.

In una situazione di emergenza in cui si verifica un errore nel server primario è possibile eseguire il failover nel server di staging. A tale scopo è possibile usare la procedura guidata di Azure AD Connect. Il secondo server può trovarsi in un data center diverso perché nessuna infrastruttura viene condivisa con il server primario. Eventuali modifiche di configurazione apportate al server primario devono essere copiate manualmente nel secondo server.

È possibile usare un server di staging per testare una nuova configurazione personalizzata e il relativo effetto sui dati. È possibile visualizzare l'anteprima delle modifiche e perfezionare la configurazione. Dopo aver ottenuto la configurazione ottimale, sarà possibile rendere attivo il server di staging e impostare il server attivo precedente sulla modalità di staging.

È anche possibile usare questo metodo per sostituire il server di sincronizzazione attivo. Preparare il nuovo server e impostarlo in modalità di staging. Verificarne lo stato di integrità, disabilitare la modalità di staging rendendolo attivo e arrestare il server attivo corrente.

Per avere a disposizione più backup in data center diversi, è possibile avere più di un server di staging.

## <a name="multiple-azure-ad-tenants"></a>Più tenant di Azure AD
È consigliabile che in Azure AD sia presente un tenant singolo per un'organizzazione.
Prima di pianificare di usare più tenant di Azure AD, vedere l'articolo [Gestione delle unità amministrative in Azure AD](../roles/administrative-units.md) che illustra gli scenari comuni in cui è possibile usare un singolo tenant.

![Topologia per più foreste e più tenant](./media/plan-connect-topologies/multiforestmultidirectory.png)

Esiste una relazione 1:1 tra un server di sincronizzazione di Azure AD Connect e un tenant di Azure AD. Per ogni tenant di Azure AD è necessaria un'installazione del server del servizio di sincronizzazione Azure AD Connect. Le istanze del tenant di Azure AD sono isolate per impostazione predefinita. Gli utenti di un tenant infatti non possono visualizzare gli utenti dell'altro tenant. Se la separazione è necessaria, questa è una configurazione supportata. In caso contrario, è consigliabile usare il modello di tenant di Azure AD singolo.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Ogni oggetto una sola volta in un tenant di Azure AD
![Topologia con filtri per una foresta singola](./media/plan-connect-topologies/singleforestfiltered.png)

In questa topologia un server del servizio di sincronizzazione Azure AD Connect è connesso a ogni tenant di Azure AD. I server di sincronizzazione di Azure AD Connect devono essere configurati per l'applicazione di filtri, in modo che ogni server possa usare un set di oggetti che si escludono a vicenda. Ad esempio, è possibile definire l'ambito di ogni server in modo che corrisponda a un dominio oppure a un'unità organizzativa specifica.

Un dominio DNS può essere registrato unicamente in un tenant singolo di Azure AD. Anche gli UPN degli utenti nell'istanza locale di Active Directory devono usare spazi dei nomi separati. Ad esempio, nella figura precedente tre suffissi UPN separati vengono registrati nell'istanza locale di Active Directory: contoso.com, fabrikam.com e wingtiptoys.com. Gli utenti di ogni dominio di Active Directory locale usano uno spazio dei nomi diverso.

>[!NOTE]
>La sincronizzazione elenco indirizzi globale non viene eseguita automaticamente in questa topologia ed è necessaria un'implementazione MIM personalizzata aggiuntiva, in modo che ogni tenant abbia un elenco indirizzi globale in Exchange Online e Skype for Business Online.


Questa topologia presenta le restrizioni seguenti per scenari altrimenti supportati:

* Un massimo di 5 tenant di Azure Active Directory può avere un ibrido di Exchange con l'istanza di Active Directory locale. Questo scenario è descritto nell' [aggiornamento guidato della configurazione ibrida di settembre 2020](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* La configurazione guidata di Exchange Server in esecuzione ibrido deve essere 2016 CU18 o 2019 CU7 o versione successiva.
* Ogni istanza di Azure AD Connect deve essere eseguita in un computer aggiunto al dominio.
* Azure AD Connect deve essere configurato usando l'opzione di filtro dominio/unità organizzativa per filtrare gli utenti dalla directory locale. L'uso di questa opzione garantisce che gli utenti vengano visualizzati solo in un singolo tenant di Exchange Online.
* I dispositivi Windows 10 possono essere associati a un solo tenant di Azure AD.
* L'opzione Single Sign-On (SSO) per la sincronizzazione dell'hash delle password e l'autenticazione pass-through può essere usata solo con un tenant di Azure AD.

Il requisito relativo a un set di oggetti che si escludono a vicenda si applica anche al writeback. Alcune funzionalità di writeback non sono supportate con questa topologia, perché presuppongono una singola configurazione locale. Queste funzionalità includono:

* Writeback dei gruppi con la configurazione predefinita.
* Writeback dispositivi.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Ogni oggetto più volte in un tenant di Azure AD
![Topologia non supportata per una foresta singola e più tenant](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Topologia non supportata per una foresta singola e più connettori](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Queste attività non sono supportate:

* Sincronizzazione dello stesso utente con più tenant di Azure AD.
* Modifica della configurazione per fare in modo che gli utenti di un tenant di Azure AD vengano visualizzati come contatti in un altro tenant di Azure AD.
* Modifica del servizio di sincronizzazione Azure AD Connect per la connessione a più tenant di Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync tramite l'uso di writeback
![Topologia non supportata per più foreste e più directory, con GALSync incentrato su Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Topologia non supportata per più foreste e più directory, con GALSync incentrato su Active Directory locale](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

I tenant di Azure AD sono isolati per impostazione predefinita. Queste attività non sono supportate:

* Modifica della configurazione del servizio di sincronizzazione Azure AD Connect per la lettura di dati da un altro tenant di Azure AD.
* Esportazione di utenti come contatti in un'altra istanza locale di Active Directory con il servizio di sincronizzazione Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync con server di sincronizzazione locale
![GALSync in una topologia per più foreste e più directory](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

È possibile usare FIM 2010 o MIM 2016 locale per sincronizzare gli utenti (tramite GALSync) tra due organizzazioni di Exchange. Gli utenti di un'organizzazione vengono visualizzati come utenti/contatti esterni nell'altra organizzazione. Sarà quindi possibile sincronizzare le due istanze locali diverse di Active Directory con i rispettivi tenant di Azure AD.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Uso di client non autorizzati per accedere al back-end Azure AD Connect
![Uso di client non autorizzati per accedere al back-end Azure AD Connect](./media/plan-connect-topologies/other-client-unsupported.png)

Il server di Azure Active Directory Connect comunica con Azure Active Directory tramite il back-end Azure Active Directory Connect. L'unico software che può essere usato per comunicare con questo back-end è Azure Active Directory Connect. Non è supportata la comunicazione con il back-end Azure Active Directory Connect usando qualsiasi altro software o metodo. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come installare Azure AD Connect per questi scenari, vedere [Installazione personalizzata di Azure Ad Connect](how-to-connect-install-custom.md).

Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](how-to-connect-sync-whatis.md).

Altre informazioni sull' [integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
