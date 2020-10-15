---
title: 'Distribuzione sicura tra aree: Azure Deployment Manager'
description: Informazioni su come distribuire un servizio in molte aree con Deployment Manager di Azure e sulle procedure di distribuzione sicure.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8b950fdc36fe3fbea1ce9436bdd7f7372c64c055
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333206"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Abilitare procedure di distribuzione sicure con Deployment Manager di Azure (anteprima pubblica)

Per distribuire il servizio in più aree e verificare che venga eseguito come previsto in ogni area, è possibile usare Azure Deployment Manager per coordinare un'implementazione a fasi del servizio. Proprio come per qualsiasi distribuzione di Azure, si definiscono le risorse per il servizio nei [modelli di Resource Manager](template-syntax.md). Dopo aver creato i modelli, si usa Deployment Manager per descrivere la topologia per il servizio e come deve essere implementato.

Deployment Manager è una funzionalità di Resource Manager. Espande le funzionalità durante la distribuzione. Usare Deployment Manager quando è necessario distribuire un servizio complesso in più aree. Con l'implementazione temporanea del servizio, è possibile individuare potenziali problemi prima che il servizio sia distribuito in tutte le aree. Se le particolari precauzioni di un'implementazione a fasi non sono necessarie, usare le [opzioni di distribuzione](deploy-portal.md) standard per Resource Manager. Deployment Manager si integra perfettamente con tutti gli strumenti di terze parti esistenti che supportano le distribuzioni di Resource Manager, ad esempio le offerte di integrazione continua e recapito continuo (CI/CD).

Azure Deployment Manager è in versione di anteprima. Aiutaci a migliorare la funzionalità fornendo [commenti e suggerimenti](https://aka.ms/admfeedback).

Per usare Deployment Manager, è necessario creare quattro file:

* Modello di topologia
* Modello di implementazione
* File dei parametri per la topologia
* File dei parametri per l'implementazione

Si distribuisce il modello di topologia prima di distribuire il modello di implementazione.

Risorse aggiuntive:

- Il [riferimento all'API REST di Azure Deployment Manager](/rest/api/deploymentmanager/).
- [Esercitazione: usare Deployment Manager di Azure con modelli di gestione risorse](./deployment-manager-tutorial.md).
- [Esercitazione: Usare il controllo integrità in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Usare un esempio di Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identità e accesso

Con Deployment Manager, un'[identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/overview.md) esegue le azioni di distribuzione. Si crea questa identità prima di avviare la distribuzione. Deve avere accesso alla sottoscrizione in cui si distribuisce il servizio e autorizzazioni sufficienti per completare la distribuzione. Per informazioni sulle azioni concesse tramite i ruoli, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

L'identità deve risiedere nella stessa posizione dell'implementazione.

## <a name="topology-template"></a>Modello di topologia

Il modello di topologia descrive le risorse di Azure che costituiscono il servizio e la posizione in cui devono essere distribuite. L'immagine seguente mostra la topologia per un servizio di esempio:

![Gerarchia dalla topologia del servizio ai servizi alle unità di servizio](./media/deployment-manager-overview/service-topology.png)

Il modello di topologia include le risorse seguenti:

* Origine dell'artefatto: posizione in cui vengono archiviati i modelli di Resource Manager e i parametri
* Topologia del servizio: punta all'origine dell'artefatto
  * Servizi: specificano la località e l'ID sottoscrizione di Azure
    * Unità di servizio: specificano gruppo di risorse, modalità di distribuzione e percorso del modello e del file di parametri

Per comprendere come funziona ogni livello, è utile osservare i valori specificati.

![Valori per ogni livello](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Origine dell'artefatto per i modelli

Nel modello di topologia si crea un'origine artefatto che contiene i modelli e i file di parametri. L'origine dell'artefatto consente di eseguire il pull dei file per la distribuzione. Più avanti in questo articolo verrà illustrata un'altra origine artefatto per i file binari.

L'esempio seguente illustra il formato generale dell'origine dell'artefatto.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

Per altre informazioni, vedere [artifactSources template reference](/azure/templates/Microsoft.DeploymentManager/artifactSources) (Informazioni di riferimento sul modello artifactSources).

### <a name="service-topology"></a>Topologia del servizio

L'esempio seguente illustra il formato generale della risorsa topologia del servizio. Si specifica l'ID risorsa dell'origine dell'artefatto che contiene i modelli e i file di parametri. La topologia del servizio include tutte le risorse del servizio. Per assicurarsi che l'origine dell'artefatto sia disponibile, la topologia del servizio dipende da essa.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Per altre informazioni, vedere [serviceTopologies template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies) (Informazioni di riferimento sul modello serviceTopologies).

### <a name="services"></a>Servizi

L'esempio seguente illustra il formato generale della risorsa servizi. In ogni servizio si specificano l'ID sottoscrizione di Azure e la località da usare per distribuire il servizio. Per la distribuzione in più aree, si definisce un servizio per ogni area. Il servizio dipende dalla topologia del servizio.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

Per altre informazioni, vedere [services template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services) (Informazioni di riferimento sul modello services).

### <a name="service-units"></a>Unità di servizio

L'esempio seguente illustra il formato generale della risorsa unità di servizio. In ogni unità di servizio si specificano il gruppo di risorse, la [modalità di distribuzione](deployment-modes.md) da usare per la distribuzione e il percorso del modello e del file di parametri. Se si specifica un percorso relativo per il modello e i parametri, il percorso completo viene costruito dalla cartella radice dell'origine degli artefatti. È possibile specificare un percorso assoluto per il modello e i parametri, ma in questo modo non è possibile assegnare facilmente un numero alle versioni. L'unità di servizio dipende dal servizio.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Ogni modello deve includere le risorse correlate che si vuole distribuire in un unico passaggio. Un'unità di servizio, ad esempio, potrebbe avere un modello che distribuisce tutte le risorse per il front-end del servizio.

Per altre informazioni, vedere [serviceUnits template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits) (Informazioni di riferimento sul modello serviceUnits).

## <a name="rollout-template"></a>Modello di implementazione

Il modello di implementazione descrive i passaggi da eseguire durante la distribuzione del servizio. Specificare la topologia del servizio da usare e definire l'ordine per la distribuzione delle unità di servizio. Include un'origine artefatto per l'archiviazione dei file binari per la distribuzione. Nel modello di implementazione si definisce la gerarchia seguente:

* Origine artefatto
* Passaggio
* Implementazione
  * Gruppi di passaggi
    * Operazioni di distribuzione

L'immagine seguente mostra la gerarchia del modello di implementazione:

![Gerarchia dall'implementazione ai passaggi](./media/deployment-manager-overview/Rollout.png)

Ogni implementazione può avere più gruppi di passaggi. Ogni gruppo di passaggi ha un'operazione di distribuzione che fa riferimento a un'unità di servizio nella topologia del servizio.

### <a name="artifact-source-for-binaries"></a>Origine dell'artefatto per i file binari

Nel modello di implementazione si crea un'origine artefatto per i file binari da distribuire nel servizio. Questa origine artefatto è simile all'[origine artefatto per i modelli](#artifact-source-for-templates), con la differenza che contiene gli script, le pagine Web, il codice compilato o altri file necessari per il servizio.

### <a name="steps"></a>Passaggi

È possibile definire un passaggio da eseguire prima o dopo l'operazione di distribuzione. Attualmente sono disponibili solo il passaggio `wait` e il passaggio ' healthCheck '.

Il passaggio wait sospende la distribuzione prima di continuare. Consente di verificare che il servizio sia in esecuzione come previsto prima di distribuire l'unità di servizio successiva. L'esempio seguente illustra il formato generale di un passaggio wait.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

La proprietà duration usa lo [standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). L'esempio precedente specifica un'attesa di un minuto.

Per altre informazioni sul passaggio del controllo integrità, vedere [Introduzione all'integrazione dell'integrità in azure Deployment Manager](./deployment-manager-health-check.md) e [esercitazione: usare il controllo integrità in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Per altre informazioni, vedere [steps template reference](/azure/templates/Microsoft.DeploymentManager/steps) (Informazioni di riferimento sul modello steps).

### <a name="rollouts"></a>Implementazioni

Per assicurarsi che l'origine dell'artefatto sia disponibile, l'implementazione dipende da essa. L'implementazione definisce i gruppi di passaggi per ogni unità di servizio distribuita. È possibile definire le azioni da eseguire prima o dopo la distribuzione. È ad esempio possibile specificare che la distribuzione rimanga in attesa dopo la distribuzione dell'unità di servizio. È possibile definire l'ordine dei gruppi di passaggi.

L'oggetto identity specifica l'[identità gestita assegnata dall'utente](#identity-and-access) che esegue le azioni di distribuzione.

L'esempio seguente illustra il formato generale dell'implementazione.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

Per altre informazioni, vedere [rollouts template reference](/azure/templates/Microsoft.DeploymentManager/rollouts) (Informazioni di riferimento sul modello rollouts).

## <a name="parameter-file"></a>File di parametri

Creare due file di parametri. Un file di parametri viene usato quando si distribuisce la topologia del servizio e l'altro viene usato per la distribuzione dell'implementazione. È necessario verificare che alcuni valori specifici siano uguali in entrambi i file di parametri.

## <a name="containerroot-variable"></a>Variabile containerRoot

Con le distribuzioni con versione, il percorso degli artefatti viene modificato con ogni nuova versione. La prima volta che si esegue una distribuzione il percorso potrebbe essere `https://<base-uri-blob-container>/binaries/1.0.0.0`. La seconda volta potrebbe essere `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager consente di ottenere più facilmente il percorso radice corretto per la distribuzione corrente usando la variabile `$containerRoot`. Questo valore viene modificato con ogni versione e non è noto prima della distribuzione.

Usare la variabile `$containerRoot` nel file di parametri per consentire al modello di distribuire le risorse di Azure. In fase di distribuzione, questa variabile viene sostituita con i valori effettivi dell'implementazione.

Durante l'implementazione, ad esempio, si crea un'origine per gli artefatti binari.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Si notino le proprietà `artifactRoot` e `sasUri`. La radice dell'artefatto potrebbe essere impostata su un valore come `binaries/1.0.0.0`. L'URI di firma di accesso condiviso è l'URI del contenitore di archiviazione con un token di firma di accesso condiviso per l'accesso. Deployment Manager crea automaticamente il valore della variabile `$containerRoot`. Combina tali valori nel formato `<container>/<artifactRoot>`.

Il modello e file di parametri devono conoscere il percorso corretto per ottenere i file binari con versione. Per distribuire file per un'app Web, ad esempio, creare il file di parametri seguente con la variabile $containerRoot. È necessario usare due barre rovesciate (`\\`) per il percorso perché la prima è un carattere di escape.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Usare quindi tale parametro nel modello:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

È possibile gestire distribuzioni con versione creando nuove cartelle e passando tale radice durante l'implementazione. Il percorso passa attraverso il modello che distribuisce le risorse.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato Deployment Manager. Passare all'articolo successivo per informazioni su come eseguire la distribuzione con Deployment Manager.

> [!div class="nextstepaction"]
> [Esercitazione: Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md)
>
> [Guida introduttiva: provare Deployment Manager di Azure in pochi minuti](https://github.com/Azure-Samples/adm-quickstart)
