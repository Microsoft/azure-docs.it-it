---
title: Trasmettere i log dell'app Azure Spring Cloud in tempo reale
description: Come usare lo streaming dei log per visualizzare immediatamente i registri applicazioni
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878005"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Trasmettere i log dell'app Azure Spring Cloud in tempo reale

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Azure Spring cloud consente lo streaming di log nell'interfaccia della riga di comando di Azure per ottenere i log della console applicazione in tempo reale per la risoluzione dei problemi È anche possibile [analizzare i log e le metriche con le impostazioni di diagnostica](./diagnostic-services.md).

## <a name="prerequisites"></a>Prerequisiti

* Installare l' [estensione dell'interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure per Spring cloud, versione minima 0.2.0.
* Un'istanza del **cloud Spring di Azure** con un'applicazione in esecuzione, ad esempio [Spring cloud app](./spring-cloud-quickstart.md).

> [!NOTE]
>  L'estensione dell'interfaccia della riga di comando ASC viene aggiornata dalla versione 0.2.0 a 0.2.1. Questa modifica influiscono sulla sintassi del comando per lo streaming dei log: `az spring-cloud app log tail` , che viene sostituito da: `az spring-cloud app logs` . Il comando: `az spring-cloud app log tail` verrà deprecato in una versione futura. Se è stata usata la versione 0.2.0, è possibile eseguire l'aggiornamento a 0.2.1. Rimuovere prima la versione precedente con il comando: `az extension remove -n spring-cloud` .  Quindi, installare 0.2.1 tramite il comando: `az extension add -n spring-cloud` .

## <a name="use-cli-to-tail-logs"></a>Usare l'interfaccia della riga di comando per la coda

Per evitare di specificare ripetutamente il nome del gruppo di risorse e dell'istanza del servizio, impostare il nome del gruppo di risorse e il nome del cluster predefiniti.
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Negli esempi seguenti, il gruppo di risorse e il nome del servizio verranno omessi nei comandi.

### <a name="tail-log-for-app-with-single-instance"></a>Log della parte finale per l'app con una singola istanza
Se un'app denominata auth-Service ha solo un'istanza, è possibile visualizzare il log dell'istanza dell'app con il comando seguente:
```azurecli
az spring-cloud app logs -n auth-service
```
Questo restituirà i log:
```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Log della parte finale per l'app con più istanze
Se per l'app sono presenti più istanze denominate `auth-service` , è possibile visualizzare il log dell'istanza usando l' `-i/--instance` opzione. 

Per prima cosa, è possibile ottenere i nomi delle istanze dell'app con il comando seguente.

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Con risultati:

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Quindi, è possibile eseguire lo streaming dei log di un'istanza dell'app con l'opzione option `-i/--instance` :

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

È anche possibile ottenere i dettagli delle istanze dell'app dal portale di Azure.  Dopo aver selezionato le **app** nel riquadro di spostamento a sinistra del servizio cloud di Azure Spring, selezionare **istanze dell'app**.

### <a name="continuously-stream-new-logs"></a>Flusso continuo di nuovi log
Per impostazione predefinita, `az spring-cloud ap log tail` stampa solo i log esistenti trasmessi alla console dell'app e quindi viene chiuso. Se si vuole eseguire lo streaming di nuovi log, aggiungere-f (--follow):  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
Per controllare tutte le opzioni di registrazione supportate:
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Passaggi successivi
* [Avvio rapido: Monitoraggio di app Azure Spring Cloud con log, metriche e traccia](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Analizzare i log e le metriche con le impostazioni di diagnostica](./diagnostic-services.md)

