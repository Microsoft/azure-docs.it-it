---
title: Diagnosticare, risolvere i problemi e risolvere i problemi - Azure Time Series Insights Documenti Microsoft
description: Questo articolo descrive come diagnosticare, risolvere i problemi e risolvere problemi comuni nell'ambiente Azure Time Series Insights.This article describes how to diagnose, troubleshoot, and solve common issues in your Azure Time Series Insights environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152587"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights

Questo articolo descrive alcuni problemi che si possono riscontrare nell'ambiente Azure Time Series Insights. L'articolo descrive le possibili cause e le relative soluzioni.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Scopri le sfide e le attenuazioni comuni dei clienti di Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: non vengono visualizzati dati

Ecco alcuni motivi per cui i dati potrebbero non essere visualizzati nello [strumento di esplorazione di Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: i dati dell'origine evento non sono in formato JSONCause A: event source data isn't in JSON format

Azure Time Series Insights supporta solo dati in formato JSON. Per esempi JSON, leggere [Forme JSON supportate](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: nella chiave dell'origine evento manca un'autorizzazione necessaria

* Per un hub IoT in Hub IoT di Azure è necessario indicare la chiave con le autorizzazioni di **connessione al servizio**. Selezionare i criteri **iothubowner** o **del servizio** poiché entrambi dispongono delle autorizzazioni di connessione al **servizio.**

   [![Autorizzazioni di connessione al servizio Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Per un hub eventi in Hub eventi di Azure è necessario indicare la chiave che dispone delle autorizzazioni di **ascolto**. Entrambi i criteri **read** o **manage** funzioneranno in quanto entrambi dispongono delle autorizzazioni di **ascolto**.

   [![Autorizzazioni di ascolto dell'hub eventi](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Causa C: il gruppo di consumer fornito non è esclusivo di Time Series Insights

Quando si registra un hub IoT o un hub eventi, è importante impostare il gruppo di consumer che si desidera usare per leggere i dati. Questo gruppo *non può essere condiviso*. Se viene condiviso, l'hub IoT o l'hub eventi sottostante disconnette automaticamente in modo casuale uno dei lettori. Specificare un gruppo di consumer univoco per la lettura dei dati da parte di Time Series Insights.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: è stato appena eseguito il provisioning dell'ambiente

I dati verranno visualizzati nell'esploratore Time Series Insights entro pochi minuti dalla creazione dell'ambiente e dei relativi dati.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: alcuni dati vengono visualizzati, ma mancano i dati

Quando i dati vengono visualizzati solo parzialmente e sembrano essere in ritardo, è necessario considerare diverse possibilità.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa A: l'ambiente viene limitato

[La limitazione è](time-series-insights-environment-mitigate-latency.md) un problema comune quando viene eseguito il provisioning degli ambienti dopo la creazione di un'origine eventi con dati. Hub IoT di Azure e Hub eventi di Azure archiviano i dati per un massimo di sette giorni. Time Series Insights inizia sempre con l'evento meno recente nell'origine evento (First-In, First-Out o *FIFO*).

Se ad esempio nell'origine evento sono presenti 5 milioni di eventi quando ci si connette a un S1, un ambiente Time Series Insights a unità singola, Time Series Insights legge circa 1 milione di eventi al giorno. Potrebbe sembrare che Time Series Insights riscontri cinque giorni di latenza. Quello che accade in effetti è che l'ambiente viene limitato nelle richieste.

In presenza di eventi meno recenti nell'origine eventi, è possibile scegliere tra due approcci:

- Modificare i limiti di conservazione dell'origine eventi per eliminare gli eventi meno recenti che non si vuole visualizzare in Time Series Insights.
- Eseguire il provisioning di un ambiente di dimensioni maggiori, in termini di numero di unità, per aumentare la velocità effettiva per gli eventi meno recenti. Ritornando all'esempio precedente, se lo stesso ambiente S1 venisse esteso a cinque unità per un giorno, sarebbe possibile recuperare entro tale giorno. Se la produzione di eventi di stato costante è di circa 1 milione o meno di eventi al giorno, è possibile ridurre la capacità dell'evento tornando a una unità dopo essere tornati in pari.

La limitazione viene applicata in base alla capacità e al tipo di SKU dell'ambiente. Tutte le origini evento all'interno dell'ambiente condividono questa capacità. Se l'origine eventi per l'hub IoT o l'hub eventi esegue il push dei dati oltre i limiti applicati, si verificheranno limitazioni e un ritardo.

Il diagramma seguente mostra un ambiente Time Series Insights con uno SKU S1 e una capacità pari a 3. È consentito l'ingresso di 3 milioni di eventi al giorno.

[![Capacità corrente dello SKU dell'ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Ad esempio, si supponga che un ambiente invii i messaggi da un hub eventi. La velocità di ingresso giornaliera è pari a circa 67.000 messaggi. Questa velocità si traduce approssimativamente in 46 messaggi al minuto. 

* Se ogni messaggio dell'hub eventi viene appiattito a un singolo evento Time Series Insights, non si verifica alcuna limitazione. 
* Se ogni messaggio dell'hub eventi viene appiattito a 100 eventi Time Series Insights , vengono inseriti 4.600 eventi ogni minuto. 

Un ambiente SKU S1 con una capacità pari a 3 consente l'ingresso di soli 2.100 eventi al minuto (1 milione di eventi al giorno = 700 eventi al minuto in tre unità = 2.100 eventi al minuto). 

Per informazioni generali sul funzionamento della logica di appiattimento, vedere [Forme JSON supportate](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Risoluzioni consigliate per la limitazione eccessiva

Per correggere il ritardo, aumentare la capacità SKU dell'ambiente. Per altre informazioni, vedere [Scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: l'inserimento iniziale dei dati cronologici rallenta l'ingresso

Se ci si connette a un'origine evento esistente, è probabile che l'hub IoT o l'hub eventi contenga già dati. L'ambiente inizia a eseguire il pull dei dati dall'inizio del periodo di conservazione dei messaggi dell'origine evento. Questa elaborazione predefinita non può essere sostituita. È possibile coinvolgere la limitazione delle richieste. La limitazione delle richieste può richiedere tempo per recuperare mentre si inseriscono i dati cronologici.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Risoluzioni consigliate per un inserimento iniziale di grandi quantità di dati

Per correggere il ritardo:

1. Aumentare la capacità SKU fino al valore massimo consentito (10 in questo caso). Dopo aver aumentato la capacità, il processo di inserimento inizia a velocizzarsi. La maggiore capacità sarà addebitata. Per vedere tale rapidità, è possibile visualizzare il grafico della disponibilità nello [strumento di esplorazione di Time Series Insights](https://insights.timeseries.azure.com).

2. Dopo avere recuperato il ritardo, diminuire nuovamente la capacità SKU alla velocità di ingresso normale.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: i dati venivano visualizzati in precedenza ma non più visualizzati

TSI non inserisce più i dati, ma gli eventi sono ancora in streaming nell'hub Iot o nell'hub eventi

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Causa A: la chiave di accesso dell'hub è stata rigenerata e l'ambiente deve essere aggiornato

Questo problema si verifica quando la chiave fornita durante la creazione dell'origine eventi non è più valida. I dati di telemetria vengono visualizzati nell'hub, ma non i messaggi ricevuti in ingresso in Time Series Insights.You would see telemetry in your hub but no Ingress Received Messages in Time Series Insights. Se non si è certi che la chiave sia stata rigenerata, è possibile cercare "Crea o aggiorna regole di autorizzazione dello spazio dei nomi" negli hub eventi o "Creare o aggiornare la risorsa IotHub" per l'hub IoT.

Per aggiornare l'ambiente Time Series Insights con la nuova chiave, aprire la risorsa hub nel portale di Azure e copiare la nuova chiave. Passare alla risorsa TSI e fare clic su Origini evento. 

   [![Chiave di aggiornamento.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selezionare le origini eventi da cui si è interrotta l'inserimento, incollare la nuova chiave e fare clic su Salva.

   [![Chiave di aggiornamento.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: l'impostazione del nome della proprietà timestamp dell'origine evento non funziona

Verificare che il nome e il valore della proprietà timestamp siano conformi alle regole seguenti:

* Il nome della proprietà timestamp fa distinzione fra maiuscole e minuscole.
* Il valore della proprietà timestamp, che proveniente dall'origine evento come stringa JSON, deve avere il formato _aaaa-MM-ggTHH:mm:ss.FFFFFFFK_. Un esempio è **2008-04-12T12:53Z**.

Il modo più semplice per garantire l'acquisizione e il corretto funzionamento del nome della proprietà timestamp consiste nell'usare lo strumento di esplorazione di Azure Time Series Insights. All'interno dello strumento di esplorazione di Azure Time Series Insights, usando il grafico, selezionare un periodo di tempo dopo aver specificato il nome della proprietà timestamp. Fare clic con il pulsante destro del mouse sulla selezione e selezionare l'opzione **Esplora eventi**.

L'intestazione della prima colonna deve essere il nome della proprietà timestamp. Accanto alla parola Timestamp ($ts) verrà visualizzata la parola **Timestamp** **($ts).**

I seguenti valori non verranno visualizzati:

- *(abc):* indica che Time Series Insights sta leggendo i valori dei dati come stringhe.
- *Icona Calendario*: Indica che Time Series Insights sta leggendo il valore dei dati come *datetime*.
- *#*: indica che Time Series Insights sta leggendo i valori dei dati come numero intero.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come ridurre la [latenza, vedere Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Informazioni su [come ridimensionare l'ambiente Time Series Insights.](time-series-insights-how-to-scale-your-environment.md)