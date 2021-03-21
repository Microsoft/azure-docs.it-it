---
title: Gestire i costi di Azure con l'automazione
description: Questo articolo illustra come gestire i costi di Azure con l'automazione.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 2a39f77e3e7409d23ab7506b525f65e01082e99e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720118"
---
# <a name="manage-costs-with-automation"></a>Gestire i costi con l'automazione

È possibile usare l'automazione di Gestione costi per creare un set personalizzato di soluzioni con cui recuperare e gestire i dati sui costi. Questo articolo riguarda gli scenari comuni di automazione di Gestione costi e le opzioni disponibili in base alla situazione. Per lo sviluppo tramite API, vengono presentati esempi di richieste API comuni per accelerare il processo.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizzare il recupero dei dati sui costi per l'analisi offline

Potrebbe essere necessario scaricare i dati sui costi di Azure per unirli ad altri set di dati. Oppure potrebbe essere necessario integrare i dati sui costi nei propri sistemi. Sono disponibili diverse opzioni in base alla quantità di dati coinvolti. In ogni caso, per usare le API e gli strumenti, è necessario avere le autorizzazioni di Gestione costi nell'ambito appropriato. Per altre informazioni, vedere [Assegnare l'accesso ai dati](./assign-access-acm-data.md).

## <a name="suggestions-for-handling-large-datasets"></a>Suggerimenti per la gestione di set di dati di grandi dimensioni

Se l'organizzazione usa diffusamente Azure tra molte risorse o sottoscrizioni, sarà presente una grande quantità di dati dettagliati sull'utilizzo. Spesso Excel non è in grado di caricare tali file di grandi dimensioni. In questa situazione è consigliabile scegliere le opzioni seguenti:

**Power BI**

Power BI consente di inserire e gestire grandi quantità di dati. I clienti con contratto Enterprise possono usare l'app modello di Power BI per analizzare i costi per l'account di fatturazione. Il report contiene le principali visualizzazioni usate dai clienti. Per altre informazioni, vedere [Analizzare i costi di Azure con l'app modello di Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).

**Connettore dati di Power BI**

Per analizzare i dati quotidianamente, è consigliabile usare il [connettore dati di Power BI](/power-bi/connect-data/desktop-connect-azure-cost-management) per eseguire un'analisi dettagliata. Tutti i report creati vengono mantenuti aggiornati dal connettore mentre vengono accumulati altri costi.

**Esportazioni di Gestione costi**

A volte non è necessario analizzare i dati quotidianamente. In questo caso, è consigliabile usare la funzionalità [Esportazioni](./tutorial-export-acm-data.md) per pianificare le esportazioni di dati in un account di archiviazione di Azure. È quindi possibile caricare i dati in Power BI, se necessario, o analizzarli in Excel se le dimensioni del file sono sufficientemente ridotte. Le esportazioni sono disponibili nel portale di Azure oppure è possibile configurarle con l'[API Esportazioni](/rest/api/cost-management/exports).

**API Dettagli utilizzo**

Se il set di dati sui costi è di piccole dimensioni, provare a usare l'[API Dettagli utilizzo](/rest/api/consumption/usageDetails). Nel caso di una quantità elevata di dati sui costi, è consigliabile richiedere la quantità di dati sull'utilizzo minima possibile per un periodo. A tale scopo, specificare un intervallo di tempo ridotto o usare un filtro nella richiesta. Ad esempio, in uno scenario in cui sono necessari tre anni di dati sui costi, l'API offre prestazioni migliori se si effettuano più chiamate per intervalli di tempo diversi invece di una singola. Da qui, è possibile caricare i dati in Excel per un'ulteriore analisi.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizzare il recupero con l'API Dettagli utilizzo

L'[API Dettagli utilizzo ](/rest/api/consumption/usageDetails) offre un modo semplice per ottenere dati sui costi non elaborati e non aggregati corrispondenti alla fattura di Azure. L'API è utile quando l'organizzazione necessita di una soluzione di recupero dei dati a livello di codice. È consigliabile usare l'API per l'analisi di set di dati sui costi di dimensioni ridotte. Nel caso di set di dati più grandi, è invece consigliabile usare le altre soluzioni identificate in precedenza. I dati in Dettagli utilizzo vengono forniti per ogni contatore al giorno. Vengono usati per il calcolo della fattura mensile. La versione disponibile a livello generale di queste API è `2019-10-01`. Usare `2019-04-01-preview` per accedere alla versione di anteprima per la prenotazione e gli acquisti in Azure Marketplace con le API.

Se si desidera ottenere regolarmente grandi quantità di dati esportati, vedere Recupero di set di dati [a costo elevato ricorrenti con le esportazioni](ingest-azure-usage-at-scale.md).

### <a name="usage-details-api-suggestions"></a>Suggerimenti per l'API Dettagli utilizzo

**Pianificazione delle richieste**

È consigliabile effettuare _non più di una richiesta_ al giorno all'API Dettagli utilizzo. Per altre informazioni sulla frequenza di aggiornamento dei dati sui costi e su come viene gestito l'arrotondamento, vedere [Informazioni sui dati di Gestione costi](./understand-cost-mgt-data.md).

**Scegliere ambiti di primo livello senza filtro**

Usare l'API per ottenere tutti i dati necessari nell'ambito di livello più alto disponibile. Attendere che vengano inseriti tutti i dati necessari prima di eseguire operazioni di filtro, raggruppamento o analisi aggregate. L'API è ottimizzata specificamente per fornire grandi quantità di dati sui costi non elaborati e non aggregati. Per altre informazioni sugli ambiti disponibili in Gestione costi, vedere [Informazioni e utilizzo degli ambiti](./understand-work-scopes.md). Una volta scaricati i dati necessari per un ambito, usare Excel per analizzare ulteriormente i dati con i filtri e le tabelle pivot.

### <a name="notes-about-pricing"></a>Note sui prezzi

Per riconciliare l'utilizzo e gli addebiti con l'elenco prezzi o la fattura, tenere presenti le informazioni seguenti.

Prezzi dell'elenco prezzi: i prezzi indicati nell'elenco prezzi sono quelli che si ricevono da Azure. Sono dimensionati in base a un'unità di misura specifica. Purtroppo, l'unità di misura non è sempre allineata a quella in cui vengono emessi gli addebiti e i dati di utilizzo effettivo delle risorse.

Prezzi nei dettagli di utilizzo: i file sull'utilizzo mostrano informazioni dimensionate che potrebbero non corrispondere esattamente all'elenco prezzi. In particolare:

- Prezzo unitario: il prezzo è dimensionato in base all'unità di misura in cui vengono effettivamente emessi gli addebiti dalle risorse di Azure. Con il dimensionamento, il prezzo non corrisponderà a quello indicato nell'elenco prezzi.
- Unità di misura: rappresenta l'unità di misura in cui vengono effettivamente emessi gli addebiti dalle risorse di Azure.
- Prezzo effettivo/tariffa per le risorse: il prezzo rappresenta la tariffa effettiva del pagamento unitario, al netto degli sconti. Si tratta del prezzo da usare con la quantità per i calcoli di prezzo x quantità in modo da riconciliare gli addebiti. Il prezzo tiene conto degli scenari seguenti e nel file è presente anche il prezzo unitario dimensionato. Di conseguenza, potrebbe essere diverso dal prezzo unitario dimensionato.
  - Livelli di prezzo diversi, ad esempio: $ 10 per le prime 100 unità, $ 8 per le unità 100 successive.
  - Quantità inclusa, ad esempio: le prime 100 unità sono gratuite e poi vengono addebitati $ 10 per unità.
  - Prenotazioni
  - Arrotondamento durante il calcolo: l'arrotondamento tiene conto della quantità consumata, dei prezzi della quantità inclusa/a livelli e del prezzo unitario dimensionato.

### <a name="a-single-resource-might-have-multiple-records-for-a-single-day"></a>Una singola risorsa potrebbe avere più record per un singolo giorno

I provider di risorse di Azure emettono l'utilizzo e gli addebiti per il sistema di fatturazione e popolano il `Additional Info` campo dei record di utilizzo. Occasionalmente, i provider di risorse possono emettere utilizzo per un determinato giorno e contrassegnare i record con diversi Data Center nel `Additional Info` campo dei record di utilizzo. Può causare la presenza di più record per un contatore o una risorsa nel file di utilizzo per un singolo giorno. In tal caso, non viene addebitato alcun addebito. I record multipli rappresentano il costo totale del contatore per la risorsa in quel giorno.

## <a name="example-usage-details-api-requests"></a>Esempi di richieste all'API Dettagli utilizzo

Le richieste di esempio seguenti vengono usate dai clienti Microsoft negli scenari comuni che si potrebbero riscontrare.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Ottenere i dettagli di utilizzo per un ambito durante un intervallo di date specifico

I dati di utilizzo restituiti dalla richiesta corrispondono alla data in cui sono stati ricevuti dal sistema di fatturazione. Possono includere i costi di più fatture. La chiamata da usare varia in base al tipo di sottoscrizione.

Per i clienti legacy con un contratto Enterprise (EA) o una sottoscrizione con pagamento in base al consumo, usare la chiamata seguente:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Per i clienti moderni con un Contratto del cliente Microsoft, usare la chiamata seguente:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Ottenere dettagli sui costi ammortizzati

Se sono necessari i costi effettivi per dimostrare gli acquisti accumulati, impostare *metric* su `ActualCost` nella richiesta seguente. Per usare i costi ammortizzati ed effettivi, è necessario usare la versione `2019-04-01-preview`. La versione corrente dell'API funziona allo stesso modo della versione `2019-10-01`, ad eccezione del nuovo attributo type/metric e dei nomi di proprietà modificati. Se si ha un Contratto del cliente Microsoft, i filtri sono `startDate` e `endDate` nell'esempio seguente.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="automate-alerts-and-actions-with-budgets"></a>Automatizzare gli avvisi e le azioni con i budget

Esistono sono due componenti essenziali per massimizzare il valore del proprio investimento nel cloud. Uno è la creazione automatica del budget. L'altro è la configurazione dell'orchestrazione basata sui costi in risposta agli avvisi del budget. Esistono diversi modi per automatizzare la creazione del budget di Azure. Diverse risposte agli avvisi si verificano quando vengono superate le soglie di avviso configurate.

Le sezioni seguenti includono le opzioni disponibili e forniscono richieste API di esempio per iniziare a usare l'automazione del budget.

### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Come vengono valutati i costi rispetto alla soglia del budget

I costi vengono valutati rispetto alla soglia del budget una volta al giorno. Quando si crea un nuovo budget o il giorno del ripristino del budget, i costi saranno zero/null rispetto alla soglia perché la valutazione potrebbe non essersi verificata.

Quando Azure rileva che i costi hanno superato la soglia, viene attivata una notifica entro l'ora del periodo di rilevamento.

### <a name="view-your-current-cost"></a>Visualizzare il costo corrente

Per visualizzare i costi correnti, è necessario effettuare una chiamata GET usando l'[API di query](/rest/api/cost-management/query).

Una chiamata GET all'API dei budget non restituirà i costi correnti indicati nell'analisi dei costi. La chiamata restituisce invece l'ultimo costo valutato.

### <a name="automate-budget-creation"></a>Automatizzare la creazione del budget

È possibile automatizzare la creazione del budget usando l'[API dei budget](/rest/api/consumption/budgets). È anche possibile creare un budget con un [modello di budget](quick-create-budget-template.md). I modelli rappresentano un modo semplice per standardizzare le distribuzioni di Azure garantendo al contempo la corretta configurazione e applicazione del controllo dei costi.

### <a name="supported-locales-for-budget-alert-emails"></a>Impostazioni locali supportate per i messaggi di posta elettronica di avviso relativi ai budget

Con i budget si riceve un avviso quando i costi superano una soglia impostata. È possibile impostare un massimo di cinque destinatari di posta elettronica per ogni budget. I destinatari ricevono i messaggi di posta elettronica di avviso entro 24 ore dal superamento della soglia del budget. È però possibile che il destinatario debba ricevere un messaggio di posta elettronica in una lingua diversa. Con l'API dei budget è possibile usare i codici di impostazioni cultura della lingua seguenti. Per definire le impostazioni cultura, usare il parametro `locale` come descritto nell'esempio seguente.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Lingue supportate dalle impostazioni cultura:

| Codice di impostazioni cultura| Linguaggio |
| --- | --- |
| it-it | Inglese (Stati Uniti) |
| ja-jp | Giapponese (Giappone) |
| zh-cn | Cinese (semplificato, Cina) |
| de-de | Tedesco (Germania) |
| es-es | Spagnolo (Spagna, internazionale) |
| fr-fr | Francese (Francia) |
| it-it | Italiano (Italia) |
| ko-kr | Coreano (Corea) |
| pt-br | Portoghese (Brasile) |
| ru-ru | Russo (Russia) |
| zh-tw | Cinese (tradizionale, Taiwan) |
| cs-cz | Ceco (Repubblica Ceca) |
| pl-pl | Polacco (Polonia) |
| tr-tr | Turco (Turchia) |
| da-dk | Danese (Danimarca) |
| en-GB | Inglese (Regno Unito) |
| hu-hu | Ungherese (Ungheria) |
| nb-no | Norvegese Bokmål (Norvegia) |
| nl-nl | Olandese (Paesi Bassi) |
| pt-pt | Portoghese (Portogallo) |
| sv-se | Svedese (Svezia) |

### <a name="common-budgets-api-configurations"></a>Configurazioni comuni dell'API dei budget

Esistono diversi modi per configurare un budget nell'ambiente Azure. Considerare prima lo scenario e quindi identificare le opzioni di configurazione che lo abilitano. Esaminare le opzioni seguenti:

- **Intervallo di tempo**: rappresenta il periodo ricorrente usato dal budget per accumulare e valutare i costi. Le opzioni più comuni sono Ogni mese, Ogni tre mesi e Ogni anno.
- **Periodo di tempo**: indica per quanto tempo il budget è valido. Il budget monitora e avvisa attivamente solo se rimane valido.
- **Notifications**
  - Indirizzi di posta elettronica di contatto: gli indirizzi di posta elettronica ricevono gli avvisi quando un budget accumula i costi e supera le soglie definite.
  - Ruoli di contatto: con questa opzione tutti gli utenti con un ruolo di Azure corrispondente nell'ambito specificato ricevono gli avvisi di posta elettronica. Ad esempio, i proprietari della sottoscrizione potrebbero ricevere un avviso per un budget creato nell'ambito della sottoscrizione.
  - Gruppi di contatti: il budget chiama i gruppi di azioni configurati quando viene superata una soglia di avviso.
- **Filtri dimensione costi**: gli stessi filtri che è possibile applicare all'analisi dei costi o all'API di query possono essere applicati al budget. Usare questo filtro per ridurre l'intervallo di costi che si sta monitorando con il budget.

Dopo aver identificato le opzioni di creazione del budget che soddisfano le proprie esigenze, creare il budget usando l'API. L'esempio seguente consente di iniziare con una configurazione di budget comune.

**Creare un budget filtrato in base a più risorse e tag**

URL richiesta: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Configurare l'orchestrazione basata sui costi per gli avvisi di budget

È possibile configurare i budget per avviare le azioni automatizzate tramite i gruppi di azioni di Azure. Per altre informazioni su come automatizzare le azioni usando i budget, vedere [Automazione con i budget di Azure](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Latenza dei dati e limiti di velocità

È consigliabile chiamare le API non più di una volta al giorno. I dati di Gestione costi vengono aggiornati ogni quattro ore man mano che vengono ricevuti dati di utilizzo dai provider di risorse di Azure. Se si effettuano chiamate più frequenti, non si ricevono più dati, ma si crea solo un carico maggiore. Per altre informazioni sulla frequenza con cui cambiano i dati e su come viene gestita la latenza, vedere [Informazioni sui dati di Gestione costi](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Codice di errore 429 - Il numero di chiamate ha superato i limiti di velocità

Per garantire un'esperienza coerente per tutti i sottoscrittori di Gestione costi, le API di Gestione costi prevedono un limite di velocità. Quando si raggiunge il limite, si riceve il codice di stato `429: Too many requests`. I limiti di velocità effettiva correnti per le API sono i seguenti:

- 30 chiamate al minuto per ambito, per utente o per applicazione.
- 200 chiamate al minuto per tenant, per utente o per applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Analizzare i costi di Azure con l'app modello di Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).
- [Creare e gestire dati esportati](./tutorial-export-acm-data.md) con Esportazioni.
- Altre informazioni sull'[API Dettagli utilizzo](/rest/api/consumption/usageDetails).
