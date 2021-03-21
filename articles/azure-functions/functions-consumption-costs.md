---
title: Stima dei costi del piano di consumo in funzioni di Azure
description: Informazioni su come stimare meglio i costi che possono verificarsi quando si esegue l'app per le funzioni in un piano a consumo in Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 4967e0ff79a638891da4f784cf2f5f1ca4ddfe51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578554"
---
# <a name="estimating-consumption-plan-costs"></a>Stima dei costi del piano a consumo

Esistono attualmente tre tipi di piani di hosting per un'app in esecuzione in funzioni di Azure, ognuno dei quali ha un proprio modello di determinazione dei prezzi: 

| Pianificare | Description |
| ---- | ----------- |
| [**Consumo**](consumption-plan.md) | Viene addebitato solo il tempo di esecuzione dell'app per le funzioni. Questo piano include una[pagina dei prezzi] di [concessione gratuita]per ogni sottoscrizione.|
| [**Premium**](functions-premium-plan.md) | Fornisce le stesse funzionalità e il meccanismo di scalabilità del piano a consumo, ma con prestazioni migliorate e accesso VNET. Il costo si basa sul piano tariffario scelto. Per altre informazioni, vedere [piano Premium di funzioni di Azure](functions-premium-plan.md). |
| [**Dedicato (servizio app)**](dedicated-plan.md) <br/>(livello Basic o superiore) | Quando è necessario eseguire in macchine virtuali dedicate o in isolamento, usare immagini personalizzate oppure usare la capacità del piano di servizio app in eccesso. Usa la [fatturazione normale del piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/). Il costo si basa sul piano tariffario scelto.|

Si è scelto il piano che meglio supporta le prestazioni della funzione e i requisiti di costo. Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Questo articolo riguarda solo il piano a consumo, perché questo piano comporta costi variabili. Questo articolo sostituisce l'articolo relativo alle [domande frequenti sulla fatturazione dei costi del piano a consumo](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions possono anche essere eseguiti in un piano a consumo. Per ulteriori informazioni sulle considerazioni sui costi quando si utilizza Durable Functions, vedere [Durable Functions fatturazione](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Costi del piano a consumo

Il *costo* di esecuzione di una singola esecuzione di funzione viene misurato in *GB-secondi*. Il costo di esecuzione viene calcolato combinando l'utilizzo della memoria con il relativo tempo di esecuzione. Funzione che viene eseguita per più costi più lunghi, così come una funzione che utilizza più memoria. 

Si consideri un caso in cui la quantità di memoria utilizzata dalla funzione rimane costante. In questo caso, il calcolo del costo è una semplice moltiplicazione. Ad esempio, supponiamo che la funzione abbia utilizzato 0,5 GB per 3 secondi. Il costo di esecuzione è quindi `0.5GB * 3s = 1.5 GB-seconds` . 

Poiché le modifiche all'utilizzo della memoria nel tempo, il calcolo è essenzialmente l'integrale dell'utilizzo della memoria nel tempo.  Il sistema esegue questo calcolo campionando l'utilizzo della memoria del processo (insieme ai processi figlio) a intervalli regolari. Come indicato nella [pagina dei prezzi], l'utilizzo della memoria viene arrotondato per eccesso al bucket 128 MB più vicino. Quando il processo utilizza 160 MB, viene addebitato il costo di 256 MB. Il calcolo prende in considerazione la concorrenza, ovvero più esecuzioni di funzioni simultanee nello stesso processo.

> [!NOTE]
> Anche se l'utilizzo della CPU non è direttamente considerato nei costi di esecuzione, può influire sul costo quando influisce sul tempo di esecuzione della funzione.

Per una funzione attivata tramite HTTP, quando si verifica un errore prima che venga avviata l'esecuzione del codice della funzione non viene addebitato alcun costo per l'esecuzione. Questo significa che 401 risposte dalla piattaforma a causa della convalida della chiave API o della funzionalità di autenticazione/autorizzazione del servizio app non vengono conteggiate rispetto al costo di esecuzione. Analogamente, le risposte del codice di stato 5xx non vengono conteggiate quando si verificano nella piattaforma prima di una funzione che elabora la richiesta. Una risposta 5xx generata dalla piattaforma dopo l'avvio dell'esecuzione del codice della funzione viene comunque conteggiata come esecuzione, anche se l'errore non viene generato dal codice della funzione.

## <a name="other-related-costs"></a>Altri costi correlati

Quando si stima il costo complessivo per l'esecuzione delle funzioni in qualsiasi piano, tenere presente che il runtime di funzioni usa diversi altri servizi di Azure, ognuno dei quali viene fatturato separatamente. Quando si calcolano i prezzi per le app per le funzioni, tutti i trigger e le associazioni che si integrano con altri servizi di Azure richiedono la creazione e il pagamento di tali servizi aggiuntivi. 

Per le funzioni in esecuzione in un piano a consumo, il costo totale è il costo di esecuzione delle funzioni, oltre al costo della larghezza di banda e dei servizi aggiuntivi. 

Quando si stimano i costi complessivi dell'app per le funzioni e dei servizi correlati, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Costo correlato | Descrizione |
| ------------ | ----------- |
| **Account di archiviazione** | Per ogni app per le funzioni è necessario avere un [account di archiviazione di Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)associato per utilizzo generico, [fatturato separatamente](https://azure.microsoft.com/pricing/details/storage/). Questo account viene utilizzato internamente dal runtime di funzioni, ma è possibile utilizzarlo anche per i trigger e le associazioni di archiviazione. Se non si ha un account di archiviazione, ne viene creato uno automaticamente quando viene creata l'app per le funzioni. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Si basa su [Application Insights](../azure-monitor/app/app-insights-overview.md) per offrire un'esperienza di monitoraggio ad alte prestazioni per le app per le funzioni. Sebbene non sia necessario, è necessario [abilitare l'integrazione Application Insights](configure-monitoring.md#enable-application-insights-integration). Ogni mese viene inclusa una concessione gratuita dei dati di telemetria. Per altre informazioni, vedere [la pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). |
| **Larghezza di banda di rete** | Non si paga per il trasferimento dei dati tra i servizi di Azure nella stessa area. Tuttavia, è possibile sostenere i costi per i trasferimenti di dati in uscita in un'altra area o all'esterno di Azure. Per altre informazioni, vedere [Dettagli prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamenti che interessano il tempo di esecuzione

I comportamenti seguenti delle funzioni possono avere un effetto sul tempo di esecuzione:

+ **Trigger e associazioni**: il tempo impiegato per leggere l'input e scrivere l'output nelle [associazioni di funzioni](functions-triggers-bindings.md) viene conteggiato come tempo di esecuzione. Ad esempio, quando la funzione usa un'associazione di output per scrivere un messaggio in una coda di archiviazione di Azure, il tempo di esecuzione include il tempo impiegato per scrivere il messaggio nella coda, incluso nel calcolo del costo della funzione. 

+ **Esecuzione asincrona**: il tempo di attesa della funzione per i risultati di una richiesta asincrona ( `await` in C#) viene conteggiato come tempo di esecuzione. Il calcolo di GB-secondo è basato sull'ora di inizio e di fine della funzione e sull'utilizzo della memoria per tale periodo. Ciò che accade nel tempo in termini di attività della CPU non è incluso nel calcolo. Potrebbe essere possibile ridurre i costi durante le operazioni asincrone utilizzando [Durable Functions](durable/durable-functions-overview.md). Non viene addebitato il tempo dedicato alle attese nelle funzioni dell'agente di orchestrazione.

## <a name="viewing-cost-related-data"></a>Visualizzazione dei dati relativi ai costi

Nella [fattura](../cost-management-billing/understand/download-azure-invoice.md)è possibile visualizzare i dati relativi ai costi delle **esecuzioni totali, funzioni** e tempi di **esecuzione**, oltre ai costi effettivi fatturati. Tuttavia, i dati della fattura sono aggregati mensili per un periodo di fatturazione precedente. 

### <a name="function-app-level-metrics"></a>Metriche a livello di app per le funzioni

Per comprendere meglio l'impatto sui costi delle funzioni, è possibile usare monitoraggio di Azure per visualizzare le metriche relative ai costi attualmente generate dalle app per le funzioni. Per ottenere questi dati, è possibile usare [Esplora metriche di monitoraggio di Azure](../azure-monitor/essentials/metrics-getting-started.md) nella [portale di Azure] o nelle API REST.

#### <a name="monitor-metrics-explorer"></a>Esplora metriche di monitoraggio

Usare [Esplora metriche di monitoraggio di Azure](../azure-monitor/essentials/metrics-getting-started.md) per visualizzare i dati relativi ai costi per le app per le funzioni del piano a consumo in formato grafico. 

1. Nella parte superiore del [portale di Azure] in **Cerca servizi, risorse e documentazione**  Cerca `monitor` e seleziona **monitoraggio** in **Servizi**.

1. A sinistra selezionare **metrica**  >  **selezionare una risorsa** e quindi usare le impostazioni sotto l'immagine per scegliere l'app per le funzioni.

    ![Selezionare la risorsa dell'app per le funzioni](media/functions-consumption-costing/select-a-resource.png)

      
    |Impostazione  |Valore consigliato  |Descrizione  |
    |---------|---------|---------|
    | Subscription    |  Sottoscrizione in uso  | Sottoscrizione con l'app per le funzioni.  |
    | Resource group     | Gruppo di risorse in uso  | Il gruppo di risorse che contiene l'app per le funzioni.   |
    | Tipo di risorsa     |  Servizi app | Le app per le funzioni vengono visualizzate come istanze di servizi app in monitoraggio. |
    | Risorsa     |  App per le funzioni  | App per le funzioni da monitorare.        |

1. Selezionare **applica** per scegliere l'app per le funzioni come risorsa da monitorare.

1. Da **metrica** scegliere **Numero esecuzioni di funzioni** e **Sum** per **aggregazione**. In questo modo viene aggiunta la somma dei conteggi di esecuzione durante il periodo scelto al grafico.

    ![Definire una metrica dell'app per le funzioni da aggiungere al grafico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selezionare **Aggiungi metrica** e ripetere i passaggi 2-4 per aggiungere **unità di esecuzione della funzione** al grafico. 

Il grafico risultante contiene i totali per entrambe le metriche di esecuzione nell'intervallo di tempo scelto, che in questo caso è due ore.

![Grafico dei conteggi delle esecuzioni delle funzioni e delle unità di esecuzione](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Poiché il numero di unità di esecuzione è molto superiore al numero di esecuzioni, il grafico mostra solo le unità di esecuzione.

Questo grafico mostra un totale di 1,11 miliardi `Function Execution Units` utilizzati in un periodo di due ore, misurato in MB-millisecondi. Per eseguire la conversione in GB-secondi, dividere per 1024000. In questo esempio l'app per le funzioni ha utilizzato `1110000000 / 1024000 = 1083.98` GB-secondi. È possibile utilizzare questo valore e moltiplicare per il prezzo corrente del tempo di esecuzione nella[pagina]dei prezzi relativa alle [funzioni], che offre il costo di queste due ore, presupponendo che siano già state utilizzate concessioni gratuite del tempo di esecuzione. 

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L' [interfaccia](/cli/azure/) della riga di comando di Azure include comandi per recuperare le metriche. È possibile usare l'interfaccia della riga di comando da un ambiente di comando locale o direttamente dal portale usando [Azure cloud Shell](../cloud-shell/overview.md). Ad esempio, il comando [AZ monitor Metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) seguente restituisce i dati orari nello stesso periodo di tempo usato in precedenza.

Assicurarsi di sostituire `<AZURE_SUBSCRIPTON_ID>` con l'ID sottoscrizione di Azure che esegue il comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Questo comando restituisce un payload JSON simile all'esempio seguente:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Questa particolare risposta indica che da `2019-09-11T21:46` a `2019-09-11T23:18` , l'app ha utilizzato 1,11 miliardi MB-millisecondi (1083,98 GB-secondi).

### <a name="function-level-metrics"></a>Metriche a livello di funzione

Le unità di esecuzione della funzione sono una combinazione di tempo di esecuzione e di utilizzo della memoria, che lo rende una metrica complessa per comprendere l'utilizzo della memoria. I dati di memoria non sono una metrica attualmente disponibile tramite monitoraggio di Azure. Tuttavia, se si vuole ottimizzare l'utilizzo della memoria dell'app, può usare i dati dei contatori delle prestazioni raccolti da Application Insights.  

Se non è già stato fatto, [abilitare Application Insights nell'app per le funzioni](configure-monitoring.md#enable-application-insights-integration). Con questa integrazione abilitata, è possibile [eseguire query sui dati di telemetria nel portale](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sul monitoraggio delle app per le funzioni](functions-monitoring.md)

[pagina dei prezzi]:https://azure.microsoft.com/pricing/details/functions/
[Azure portal]: https://portal.azure.com
