---
title: Esecuzione di query sui dati Azure Time Series Insights Gen2 | Microsoft Docs
description: Cenni preliminari sulle query sui dati e sull'API REST in Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374134"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Esecuzione di query sui dati da Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 consente l'esecuzione di query sui dati su eventi e metadati archiviati nell'ambiente tramite API per la superficie pubblica. Queste API vengono usate anche da [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md).

In Azure Time Series Insights Gen2 sono disponibili tre categorie di API principali:

* **API dell'ambiente**: queste API consentono di eseguire query sull'ambiente di Azure Time Series Insights Gen2. Questi possono essere usati per raccogliere l'elenco di ambienti a cui il chiamante ha accesso e i metadati dell'ambiente.
* **API time series Model-Query (TSM-Q)**: Abilita le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD) sui metadati archiviati nel modello Time Series dell'ambiente. Questi possono essere usati per accedere e modificare le istanze, i tipi e le gerarchie.
* **API TSQ (Time Series query)**: consente il recupero di dati di telemetria o di eventi durante la registrazione dal provider di origine e consente di calcolare e aggregazioni efficienti sui dati utilizzando funzioni scalari e di aggregazione avanzate.

Azure Time Series Insights Gen2 utilizza un linguaggio delle espressioni basato su stringa, [ovvero TSX (Time Series Expression)](/rest/api/time-series-insights/reference-time-series-expression-syntax), per esprimere calcoli nelle [variabili Time Series](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Cenni preliminari sulle API Gen2 Azure Time Series Insights

Sono supportate le API principali seguenti.

[![Panoramica delle query su serie temporali](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>API di ambiente

* [Get environments API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): restituisce l'elenco di ambienti a cui il chiamante è autorizzato ad accedere.
* [Get environments Availability API](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): restituisce la distribuzione del numero di eventi sul timestamp dell'evento `$ts` . Questa API consente di determinare se sono presenti eventi nell'ambiente restituendo il numero di eventi suddivisi in intervalli di tempo, se presenti.
* [Get Event schema API](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): restituisce i metadati dello schema dell'evento per un intervallo di ricerca specificato. Tale API consente di recuperare tutti i metadati e le proprietà disponibili nello schema per l'intervallo di ricerca specificato.

## <a name="time-series-model-query-tsm-q-apis"></a>API TSM-Q (Time Series Model-Query)

La maggior parte di queste API supporta l'operazione di esecuzione batch per abilitare le operazioni CRUD batch su più entità del modello Time Series:

* [API delle impostazioni del modello](/rest/api/time-series-insights/reference-model-apis): Abilita *Get* e *patch* per il tipo predefinito e il nome del modello dell'ambiente.
* [API dei tipi](/rest/api/time-series-insights/reference-model-apis#types-api): Abilita CRUD sui tipi Time Series e sulle variabili associate.
* [API gerarchie](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): Abilita CRUD nelle gerarchie della serie temporale e nei percorsi dei campi associati.
* [API istanze](/rest/api/time-series-insights/reference-model-apis#instances-api): Abilita CRUD nelle istanze della serie temporale e nei campi di istanza associati. Inoltre, l'API instances supporta le operazioni seguenti:
  * [Search](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): Recupera un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.
  * [Suggerisci](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): Cerca e suggerisce un elenco parziale dei riscontri nella ricerca di istanze di serie temporali in base agli attributi dell'istanza.

## <a name="time-series-query-tsq-apis"></a>API TSQ (Time Series Query)

Queste API sono disponibili in entrambi i punti vendita (caldo e a freddo) nella soluzione di archiviazione a più livelli. 

* [Get Events API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): consente la query e il recupero di eventi non elaborati e dei timestamp degli eventi associati Man mano che vengono registrati nel Azure Time Series Insights Gen2 dal provider di origine. Questa API consente il recupero di eventi non elaborati per un determinato ID della serie temporale e un intervallo di ricerca. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato.

  > [!IMPORTANT]
  > Come parte delle [prossime modifiche apportate alle regole di escape e Flat JSON](./ingestion-rules-update.md), le matrici verranno archiviate come tipo **dinamico** . Le proprietà del payload archiviate come questo tipo sono **accessibili solo tramite l'API Get Events**.

* [Get Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): consente di eseguire query e recuperare i valori calcolati e i timestamp degli eventi associati applicando calcoli definiti dalle variabili in eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato.

* [API della serie aggregata](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): consente l'esecuzione di query e il recupero di valori aggregati e dei timestamp di intervallo associati applicando calcoli definiti dalle variabili in eventi non elaborati. Queste variabili possono essere definite nel modello Time Series o fornite inline nella query. Questa API supporta la paginazione per recuperare il set di dati completo della risposta per l'input selezionato.
  
  Per un intervallo e un intervallo di ricerca specificati, questa API restituisce una risposta aggregata per ogni intervallo per variabile per un ID di serie temporale. Il numero di intervalli nel set di dati della risposta viene calcolato contando i segni di graduazione dell'epoca (il numero di millisecondi trascorsi dal 1 ° gennaio 1970) e dividendo i segni di graduazione in base alle dimensioni dell'intervallo specificato nella query.

  I timestamp restituiti nel set di risposte sono i limiti dell'intervallo sinistro, non gli eventi campionati dall'intervallo.


### <a name="selecting-store-type"></a>Selezione del tipo di archivio

Le API precedenti possono essere eseguite solo su uno dei due tipi di archiviazione (a freddo o a caldo) in una singola chiamata. I parametri URL query vengono utilizzati per specificare il [tipo di archivio](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) in cui deve essere eseguita la query. 

Se non si specifica alcun parametro, per impostazione predefinita la query verrà eseguita nell'archivio a freddo. Se una query si estende in un intervallo di tempo che si sovrappone sia all'archivio a freddo che a quello caldo, è consigliabile instradare la query a Cold Store per ottenere la migliore esperienza perché warm Store conterrà solo dati parziali. 

[Azure Time Series Insights Explorer](./concepts-ux-panels.md) e il [connettore Power bi](./how-to-connect-power-bi.md) effettuano chiamate alle API precedenti e selezionano automaticamente il parametro storeType corretto, se pertinente. 


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle diverse variabili che possono essere definite nel [modello Time Series](./concepts-model-overview.md).
* Scopri di più su come eseguire query sui dati da [esplora Azure Time Series Insights](./concepts-ux-panels.md).
