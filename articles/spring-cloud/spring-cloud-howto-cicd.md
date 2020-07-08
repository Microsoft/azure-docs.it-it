---
title: Integrazione continua/distribuzione continua per il cloud di Azure Spring
description: Integrazione continua/distribuzione continua per il cloud di Azure Spring
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76278524"
---
# <a name="cicd-for-azure-spring-cloud"></a>Integrazione continua/distribuzione continua per il cloud di Azure Spring

Gli strumenti di integrazione continua e recapito continuo consentono agli sviluppatori di distribuire rapidamente gli aggiornamenti alle applicazioni esistenti con il minimo sforzo e rischio. Azure DevOps consente di organizzare e controllare questi processi chiave. Attualmente, Azure Spring cloud non offre un plug-in Azure DevOps specifico.  Tuttavia, è possibile integrare le applicazioni Spring cloud con DevOps usando un' [attività dell'interfaccia](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)della riga di comando di Azure. Questo articolo illustra come usare un'attività dell'interfaccia della riga di comando di Azure con Azure Spring cloud per l'integrazione con Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Creare una connessione al servizio Azure Resource Manager

Leggere [questo articolo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) per informazioni su come creare una connessione del servizio Azure Resource Manager al progetto DevOps di Azure. Assicurarsi di selezionare la stessa sottoscrizione usata per l'istanza del servizio cloud Spring di Azure.

## <a name="azure-cli-task-templates"></a>Modelli di attività CLI di Azure

### <a name="deploy-artifacts"></a>Distribuire artefatti

È possibile compilare e distribuire i progetti utilizzando una serie di `tasks` . Questo frammento di codice definisce innanzitutto un'attività Maven per compilare l'applicazione, seguita da una seconda attività che distribuisce il file JAR usando l'estensione dell'interfaccia della riga di comando di Azure Spring cloud.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Distribuisci dall'origine

È possibile eseguire la distribuzione direttamente in Azure senza un'istruzione di compilazione separata.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
