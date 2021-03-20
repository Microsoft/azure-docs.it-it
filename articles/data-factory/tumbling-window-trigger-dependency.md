---
title: Crea dipendenze trigger finestra a cascata
description: Informazioni su come creare dipendenze del trigger di finestra a cascata in Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361469"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Creare una dipendenza del trigger di finestra a cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive la procedura per creare una dipendenza del trigger di finestra a cascata. Per informazioni più specifiche ed esempi sui trigger di finestra a cascata, vedere [Procedura per creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).

Per creare una catena di dipendenze e accertarsi che un trigger venga eseguito solo dopo la corretta esecuzione di un altro trigger nella data factory, usare questa funzionalità avanzata per creare una dipendenza in una finestra a cascata.

Per una dimostrazione su come creare pipeline dipendenti nel Azure Data Factory usando il trigger di finestra a cascata, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Creare una dipendenza nell'interfaccia utente di Data Factory

Per creare dipendenze di un trigger, selezionare **Trigger > Avanzate > Nuovo**, quindi scegliere il trigger da cui dipendere con le dimensioni e gli scostamenti appropriati. Selezionare **Fine** e pubblicare le modifiche alla data factory per attivare le dipendenze.

![Creazione di dipendenze](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Creazione di dipendenze")

## <a name="tumbling-window-dependency-properties"></a>Proprietà di una dipendenza in una finestra a cascata

Un trigger di finestra a cascata con una dipendenza presenta le proprietà seguenti:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

La tabella seguente fornisce l'elenco degli attributi necessari per definire una dipendenza in una finestra a cascata.

| **Nome proprietà** | **Descrizione**  | **Tipo** | **Obbligatorio** |
|---|---|---|---|
| type  | Tutti i trigger presenti in una finestra a cascata vengono visualizzati nell'elenco a tendina. Scegliere il trigger da cui dipendere.  | TumblingWindowTriggerDependencyReference o SelfDependencyTumblingWindowTriggerReference | Sì |
| offset | Scostamento del trigger delle dipendenze. Fornire un valore nel formato di intervallo di tempo ed entrambi gli offset negativi e positivi sono consentiti. Questa proprietà è obbligatoria se il trigger dipende da se stesso e in tutti gli altri casi è facoltativo. L'auto-dipendenza deve avere sempre uno scostamento negativo. Se non viene specificato alcun valore, la finestra è uguale a quella del trigger. | TimeSpan<br/>(hh:mm:ss) | Dipendenza autonoma: Sì<br/>Altro: No |
| size | Dimensione della finestra a cascata di dipendenze. Fornire un valore TimeSpan positivo. Questa proprietà è facoltativa. | TimeSpan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> Un trigger di finestra a cascata può dipendere da un massimo di cinque altri trigger.

## <a name="tumbling-window-self-dependency-properties"></a>Proprietà di auto-dipendenza in una finestra a cascata

Negli scenari in cui il trigger non deve passare alla finestra successiva finché la finestra precedente non viene completata correttamente, compilare una dipendenza autonoma. Un trigger di dipendenza autonoma che dipende dal successo delle esecuzioni precedenti di se stesso entro l'ora precedente avrà le proprietà indicate nel codice seguente.

> [!NOTE]
> Se la pipeline attivata si basa sull'output delle pipeline nelle finestre attivate in precedenza, è consigliabile usare solo la dipendenza del trigger di finestra a cascata. Per limitare le esecuzioni di trigger paralleli, impostare la concorrenza del trigger massimo.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Scenari ed esempi di utilizzo

Di seguito sono illustrate le illustrazioni degli scenari e l'utilizzo delle proprietà di dipendenza della finestra a cascata.

### <a name="dependency-offset"></a>Scostamento della dipendenza

![Esempio di offset](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Esempio di offset")

### <a name="dependency-size"></a>Dimensione della dipendenza

![Esempio di dimensioni](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Esempio di dimensioni")

### <a name="self-dependency"></a>Auto-dipendenza

![Dipendenza autonoma](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Auto-dipendenza")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dipendenza di un altro trigger in una finestra a cascata

Un processo di elaborazione dei dati di telemetria giornaliero a seconda di un altro processo giornaliero che esegue l'aggregazione degli ultimi sette giorni e genera flussi di finestra in sequenza per sette giorni:

![Esempio di dipendenza](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Esempio di dipendenza")

### <a name="dependency-on-itself"></a>Dipendenza su se stesso

Un processo giornaliero senza interruzioni nei flussi di output del processo:

![Esempio di dipendenza autonoma](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Esempio di dipendenza autonoma")

## <a name="monitor-dependencies"></a>Monitorare le dipendenze

È possibile monitorare la catena delle dipendenze e le finestre corrispondenti dalla pagina di monitoraggio dell'esecuzione del trigger. Navigare alla voce **Monitoraggio > Esecuzioni di trigger**. Se un trigger di finestra a cascata presenta dipendenze, il nome del trigger conterrà un collegamento ipertestuale alla visualizzazione di monitoraggio delle dipendenze.  

![Monitorare le esecuzioni del trigger](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Monitorare le esecuzioni di trigger-intere alla visualizzazione delle dipendenze della finestra a cascata")

Fare clic sul nome del trigger per visualizzare le dipendenze del trigger. Il pannello a destra mostra informazioni dettagliate sull'esecuzione di trigger, ad esempio RunID, ora finestra, stato e così via.

![Visualizzazione elenco dipendenze monitoraggio](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Visualizzazione elenco dipendenze monitoraggio")

È possibile visualizzare lo stato delle dipendenze e Windows per ogni trigger dipendente. Se uno dei trigger di dipendenza ha esito negativo, è necessario eseguirlo di nuovo affinché il trigger dipendente venga eseguito.

Un trigger di finestra a cascata attenderà le dipendenze per _sette giorni_ prima del timeout. Dopo sette giorni, l'esecuzione del trigger avrà esito negativo.

Per un oggetto visivo più visualizzato per visualizzare la pianificazione delle dipendenze del trigger, selezionare la visualizzazione Gantt.

![Diagramma di Gantt del monitoraggio delle dipendenze](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Visualizzazione diagramma di Gantt dipendenze monitoraggio")

Le caselle trasparenti mostrano le finestre di dipendenza per ogni trigger Down dipendente dal flusso, mentre le caselle colorate a tinta unita sopra mostrano le singole esecuzioni di finestra. Di seguito sono riportati alcuni suggerimenti per l'interpretazione della vista del diagramma di Gantt:

* La casella trasparente esegue il rendering blu quando le finestre dipendenti sono in stato in sospeso o in esecuzione
* Una volta che tutte le finestre sono state riuscite per un trigger dipendente, la finestra trasparente diventa verde
* La casella trasparente esegue il rendering di rosso quando una finestra dipendente non riesce. Cercare una casella rossa a tinta unita per identificare l'esecuzione della finestra di errore

Per rieseguire una finestra nella visualizzazione diagramma di Gantt, selezionare la casella colore a tinta unita per la finestra. viene visualizzato un pannello azione con i dettagli e le opzioni Riesegui

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)
