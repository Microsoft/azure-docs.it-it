---
title: Azure front door-monitoraggio delle regole di routing corrispondente | Microsoft Docs
description: Questo articolo consente di comprendere il modo in cui la regola di routing da usare per una richiesta in ingresso corrisponde a quella di Azure.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 420aa52293da14a0dfe8fbdfe681440ee4309e6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80878596"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Individuazione della corrispondenza tra le richieste e una regola di routing in Frontdoor

Dopo aver stabilito una connessione ed eseguito un handshake TLS, quando una richiesta si trova in un ambiente di sportello anteriore, una delle prime cose che la porta principale è quella di determinare da tutte le configurazioni, quale regola di routing specifica in modo che corrisponda alla richiesta e quindi a eseguire l'azione definita. Il documento seguente illustra in che modo Frontdoor determina la configurazione di routing da usare durante l'elaborazione di una richiesta HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struttura di una configurazione di routing di Frontdoor
Una configurazione di una regola di routing di Frontdoor è costituita da due parti principali: un "lato sinistro" e un "lato destro". Viene verificata la corrispondenza della richiesta in ingresso con il lato sinistro della route, mentre il lato destro definisce la modalità di elaborazione della richiesta.

### <a name="incoming-match-left-hand-side"></a>Corrispondenza in ingresso (lato sinistro)
Le seguenti proprietà determinano se la richiesta in ingresso corrisponde alla regola di routing (o lato sinistro):

* **Protocolli HTTP** (HTTP/HTTPS)
* **Host** (ad esempio, www \. foo.com, \* . bar.com)
* **Percorsi** (ad esempio, /\*, /users/\*, /file.gif)

Queste proprietà vengono espanse internamente, pertanto ogni combinazione di protocollo/host/percorso è un set di corrispondenze possibili.

### <a name="route-data-right-hand-side"></a>Dati della route (lato destro)
La decisione su come elaborare la richiesta dipende dal fatto che la memorizzazione nella cache sia abilitata o meno per la specifica route. Pertanto, se non è disponibile una risposta memorizzata nella cache per la richiesta, la richiesta sarà inoltrata al back-end appropriato nel pool back-end configurato.

## <a name="route-matching"></a>Corrispondenza delle route
Questa sezione descrive la modalità di individuazione della corrispondenza per una determinata regola di routing in Frontdoor. Il concetto di base è che viene sempre verificata **prima la corrispondenza più specifica** esaminando solo il "lato sinistro".  Viene prima verificata la corrispondenza in base al protocollo HTTP, quindi in base all'host front-end e infine in base al percorso.

### <a name="frontend-host-matching"></a>Corrispondenza di host front-end
Per la corrispondenza degli host front-end, viene usata la logica seguente:

1. Cercare eventuali routing con una corrispondenza esatta per l'host.
2. Se nessun host front-end corrisponde in modo esatto, rifiutare la richiesta e inviare un errore 400 - Richiesta non valida.

Per spiegare ulteriormente questo processo, esaminiamo una configurazione di esempio delle route di Frontdoor (solo lato sinistro):

| Regola di routing | Host front-end | Percorso |
|-------|--------------------|-------|
| Una | foo.contoso.com | /\* |
| b | foo.contoso.com | /users/\* |
| C | www \. Fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Se venissero inviate a Frontdoor, le richieste in ingresso seguenti corrisponderebbero alle regole di routing indicate di seguito dall'esempio precedente:

| Host front-end in ingresso | Regole di routing corrispondenti |
|---------------------|---------------|
| foo.contoso.com | A, B |
| \.Fabrikam.com www | C |
| images.fabrikam.com | Errore 400 - Richiesta non valida |
| foo.adventure-works.com | C |
| contoso.com | Errore 400 - Richiesta non valida |
| \.Adventure-Works.com www | Errore 400 - Richiesta non valida |
| \.northwindtraders.com www | Errore 400 - Richiesta non valida |

### <a name="path-matching"></a>Corrispondenza del percorso
Dopo aver determinato lo specifico host front-end e filtrato le possibili regole di routing solo alle route con tale host front-end, Frontdoor filtra le regole di routing in base al percorso della richiesta. Usiamo una logica simile a quella per gli host front-end:

1. Cercare eventuali regole di routing con una corrispondenza esatta per il percorso
2. Se nessun percorso corrisponde in modo esatto, cercare le regole di routing con un percorso con caratteri jolly corrispondente
3. Se non viene trovata alcuna regola di routing con un percorso corrispondente, rifiutare la richiesta e restituire una risposta HTTP di errore 400 - Richiesta non valida.

>[!NOTE]
> Eventuali percorsi senza caratteri jolly vengono considerati percorsi di corrispondenza esatta. Anche se il percorso termina con una barra, viene comunque considerato una corrispondenza esatta.

Per chiarire ulteriormente il processo, esaminiamo un altro set di esempi:

| Regola di routing | Host front-end    | Percorso     |
|-------|---------|----------|
| Una     | www\.contoso.com | /        |
| b     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Tale configurazione produrrà la tabella corrispondente di esempio seguente :

| Richiesta in ingresso    | Route corrispondente |
|---------------------|---------------|
| \.contoso.com/www            | Una             |
| \.contoso.com/a www           | b             |
| \.contoso.com/AB www          | C             |
| \.contoso.com/ABC www         | D             |
| \.contoso.com/abzzz www       | b             |
| \.contoso.com/ABC/www        | E             |
| \.contoso.com/ABC/d www       | F             |
| \.contoso.com/ABC/def www     | G             |
| \.contoso.com/ABC/defzzz www  | F             |
| \.contoso.com/ABC/def/ghi www | F             |
| \.contoso.com/path www        | b             |
| \.contoso.com/path/www       | H             |
| \.contoso.com/path/zzz www    | b             |

>[!WARNING]
> </br> Se non sono presenti regole di routing per un host front-end con corrispondenza esatta con un percorso di route catch-all (`/*`), non vi sarà alcuna corrispondenza con alcuna regola di routing.
>
> Configurazione di esempio:
>
> | Route | Host             | Percorso    |
> |-------|------------------|---------|
> | Una     | profile.contoso.com | /api/\* |
>
> Tabella corrispondente:
>
> | Richiesta in ingresso       | Route corrispondente |
> |------------------------|---------------|
> | profile.domain.com/other | No. Errore 400 - Richiesta non valida |

### <a name="routing-decision"></a>Decisione di routing
Dopo aver individuato la corrispondenza con una singola regola di routing di Frontdoor, è necessario scegliere la modalità di elaborazione della richiesta. Se per la regola di routing corrispondente Frontdoor dispone di una risposta memorizzata nella cache, questa viene restituita al client. In caso contrario, l'elemento successivo che viene valutato è se è stata configurata o meno la [riscrittura URL (percorso di inoltro personalizzato)](front-door-url-rewrite.md) per la regola di routing corrispondente. Se non è definito un percorso di inoltro personalizzato, la richiesta viene inoltrata così com'è al back-end appropriato nel pool back-end configurato. In caso contrario, il percorso della richiesta viene aggiornato in base al [percorso di inoltro personalizzato](front-door-url-rewrite.md) definito e quindi inoltrata al back-end.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
