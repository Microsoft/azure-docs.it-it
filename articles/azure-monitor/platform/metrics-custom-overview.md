---
title: Metriche personalizzate in monitoraggio di Azure (anteprima)
description: Informazioni sulle metriche personalizzate in Monitoraggio di Azure e sul modo in cui vengono modellate.
author: ancav
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2020
ms.subservice: metrics
ms.openlocfilehash: 9581bb17e29a25b618a90aece5675d132c14a97c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081492"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Metriche personalizzate in monitoraggio di Azure (anteprima)

Quando si distribuiscono risorse e applicazioni in Azure, è opportuno iniziare a raccogliere dati di telemetria per ottenere informazioni dettagliate sulle prestazioni e sulla stato relativi. Azure rende alcune metriche predefinite disponibili all'utente. Queste metriche sono denominate [standard o Platform](./metrics-supported.md). Tuttavia, esse sono intrinsecamente limitate. 

È opportuno pertanto raccogliere alcuni indicatori delle prestazioni personalizzati o metriche specifiche dell'azienda per ottenere informazioni più dettagliate. Le metriche **personalizzate** possono essere raccolte tramite i dati di telemetria dell'applicazione o un agente in esecuzione sulle risorse di Azure o anche all'esterno del sistema di monitoraggio e inviate direttamente a Monitoraggio di Azure. Dopo la pubblicazione in Monitoraggio di Azure, è possibile esplorare le metriche personalizzate per le risorse e le applicazioni di Azure, nonché eseguire query e inviare avvisi in modo analogo a come si opera sulle metriche standard generate da Azure.

Le metriche personalizzate di monitoraggio di Azure sono attualmente disponibili in anteprima pubblica. 

## <a name="methods-to-send-custom-metrics"></a>Metodi per l'invio di metriche personalizzate

È possibile inviare le metriche personalizzate a Monitoraggio di Azure mediante diversi metodi:
- Instrumentare l'applicazione usando Azure Application Insights SDK e inviare i dati di telemetria personalizzati a Monitoraggio di Azure. 
- Installare l'estensione Diagnostica di Azure per Windows (WAD) nella [macchina virtuale di Azure](collect-custom-metrics-guestos-resource-manager-vm.md), nel [set di scalabilità di macchine virtuali](collect-custom-metrics-guestos-resource-manager-vmss.md), nella [macchina virtuale classica](collect-custom-metrics-guestos-vm-classic.md) o nel [servizio cloud classico](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e inviare i contatori delle prestazioni a Monitoraggio di Azure. 
- Installare l'[agente InfluxDB Telegraf](collect-custom-metrics-linux-telegraf.md) nella macchina virtuale Linux di Azure e inviare le metriche tramite il plug-in di output di Monitoraggio di Azure.
- Inviare metriche personalizzate [direttamente all'API REST di monitoraggio di Azure](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Modello di determinazione dei prezzi e conservazione

Vedere la [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) per informazioni dettagliate su quando verrà abilitata la fatturazione per le query metriche e metriche personalizzate. In questa pagina sono disponibili dettagli specifici sui prezzi per tutte le metriche, incluse le metriche personalizzate e le query sulle metriche. In breve, non è previsto alcun costo per inserire metriche standard (metriche della piattaforma) nell'archivio delle metriche di monitoraggio di Azure, ma le metriche personalizzate comportano costi quando entrano nella disponibilità generale. Le query dell'API metrica sono soggette a costi.

Le metriche personalizzate vengono mantenute per la [stessa quantità di tempo delle metriche della piattaforma](data-platform-metrics.md#retention-of-metrics). 

> [!NOTE]  
> Le metriche inviate a monitoraggio di Azure tramite il Application Insights SDK vengono fatturate come dati di log inseriti. Vengono addebitate solo le metriche aggiuntive solo se è stata selezionata la funzionalità Application Insights [Abilita avvisi sulle dimensioni metriche personalizzate](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) . Questa casella di controllo Invia i dati al database di metriche di monitoraggio di Azure usando l'API metrica personalizzata per consentire gli avvisi più complessi.  Scopri di più sul [modello di determinazione prezzi Application Insights](../app/pricing.md#pricing-model) e sui [prezzi nella tua area](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Come inviare metriche personalizzate

Quando si inviano le metriche personalizzate a Monitoraggio di Azure, ogni punto dati (o valore) segnalato deve includere le informazioni seguenti.

### <a name="authentication"></a>Autenticazione
Per inviare le metriche personalizzate a Monitoraggio di Azure, l'entità a cui inviare la metrica deve disporre di un token di Azure Active Directory (Azure AD) valido nell'intestazione **Bearer** della richiesta. Sono supportati alcuni modi per acquisire un token di connessione valido:
1. [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Assegna un'identità a una risorsa di Azure, ad esempio una macchina virtuale. Identità del servizio gestita (MSI) è progettata per fornire alle risorse le autorizzazioni per eseguire determinate operazioni. Ad esempio può consentire a una risorsa di generare metriche su se stessa. Una risorsa o la relativa identità del servizio gestita possono ricevere autorizzazioni di **Autore delle metriche di monitoraggio** su di un'altra risorsa. Con questa autorizzazione, l'identità del servizio gestita può generare metriche anche per le altre risorse.
2. [Azure ad entità servizio](../../active-directory/develop/app-objects-and-service-principals.md). In questo scenario, a un'applicazione o servizio Azure AD possono essere concesse autorizzazioni per generare metriche su una risorsa di Azure.
Per autenticare la richiesta, Monitoraggio di Azure convalida il token dell'applicazione usando le chiavi pubbliche di Azure AD. Il ruolo di **autore delle metriche di monitoraggio** dispone già di tale autorizzazione. È disponibile nel portale di Azure. All'entità servizio, in base alle risorse per cui genera le metriche personalizzate, può essere assegnato il ruolo di **autore delle metriche di monitoraggio** nell'ambito richiesto. Esempi possono essere una sottoscrizione, un gruppo di risorse o una risorsa specifica.

> [!TIP]  
> Quando si richiede che un token di Azure AD generi metriche personalizzate, assicurarsi che il destinatario o la risorsa per cui è richiesto il token sia `https://monitoring.azure.com/`. Assicurarsi di includere la barra finale (/).

### <a name="subject"></a>Oggetto
Questa proprietà consente di acquisire l'ID risorsa di Azure per cui viene indicata la metrica personalizzata. L'informazione viene codificata nell'URL della chiamata API eseguita. Ogni API può inviare solo i valori delle metriche per una singola risorsa di Azure.

> [!NOTE]  
> Non è possibile generare metriche personalizzate in base all'ID risorsa di un gruppo di risorse o di una sottoscrizione.


### <a name="region"></a>Area
Questa proprietà consente di acquisire l'area di Azure in cui è distribuita la risorsa per cui si generano le metriche. Le metriche devono essere inviate allo stesso endpoint regionale di Monitoraggio di Azure dell'area in cui la risorsa è distribuita. Per una macchina virtuale distribuita negli Stati Uniti occidentali, ad esempio, le metriche personalizzate devono essere inviate all'endpoint di Monitoraggio di Azure in tale area. Le informazioni sulle aree sono codificate anche nell'URL della chiamata API.

> [!NOTE]  
> Nella versione di anteprima pubblica le metriche personalizzate sono disponibili in un subset di aree di Azure. Un elenco delle aree supportate è documentato in una sezione successiva di questo articolo.
>
>

### <a name="timestamp"></a>Timestamp
Ogni punto dati inviato a Monitoraggio di Azure deve essere contrassegnato con un timestamp. Il timestamp consente di acquisire il valore DateTime in corrispondenza del quale il valore della metrica viene misurato o raccolto. Monitoraggio di Azure accetta i dati di metrica con valori di timestamp non superiori a 20 minuti precedenti e 5 minuti successivi. Il timestamp deve essere in formato ISO 8601.

### <a name="namespace"></a>Spazio dei nomi
Gli spazi dei nomi consentono di classificare o raggruppare metriche simili. Usando gli spazi dei nomi, è possibile isolare i gruppi di metriche per cui possono essere raccolte informazioni dettagliate o indicatori di prestazioni diversi. Ad esempio, si potrebbe avere uno spazio dei nomi denominato **contosomemorymetrics** che tiene traccia delle metriche di utilizzo della memoria che profilano l'app. Un altro spazio dei nomi denominato **contosoapptransaction** può tenere traccia di tutte le metriche sulle transazioni utente nell'applicazione.

### <a name="name"></a>Nome
**Nome** è il nome della metrica che viene segnalata. In genere, il nome è sufficientemente descrittivo per aiutare a identificare l'elemento misurato. Un esempio è una metrica che misura il numero di byte di memoria utilizzati su una determinata macchina virtuale. Potrebbe avere un nome di metrica come ad esempio **Byte di memoria In uso**.

### <a name="dimension-keys"></a>Chiavi di dimensione
Una dimensione è una coppia chiave o valore che consente di descrivere caratteristiche aggiuntive sulla metrica raccolta. Usando le caratteristiche aggiuntive, è possibile raccogliere altri dati sulla metrica, così da ottenere informazioni più dettagliate. Alla metrica **Byte di memoria in uso**, ad esempio, può essere associata una chiave di dimensione denominata **Processo** che acquisisce il numero di byte di memoria usati da ogni processo in una macchina virtuale. Utilizzando questa chiave, è possibile filtrare la metrica per verificare la quantità di processi specifici della memoria utilizzata o per identificare i primi cinque processi in base all'utilizzo della memoria.
Le dimensioni sono facoltative e non tutte le metriche possono avere dimensioni. Una metrica personalizzata può avere fino a 10 dimensioni.

### <a name="dimension-values"></a>Valori di dimensione
Quando si segnala un punto dati delle metriche, per ogni chiave di dimensione per la metrica indicata è presente un valore di dimensione corrispondente. Se ad esempio si vuole indicare la memoria usata da ContosoApp nella macchina virtuale, lo scenario è il seguente:

* Il nome della metrica è **Byte di memoria in uso**.
* La chiave di dimensione è **Processo**.
* Il valore di dimensione è **ContosoApp.exe**.

Quando si pubblica un valore della metrica, è possibile specificare solo un unico valore di dimensione per chiave di dimensione. Se si raccoglie la stessa metrica di uso della memoria per più processi nella macchina virtuale, è possibile indicare più valori di metrica per tale timestamp. Ogni valore della metrica specifica un valore di dimensione diverso per la chiave di dimensione **Processo**.
Le dimensioni sono facoltative e non tutte le metriche possono avere dimensioni. Se una metrica post definisce le chiavi della dimensione, i valori della dimensione corrispondenti sono obbligatori.

### <a name="metric-values"></a>Valori delle metriche
Monitoraggio di Azure archivia tutte le metriche a intervalli di granularità di un minuto. Siamo consapevoli che durante un minuto specificato, potrebbe essere necessario campionare più volte una metrica. Un esempio è l'utilizzo della CPU. Oppure potrebbe essere necessario misurarla per diversi eventi discreti. Un esempio sono le latenze delle transazioni di accesso. Per limitare il numero di valori non elaborati che è necessario generare e pagare in Monitoraggio di Azure, è possibile pre-aggregare in locale e generare i valori in locale, come indicato di seguito.

* **Min**: valore minimo valore osservato da tutti i campioni e da tutte le osservazioni durante il minuto.
* **Max**: valore massimo osservato da tutti i campioni e da tutte le osservazioni durante il minuto.
* **Somma**: somma di tutti i valori osservati da tutti i campioni e da tutte le misurazioni durante il minuto.
* **Numero**: numero di campioni/misurazioni acquisiti durante il minuto.

Se ad esempio sono presenti quattro transazioni di accesso all'app durante un determinato minuto, le latenze misurate risultanti per ognuna potrebbero essere le seguenti:

|Transazione 1|Transazione 2|Transazione 3|Transazione 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Quindi la pubblicazione delle metriche risultante in Monitoraggio di Azure sarebbe la seguente:
* Min: 4
* Max: 16
* Somma: 40
* Numero: 4

Se l'applicazione non è in grado di eseguire una pre-aggregazione in locale e deve generare ogni campione o evento discreto immediatamente al momento della raccolta, è possibile generare i valori di misura non elaborati. Ogni volta che per l'app si verifica una transazione di accesso, ad esempio, si pubblica una metrica di Monitoraggio di Azure con una singola unità di misura. Una transazione di accesso di 12 ms determinerebbe quindi la pubblicazione delle metriche seguenti:
* Min: 12
* Max: 12
* Somma: 12
* Numero: 1

Con questo processo, è possibile generare più valori per la stessa combinazione di dimensione e metrica durante un determinato minuto. Monitoraggio di Azure acquisisce quindi tutti i valori non elaborati generati per un determinato minuto e ne esegue l'aggregazione.

### <a name="sample-custom-metric-publication"></a>Pubblicazione di metrica personalizzata di esempio
Nell'esempio seguente si crea una metrica personalizzata denominata **Byte di memoria in uso** nello spazio dei nomi delle metriche **Profilo di memoria** per una macchina virtuale. Alla metrica è associata una singola dimensione denominata **Processo**. Per il timestamp specificato, vengono generati valori delle metriche per due processi diversi:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, l'estensione diagnostica, e l'agente InfluxData Telegraf sono già configurati per generare i valori delle metriche in base all'endpoint a livello di area corretto e per includere in ogni emissione tutte le proprietà indicate in precedenza.
>
>

## <a name="custom-metric-definitions"></a>Definizioni di metriche personalizzate
Non è necessario predefinire una metrica personalizzata in Monitoraggio di Azure prima che venga generata. Ogni punto dati di metrica pubblicato contiene spazio dei nomi, nome e informazioni sulle dimensioni. In tal modo la prima volta che una metrica personalizzata viene generata in Monitoraggio di Azure, viene creata automaticamente una definizione di metrica. Questa definizione di metrica è quindi individuabile in qualsiasi risorsa per cui viene generata la metrica tramite le definizioni di metrica.

> [!NOTE]  
> Monitoraggio di Azure non supporta ancora la definizione di **unità** per una metrica personalizzata.

## <a name="using-custom-metrics"></a>Uso delle metriche personalizzate
Dopo l'invio delle metriche personalizzate a Monitoraggio di Azure, è possibile esplorarle tramite il portale di Azure e sottoporle a query tramite le API REST di Monitoraggio di Azure. È inoltre possibile creare avvisi su di esse per ricevere una notifica quando vengono soddisfatte determinate condizioni.

> [!NOTE]
> È necessario essere un ruolo di lettore o collaboratore per visualizzare le metriche personalizzate.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Esplorare le metriche personalizzate tramite il portale di Azure
1.    Accedere al [portale di Azure](https://portal.azure.com).
2.    Selezionare il riquadro **Monitoraggio**.
3.    Selezionare **Metriche**.
4.    Selezionare una risorsa per cui sono state generate metriche personalizzate.
5.    Selezionare lo spazio dei nomi di metriche per la metrica personalizzata.
6.    Selezionare la metrica personalizzata.

## <a name="supported-regions"></a>Aree supportate
Nella versione di anteprima pubblica la possibilità di pubblicare metriche personalizzate è disponibile solo in un subset di aree di Azure. Tale restrizione significa che le metriche possono essere pubblicate solo per le risorse in una delle aree supportate. La seguente tabella elenca il set di aree di Azure supportate per le metriche personalizzate. Elenca inoltre gli endpoint corrispondenti nei quali le metriche per le risorse in tali aree devono essere pubblicate:

|Area di Azure |Prefisso di endpoint a livello di area|
|---|---|
| **Stati Uniti e Canada** | |
|Stati Uniti centro-occidentali | https: \/ /westcentralus.Monitoring.Azure.com |
|West US 2       | https: \/ /westus2.Monitoring.Azure.com |
|Stati Uniti centro-settentrionali | https: \/ /northcentralus.Monitoring.Azure.com
|Stati Uniti centro-meridionali| https: \/ /southcentralus.Monitoring.Azure.com |
|Stati Uniti centrali      | https: \/ /centralus.Monitoring.Azure.com |
|Canada centrale | https: \/ /canadacentral.Monitoring.Azure.com |
|Stati Uniti orientali| https: \/ /eastus.Monitoring.Azure.com |
|Stati Uniti orientali 2 | https: \/ /eastus2.Monitoring.Azure.com |
| **Europa** | |
|Europa settentrionale    | https: \/ /northeurope.Monitoring.Azure.com |
|Europa occidentale     | https: \/ /westeurope.Monitoring.Azure.com |
|Regno Unito meridionale | https: \/ /uksouth.Monitoring.Azure.com
|Francia centrale | https: \/ /francecentral.Monitoring.Azure.com |
| **Africa** | |
|Sudafrica settentrionale | https: \/ /southafricanorth.Monitoring.Azure.com |
| **Asia** | |
|India centrale | https: \/ /centralindia.Monitoring.Azure.com |
|Australia orientale | https: \/ /australiaeast.Monitoring.Azure.com |
|Giappone orientale | https: \/ /japaneast.Monitoring.Azure.com |
|Asia sud-orientale  | https: \/ /SouthEastAsia.Monitoring.Azure.com |
|Asia orientale | https: \/ /eastasia.Monitoring.Azure.com |
|Corea centrale   | https: \/ /koreacentral.Monitoring.Azure.com |

## <a name="latency-and-storage-retention"></a>Latenza e conservazione dell'archiviazione

L'aggiunta di una nuova metrica o di una nuova dimensione da aggiungere a una metrica potrebbe richiedere fino a 2-3 minuti. Una volta nel sistema, i dati dovrebbero essere visualizzati in meno di 30 secondi il 99% del tempo. 

Se si elimina una metrica o si rimuove una dimensione, la modifica può richiedere una settimana al mese per essere eliminata dal sistema.

## <a name="quotas-and-limits"></a>Quote e limiti
Monitoraggio di Azure impone le seguenti limitazioni d'uso in relazione alle metriche personalizzate:

|Category|Limite|
|---|---|
|Serie temporale attiva/sottoscrizioni/area|50.000|
|Chiavi di dimensione per metrica|10|
|Lunghezza della stringa per gli spazi dei nomi delle metriche, i nomi delle metriche e le chiavi e i valori di dimensione|256 caratteri|

Una serie temporale attiva è definita come una combinazione univoca di metrica, chiave di dimensione o valore di dimensione con valori della metrica pubblicati nelle 12 ore precedenti.

## <a name="next-steps"></a>Passaggi successivi
Usare le metriche personalizzate da servizi diversi: 
 - [Macchine virtuali](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Set di scalabilità di macchine virtuali](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Macchine virtuali di Azure (versione classica)](collect-custom-metrics-guestos-vm-classic.md)
 - [Macchina virtuale Linux con agente Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Servizi cloud classici](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
