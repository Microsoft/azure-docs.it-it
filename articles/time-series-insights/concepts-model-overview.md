---
title: Modello Time Series-Azure Time Series Insights | Microsoft Docs
description: Informazioni sul modello Time Series in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: c5a22987b1d67f9e9f8384e5376343af2f91b5e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049850"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modello Time Series in Azure Time Series Insights Preview

Questo articolo descrive il modello Time Series, le funzionalità e come iniziare a creare e aggiornare i propri modelli nell'ambiente di Azure Time Series Insights Preview.

> [!TIP]
>
> * Passare all'ambiente [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) per un esempio di modello Live Time Series.
> * Per informazioni su come spostarsi nell'interfaccia utente del modello Time Series, vedere informazioni su [Azure Time Series Insights Preview Explorer](time-series-insights-update-explorer.md) .
> * Informazioni [su come usare il modello Time Series](time-series-insights-update-how-to-tsm.md) con esplora Web Time Series Insights.

## <a name="summary"></a>Summary

I dati raccolti dai dispositivi IoT non includono in genere informazioni contestuali, cosa che rende difficile trovare e analizzare rapidamente i sensori. La motivazione principale per il modello Time Series è semplificare la ricerca e l'analisi dei dati delle serie temporali o degli Internet. Questo obiettivo viene raggiunto consentendo la cura, la manutenzione e l'arricchimento dei dati relativi alle serie temporali per preparare i set di dati pronti per il consumo per l'analisi.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: nuovo forno intelligente di contoso

**Si consideri lo scenario fittizio di uno smart forno contoso.** In questo scenario, si supponga che ogni Smart forno Contoso disponga di cinque sensori di temperatura, uno per ognuno dei quattro masterizzatori principali e uno per il forno stesso. Fino a poco tempo fa, ogni sensore di temperatura di Contoso ha inviato, archiviato e visualizzato i dati singolarmente. Per il monitoraggio dell'appliance di cucina, Contoso si basa sui grafici di base, uno per ogni singolo sensore.

Sebbene Contoso sia stato soddisfatto con la soluzione iniziale di dati e visualizzazione, sono emerse diverse limitazioni:

* I clienti hanno voluto capire il livello di surriscaldamento del forno quando la maggior parte dei principali masterizzatori si trovava in questo momento. Contoso ha avuto più difficoltà nell'analizzare e presentare una risposta unificata sulle condizioni del forno generale.
* I tecnici di Contoso volevano verificare che i primi masterizzatori eseguiti simultaneamente non comportino un risparmio energetico inefficiente. Si è verificata una difficoltà nel riferimento incrociato che i sensori di temperatura e tensione erano associati tra loro e come individuarli nell'archivio.
* Il team di Contoso Quality Assurance voleva controllare e confrontare la cronologia tra due versioni del sensore. Si è verificato un problema durante la determinazione dei dati appartenenti alla versione del sensore.

Senza la possibilità di strutturare, organizzare e definire il modello Time Series per lo Smart forno, ogni sensore di temperatura mantiene i punti dati dislocati, isolati e meno informativi. Trasformare questi punti dati in informazioni dettagliate di utilità pratica è stato più difficile perché ogni set di dati viveva in modo indipendente dagli altri.

Queste limitazioni hanno rivelato l'importanza degli strumenti di aggregazione e visualizzazione dei dati intelligenti per accompagnare il nuovo forno di Contoso:

* La visualizzazione dei dati risulta utile quando si è in grado di associare e combinare i dati in una visualizzazione pratica. Un esempio mostra i sensori di tensione insieme ai sensori di temperatura.
* La gestione dei dati multidimensionali per diverse entità, insieme alle funzionalità di confronto, zoom e intervallo di tempo, può essere difficile da realizzare.

Il **modello Time Series rappresenta una soluzione pratica** per molti degli scenari rilevati in questo esempio fittizio:

[![Esempio di grafico della serie temporale modello di Smart forno](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Il modello Time Series gioca un ruolo fondamentale nelle query e nello spostamento perché contestualizzare i dati consentendo il disegno di confronti tra intervalli di tempo e tra i tipi di sensore e dispositivo. (**A**)
* I dati sono ulteriormente contestuale perché i dati salvati in modo permanente nel modello Time Series conservano i calcoli delle query in serie temporali come variabili e li riutilizza in fase di query.
* Il modello Time Series organizza e aggrega i dati per migliorare la visualizzazione e le funzionalità di gestione. (**B**)

### <a name="key-capabilities"></a>Funzionalità principali

Allo scopo di semplificare e agevolare la gestione della contestualizzazione delle serie temporali, il modello di serie temporale abilita le funzionalità seguenti in Anteprima di Time Series Insights. Consente di:

* Consente di creare e gestire calcoli o formule sfruttando funzioni scalari, operazioni di aggregazione e così via.
* Definire le relazioni padre-figlio per consentire lo spostamento, la ricerca e il riferimento.
* Definire le proprietà associate alle istanze, definite come campi dell' *istanza*, e usarle per creare gerarchie.

### <a name="components"></a>Componenti

Il modello Time Series è costituito da tre componenti principali:

* [Istanze del modello Time Series](#time-series-model-instances)
* [Gerarchie di modelli Time Series](#time-series-model-hierarchies)
* [Tipi di modelli serie temporale](#time-series-model-types)

Questi componenti vengono combinati per specificare un modello Time Series e per organizzare i dati Azure Time Series Insights.

[![Grafico di panoramica del modello Time Series](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

È possibile creare e gestire un modello Time Series tramite l'interfaccia [Time Series Insights Preview](time-series-insights-update-how-to-tsm.md) . Le impostazioni del modello Time Series possono essere gestite tramite l' [API delle impostazioni del modello](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Istanze di modello serie temporale

Le *istanze* del modello Time Series sono rappresentazioni virtuali delle serie temporali.

Nella maggior parte dei casi, le istanze vengono identificate in modo univoco da **DeviceID** o **AssetID**, che vengono salvate come ID di serie temporali.

Alle istanze sono associate informazioni descrittive denominate *proprietà dell'istanza*, ad esempio un ID della serie temporale, un tipo, un nome, una descrizione, gerarchie e campi di istanza. Come minimo, le proprietà delle istanze includono informazioni sulla gerarchia.

I *campi di istanza* sono una raccolta di informazioni descrittive che possono includere valori per i livelli della gerarchia, nonché produttore, operatore e così via.

Dopo la configurazione di un'origine evento per l'ambiente Time Series Insights, le istanze vengono individuate e create automaticamente in un modello Time Series. Le istanze possono essere create o aggiornate tramite Esplora Time Series Insights usando le query del modello Time Series.

La [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) offre diversi esempi di istanze attive.

[![Esempio di istanza di modello Time Series](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Proprietà istanza

Le istanze sono definite da **timeSeriesId**, **typeid**, **Name**, **Description**, **hierarchyids**e **instanceFields**. Ogni istanza esegue il mapping a un solo *tipo*e una o più *gerarchie*.

| Proprietà | Descrizione |
| --- | ---|
| timeSeriesId | ID univoco della serie temporale a cui è associata l'istanza. Nella maggior parte dei casi, le istanze vengono identificate in modo univoco da una proprietà come deviceId o assetId. In alcuni casi, è possibile usare un ID composito più specifico che combina fino a tre proprietà. |
| typeId | ID di stringa univoco con distinzione tra maiuscole e minuscole del tipo di modello Time Series a cui è associata l'istanza. Per impostazione predefinita, tutte le nuove istanze individuate vengono associate a un tipo predefinito.
| name | La proprietà **Name** è facoltativa e con distinzione tra maiuscole e minuscole. Se il **nome** non è disponibile, il valore predefinito è **timeSeriesId**. Se viene specificato un nome, **timeSeriesId** è ancora disponibile nel [pozzetto](time-series-insights-update-explorer.md#4-time-series-well). |
| description | Descrizione di testo dell'istanza. |
| hierarchyId | Definisce le gerarchie a cui appartiene l'istanza. |
| instanceFields | Proprietà di un'istanza di e di qualsiasi dato statico che definisce un'istanza di. Definiscono i valori delle proprietà non di gerarchia o di gerarchia, supportando anche l'indicizzazione per eseguire operazioni di ricerca. |

> [!NOTE]
> Le gerarchie vengono compilate utilizzando campi di istanza. Per ulteriori definizioni delle proprietà di istanza, è possibile aggiungere **instanceFields** aggiuntive.

Le istanze hanno la rappresentazione JSON seguente:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Per Time Series Insights API dell'istanza e il supporto di creazione, lettura, aggiornamento ed eliminazione (CRUD), vedere l'articolo sull' [esecuzione di query sui dati](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest dell'API dell'istanza](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Gerarchie di modelli serie temporale

Le *gerarchie* dei modelli Time Series organizzano le istanze specificando i nomi delle proprietà e le relative relazioni.

È possibile configurare più gerarchie in un determinato ambiente di Time Series Insights. È possibile eseguire il mapping di un'istanza del modello Time Series a una singola gerarchia o a più gerarchie (relazione molti-a-molti).

L'interfaccia client [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) Visualizza un'istanza standard e una gerarchia dei tipi.

[![Esempio di gerarchia del modello Time Series](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definizione della gerarchia

Le gerarchie sono definite in base all' **ID**, al **nome**e all' **origine**della gerarchia.

| Proprietà | Descrizione |
| ---| ---|
| id | Identificatore univoco per la gerarchia, che viene utilizzato, ad esempio, quando si definisce un'istanza di. |
| name | Stringa utilizzata per fornire un nome per la gerarchia. |
| source | Specifica la gerarchia organizzativa o il percorso, ovvero un ordine padre-figlio dall'alto verso il basso della gerarchia che gli utenti desiderano creare. Le proprietà padre-figlio eseguono il mapping dei campi di istanza. |

Le gerarchie sono rappresentate in JSON come:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Nell'esempio JSON precedente:

* `Location`definisce una gerarchia con padre `states` e figlio `cities` . Ogni `location` può avere più `states` di un, che a sua volta può avere più `cities` .
* `ManufactureDate`definisce una gerarchia con padre `year` e figlio `month` . Ogni `ManufactureDate` può avere più `years` di un, che a sua volta può avere più `months` .

> [!TIP]
> Per Time Series Insights API dell'istanza e il supporto CRUD, vedere l'articolo relativo all' [esecuzione di query sui dati](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest dell'API Hierarchy](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Esempio di gerarchia

Si consideri un esempio in cui la gerarchia **H1** presenta `building` , `floor` e `room` come parte della definizione **instanceFieldNames** :

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Considerati i campi di istanza utilizzati nella definizione precedente e in diverse serie temporali, gli attributi e i valori della gerarchia vengono visualizzati come illustrato nella tabella seguente:

| ID serie temporale | Campi di istanza |
| --- | --- |
| ID1 | "Building" = "1000", "Floor" = "10", "Room" = "55"  |
| ID2 | "Building" = "1000", "Room" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "Building" = "1000", "Floor" = "10"  |
| ID5 | Non è impostato alcun valore di "Building", "Floor" o "Room". |

Le serie temporali **ID1** e **ID4** vengono visualizzate come parte della gerarchia **H1** in [Esplora Azure Time Series Insights](time-series-insights-update-explorer.md) perché dispongono di parametri di *compilazione*, *floor*e *room* completamente definiti e ordinati correttamente.

Le altre sono classificate in *istanze senza padre* perché non sono conformi alla gerarchia dei dati specificata.

## <a name="time-series-model-types"></a>Tipi di modelli serie temporale

I *tipi* di modello serie temporale consentono di definire variabili o formule per eseguire calcoli. I tipi sono associati a un'istanza di Time Series Insights specifica.

Un tipo può avere una o più variabili. Un'istanza del modello Time Series, ad esempio, può essere di tipo *sensore di temperatura*, costituito dalle variabili *Media temperature*, *min temperature*e *max temperature*.

La [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) Visualizza diversi tipi di modelli Time Series associati alle rispettive istanze.

[![Esempio di tipo di modello Time Series](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Per Time Series Insights API dell'istanza e il supporto CRUD, leggere l'articolo sull' [esecuzione di query sui dati](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest dell'API di tipo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Proprietà del tipo

I tipi di modello Time Series sono definiti in base all' **ID**, al **nome**, alla **Descrizione**e alle **variabili**.

| Proprietà | Descrizione |
| ---| ---|
| id | ID di stringa univoca con distinzione tra maiuscole e minuscole per il tipo. |
| name | Stringa utilizzata per fornire un nome per il tipo. |
| description | Descrizione della stringa per il tipo. |
| variables | Specificare le variabili associate al tipo. |

I tipi sono conformi all'esempio JSON seguente:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>variables

I tipi di Time Series Insights potrebbero avere molte variabili che specificano le regole relative a formule e calcolo per gli eventi.

Ogni variabile può essere di uno dei tre *tipi*seguenti: *numeric*, *CATEGORICAL*e *Aggregate*.

* I tipi **numerici** funzionano con valori continui.
* I tipi **categorici** funzionano con un set definito di valori discreti.
* I valori di **aggregazione** combinano più variabili di un singolo tipo (tutti numerici o categorici).

Nella tabella seguente vengono illustrate le proprietà rilevanti per ogni tipo di variabile.

[![Tabella delle variabili del modello Time Series](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variabili numeriche

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo proveniente dal dispositivo o dai sensori oppure trasformati usando le espressioni della serie temporale. Le variabili di tipo numerico devono essere di tipo *Double*.|
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. Per le variabili numeriche sono disponibili opzioni di interpolazione *lineare* e *passaggio* . |
| Aggregazione di variabile | Supporta il calcolo tramite gli operatori *AVG*, *min*, *Max*, *Sum*, *count*, *First*, *Last* e Time-weighted (*AVG*, *min*, *Max*, *Sum*, *Left*). |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variabili categoriche

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo proveniente dal dispositivo o dai sensori. Le variabili di tipo categorico devono essere *Long* o *String*. |
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. L'opzione di interpolazione dei *passaggi* è disponibile per le variabili categoriche. |
| Categorie di variabili | Le categorie creano un mapping tra i valori provenienti dal dispositivo o dai sensori a un'etichetta. |
| Categoria predefinita variabile | La categoria predefinita è per tutti i valori di cui non è stato eseguito il mapping nella proprietà "categorie". |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variabili di aggregazione

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Aggregazione di variabile | Supporto del calcolo tramite *AVG*, *min*, *Max*, *Sum*, *count*, *First*, *Last*. |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Le variabili vengono archiviate nella definizione del tipo di un modello Time Series e possono essere fornite inline tramite le [API di query](concepts-query-overview.md) per eseguire l'override della definizione archiviata.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [esecuzione di query sui dati](concepts-query-overview.md)

* Leggere la documentazione di riferimento del [modello Time Series](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
