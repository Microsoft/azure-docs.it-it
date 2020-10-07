---
title: Registri di contenitori gestiti
description: Introduzione al servizio Registro Azure Container, che offre registri Docker privati, gestiti e basati sul cloud.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 6951dfe3eecc8764dda9788393a7348e9267cef8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "86248867"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introduzione ai registri per contenitori Docker privati in Azure

Registro Azure Container è un servizio gestito di registri Docker privato basato sull'applicazione open source Docker Registry 2.0. Creare e gestire registri contenitori di Azure per archiviare e gestire le immagini del contenitore Docker private e gli artefatti correlati.

Usare i registri contenitori di Azure con la pipeline di sviluppo e distribuzione di contenitori esistente oppure usare Attività del Registro Azure Container per compilare immagini di contenitore in Azure. È possibile eseguire compilazioni su richiesta o automatizzare completamente le compilazioni con trigger quali i commit del codice sorgente e gli aggiornamenti delle immagini di base.

Per altre informazioni sui concetti di Docker e registro, vedere la [panoramica su Docker](https://docs.docker.com/engine/docker-overview/) e l'articolo [Registri, repository e immagini](container-registry-concepts.md).

## <a name="use-cases"></a>Casi d'uso

Effettuare il pull di immagini da un Registro Azure Container a varie destinazioni di distribuzione:

* **Sistemi di orchestrazione scalabili** che gestiscono applicazioni in contenitori fra più cluster host, quali [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* **Servizi di Azure** che supportano la creazione e l'esecuzione di applicazioni su vasta scala, inclusi il [servizio Azure Kubernetes](../aks/index.yml), il [servizio app](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) e altri.

Gli sviluppatori possono anche effettuare il push in un registro contenitori nell'ambito di un flusso di lavoro di sviluppo di contenitori, ad esempio specificando come destinazione un registro contenitori da uno strumento di distribuzione e recapito continui, come [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) o [Jenkins](https://jenkins.io/).

Configurare Attività del Registro Azure Container per ricompilare automaticamente le immagini dell'applicazione quando vengono aggiornate le immagini di base oppure automatizzare la compilazione di immagini quando il team esegue il commit di codice in un repository Git. Creare attività in più passaggi per automatizzare la compilazione, i test e l'applicazione di patch di più immagini del contenitore in parallelo nel cloud.

Azure offre strumenti, tra cui l'interfaccia della riga di comando di Azure, il portale di Azure e il supporto di API, per gestire i registri contenitori di Azure. Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.

## <a name="key-features"></a>Funzionalità principali

* **Livelli di servizio dei registri**: creare uno o più registri contenitori nella sottoscrizione di Azure. I registri sono disponibili in tre livelli: [Basic, Standard e Premium](container-registry-skus.md), ognuno dei quali supporta l'integrazione webhook, l'autenticazione del registro con Azure Active Directory e la funzionalità di eliminazione. Sfruttare l'archiviazione locale con prossimità di rete delle immagini contenitore creando un registro nella stessa località di Azure delle distribuzioni. Usare la funzionalità di [replica geografica](container-registry-geo-replication.md) dei registri Premium per scenari avanzati di replica e distribuzione di immagini del contenitore. 

* **Sicurezza e accesso**. L'accesso a un registro viene eseguito usando l'interfaccia della riga di comando di Azure oppure il comando `docker login` standard. Registro Azure Container trasferisce le immagini del contenitore tramite HTTPS e supporta TLS per proteggere le connessioni client. 

  > [!IMPORTANT]
  > A partire dal 13 gennaio 2020, Registro Azure Container richiederà l'uso di TLS 1.2 in tutte le connessioni sicure da server e applicazioni. Abilitare TLS 1.2 usando qualsiasi client Docker recente (versione 18.03.0 o successiva). Il supporto per TLS 1.0 e 1.1 verrà ritirato. 

  Per [controllare l'accesso](container-registry-authentication.md) a un registro contenitori si usa un'identità di Azure, un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) supportata da Azure Active Directory o un account amministratore specificato. Usare il controllo degli accessi in base al ruolo per assegnare agli utenti o ai sistemi autorizzazioni specifiche per un registro.

  Le funzionalità di sicurezza del livello di servizio Premium includono [attendibilità dei contenuti](container-registry-content-trust.md) per la firma dei tag di immagine e [firewall e reti virtuali (anteprima)](container-registry-vnet.md) per limitare l'accesso al registro. Il Centro sicurezza di Azure è facoltativamente integrabile con Registro Azure Container per [analizzare le immagini](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) ogni volta che viene eseguito il push di un'immagine in un registro.

* **Immagini e artefatti supportati**. Le immagini sono raggruppate in un repository e ognuna rappresenta uno snapshot di sola lettura di un contenitore compatibile con Docker. I registri contenitori di Azure possono includere immagini sia Windows che Linux. L'utente controlla i nomi delle immagini per tutte le distribuzioni di contenitori. Usare i [comandi di Docker](https://docs.docker.com/engine/reference/commandline/) standard per effettuare il pull e il push di immagini da e verso un repository. Oltre alle immagini del contenitore Docker, Registro Azure Container archivia i [formati del contenuto correlato](container-registry-image-formats.md), ad esempio [grafici Helm](container-registry-helm-repos.md) e immagini incorporate nella [Specifica di formato di immagine OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Compilazione di immagini automatizzata**. Usare [Attività del Registro Azure Container](container-registry-tasks-overview.md) per semplificare la compilazione, il test, il push e la distribuzione di immagini in Azure. Ad esempio, usare Attività del Registro Azure Container per estendere il ciclo interno di sviluppo nel cloud eseguendo l'offload delle operazioni di `docker build` in Azure. Configurare le attività di compilazione per automatizzare la pipeline di applicazione delle patch al sistema operativo e al framework del contenitore e compilare automaticamente le immagini quando il team esegue il commit del codice nel controllo dell'origine.

  Le [attività in più passi](container-registry-tasks-overview.md#multi-step-tasks) consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi dell'attività definiscono singole operazioni di compilazione e push dell'immagine contenitore. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un registro contenitori con il portale di Azure](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure)
* [Automatizzare le compilazioni e la manutenzione dei contenitori con Attività del Registro Azure Container](container-registry-tasks-overview.md)
