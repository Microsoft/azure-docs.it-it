---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486102"
---
Le voci del documento `deploymentconfig.json` vengono mappate ai parametri di [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro per il metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per il servizio Azure Kubernetes, il valore deve essere `aks`. |
| `autoScaler` | ND | Contiene gli elementi di configurazione per la scalabilità automatica. Vedere la tabella sulla scalabilità automatica. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Indica se abilitare o meno la scalabilità automatica per il servizio Web. Se `numReplicas` = `0`, `True`; in caso contrario, `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Il numero minimo di contenitori da usare per la scalabilità automatica di questo servizio Web. Impostazione predefinita: `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Il numero massimo di contenitori da usare per la scalabilità automatica di questo servizio Web. Impostazione predefinita: `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | La frequenza dei tentativi di dimensionamento automatico di questo servizio Web. Impostazione predefinita: `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | L'utilizzo di destinazione (in percentuale su 100) che è necessario provare a mantenere per la scalabilità automatica di questo servizio Web. Impostazione predefinita: `70`. |
| `dataCollection` | ND | Contiene gli elementi di configurazione per la raccolta di dati. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Indica se abilitare o meno la raccolta di dati del modello per il servizio Web. Impostazione predefinita: `False`. |
| `authEnabled` | `auth_enabled` | Indica se abilitare o meno l'autenticazione della chiave per il servizio Web. `tokenAuthEnabled` e `authEnabled` non possono essere entrambi `True`. Impostazione predefinita: `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Indica se abilitare o meno l'autenticazione del token per il servizio Web. `tokenAuthEnabled` e `authEnabled` non possono essere entrambi `True`. Impostazione predefinita: `False`. |
| `containerResourceRequirements` | ND | Contenitore per le entità CPU e memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Il numero di core di CPU da allocare per questo servizio Web. Il valore predefinito è `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | La quantità di memoria (in GB) da allocare per questo servizio Web. Il valore predefinito è `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Indica se abilitare o meno la registrazione di Application Insights per il servizio Web. Impostazione predefinita: `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Il timeout da applicare per l'assegnazione del punteggio alle chiamate al servizio Web. Impostazione predefinita: `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Il numero massimo di richieste simultanee per nodo per questo servizio Web. Impostazione predefinita: `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Il tempo massimo di permanenza di una richiesta nella coda (in millisecondi) prima che venga restituito un errore 503. Impostazione predefinita: `500`. |
| `numReplicas` | `num_replicas` | Il numero di contenitori da allocare per questo servizio Web. Nessun valore predefinito. Se questo parametro non è impostato, la scalabilità automatica è abilitata per impostazione predefinita. |
| `keys` | ND | Contiene gli elementi di configurazione per le chiavi. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Una chiave di autenticazione primaria da usare per questo servizio Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Una chiave di autenticazione secondaria da usare per questo servizio Web |
| `gpuCores` | `gpu_cores` | Il numero di core di GPU (replica per contenitore) da allocare per questo servizio Web. Il valore predefinito è 1. Supporta solo valori numerici interi. |
| `livenessProbeRequirements` | ND | Contiene gli elementi di configurazione per i requisiti del probe di attività. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | La frequenza (in secondi) con cui eseguire il probe di attività. Il valore predefinito è 10 secondi. Il valore minimo è 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Il numero di secondi dopo l'avvio del contenitore prima che vengano avviati i probe di attività. Il valore predefinito è 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Il numero di secondi trascorsi i quali si verifica il timeout del probe di attività. Il valore predefinito è 2 secondi. Il valore minimo è 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Il numero minimo di successi consecutivi perché il probe di attività venga considerato riuscito dopo un errore. Assume il valore predefinito 1. Il valore minimo è 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando un pod viene avviato e il probe di attività non riesce, Kubernetes continuerà a provare per il numero di volte indicato in failureThreshold prima di rinunciare. Il valore predefinito è 3. Il valore minimo è 1. |
| `namespace` | `namespace` | Lo spazio dei nomi di Kubernetes in cui è distribuito il servizio Web. Fino a 63 caratteri alfanumerici minuscoli ('a'-'z', '0'-'9') e trattini ('-'). Il primo e l'ultimo carattere non possono essere trattini. |

Il codice JSON seguente è un esempio di configurazione della distribuzione da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
