---
title: Domini eventi in Griglia di eventi di Azure
description: Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali.
services: event-grid
author: femila
ms.service: event-grid
ms.author: femila
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 11ecf5c2a7bea8a9ef2047bdabcbfd4d844c5a3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559499"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informazioni sui domini eventi per la gestione di argomenti di Griglia di eventi

Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali. Usare i domini eventi per:

* Gestire architetture di gestione degli eventi su larga scala.
* Gestire l'autenticazione e l'autorizzazione.
* Partizionare gli argomenti senza doverli gestire singolarmente.
* Evitare di dover pubblicare singolarmente in ogni endpoint degli argomenti.

## <a name="event-domain-overview"></a>Panoramica del dominio eventi

Un dominio eventi è uno strumento per la gestione di un numero elevato di argomenti di Griglia di eventi correlati alla stessa applicazione. Si può paragonare a un meta-argomento che può avere migliaia di singoli argomenti.

I domini eventi mettono a disposizione la stessa architettura usata dai servizi di Azure, come Archiviazione e hub IoT, per pubblicare i propri eventi. Consentono di pubblicare eventi in migliaia di argomenti. I domini offrono anche il controllo dell'autenticazione e dell'autorizzazione su ogni argomento, consentendo di partizionare i tenant.

### <a name="example-use-case"></a>Esempio di caso d'uso

I domini eventi si possono comprendere più facilmente con un esempio. Supponiamo di eseguire contoso Operating Machinery, dove Produci i trattori, le attrezzature di scavo e altri macchinari pesanti. Quando si gestisce un'azienda, si esegue il push verso i clienti di informazioni in tempo reale riguardanti la manutenzione delle apparecchiature, l'integrità dei sistemi e gli aggiornamenti dei contratti. Tutte queste informazioni vengono inoltrate a vari endpoint, tra cui l'app, gli endpoint dei clienti e altre infrastrutture configurate dai clienti.

I domini eventi consentono di modellare Contoso Construction Machinery come una singola entità di gestione degli eventi. Ogni cliente viene rappresentato come un argomento all'interno del dominio. L'autenticazione e l'autorizzazione vengono gestite mediante Azure Active Directory. Ogni cliente può sottoscrivere il proprio argomento e ricevere i relativi eventi. L'accesso gestito tramite il dominio eventi garantisce che il cliente possa accedere solo al proprio argomento.

Offre inoltre un singolo endpoint, in cui è possibile pubblicare tutti gli eventi dei clienti. Griglia di eventi assicurerà che ogni argomento venga a conoscenza solo degli eventi con ambito a livello del proprio tenant.

![Esempio di Contoso Construction](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Gestione degli accessi

Con un dominio si ottiene il controllo granulare dell'autenticazione e dell'autorizzazione su ogni argomento, tramite il controllo degli accessi in base al ruolo di Azure. È possibile usare questi ruoli per limitare ogni tenant nell'applicazione ai soli argomenti a cui si vuole concedere l'accesso.

Il controllo degli accessi in base al ruolo nei domini eventi funziona allo stesso modo del [controllo di accesso gestito](security-authorization.md) nel resto di Griglia di eventi e di Azure. Usare il controllo degli accessi in base al ruolo per creare e applicare definizioni del ruolo personalizzate nei domini eventi.

### <a name="built-in-roles"></a>Ruoli predefiniti

Griglia di eventi include due definizioni del ruolo predefinite per semplificare l'uso del controllo degli accessi in base al ruolo con i domini eventi. Questi ruoli sono **EventSubscription EventGrid Contributor (Preview)** e **EventGrid EventSubscription Reader (Preview)** e vengono assegnati a utenti che necessitano di sottoscrivere argomenti del dominio eventi del proprietario. È possibile definire l'ambito dell'assegnazione di ruolo solo per l'argomento a cui gli utenti devono sottoscrivere.

Per informazioni su questi ruoli, vedere [Ruoli predefiniti per Griglia di eventi](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Sottoscrizione di argomenti

La sottoscrizione di eventi su un argomento all'interno di un dominio eventi equivale alla [creazione di una sottoscrizione di eventi su un argomento personalizzato](./custom-event-quickstart.md) o alla sottoscrizione di un evento da un servizio di Azure.

### <a name="domain-scope-subscriptions"></a>Sottoscrizioni di ambiti di dominio

I domini eventi consentono anche di sottoscrivere ambiti di dominio. Una sottoscrizione di eventi in un dominio eventi riceverà tutti gli eventi inviati al dominio, indipendentemente dall'argomento a cui gli eventi vengono inviati. Le sottoscrizioni di ambiti di dominio possono essere utili a scopi di gestione e controllo.

## <a name="publishing-to-an-event-domain"></a>Pubblicazione in un dominio eventi

Quando si crea un dominio eventi, si ottiene un endpoint di pubblicazione come se si fosse creato un argomento in Griglia di eventi. 

Per pubblicare gli eventi in un argomento di un dominio di evento, effettuare il push degli eventi nell'endpoint del dominio nello [stesso modo in cui si farebbe per un argomento personalizzato](./post-to-custom-topic.md). L'unica differenza è che è necessario specificare l'argomento al quale si vuole recapitare l'evento.

Ad esempio, pubblicando la matrice di eventi seguente, l'evento con `"id": "1111"` verrebbe inviato all'argomento `foo`, mentre l'evento con `"id": "2222"` verrebbe inviato all'argomento `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

I domini eventi gestiscono automaticamente la pubblicazione negli argomenti. Invece di pubblicare gli eventi in ogni argomento gestito individualmente, è possibile pubblicare tutti gli eventi nell'endpoint del dominio. Griglia di eventi assicurerà che ogni evento venga inviato all'argomento corretto.

## <a name="limits-and-quotas"></a>Limiti e quote
Di seguito sono riportati i limiti e le quote correlati ai domini eventi:

- argomenti 100.000 per dominio evento 
- 100 domini eventi per ogni sottoscrizione di Azure 
- 500 sottoscrizioni di eventi per argomento in un dominio di evento
- 50 sottoscrizioni ambito dominio 
- frequenza di inserimento degli eventi 5.000 al secondo (in un dominio)

Se questi limiti non soddisfano l'utente, contattare il team del prodotto aprendo un ticket di supporto o inviando un messaggio di posta elettronica a [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prezzi
I domini degli eventi utilizzano gli stessi [prezzi delle operazioni](https://azure.microsoft.com/pricing/details/event-grid/) utilizzate da tutte le altre funzionalità di griglia di eventi.

Nei domini eventi le operazioni funzionano come negli argomenti personalizzati. Ogni ingresso di un evento in un dominio eventi è un'operazione e ogni tentativo di recapito di un evento è un'operazione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione di domini eventi, sulla creazione di argomenti, sulla creazione di sottoscrizioni di eventi e sulla pubblicazione di eventi, vedere [gestire i domini di eventi](./how-to-event-domains.md).
