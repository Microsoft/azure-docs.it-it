---
title: (DEPRECATO) Esercitazione sul servizio Azure Container - Preparare l'app
description: Esercitazione sul servizio Azure Container - App di preparazione
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b67e3008bc2ad681e222af6b9edc4a08875732e4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76275435"
---
# <a name="deprecated-create-container-images-to-be-used-with-azure-container-service"></a>(DEPRECATO) Creare immagini del contenitore da usare con il servizio Azure Container

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Esercitazione: Preparare un'applicazione per il servizio Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In questa esercitazione, parte uno di sette, si prepara un'applicazione multi-contenitore per l'uso in Kubernetes. I passaggi completati comprendono:  

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub  
> * Creazione di un'immagine del contenitore dall'origine applicazione
> * Test dell'applicazione in un ambiente Docker locale

Al termine, l'applicazione seguente sarà accessibile nell'ambiente di sviluppo locale.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Nelle esercitazioni successive, l'immagine del contenitore verrà caricata in un'istanza di Registro Azure Container e quindi eseguita in un cluster Kubernetes ospitato in Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali. Se necessario, vedere [Introduzione a Docker]( https://docs.docker.com/get-started/) per una panoramica sui concetti fondamentali relativi al contenitore. 

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È pertanto consigliabile usare un ambiente di sviluppo completo di Docker.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio utilizzata in questa esercitazione è un'app di voto base. L'applicazione è costituita da un componente Web front-end e un'istanza Redis back-end. Viene creato un pacchetto del componente Web in un'immagine del contenitore personalizzata. L'istanza di Redis usa un'immagine non modificata dell'hub Docker.  

Usare git per scaricare una copia dell'applicazione per l'ambiente di sviluppo.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Cambiare directory in modo da usare la directory clonata.

```
cd azure-voting-app-redis
```

All'interno della directory sono disponibili il codice sorgente dell'applicazione, un file Docker Compose creato in precedenza e un file manifesto Kubernetes. Questi file vengono usati in tutta la serie di esercitazioni. 

## <a name="create-container-images"></a>Creare immagini del contenitore

[Docker Compose](https://docs.docker.com/compose/) può essere usato per automatizzare la creazione di immagini del contenitore e la distribuzione di applicazioni multi-contenitore.

Eseguire il file `docker-compose.yml` per creare l'immagine del contenitore, scaricare l'immagine Redis e avviare l'applicazione.

```bash
docker-compose up -d
```

Al termine usare il comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) per vedere le immagini create.

```bash
docker images
```

Si noti che sono state scaricate o create tre immagini. L'immagine `azure-vote-front` contiene l'applicazione e usa l'immagine `nginx-flask` come base. L'immagine `redis` viene usata per avviare un'istanza di Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Eseguire il comando [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) per vedere i contenitori in esecuzione.

```bash
docker ps
```

Output:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testare l'applicazione in locale

Passare a `http://localhost:8080` per vedere l'applicazione in esecuzione.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che le funzionalità dell'applicazione sono state verificate, i contenitori in esecuzione possono essere arrestati e rimossi. Non eliminare le immagini del contenitore. L'immagine `azure-vote-front` verrà caricata in un'istanza di Registro Azure Container nella prossima esercitazione.

Eseguire questo comando per arrestare i contenitori in esecuzione.

```bash
docker-compose stop
```

Eliminare le risorse e i contenitori arrestati con il comando seguente.

```bash
docker-compose down
```

Al termine si avrà un'immagine del contenitore con l'applicazione Azure Vote.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata testata un'applicazione e sono state create le immagini del contenitore per l'applicazione stessa. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub  
> * Creazione di un'immagine del contenitore dall'origine applicazione
> * Test dell'applicazione in un ambiente Docker locale

Passare alla prossima esercitazione per apprendere informazioni sull'archiviazione delle immagini del contenitore in Registro Azure Container.

> [!div class="nextstepaction"]
> [Eseguire il push delle immagini in Registro Azure Container](./container-service-tutorial-kubernetes-prepare-acr.md)
