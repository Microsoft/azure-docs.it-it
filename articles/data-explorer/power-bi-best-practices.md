---
title: Procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Azure Esplora dati
description: Questo articolo illustra le procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251739"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Procedure consigliate per l'uso di Power BI per eseguire query e visualizzare i dati di Azure Esplora dati

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. [Power bi](https://docs.microsoft.com/power-bi/) è una soluzione di analisi business che consente di visualizzare i dati e condividere i risultati nell'intera organizzazione. Azure Esplora dati offre tre opzioni per la connessione ai dati Power BI. Usare il [connettore incorporato](power-bi-connector.md), [importare una query da Azure Esplora dati in Power bi](power-bi-imported-query.md)oppure usare una [query SQL](power-bi-sql-query.md). Questo articolo fornisce suggerimenti per l'esecuzione di query e la visualizzazione dei dati di Azure Esplora dati con Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Procedure consigliate per l'utilizzo di Power BI 

Quando si utilizzano terabyte di dati non elaborati aggiornati, attenersi alle linee guida seguenti per evitare che i dashboard e i report di Power BI siano bloccati e aggiornati:

* **Travel Light** : consente di Power bi i dati necessari per i report. Per un'analisi interattiva approfondita, usare l' [interfaccia utente Web di Azure Esplora dati](web-query-data.md) ottimizzata per l'esplorazione ad hoc con il linguaggio di query kusto.

* **Modello composito** : usare il [modello composito](https://docs.microsoft.com/power-bi/desktop-composite-models) per combinare i dati aggregati per i dashboard di primo livello con dati non elaborati operativi filtrati. È possibile definire chiaramente quando utilizzare dati non elaborati e quando utilizzare una vista aggregata. 

* Modalità di **importazione rispetto alla modalità DirectQuery** : usare la modalità di **importazione** per l'interazione di set di dati più piccoli. Utilizzare la modalità **DirectQuery** per set di dati di grandi dimensioni e aggiornati di frequente. Ad esempio, creare tabelle delle dimensioni usando la modalità di **importazione** poiché sono di piccole dimensioni e non cambiano spesso. Impostare l'intervallo di aggiornamento in base alla frequenza prevista di aggiornamenti dei dati. Creare tabelle dei fatti usando la modalità **DirectQuery** poiché le tabelle sono di grandi dimensioni e contengono dati non elaborati. Utilizzare queste tabelle per presentare i dati filtrati utilizzando Power BI [drill-through](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Parallelismo** : Azure Esplora dati è una piattaforma di dati scalabile linearmente, pertanto è possibile migliorare le prestazioni del rendering del dashboard aumentando il parallelismo del flusso end-to-end come indicato di seguito:

   * Aumentare il numero di [connessioni simultanee in DirectQuery in Power bi](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Usare la [coerenza debole per migliorare il parallelismo](/azure/kusto/concepts/queryconsistency). Questo può influisca sull'aggiornamento dei dati.

* **Sezionamenti effettivi** : usare i filtri dei dati di [sincronizzazione](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) per impedire ai report di caricare i dati prima di essere pronti. Dopo aver strutturato il set di dati, inserire tutti gli oggetti visivi e contrassegnare tutti i filtri dei dati, è possibile selezionare il filtro dei dati di sincronizzazione per caricare solo i dati necessari.

* **Usare i filtri** : usare il maggior numero possibile di filtri Power BI per concentrare la ricerca di Azure Esplora dati sulle partizioni di dati rilevanti.

* Oggetti **visivi efficienti** : selezionare gli oggetti visivi più performanti per i dati.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Suggerimenti per l'uso di Azure Esplora dati Connector per Power BI per eseguire query sui dati

La sezione seguente include suggerimenti e consigli per l'uso del linguaggio di query kusto con Power BI. Usare [Azure Esplora dati Connector per Power bi](power-bi-connector.md) per visualizzare i dati

### <a name="complex-queries-in-power-bi"></a>Query complesse in Power BI

Le query complesse sono più facilmente espresse in kusto rispetto a Power Query. Devono essere implementate come [funzioni kusto](/azure/kusto/query/functions)e richiamate in Power bi. Questo metodo è obbligatorio quando si **DirectQuery** USA DirectQuery `let` con le istruzioni nella query kusto. Poiché Power BI unisce due query e `let` le istruzioni non possono essere utilizzate con l' `join` operatore, possono verificarsi errori di sintassi. Quindi, salvare ogni parte del join come funzione kusto e consentire a Power BI di unire queste due funzioni insieme.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Come simulare un operatore di data e ora relativo

Power BI non contiene un operatore di data e ora *relativo* , `ago()`ad esempio.
Per simulare `ago()`, utilizzare una combinazione di `DateTime.FixedLocalNow()` funzioni `#duration` e Power bi.

Anziché questa query utilizzando l' `ago()` operatore:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Usare la query equivalente seguente:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Raggiungimento dei limiti delle query kusto 

Le query kusto restituiscono, per impostazione predefinita, fino a 500.000 righe o 64 MB, come descritto in [limiti di query](/azure/kusto/concepts/querylimits). È possibile eseguire l'override di queste impostazioni predefinite usando le **Opzioni avanzate** nella finestra di connessione di **Azure Esplora dati (kusto)** :

![opzioni avanzate](media/power-bi-best-practices/advanced-options.png)

Queste opzioni inviano [istruzioni set](/azure/kusto/query/setstatement) con la query per modificare i limiti di query predefiniti:

  * Il **numero di record del risultato della query Limit** genera un`set truncationmaxrecords`
  * **Limita dimensioni dati risultati query in byte** genera un`set truncationmaxsize`
  * **Disabilita il troncamento del set di risultati** genera un'`set notruncation`

### <a name="using-query-parameters"></a>Utilizzo di parametri di query

È possibile utilizzare i [parametri di query](/azure/kusto/query/queryparametersstatement) per modificare la query in modo dinamico. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Utilizzo di un parametro di query nei dettagli della connessione

Utilizzare un parametro di query per filtrare le informazioni nella query e ottimizzare le prestazioni di esecuzione delle query.
 
Nella finestra **modifica query** **Home** > **Editor avanzato**

1. Trovare la sezione seguente della query:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Ad esempio:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Sostituire la parte pertinente della query con il parametro. Suddividere la query in più parti e concatenarle usando una e commerciale (&) insieme al parametro.

   Nella query precedente, ad esempio, si prenderà `State == 'ALABAMA'` la parte e la si suddividerà in: `State == '` e `'` e il `State` parametro verrà inserito tra di essi:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Se la query contiene virgolette, codificarle correttamente. Ad esempio, la query seguente: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   verrà visualizzato nel **Editor avanzato** come segue con due virgolette:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   È necessario sostituirlo con la seguente query con tre virgolette:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Usare un parametro di query nei passaggi della query

È possibile usare un parametro di query in qualsiasi passaggio della query che lo supporta. Filtrare, ad esempio, i risultati in base al valore di un parametro.

![filtrare i risultati usando un parametro](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Non usare Power BI utilità di pianificazione dell'aggiornamento dati per inviare comandi di controllo a kusto

Power BI include un'utilità di pianificazione dell'aggiornamento dati che consente di eseguire periodicamente query su un'origine dati. Questo meccanismo non deve essere utilizzato per pianificare i comandi di controllo in kusto perché Power BI presuppone che tutte le query siano di sola lettura.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI possibile inviare query brevi (&lt;2000 caratteri) a kusto

Se l'esecuzione di una query in Power BI restituisce l'errore seguente: _"DataSource. Error: Web. Contents non è riuscito a ottenere il contenuto da..."_ la query è probabilmente più lunga di 2000 caratteri. Power BI USA **PowerQuery** per eseguire una query su kusto generando una richiesta HTTP Get che codifica la query come parte dell'URI recuperato. Pertanto, le query kusto rilasciate da Power BI sono limitate alla lunghezza massima di un URI di richiesta (2000 caratteri, meno offset ridotto). Come soluzione alternativa, è possibile definire una [funzione archiviata](/azure/kusto/query/schema-entities/stored-functions) in Kusto e Power bi usare tale funzione nella query.

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare i dati con il connettore Esplora dati di Azure per Power BI](power-bi-connector.md)




