---
title: Risoluzione dei problemi relativi agli avvisi delle metriche di Azure
description: Problemi comuni relativi agli avvisi delle metriche di monitoraggio di Azure e alle possibili soluzioni.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 03/15/2021
ms.openlocfilehash: f14142632f6ded9f598d6e94fd1e91ec17f6d0a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466498"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Risoluzione dei problemi negli avvisi relativi alle metriche di Monitoraggio di Azure 

Questo articolo illustra i problemi comuni negli [avvisi delle metriche](alerts-metric-overview.md) di monitoraggio di Azure e come risolverli.

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](./alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>L'avviso della metrica dovrebbe essere stato attivato ma non 

Se si ritiene che un avviso di metrica debba essere stato attivato ma non è stato attivato e non è stato trovato nella portale di Azure, provare a eseguire i passaggi seguenti:

1. **Configurazione** : esaminare la configurazione della regola di avviso della metrica per assicurarsi che sia configurata correttamente:
    - Verificare che il **tipo di aggregazione** e la **granularità di aggregazione (periodo)** siano configurati come previsto. Il **tipo di aggregazione** determina il modo in cui vengono aggregati i valori delle metriche [(altre informazioni](../essentials/metrics-aggregation-explained.md#aggregation-types)) e la **granularità di aggregazione (periodo)** controlla la distanza con cui la valutazione aggrega i valori delle metriche ogni volta che viene eseguita la regola di avviso.
    -  Verificare che il **valore di soglia** o la **sensibilità** siano configurati come previsto.
    - Per una regola di avviso che utilizza soglie dinamiche, controllare se sono configurate impostazioni avanzate, in quanto il **numero di violazioni** può filtrare gli avvisi e **ignorare i dati prima** che possa influisca sulla modalità di calcolo delle soglie.

       > [!NOTE] 
       > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

2. **Generato ma nessuna notifica** : esaminare l' [elenco degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) attivati per verificare se è possibile individuare l'avviso generato. Se è possibile visualizzare l'avviso nell'elenco, ma si verifica un problema con alcune delle azioni o notifiche, vedere altre informazioni [qui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Già attivo** : controllare se è già presente un avviso attivato per la serie temporale della metrica per cui si prevede di ricevere un avviso. Gli avvisi delle metriche sono avvisi con stato, di conseguenza, una volta generato un avviso in una serie temporale della metrica specifica, gli avvisi successivi in tale serie temporale non verranno attivati fino a quando il problema non viene più rilevato. Questa scelta di progettazione riduce il rumore. L'avviso viene risolto automaticamente quando la condizione di avviso non viene soddisfatta per tre valutazioni consecutive.

4. **Dimensioni usate** : se sono stati selezionati alcuni [valori di dimensione per una metrica](./alerts-metric-overview.md#using-dimensions), la regola di avviso monitora ogni singola serie temporale della metrica (definita dalla combinazione dei valori della dimensione) per una violazione di soglia. Per monitorare anche le serie temporali delle metriche aggregate (senza dimensioni selezionate), configurare un'altra regola di avviso sulla metrica senza selezionare le dimensioni.

5. **Granularità di aggregazione e ora** : se si visualizza la metrica usando i [grafici di metrica](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verificare che:
    * L' **aggregazione** selezionata nel grafico delle metriche è uguale a quella del **tipo di aggregazione** nella regola di avviso
    * La **granularità temporale** selezionata corrisponde alla **granularità di aggregazione (periodo)** nella regola di avviso (e non è impostata su' Automatic ')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Avviso di metrica generato quando non dovrebbe avere

Se si ritiene che l'avviso della metrica non debba essere stato attivato ma è stato fatto, i passaggi seguenti potrebbero aiutare a risolvere il problema.

1. Esaminare l' [elenco degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) attivati per individuare l'avviso attivato e fare clic per visualizzarne i dettagli. Esaminare le informazioni fornite in **perché questo avviso** è stato attivato? per visualizzare il grafico delle metriche, il **valore della metrica** e il **valore soglia** nel momento in cui è stato attivato l'avviso.

    > [!NOTE] 
    > Se si usa un tipo di condizione per le soglie dinamiche e si ritiene che le soglie usate non siano corrette, fornire commenti e suggerimenti tramite l'icona imbronciata. Questo feedback avrà un effetto sulla ricerca algoritmica di machine learning e contribuirà a migliorare i rilevamenti futuri.

2. Se sono stati selezionati più valori di dimensione per una metrica, l'avviso viene attivato quando **una** serie temporale metrica, definita dalla combinazione di valori di dimensione, viola la soglia. Per altre informazioni sull'uso delle dimensioni negli avvisi delle metriche, vedere [qui](./alerts-metric-overview.md#using-dimensions).

3. Esaminare la configurazione della regola di avviso per assicurarsi che sia configurata correttamente:
    - Verificare che il **tipo di aggregazione**, la **granularità dell'aggregazione (periodo)** e il **valore di soglia** o la **sensibilità** siano configurati come previsto.
    - Per una regola di avviso che utilizza soglie dinamiche, controllare se sono configurate impostazioni avanzate, in quanto il **numero di violazioni** può filtrare gli avvisi e **ignorare i dati prima** che possa influisca sulla modalità di calcolo delle soglie

   > [!NOTE]
   > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

4. Se si visualizza la metrica usando un [grafico delle metriche](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verificare che:
    - L' **aggregazione** selezionata nel grafico delle metriche è uguale a quella del **tipo di aggregazione** nella regola di avviso
    - La **granularità temporale** selezionata corrisponde alla **granularità di aggregazione (periodo)** nella regola di avviso (e non è impostata su' Automatic ')

5. Se l'avviso è stato generato quando sono già stati generati avvisi che monitorano gli stessi criteri (che non sono stati risolti), controllare se la regola di avviso è stata configurata con la proprietà *Automitigate* impostata su **false** (questa proprietà può essere configurata solo tramite REST/PowerShell/CLI, quindi controllare lo script usato per distribuire la regola di avviso). In tal caso, la regola di avviso non risolve automaticamente gli avvisi attivati e non richiede la risoluzione di un avviso attivato prima dell'attivazione.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Non è possibile trovare la metrica per l'avviso sulle metriche Guest delle macchine virtuali

Per inviare un avviso sulle metriche del sistema operativo guest delle macchine virtuali (ad esempio: memoria, spazio su disco), assicurarsi di avere installato l'agente richiesto per raccogliere i dati nelle metriche di monitoraggio di Azure:
- [Per macchine virtuali Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Per macchine virtuali Linux](../essentials/collect-custom-metrics-linux-telegraf.md)

Per ulteriori informazioni sulla raccolta di dati dal sistema operativo guest di una macchina virtuale, vedere [qui](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Se sono state configurate le metriche Guest da inviare a un'area di lavoro Log Analytics, le metriche vengono visualizzate sotto la risorsa Log Analytics area di lavoro e inizieranno a visualizzare i dati **solo** dopo la creazione di una regola di avviso che li monitora. A tal proposito, seguire la procedura per [configurare un avviso delle metriche per i log](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Il monitoraggio di una metrica Guest per più macchine virtuali con una singola regola di avviso non è attualmente supportato dagli avvisi delle metriche. È possibile ottenere questo risultato con una [regola di avviso del log](./alerts-unified-log.md). A tale scopo, verificare che le metriche Guest vengano raccolte in un'area di lavoro Log Analytics e creare una regola di avviso del log nell'area di lavoro.

## <a name="cant-find-the-metric-to-alert-on"></a>Impossibile trovare la metrica per l'avviso

Se si sta cercando di generare un avviso per una metrica specifica ma non è possibile visualizzarla durante la creazione di una regola di avviso, verificare quanto segue:
- Se non è possibile vedere le metriche per il tipo di risorsa, [verificare se tale tipo di risorsa è supportato per gli avvisi delle metriche](./alerts-metric-near-real-time.md).
- Se è possibile visualizzare alcune metriche per la risorsa, ma non si riesce a trovare una metrica specifica, [verificare se tale metrica è disponibile](../essentials/metrics-supported.md) e, in tal caso, vedere la descrizione della metrica per verificare se è disponibile solo in versioni o edizioni specifiche della risorsa.
- Se la metrica non è disponibile per la risorsa, potrebbe essere disponibile nei log della risorsa e potrebbe essere monitorata usando gli avvisi del log. Vedere qui per altre informazioni su come [raccogliere e analizzare i log delle risorse da una risorsa di Azure](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Impossibile trovare la dimensione metrica su cui inviare l'avviso

Se si sta cercando di avvisare su [valori di dimensione specifici di una metrica](./alerts-metric-overview.md#using-dimensions), ma questi valori non sono stati trovati, tenere presente quanto segue:

1. La visualizzazione dei valori delle dimensioni nell'elenco **Valori delle dimensioni** potrebbe richiedere alcuni minuti
1. I valori delle dimensioni visualizzati si basano sui dati delle metriche raccolti l'ultimo giorno
1. Se il valore della dimensione non è ancora stato emesso o non è visualizzato, è possibile usare l'opzione "Aggiungi valore personalizzato" per aggiungere un valore di dimensione personalizzato
1. Se si vuole avvisare tutti i valori possibili di una dimensione (compresi i valori futuri), scegliere l'opzione ' Seleziona tutti i valori correnti e futuri '

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Regole di avviso delle metriche ancora definite in una risorsa eliminata 

Quando si elimina una risorsa di Azure, le regole di avviso delle metriche associate non vengono eliminate automaticamente. Per eliminare le regole di avviso associate a una risorsa che è stata eliminata:

1. Aprire il gruppo di risorse in cui è stata definita la risorsa eliminata
1. Nell'elenco che mostra le risorse selezionare la casella di controllo **Mostra tipi nascosti**
1. Filtrare l'elenco in base al tipo = = **microsoft.insights/metricalerts**
1. Selezionare le regole di avviso pertinenti e selezionare **Elimina** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Crea avvisi di metrica ogni volta che viene soddisfatta la condizione

Gli avvisi delle metriche sono con stato per impostazione predefinita e pertanto non vengono generati avvisi aggiuntivi se è già presente un avviso attivato in una determinata serie temporale. Se si vuole creare una regola di avviso metrica specifica senza stato e ricevere un avviso per ogni valutazione in cui viene soddisfatta la condizione di avviso, creare la regola di avviso a livello di codice (ad esempio, tramite [Gestione risorse](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [Rest](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert)) e impostare la proprietà *automitigate* su "false".

> [!NOTE] 
> La creazione di una regola di avviso della metrica senza stato impedisce la risoluzione degli avvisi attivati, quindi anche dopo che la condizione non è più soddisfatta, gli avvisi attivati rimarranno in uno stato attivato fino al periodo di conservazione di 30 giorni.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definire una regola di avviso per una metrica personalizzata che non è ancora stata emessa

Quando si crea una regola di avviso per la metrica, il nome della metrica viene convalidato in base all' [API delle definizioni delle metriche](/rest/api/monitor/metricdefinitions/list) per assicurarsi che esista. In alcuni casi, si vuole creare una regola di avviso su una metrica personalizzata anche prima che venga emessa. Ad esempio, quando si crea (usando un modello di Gestione risorse) una risorsa Application Insights che emette una metrica personalizzata, insieme a una regola di avviso che monitora tale metrica.

Per evitare che la distribuzione abbia esito negativo quando si tenta di convalidare le definizioni della metrica personalizzata, è possibile usare il parametro *skipMetricValidation* nella sezione criteri della regola di avviso, che causerà l'omissione della convalida della metrica. Vedere l'esempio seguente per informazioni su come usare questo parametro in un modello di Gestione risorse. Per ulteriori informazioni, vedere gli [esempi di modelli di gestione risorse completi per la creazione di regole di avviso per la metrica](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Esportare il modello di Azure Resource Manager di una regola di avviso per la metrica tramite il portale di Azure

L'esportazione del modello di Gestione risorse di una regola di avviso per le metriche consente di comprendere la sintassi e le proprietà JSON e può essere usata per automatizzare le distribuzioni future.
1. Nella portale di Azure aprire la regola di avviso per visualizzarne i dettagli.
2. Scegliere **Proprietà**.
3. In **automazione** selezionare **Esporta modello**.

## <a name="metric-alert-rules-quota-too-small"></a>Quota regole di avviso metrica troppo piccola

Il numero di regole di avviso per la metrica consentito per ogni sottoscrizione è soggetto ai [limiti della quota](../service-limits.md).

Se è stato raggiunto il limite di quota, i passaggi seguenti possono contribuire a risolvere il problema:
1. Provare a eliminare o disabilitare le regole di avviso delle metriche che non vengono più usate.

2. Passare a usare le regole di avviso della metrica che monitorano più risorse. Con questa funzionalità, una singola regola di avviso può monitorare più risorse usando solo una regola di avviso calcolata in base alla quota. Per altre informazioni su questa funzionalità e sui tipi di risorse supportati, vedere [qui](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Se è necessario aumentare il limite di quota, aprire una richiesta di supporto e fornire le informazioni seguenti:

    - ID sottoscrizione per i quali è necessario aumentare il limite di quota
    - Tipo di risorsa per l'aumento della quota: **avvisi metrica** o **avvisi metrica (versione classica)**
    - Limite di quota richiesto

## <a name="check-total-number-of-metric-alert-rules"></a>Controllare il numero totale di regole di avviso per la metrica

Per verificare l'utilizzo corrente delle regole di avviso delle metriche, attenersi alla procedura riportata di seguito.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Aprire la schermata **Avvisi** e fare clic su **Gestisci regole di avviso**
2. Filtrare per la sottoscrizione pertinente usando il controllo elenco a discesa **sottoscrizione**
3. Assicurarsi di non filtrare per un gruppo di risorse, un tipo di risorsa o una risorsa specifici
4. Nel controllo elenco a discesa **tipo di segnale** selezionare **metrica**
5. Verificare che il controllo elenco a discesa **stato** sia impostato su **abilitato**
6. Il numero totale di regole di avviso delle metriche viene visualizzato sopra l'elenco delle regole di avviso

### <a name="from-api"></a>Nell'API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [Elenco per sottoscrizione](/rest/api/monitor/metricalerts/listbysubscription)
- Interfaccia della riga di comando di Azure - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gestione delle regole di avviso tramite modelli di Gestione risorse, API REST, PowerShell o l'interfaccia della riga di comando di Azure

Se si verificano problemi durante la creazione, l'aggiornamento, il recupero o l'eliminazione degli avvisi delle metriche usando Gestione risorse modelli, l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, i passaggi seguenti possono contribuire a risolvere il problema.

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

- Rivedere l'elenco di [Errori di distribuzione di Azure comuni](../../azure-resource-manager/templates/common-deployment-errors.md) e provare a risolvere il problema
- Per verificare che tutti i parametri siano stati passati correttamente, vedere gli [avvisi sulle metriche Azure Resource Manager esempi di modelli](./alerts-metric-create-templates.md) .

### <a name="rest-api"></a>API REST

Esaminare la [Guida dell'API REST](/rest/api/monitor/metricalerts/) per verificare che tutti i parametri siano stati passati correttamente

### <a name="powershell"></a>PowerShell

Assicurarsi di usare i cmdlet di PowerShell corretti per gli avvisi delle metriche:

- I cmdlet di PowerShell per gli avvisi delle metriche sono disponibili nel [modulo Az.Monitor](/powershell/module/az.monitor/)
- Assicurarsi di usare i cmdlet che terminano con ' v2' per gli avvisi della metrica nuovi (non classici), ad esempio [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Assicurarsi di usare i comandi dell'interfaccia della riga di comando corretti per gli avvisi delle metriche:

- I comandi dell'interfaccia della riga di comando per gli avvisi delle metriche iniziano con `az monitor metrics alert`. Rivedere le [informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/monitor/metrics/alert) per apprenderne la sintassi.
- È possibile visualizzare un [esempio che illustra come usare l'interfaccia della riga di comando per gli avvisi delle metriche](./alerts-metric.md#with-azure-cli)
- Per generare un avviso su una metrica personalizzata, assicurarsi di anteporre al nome della metrica lo spazio dei nomi pertinente: NAMESPACE.METRIC

### <a name="general"></a>Generale

- Se si riceve un `Metric not found` errore:

   - Per una metrica della piattaforma: assicurarsi di usare il nome della **metrica** nella [pagina metrica supportata di monitoraggio di Azure](../essentials/metrics-supported.md)e non il **nome visualizzato della metrica** .

   - Per una metrica personalizzata: assicurarsi che la metrica sia già emessa (non è possibile creare una regola di avviso su una metrica personalizzata che non esiste ancora) e che si fornisca lo spazio dei nomi della metrica personalizzata (vedere un esempio di modello di Gestione risorse [qui](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Se si stanno creando [avvisi sulle metriche nei log](./alerts-metric-logs.md), verificare che siano incluse le dipendenze appropriate. Vedere il [modello di esempio](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se si sta creando una regola di avviso che contiene più criteri, tenere presente i vincoli seguenti:

   - È possibile selezionare un solo valore per dimensione all'interno di ogni criterio
   - Non è possibile usare "\*" come valore di dimensione
   - Quando le metriche configurate in criteri diversi supportano la stessa dimensione, un valore della dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte le metriche (vedere [un esempio di](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)modello Gestione risorse)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nessuna autorizzazione per la creazione di regole di avviso delle metriche

Per creare una regola di avviso per la metrica, è necessario disporre delle autorizzazioni seguenti:

- Autorizzazione Read per la risorsa di destinazione della regola di avviso
- Autorizzazione di scrittura per il gruppo di risorse in cui viene creata la regola di avviso (se si sta creando la regola di avviso dal portale di Azure, la regola di avviso viene creata per impostazione predefinita nello stesso gruppo di risorse in cui risiede la risorsa di destinazione)
- Autorizzazione Read per qualsiasi gruppo di azione associato alla regola di avviso (se applicabile)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Limitazioni di denominazione per le regole di avviso delle metriche

Prendere in considerazione le restrizioni seguenti per i nomi delle regole di avviso delle metriche:

- Non è possibile modificare i nomi delle regole di avviso delle metriche (rinominate) dopo la creazione
- I nomi delle regole di avviso delle metriche devono essere univoci in un gruppo di risorse
- I nomi delle regole di avviso per la metrica non possono contenere i caratteri seguenti: * # & +:  < > ? @ % { } \ / 
- I nomi delle regole di avviso della metrica non possono terminare con uno spazio o un punto

> [!NOTE] 
> Se il nome della regola di avviso contiene caratteri che non sono alfabetici o numerici, ad esempio spazi, segni di punteggiatura o simboli, questi caratteri possono essere codificati in URL quando vengono recuperati da determinati client.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrizioni relative all'uso di dimensioni in una regola di avviso metrica con più condizioni

Gli avvisi delle metriche supportano gli avvisi sulle metriche multidimensionali e supportano la definizione di più condizioni (fino a 5 condizioni per regola di avviso).

Quando si usano le dimensioni in una regola di avviso che contiene più condizioni, tenere presenti i vincoli seguenti:
- È possibile selezionare un solo valore per dimensione all'interno di ogni condizione.
- Non è possibile usare l'opzione per "selezionare tutti i valori correnti e futuri" (Select \* ).
- Quando le metriche configurate in condizioni diverse supportano la stessa dimensione, un valore della dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte le metriche (nelle condizioni pertinenti).
Ad esempio:
    - Si consideri una regola di avviso metrica definita in un account di archiviazione e che monitora due condizioni:
        * Totale **transazioni** > 5
        * Media **SuccessE2ELatency** > 250 ms
    - Desidero aggiornare la prima condizione e monitorare solo le transazioni in cui la dimensione **ApiName** è uguale a *"GetBlob"*
    - Poiché le metriche **Transactions** e **SuccessE2ELatency** supportano una dimensione **ApiName** , sarà necessario aggiornare entrambe le condizioni e fare in modo che entrambe specifichino la dimensione **ApiName** con un valore *"GetBlob"* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>Impostazione del periodo e della frequenza della regola di avviso

È consigliabile scegliere una *granularità di aggregazione (periodo)* maggiore della *frequenza di valutazione*, per ridurre la probabilità che manchi la prima valutazione della serie temporale aggiunta nei casi seguenti:
-   Regola di avviso metrica che monitora più dimensioni: quando viene aggiunta una nuova combinazione di valori di dimensione
-   Regola di avviso metrica che monitora più risorse: quando una nuova risorsa viene aggiunta all'ambito
-   Regola di avviso metrica che monitora una metrica che non viene emessa continuamente (metrica di tipo sparse): quando la metrica viene emessa dopo un periodo di tempo superiore a 24 ore in cui non è stata emessa

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>I bordi della soglia dinamica non sembrano adattarsi ai dati

Se il comportamento di una metrica è stato modificato di recente, le modifiche non verranno necessariamente applicate immediatamente ai bordi della soglia dinamica (limiti superiori e inferiori), perché vengono calcolate in base ai dati delle metriche degli ultimi 10 giorni. Quando si visualizzano i bordi della soglia dinamica per una determinata metrica, assicurarsi di esaminare la tendenza della metrica nell'ultima settimana e non solo per le ore o i giorni recenti.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Perché la stagionalità settimanale non è stata rilevata da soglie dinamiche?

Per identificare la stagionalità settimanale, il modello di soglie dinamiche richiede almeno tre settimane di dati cronologici. Quando sono disponibili dati cronologici sufficienti, qualsiasi stagionalità settimanale presente nei dati della metrica verrà identificata e il modello verrà regolato di conseguenza. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Le soglie dinamiche mostrano un limite inferiore negativo per una metrica anche se la metrica ha sempre valori positivi

Quando una metrica presenta una fluttuazione elevata, le soglie dinamiche compilano un modello più ampio intorno ai valori della metrica, che può comportare un bordo inferiore inferiore a zero. In particolare, questo problema può verificarsi nei casi seguenti:
1. La sensibilità è impostata su low 
2. I valori mediano sono vicini a zero
3. La metrica presenta un comportamento irregolare con varianza elevata (sono presenti picchi o DIP nei dati)

Quando il limite inferiore presenta un valore negativo, significa che la metrica raggiunge un valore zero in base al comportamento irregolare della metrica. È possibile scegliere una sensibilità più elevata o una maggiore *granularità di aggregazione (periodo)* per rendere il modello meno sensibile oppure utilizzare l'opzione *Ignora dati prima* per escludere un irregulaity recente dai dati cronologici utilizzati per compilare il modello.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali sulla risoluzione dei problemi relativi a avvisi e notifiche, vedere [risoluzione dei problemi negli avvisi di monitoraggio di Azure](alerts-troubleshoot.md).
