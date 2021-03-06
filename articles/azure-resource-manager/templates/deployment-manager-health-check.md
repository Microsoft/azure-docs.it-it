---
title: "Implementazione dell'integrazione dell'integrità: Gestione distribuzione di Azure"
description: Descrive come distribuire un servizio in più aree con Azure Deployment Manager. Illustra le procedure di distribuzione sicure per verificare la stabilità della distribuzione prima dell'implementazione in tutte le aree.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99536968"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzione all'integrazione dell'integrità in Azure Gestione distribuzione (anteprima pubblica)

[Gestione distribuzione di Azure](./deployment-manager-overview.md) consente di eseguire il rollout di gestione temporanea delle risorse Azure Resource Manager. Le risorse vengono distribuite in base all'area in modo ordinato. Il controllo integrità integrato del Gestione distribuzione di Azure può monitorare rollout e arrestare automaticamente rollout problematici, in modo da poter risolvere i problemi e ridurre la scalabilità dell'effetto. Questa funzionalità può ridurre la mancata disponibilità dei servizi causata da regressioni negli aggiornamenti.

## <a name="health-monitoring-providers"></a>Provider di monitoraggio dello stato

Per rendere il più semplice possibile l'integrazione dell'integrità, Microsoft collabora con alcune delle principali società di monitoraggio dell'integrità dei servizi per fornire una semplice soluzione copia/incolla per integrare i controlli integrità con le distribuzioni. Se non si usa ancora un Health Monitor, queste sono ottime soluzioni per iniziare:

| ![monitoraggio del provider di monitoraggio integrità di Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![provider di monitoraggio integrità di Azure Deployment Manager datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![provider di monitoraggio integrità di Azure Deployment Manager site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![fronte d'onda del provider di monitoraggio integrità di Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Monitoraggio di Azure, la piattaforma di osservazione completa dello stack di Microsoft per il cloud nativo & il monitoraggio ibrido e l'analisi. |Datadog, la piattaforma di monitoraggio e analisi leader per gli ambienti cloud moderni. Scopri [in che modo Datadog si integra con Azure Gestione distribuzione](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, la soluzione di monitoraggio dei servizi cloud pubblici e privati all-in-One. Scopri [in che modo Site24x7 si integra con Azure Gestione distribuzione](https://www.site24x7.com/azure/adm.html).| Fronte d'onda, la piattaforma di monitoraggio e analisi per ambienti di applicazioni a più cloud. Scopri [in che modo il fronte del fronte si integra con Azure Gestione distribuzione](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Modalità di determinazione dell'integrità del servizio

I [provider di monitoraggio dello stato](#health-monitoring-providers) offrono diversi meccanismi per il monitoraggio dei servizi e segnalano eventuali problemi di integrità del servizio. [Monitoraggio di Azure](../../azure-monitor/overview.md) è un esempio di un'offerta di questo tipo. Monitoraggio di Azure può essere usato per creare avvisi quando vengono superate determinate soglie. Ad esempio, il picco di utilizzo della memoria e della CPU supera i livelli previsti quando si distribuisce un nuovo aggiornamento al servizio. Quando viene notificato, è possibile eseguire azioni correttive.

Questi provider di integrità offrono in genere API REST in modo che lo stato dei monitoraggi del servizio possa essere esaminato a livello di codice. Le API REST possono essere riportate con un semplice segnale integro/non integro (determinato dal codice di risposta HTTP) e/o con informazioni dettagliate sui segnali ricevuti.

Il nuovo `healthCheck` passaggio di gestione distribuzione di Azure consente di dichiarare i codici http che indicano un servizio integro. Per i risultati REST complessi è possibile specificare espressioni regolari che, quando corrispondenti, indicano una risposta integro.

Il flusso per configurare i controlli di integrità di Gestione distribuzione di Azure:

1. Creare i monitoraggi di integrità tramite un provider di servizi di integrità di propria scelta.
1. Creare uno o più `healthCheck` passaggi come parte dell'implementazione del Gestione distribuzione di Azure. Completare i `healthCheck` passaggi con le seguenti informazioni:

    1. URI per l'API REST per i monitoraggi integrità (come definito dal provider del servizio integrità).
    1. Informazioni di autenticazione. Attualmente è supportata solo l'autenticazione di tipo chiave API. Per monitoraggio di Azure, il tipo di autenticazione deve essere impostato come `RolloutIdentity` l'identità gestita assegnata dall'utente usata per l'implementazione di azure Gestione distribuzione estende per monitoraggio di Azure.
    1. [Codici di stato http](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) o espressioni regolari che definiscono una risposta integro. È possibile fornire espressioni regolari, che devono corrispondere a tutti affinché la risposta venga considerata integra, oppure è possibile fornire espressioni di cui qualsiasi deve corrispondere affinché la risposta venga considerata integra. Sono supportati entrambi i metodi.

    Il codice JSON seguente è un esempio di integrazione di monitoraggio di Azure con Gestione distribuzione di Azure. Nell'esempio viene usato `RolloutIdentity` e viene stabilito un controllo di integrità in cui un'implementazione viene eseguita se non sono presenti avvisi. L'unica API di monitoraggio di Azure supportata: [Alerts – Get all](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    Il codice JSON seguente è un esempio per tutti gli altri provider di monitoraggio dello stato:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Richiamare i `healthCheck` passaggi al momento appropriato nell'implementazione di Azure Gestione distribuzione. Nell'esempio seguente `healthCheck` viene richiamato un passaggio in `postDeploymentSteps` di `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Per esaminare un esempio, vedere [esercitazione: usare il controllo integrità in Azure Gestione distribuzione](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Fasi di un controllo integrità

A questo punto Gestione distribuzione di Azure è in grado di eseguire una query per l'integrità del servizio e le fasi dell'implementazione a tale scopo. Tuttavia, Azure Gestione distribuzione consente anche di configurare in modo approfondito la tempistica di questi controlli. Un `healthCheck` passaggio viene eseguito in tre fasi sequenziali, tutte con durata configurabile:

1. Attesa

    1. Al termine di un'operazione di distribuzione, è possibile che le macchine virtuali vengano riavviate, riconfigurate in base ai nuovi dati o addirittura avviate per la prima volta. La creazione di segnali di integrità da parte del provider di monitoraggio dell'integrità è utile anche per i servizi. Durante questo processo tumultuoso potrebbe non essere opportuno verificare l'integrità del servizio perché l'aggiornamento non ha ancora raggiunto uno stato stabile. Il servizio può infatti oscillare tra Stati integri e non integri in quanto le risorse vengono stabilite.
    1. Durante la fase di attesa, l'integrità del servizio non viene monitorata. Viene utilizzato per consentire alle risorse distribuite il tempo di cottura prima di iniziare il processo di controllo dell'integrità.

1. Elastic

    1. Poiché è impossibile capire in tutti i casi il tempo necessario prima che le risorse diventino stabili, la fase elastica consente un periodo di tempo flessibile tra il momento in cui le risorse sono potenzialmente instabili e quando sono necessarie per mantenere uno stato stabile integro.
    1. Quando inizia la fase elastica, Azure Gestione distribuzione inizia a eseguire periodicamente il polling dell'endpoint REST fornito per l'integrità del servizio. L'intervallo di polling è configurabile.
    1. Se il monitoraggio dell'integrità viene restituito con segnali che indicano che il servizio non è integro, questi segnali vengono ignorati, la fase elastica continua e il polling continua.
    1. Quando il monitoraggio dell'integrità restituisce segnali che indicano che il servizio è integro, la fase elastica termina e inizia la fase HealthyState.
    1. Quindi, la durata specificata per la fase elastica è l'intervallo di tempo massimo che può essere impiegato per il polling per l'integrità del servizio prima che una risposta integra venga considerata obbligatoria.

1. HealthyState

    1. Durante la fase HealthyState, l'integrità dei servizi viene continuamente sottoposta a polling nello stesso intervallo della fase elastica.
    1. Si prevede che il servizio mantenga i segnali integri dal provider di monitoraggio dello stato per l'intera durata specificata.
    1. Se in qualsiasi momento viene rilevata una risposta non integra, Azure Gestione distribuzione arresterà l'intera implementazione e restituirà la risposta REST che trasporta i segnali del servizio non integro.
    1. Al termine della durata del HealthyState, l'oggetto `healthCheck` è completo e la distribuzione continua fino al passaggio successivo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come integrare il monitoraggio dello stato in Azure Gestione distribuzione. Passare all'articolo successivo per informazioni su come eseguire la distribuzione con Deployment Manager.

> [!div class="nextstepaction"]
> [Esercitazione: usare il controllo integrità in Azure Gestione distribuzione](./deployment-manager-tutorial-health-check.md)
