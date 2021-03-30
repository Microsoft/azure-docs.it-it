---
title: Panoramica e concetti chiave di Gestione API di Azure | Documentazione Microsoft
description: Informazioni su API, prodotti, ruoli, gruppi e altri concetti chiave di Gestione API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 85fa79cdfc7036be5b0ab20e49986a1d075152c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86254657"
---
# <a name="about-api-management"></a>Informazioni su Gestione API

Gestione API consente di creare rapidamente gateway API coerenti e moderni per servizi back-end esistenti.

Gestione API consente alle organizzazioni di pubblicare API per consentire a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei rispettivi dati e servizi. Le aziende di tutto il mondo avvertono l'esigenza di estendere le proprie attività come piattaforma digitale, creando nuovi canali, trovando nuovi clienti e stimolando un coinvolgimento maggiore con quelli esistenti. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. È possibile usare Gestione API di Azure per avviare un programma API completo basato su qualsiasi back-end.

Questo articolo offre una panoramica degli scenari comuni correlati a Gestione API.  Viene fornita anche una breve panoramica dei componenti principali del sistema di Gestione API. Infine, ogni componente viene analizzato più in dettaglio.

## <a name="overview"></a>Panoramica

Per usare Gestione API, gli amministratori creano API. Ogni API è costituita da una o più operazioni e ogni API può essere aggiunta a uno o più prodotti. Per usare un'API, gli sviluppatori sottoscrivono un prodotto che contiene tale API, quindi possono chiamare l'operazione dell'API, in base ai criteri di utilizzo eventualmente applicabili. Gli scenari comuni includono:

* **protezione dell'infrastruttura mobile** con la delega dell'accesso con le chiavi API, la prevenzione degli attacchi DOS con la limitazione oppure l'uso di criteri di sicurezza avanzata come la convalida dei token JWT.
* **abilitazione degli ecosistemi del partner ISV** , offrendo al partner un rapido caricamento attraverso il portale degli sviluppatori e compilando un'interfaccia API per la separazione dalle implementazioni interne non pronte per l'utilizzo da parte del partner.
* **esecuzione di un programma API interno** , offrendo all'organizzazione una posizione centralizzata per la comunicazione della disponibilità e delle ultime modifiche apportate alle API e delegando l'accesso in base agli account aziendali, tutti basati su un canale protetto tra il gateway dell'API e il back-end.

Il sistema è costituito dai componenti seguenti:

* Il **gateway API** è l'endpoint che:
  
  * Accetta chiamate API e le indirizza al back-end.
  * Verifica le chiavi API, i token JWT, i certificati e altre credenziali.
  * Applica le quote di utilizzo e i limiti di velocità.
  * Trasforma rapidamente l'API senza apportare modifiche al codice.
  * Memorizza nella cache le risposte back-end configurate.
  * Registra i metadati della chiamata a scopo di analisi.
* Il **portale di Azure** è l'interfaccia amministrativa in cui si configura il programma API. Può essere usato per:
  
  * Definire o importare lo schema API.
  * Creare pacchetti di API nei prodotti.
  * Configurare criteri come quote o trasformazioni sulle API.
  * Ottenere informazioni dall'analisi.
  * Gestire gli utenti.
* Il **portale per sviluppatori** funge da principale presenza Web per gli sviluppatori, che potranno:
  
  * Leggere la documentazione relativa alle API.
  * Provare un'API usando la console interattiva.
  * Creare un account e sottoscrivere per ottenere le chiavi API.
  * Accedere all'analisi di utilizzo personalizzata.

Per altre informazioni, vedere il white paper in formato PDF [Cloud-based API Management: Harnessing the Power of APIs](https://j.mp/ms-apim-whitepaper) sulla gestione delle API basate sul cloud. Questo white paper introduttivo sulla gestione delle API redatto da CITO Research tratta gli argomenti seguenti: 
 
 * Problematiche e requisiti comuni delle API
 * Disaccoppiamento delle API e presentazione delle facciate
 * Rendere operativi gli sviluppatori rapidamente
 * Protezione dell'accesso
 * Analisi e metrica
 * Ottenere il controllo e la conoscenza con una piattaforma di gestione API
 * Uso di soluzioni cloud e locali a confronto
 * Gestione API di Azure
 
## <a name="apis-and-operations"></a><a name="apis"> </a>API e operazioni
Le API costituiscono la base di un'istanza del servizio Gestione API. Ogni API rappresenta un set di operazioni a disposizione degli sviluppatori. Ogni API contiene un riferimento al servizio back-end che implementa l'API e le relative operazioni sono mappate alle operazioni implementate dal servizio back-end. Le operazioni in Gestione API sono altamente configurabili e offrono il controllo sul mapping degli URL, sui parametri di query e percorsi, sul contenuto della richiesta e della risposta e sulla memorizzazione nella cache della risposta delle operazioni. È anche possibile implementare i criteri relativi a restrizioni IP, quote e limitazione di frequenza al livello dell'API o della singola operazione.

Per altre informazioni, vedere [Come creare API in Gestione API di Azure][How to create APIs] e [Come aggiungere operazioni a un'API in Gestione API di Azure][How to add operations to an API].

## <a name="products"></a><a name="products"></a> Prodotti
I prodotti rappresentano il modo in cui le API sono presentate agli sviluppatori. I prodotti in Gestione API contengono una o più API e sono configurati con un titolo, una descrizione e le condizioni per l'utilizzo. I prodotti possono essere **aperti** o **protetti**. Per usare i prodotti protetti, è prima di tutto necessaria una sottoscrizione, mentre i prodotti aperti possono essere usati senza sottoscrizione. Quando un prodotto è pronto per essere usato dagli sviluppatori, può essere pubblicato. Dopo la pubblicazione, gli sviluppatori possono visualizzare il prodotto e, nel caso dei prodotti protetti, possono sottoscriverlo. L'approvazione della sottoscrizione è configurata al livello del prodotto e può richiedere l'approvazione dell'amministratore o essere approvata automaticamente.

I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. I prodotti garantiscono la visibilità ai gruppi e gli sviluppatori possono visualizzare ed effettuare la sottoscrizione ai prodotti visibili ai gruppi ai quali appartengono. 

## <a name="groups"></a><a name="groups"></a> Gruppi di
I gruppi permettono di gestire la visibilità dei prodotti agli sviluppatori. Gestione API include i gruppi di sistema non modificabili seguenti:

* **Amministratori**: gli amministratori delle sottoscrizioni di Azure appartengono a questo gruppo. Gli amministratori gestiscono le istanze del servizio Gestione API e creano le API, le operazioni e i prodotti usati dagli sviluppatori.
* **Sviluppatori**: gli utenti autenticati del portale per sviluppatori sono inclusi in questo gruppo. Gli sviluppatori sono i clienti che compilano applicazioni usando le API. Agli sviluppatori viene concesso di accedere al portale per sviluppatori e di creare applicazioni che chiamano le operazioni di un'API.
* **Guest** : gli utenti non autenticati del portale per sviluppatori, ad esempio i potenziali clienti, che visitano il portale per sviluppatori di un'istanza di Gestione API rientrano in questo gruppo. È possibile concedere agli utenti guest un determinato livello di accesso di sola lettura, ad esempio la possibilità di visualizzare le API ma non di chiamarle.

Oltre a questi gruppi di sistema, gli amministratori possono creare gruppi personalizzati oppure [sfruttare i gruppi esterni nei tenant di Azure Active Directory associati](api-management-howto-aad.md). È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API. Ad esempio, si può creare un gruppo personalizzato per gli sviluppatori affiliati a un'organizzazione partner specifica e consentire loro di accedere alle API da un prodotto che contiene solo le API pertinenti. Un utente può essere un membro di più gruppi.

Per ulteriori informazioni, vedere  [come creare e usare i gruppi][How to create and use groups].

## <a name="developers"></a><a name="developers"></a> Sviluppatori
Gli sviluppatori rappresentano gli account utente nell'istanza del servizio Gestione API. Gli sviluppatori possono essere creati o invitati a partecipare dagli amministratori oppure possono iscriversi nel [Portale per sviluppatori][Developer portal]. Ogni sviluppatore è membro di uno o più gruppi e può effettuare la sottoscrizione ai prodotti che concedono la visibilità a tali gruppi.

Quando gli sviluppatori effettuano la sottoscrizione a un prodotto, viene concessa loro la chiave primaria e secondaria per il prodotto. Questa chiave viene usata per effettuare le chiamate alle API del prodotto.

Per altre informazioni, vedere [Come creare o invitare sviluppatori][How to create or invite developers] e [Come associare gruppi a sviluppatori][How to associate groups with developers].

## <a name="policies"></a><a name="policies"></a> Criteri di
I criteri sono una funzionalità avanzata di Gestione API e consentono al portale di Azure di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare il numero di chiamate in ingresso da uno sviluppatore. Sono anche disponibili numerosi altri criteri.

Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose](./api-management-advanced-policies.md#choose) e [set variable](./api-management-advanced-policies.md#set-variable), sono basati su espressioni di criteri. Per altre informazioni, vedere [criteri avanzati](./api-management-advanced-policies.md#AdvancedPolicies) ed [espressioni di criteri](./api-management-policy-expressions.md).


Per un elenco completo dei criteri di Gestione API, vedere [Riferimenti per i criteri][Policy reference]. Per altre informazioni sull'uso e la configurazione dei criteri, vedere [Criteri di Gestione API][API Management policies]. Per un'esercitazione sulla creazione di un prodotto con criteri per la limitazione della frequenza e per le quote, vedere [Come creare e configurare impostazioni di prodotto avanzate][How create and configure advanced product settings].


## <a name="developer-portal"></a><a name="developer-portal"></a> Portale per sviluppatori
Il portale per sviluppatori consente agli sviluppatori di visualizzare informazioni sulle API, visualizzare e chiamare operazioni ed effettuare la sottoscrizione ai prodotti. I potenziali clienti possono visitare il portale per sviluppatori, visualizzare API e operazioni ed effettuare l'iscrizione. L'URL del portale per sviluppatori è disponibile nel dashboard del portale di Azure per l'istanza del servizio Gestione API.

È possibile personalizzare l'aspetto del portale per sviluppatori aggiungendo contenuto personalizzato, personalizzando gli stili e aggiungendo il proprio logo.

## <a name="api-management-and-the-api-economy"></a>Gestione API ed economia delle API

Per altre informazioni su Gestione API, guardare la presentazione seguente dalla conferenza Microsoft Ignite 2017.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Passaggi successivi

Completare la guida introduttiva seguente e iniziare a usare Gestione API:

> [!div class="nextstepaction"]
> [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
