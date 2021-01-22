---
title: Concetti relativi a Griglia di eventi di Azure
description: Vengono descritti il servizio Griglia di eventi di Azure e i concetti correlati. Vengono definiti diversi componenti chiave di Griglia di eventi.
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 6edc8a3980bfea15f28cfb7114bb9f8350a47a3f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685704"
---
# <a name="concepts-in-azure-event-grid"></a>Concetti di Griglia di eventi di Azure

Questo articolo illustra i concetti principali di Griglia di eventi di Azure.

## <a name="events"></a>Eventi

Un evento è la quantità minima di informazioni che descrive in modo completo qualcosa che si è verificato nel sistema. Ogni evento ha informazioni comuni, come: l'origine dell'evento, l'ora in cui l'evento si è verificato e un identificatore univoco. Ogni evento ha anche informazioni specifiche rilevanti solo per il tipo di evento specifico. Un evento di creazione di un nuovo file in Archiviazione di Azure, ad esempio, contiene i dettagli sul file, come il valore `lastTimeModified`. Un evento di Hub eventi include invece l'URL del file di acquisizione. 

Le dimensioni massime consentite per un evento sono pari a 1 MB. Gli eventi oltre 64 KB vengono addebitati in incrementi di 64 KB. Per le proprietà che vengono inviate in un evento, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="publishers"></a>Autori

Un editore è un utente o un'organizzazione che decide di inviare eventi a Griglia di eventi. Microsoft pubblica gli eventi per diversi servizi di Azure. È possibile pubblicare eventi dalla propria applicazione. Le organizzazioni che ospitano servizi all'esterno di Azure possono pubblicare eventi tramite Griglia di eventi.

## <a name="event-sources"></a>Origini eventi

Un'origine evento è la posizione in cui si verifica l'evento. Ogni origine evento è correlata a uno o più tipi di evento. Archiviazione di Azure è, ad esempio, l'origine degli eventi creati dai BLOB. Hub IoT è l'origine degli eventi creati dai dispositivi. L'applicazione è l'origine degli eventi personalizzati definiti dall'utente. Le origini eventi sono responsabili dell'invio degli eventi a Griglia di eventi.

Per informazioni sull'implementazione di una delle origini di Griglia di eventi supportate, vedere [Event sources in Azure Event Grid](overview.md#event-sources) (Origini eventi in Griglia di eventi di Azure).

## <a name="topics"></a>Argomenti

L'argomento di Griglia di eventi include un endpoint a cui l'origine invia gli eventi. L'editore crea l'argomento di Griglia di eventi e decide se un'origine evento necessita di uno o più argomenti. L'argomento viene usato per una raccolta di eventi correlati. Per rispondere a determinati tipi di eventi, i sottoscrittori scelgono gli argomenti da sottoscrivere.

Gli **argomenti di sistema** sono argomenti predefiniti forniti da servizi di Azure, ad esempio archiviazione di Azure, Hub eventi di Azure e il bus di servizio di Azure. È possibile creare argomenti di sistema nella sottoscrizione di Azure e sottoscriverli. Per ulteriori informazioni, vedere [Cenni preliminari sugli argomenti di sistema](system-topics.md). 

Gli **argomenti personalizzati** sono argomenti dell'applicazione e di terze parti. Quando l'utente crea o gli viene assegnato l'accesso a un argomento personalizzato, tale argomento personalizzato viene visualizzato nella sottoscrizione. Per ulteriori informazioni, vedere [argomenti personalizzati](custom-topics.md). Quando si progetta l'applicazione, è possibile decidere il numero di argomenti da creare. Per soluzioni di grandi dimensioni, creare un argomento personalizzato per ogni categoria di eventi correlati. Ad esempio, si consideri un'applicazione che invia gli eventi correlati alla modifica degli account utente e all'elaborazione degli ordini. È improbabile che un gestore eventi richieda entrambe le categorie di eventi. Creare due argomenti personalizzati e consentire ai gestori eventi di sottoscrivere l'argomento di interesse. Per le soluzioni di dimensioni ridotte, è preferibile inviare tutti gli eventi a un singolo argomento. I sottoscrittori di eventi possono filtrare in base ai tipi di eventi desiderati.

È disponibile un altro tipo di argomento: **argomento del partner**. La funzionalità [eventi partner](partner-events-overview.md) consente a un provider SaaS di terze parti di pubblicare gli eventi dai relativi servizi per renderli disponibili agli utenti che possono sottoscrivere tali eventi. Il provider SaaS espone un tipo di argomento, un **argomento partner**, che i Sottoscrittori utilizzano per utilizzare gli eventi. Offre inoltre un modello di pubblicazione-sottoscrizione pulito, separando i problemi e la proprietà delle risorse utilizzate dai Publisher e dai sottoscrittori di eventi.

## <a name="event-subscriptions"></a>Sottoscrizioni di eventi

Una sottoscrizione indica a Griglia di eventi gli eventi in un argomento che l'utente desidera ricevere. Quando si crea la sottoscrizione l'utente indica un endpoint per la gestione dell'evento. È possibile filtrare gli eventi inviati all'endpoint. È possibile filtrare in base a tipo di evento o criterio di oggetto. Per altre informazioni, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).

Per esempi sulla creazione di sottoscrizioni, vedere:

* [Esempi dell'interfaccia della riga di comando di Azure per la Griglia di eventi](cli-samples.md)
* [Esempi di Azure PowerShell per Griglia di eventi](powershell-samples.md)
* [Modelli di Azure Resource Manager per Griglia di eventi](template-samples.md)

Per informazioni su come ottenere le attuali sottoscrizioni di Griglia di eventi, vedere [Sottoscrizioni di Griglia di eventi di Azure](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Scadenza della sottoscrizione di eventi
La sottoscrizione dell'evento scade automaticamente dopo tale data. Impostare una scadenza per le sottoscrizioni di eventi necessarie solo per un periodo di tempo limitato in modo da non doversi preoccupare della pulizia delle sottoscrizioni. Ad esempio, quando si crea una sottoscrizione di eventi per testare uno scenario, è consigliabile impostare una scadenza. 

Per un esempio di impostazione di una scadenza, vedere [Creare una sottoscrizione con filtri avanzati](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Gestori eventi

Dal punto di vista di Griglia di eventi, un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Griglia di eventi supporta diversi tipi di gestori. È possibile usare un servizio di Azure supportato o il proprio webhook come gestore. A seconda del tipo di gestore, Griglia di eventi segue meccanismi diversi per garantire il recapito dell'evento. Per i gestori eventi webhook HTTP, l'evento viene ripetuto fino a quando il gestore non restituisce un codice di stato `200 – OK`. Per la coda di Archiviazione di Azure, gli eventi vengono ripetuti fino a quando il servizio di accodamento non riesce a elaborare correttamente il push del messaggio nella coda.

Per informazioni sull'implementazione di una delle origini di Griglia di eventi supportate, vedere [Event handlers in Azure Event Grid](event-handlers.md) (Gestori di eventi in Griglia di eventi di Azure).

## <a name="security"></a>Security

Griglia di eventi fornisce la sicurezza per la sottoscrizione e la pubblicazione degli argomenti. Quando si esegue la sottoscrizione, è necessario possedere le autorizzazioni appropriate per la risorsa o l'argomento di Griglia di eventi. Quando si esegue la pubblicazione, è necessario avere un token di firma di accesso condiviso o l'autenticazione con chiave per l'argomento. Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

## <a name="event-delivery"></a>Recapito di eventi

Se Griglia di eventi non è in grado di confermare che un evento è stato ricevuto dall'endpoint del sottoscrittore, esegue di nuovo il recapito dell'evento. Per altre informazioni, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).

## <a name="batching"></a>Creazione di batch

Quando si usa un argomento personalizzato, gli eventi devono sempre essere pubblicati in una matrice. Questa può essere un batch per gli scenari con velocità effettiva bassa. Tuttavia, per casi d'uso con volumi elevati, si consiglia di eseguire in batch più eventi contemporaneamente per ogni pubblicazione, per ottenere una maggiore efficienza. I batch possono essere fino a 1 MB e le dimensioni massime di un evento sono pari a 1 MB. 

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
