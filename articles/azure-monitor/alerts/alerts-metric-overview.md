---
title: Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure.
description: È disponibile una panoramica delle operazioni eseguibili con gli avvisi delle metriche e del relativo funzionamento in Monitoraggio di Azure.
ms.date: 03/11/2021
ms.topic: conceptual
ms.openlocfilehash: 8a243f0a2130e0ec2ebafe726f48e07c148807c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016084"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure

Gli avvisi delle metriche in Monitoraggio di Azure si basano su metriche multidimensionali. Queste metriche possono essere [metriche di piattaforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [metriche personalizzate](../essentials/metrics-custom-overview.md), [log comuni di Monitoraggio di Azure convertiti in metriche](./alerts-metric-logs.md) e metriche di Application Insights. Gli avvisi delle metriche eseguono valutazioni a intervalli regolari per verificare se le condizioni in una o più serie temporale di metriche vengono soddisfatte e, in caso affermativo, inviano una notifica. Gli avvisi delle metriche sono avvisi con stato, ovvero inviano notifiche solo quando lo stato cambia.

## <a name="how-do-metric-alerts-work"></a>Funzionamento degli avvisi delle metriche

È possibile definire una regola di avviso delle metriche specificando una risorsa di destinazione da monitorare, il nome della metrica, il tipo di condizione (statica o dinamica) e la condizione (un operatore e una soglia/sensibilità), oltre a un gruppo di azioni da attivare quando viene generata la regola di avviso. I tipi di condizione influiscono sulla modalità di determinazione delle soglie. [Altre informazioni sulle opzioni di sensibilità e i tipi di condizione delle soglie dinamiche](../alerts/alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regola di avviso con tipo di condizione statica

Supponiamo di aver creato una semplice regola di avviso delle metriche con soglia statica, come indicato di seguito:

- Risorsa di destinazione (risorsa di Azure da monitorare): myVM
- Metrica: CPU percentuale
- Tipo di condizione: static
- Tipo di aggregazione, ovvero una statistica eseguita su valori di metrica non elaborati. I [tipi di aggregazione supportati](../essentials/metrics-aggregation-explained.md#aggregation-types) sono minimo, massimo, medio, totale, numero): media
- Periodo (la finestra di ricerca su cui sono controllati i valori delle metriche): negli ultimi 5 minuti
- Frequenza (la frequenza con cui l'avviso della metrica controlla se le condizioni sono soddisfatte): 1 minuto
- Operatore: maggiore di
- Soglia: 70

Dal momento in cui viene creata la regola di avviso, il monitoraggio viene eseguito ogni minuto, analizza i valori delle metriche per gli ultimi 5 minuti e controlla se la media di questi valori supera 70. Se la condizione viene soddisfatta, ovvero il valore medio di CPU percentuale per gli ultimi 5 minuti supera 70, la regola di avviso genera una notifica attivata. Se è stata configurata un'azione di webhook o posta elettronica nel gruppo di azioni associato alla regola di avviso, si riceverà una notifica attivata su entrambe.

Quando si usano più condizioni in una regola, la regola "con" le condizioni insieme. Ovvero, un avviso viene generato quando tutte le condizioni nella regola di avviso restituiscono true e si risolvono quando una delle condizioni non è più vera. Un esempio di questo tipo di regola di avviso è il monitoraggio di una macchina virtuale di Azure e l'avviso quando la percentuale di CPU è superiore al 90% e la lunghezza della coda supera 300 elementi.

### <a name="alert-rule-with-dynamic-condition-type"></a>Regola di avviso con tipo di condizione dinamica

Supponiamo di aver creato una semplice regola di avviso delle metriche con soglie dinamiche, come indicato di seguito:

- Risorsa di destinazione (risorsa di Azure da monitorare): myVM
- Metrica: CPU percentuale
- Tipo di condizione: Dynamic
- Tipo di aggregazione, ovvero una statistica eseguita su valori di metrica non elaborati. I [tipi di aggregazione supportati](../essentials/metrics-aggregation-explained.md#aggregation-types) sono minimo, massimo, medio, totale, numero): media
- Periodo (la finestra di ricerca su cui sono controllati i valori delle metriche): negli ultimi 5 minuti
- Frequenza (la frequenza con cui l'avviso della metrica controlla se le condizioni sono soddisfatte): 1 minuto
- Operatore: maggiore di
- Sensibilità: media
- Punti di ricerca indietro: 4
- Numero di violazioni: 4

Dopo la creazione della regola di avviso, l'algoritmo di Machine Learning relativo alle soglie dinamiche acquisisce i dati cronologici disponibili, calcola la soglia più adatta al modello di comportamento della serie di metriche e, in base ai nuovi dati, migliora continuamente l'apprendimento per rendere la soglia più precisa.

Dal momento in cui viene creata la regola di avviso, ogni minuto vengono monitorati i valori delle metriche degli ultimi 20 minuti raggruppati in periodi di 5 minuti per verificare se la media dei valori del periodo in ognuno dei 4 periodi supera la soglia prevista. Se la condizione viene soddisfatta, ossia se la media di Percentuale CPU negli ultimi 20 minuti (quattro periodi di 5 minuti) si è discostata per quattro volte dal comportamento previsto, la regola di avviso genera una notifica attivata. Se è stata configurata un'azione di webhook o posta elettronica nel gruppo di azioni associato alla regola di avviso, si riceverà una notifica attivata su entrambe.

### <a name="view-and-resolution-of-fired-alerts"></a>Visualizzazione e risoluzione degli avvisi generati

Gli esempi di generazione di regole di avviso riportati sopra possono essere visualizzati anche nel portale di Azure, nel pannello **Tutti gli avvisi**.

Se l'utilizzo in "myVM" rimane al di sopra della soglia nei controlli successivi, la regola di avviso non verrà nuovamente generata fino alla risoluzione delle condizioni.

Dopo un certo periodo di tempo, l'utilizzo di "myVM" Torna al normale (scende al di sotto della soglia). la regola di avviso monitora la condizione altre due volte, per inviare una notifica risolta. La regola di avviso invia una notifica risolta/disattivata se la condizione di avviso non viene soddisfatta per tre volte consecutive, per ridurre il rumore in caso di condizioni instabili.

Quando la notifica risolta viene inviata tramite posta elettronica o webhook, anche lo stato dell'istanza di avviso (denominato stato di monitoraggio) nel portale di Azure viene impostato come risolto.

> [!NOTE]
>
> Quando una regola di avviso monitora più condizioni, un avviso attivato viene risolto se almeno una delle condizioni non viene più soddisfatta per tre periodi consecutivi.

### <a name="using-dimensions"></a>Uso delle dimensioni

Gli avvisi delle metriche in Monitoraggio di Azure supportano anche il monitoraggio di più combinazioni di valori di dimensioni con una sola regola. I motivi per cui usare più combinazioni di dimensioni vengono qui illustrati con l'aiuto di un esempio.

Si supponga di avere un piano di servizio app per il sito Web. Si intende monitorare l'utilizzo della CPU in più istanze che eseguono il sito Web o l'app. A tale scopo, è possibile usare una regola di avviso delle metriche come indicato di seguito:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: static
- Dimensioni
  - Istanza = InstanceName1, InstanceName2
- Tipo di aggregazione: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 minuto
- Operatore: Maggiore di
- Soglia: 70

Come in precedenza, questa regola monitora se l'utilizzo medio della CPU negli ultimi 5 minuti supera il 70%. Tuttavia, con la stessa regola è possibile monitorare due istanze che eseguono il sito Web. Ogni istanza verrà monitorata individualmente e si riceveranno notifiche individuali.

Supponiamo di avere un'app Web che registra un numero di richieste elevato, a causa delle quali è necessario aggiungere altre istanze. La regola precedente continua a monitorare solo due istanze. Tuttavia, è possibile creare una regola nel modo seguente:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: static
- Dimensioni
  - Istanza = *
- Tipo di aggregazione: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 minuto
- Operatore: Maggiore di
- Soglia: 70

Questa regola monitorerà automaticamente tutti i valori per l'istanza, ovvero è possibile monitorare le istanze in tempo reale senza dover modificare nuovamente la regola di avviso per la metrica.

Quando vengono monitorate più dimensioni, le regole di avviso con soglie dinamiche possono definire soglie personalizzate per centinaia di serie di metriche alla volta. Le soglie dinamiche riducono il numero di regole di avviso da gestire e consentono un notevole risparmio di tempo sulla gestione e la creazione delle regole di avviso.

Supponiamo di avere un'app Web con numerose istanze e di non sapere quale sia la soglia più adatta. Le regole descritte sopra usano sempre una soglia del 70%. Tuttavia, è possibile creare una regola nel modo seguente:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: Dynamic
- Dimensioni
  - Istanza = *
- Tipo di aggregazione: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 minuto
- Operatore: Maggiore di
- Sensibilità: media
- Punti di ricerca: 1
- Numero di violazioni: 1

Questa regola monitora se l'utilizzo medio della CPU negli ultimi 5 minuti supera il comportamento previsto per ogni istanza. Con la stessa regola è possibile monitorare le istanze in tempo reale senza dover modificare nuovamente la regola di avviso per la metrica. Ogni istanza otterrà una soglia adatta al modello di comportamento della serie di metriche e cambierà continuamente in base ai nuovi dati per rendere la soglia più precisa. Ogni istanza verrà monitorata individualmente e si riceveranno notifiche individuali.

È anche possibile aumentare i periodi da monitorare e il numero di violazioni per consentire il filtro degli avvisi in modo che vengano generati solo in base alla definizione di deviazione significativa specificata dall'utente. [Altre informazioni sulle opzioni avanzate delle soglie dinamiche](../alerts/alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> È consigliabile scegliere una *granularità di aggregazione (periodo)* maggiore della *frequenza di valutazione*, per ridurre la probabilità che manchi la prima valutazione della serie temporale aggiunta nei casi seguenti:
> - Regola di avviso metrica che monitora più dimensioni: quando viene aggiunta una nuova combinazione di valori di dimensione
> - Regola di avviso metrica che monitora più risorse: quando una nuova risorsa viene aggiunta all'ambito
> - Regola di avviso metrica che monitora una metrica che non viene emessa continuamente (metrica di tipo sparse): quando la metrica viene emessa dopo un periodo di tempo superiore a 24 ore in cui non è stata emessa



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoraggio su larga scala mediante gli avvisi delle metriche in Monitoraggio di Azure

Fino a questo punto abbiamo visto che un solo avviso di metrica può essere usato per monitorare una o molte serie temporali di metriche correlate a una singola risorsa di Azure. Spesso si ha l'esigenza di applicare la stessa regola di avviso a molte risorse. Monitoraggio di Azure supporta anche il monitoraggio di più risorse (dello stesso tipo) con una regola di avviso di metrica, per le risorse presenti nella stessa area di Azure. 

Questa funzionalità è attualmente supportata per le metriche della piattaforma (non per le metriche personalizzate) per i servizi seguenti nei cloud di Azure seguenti:

| Servizio | Public Azure (Azure pubblico) | Enti governativi | Cina |
|:--------|:--------|:--------|:--------|
| Macchine virtuali<sup>1</sup>  | **Sì** | **Sì** | **Sì** |
| Database di SQL Server | **Sì** | **Sì** | **Sì** |
| Pool elastici di SQL Server | **Sì** | **Sì** | **Sì** |
| Pool di capacità dei file NetApp | **Sì** | **Sì** | **Sì** |
| Volumi di file NetApp | **Sì** | **Sì** | **Sì** |
| Key Vault | **Sì** | **Sì** | **Sì** |
| Cache di Azure per Redis | **Sì** | **Sì** | **Sì** |
| Dispositivi perimetrali data box | **Sì** | **Sì** | **Sì** |

<sup>1</sup> non supportato per le metriche di rete della macchina virtuale (rete totale, totale di rete, flussi in ingresso, flussi in uscita, velocità di creazione massima dei flussi in ingresso, velocità di creazione massima dei flussi in uscita).

È possibile specificare l'ambito di monitoraggio tramite una singola regola di avviso metrica in uno dei tre modi seguenti. Con le macchine virtuali, ad esempio, è possibile specificare l'ambito come:  

- elenco di macchine virtuali (in un'area di Azure) all'interno di una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in uno o più gruppi di risorse in una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in una sottoscrizione

> [!NOTE]
>
> L'ambito di una regola di avviso per la metrica di più risorse deve contenere almeno una risorsa del tipo di risorsa selezionato.

La creazione di regole di avviso per le metriche che monitorano più risorse equivale a [creare qualsiasi altro avviso per le metriche](../alerts/alerts-metric.md) che monitora una sola risorsa. L'unica differenza è che occorre selezionare tutte le risorse da monitorare. È possibile creare queste regole anche tramite i [modelli di Azure Resource Manager](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Si riceveranno notifiche singole per ogni risorsa monitorata.

> [!NOTE]
>
> In una regola di avviso delle metriche che monitora più risorse è supportata una sola condizione.

## <a name="typical-latency"></a>Latenza tipica

Per gli avvisi delle metriche si riceve in genere una notifica in meno di 5 minuti se si imposta la frequenza delle regole di avviso su 1 minuto. In caso di carico elevato per i sistemi di notifica, la latenza potrebbe essere superiore.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipi di risorse supportati per gli avvisi delle metriche

L'elenco completo dei tipi di risorse supportati è disponibile in questo [articolo](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come creare, visualizzare e gestire gli avvisi delle metriche in Azure](../alerts/alerts-metric.md)
- [Informazioni su come creare avvisi in Azure Montir Esplora metriche](../essentials/metrics-charts.md#alert-rules)
- [Informazioni su come distribuire gli avvisi delle metriche mediante modelli di Azure Resource Manager](./alerts-metric-create-templates.md)
- [Altre informazioni sui gruppi di azioni](./action-groups.md)
- [Altre informazioni sul tipo di condizione delle soglie dinamiche](../alerts/alerts-dynamic-thresholds.md)
- [Altre informazioni sulla risoluzione dei problemi negli avvisi delle metriche](alerts-troubleshoot-metric.md)
