---
title: Esercitazione su Kubernetes in Azure - Preparare un'applicazione
description: In questa esercitazione sul servizio Azure Kubernetes viene illustrato come preparare e compilare con Docker Compose un'app multicontenitore che è quindi possibile distribuire nel servizio Azure Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 15bf29c676c4ca41fc2d005f3500a89ed6b9c380
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576337"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Esercitazione: Preparare un'applicazione per il servizio Azure Kubernetes

In questa esercitazione, parte uno di sette, si prepara un'applicazione multi-contenitore per l'uso in Kubernetes. Per compilare e testare in locale un'applicazione, si usano strumenti di sviluppo esistenti, ad esempio Docker Compose. Si apprenderà come:

> [!div class="checklist"]
> * Clonare l'origine di un'applicazione di esempio da GitHub
> * Creare un'immagine del contenitore dall'origine dell'applicazione di esempio
> * Testare l'applicazione multicontenitore in un ambiente Docker locale

Al termine, l'applicazione seguente viene eseguita nell'ambiente di sviluppo locale:

![Immagine del cluster Kubernetes in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

In altre esercitazioni l'immagine del contenitore verrà caricata in un'istanza di Registro Azure Container e quindi distribuita in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, ad esempio contenitori, immagini del contenitore e comandi `docker`. Per una panoramica sulle nozioni di base dei contenitori, vedere l'[introduzione a Docker][docker-get-started].

Per completare questa esercitazione, è necessario un ambiente di sviluppo Docker locale in cui sono in esecuzione i contenitori Linux. Docker offre pacchetti che consentono di configurare Docker in un sistema [Mac][docker-for-mac], [Windows][docker-for-windows] o [Linux][docker-for-linux].

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di queste esercitazioni. È pertanto consigliabile usare un ambiente di sviluppo completo di Docker.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio utilizzata in questa esercitazione è un'app di voto base. L'applicazione è costituita da un componente Web front-end e un'istanza Redis back-end. Viene creato un pacchetto del componente Web in un'immagine del contenitore personalizzata. L'istanza di Redis usa un'immagine non modificata dell'hub Docker.

Usare [git][] per clonare l'applicazione di esempio nell'ambiente di sviluppo:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Passare alla directory clonata.

```console
cd azure-voting-app-redis
```

All'interno della directory sono disponibili il codice sorgente dell'applicazione, un file Docker Compose creato in precedenza e un file manifesto Kubernetes. Questi file vengono usati in tutta la serie di esercitazioni.

## <a name="create-container-images"></a>Creare immagini del contenitore

[Docker Compose][docker-compose] può essere usato per automatizzare la compilazione di immagini del contenitore e la distribuzione di applicazioni multicontenitore.

Usare il file `docker-compose.yaml` di esempio per creare l'immagine del contenitore, scaricare l'immagine Redis e avviare l'applicazione:

```console
docker-compose up -d
```

Al termine usare il comando [docker images][docker-images] per vedere le immagini create. Sono state scaricate o create tre immagini. L'immagine *azure-vote-front* contiene l'applicazione front-end e usa come base l'immagine *nginx-flask*. L'immagine *redis* viene usata per avviare un'istanza di Redis.

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Eseguire il comando [docker ps][docker-ps] per vedere i contenitori in esecuzione:

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testare l'applicazione in locale

Per vedere l'applicazione in esecuzione, immettere `http://localhost:8080` in un Web browser locale. L'applicazione di esempio viene caricata, come illustrato nell'esempio seguente:

![Immagine del cluster Kubernetes in Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che le funzionalità dell'applicazione sono state verificate, i contenitori in esecuzione possono essere arrestati e rimossi. Non eliminare le immagini del contenitore perché nell'esercitazione successiva l'immagine *azure-vote-front* verrà caricata in un'istanza di Registro Azure Container.

Arrestare e rimuovere le istanze di contenitore e le risorse con il comando [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

Dopo che l'applicazione locale è stata rimossa, è disponibile un'immagine Docker contenente l'applicazione Azure Vote, *azure-vote-front*, da usare nell'esercitazione successiva.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata testata un'applicazione e sono state create le immagini del contenitore per l'applicazione stessa. Si è appreso come:

> [!div class="checklist"]
> * Clonare l'origine di un'applicazione di esempio da GitHub
> * Creare un'immagine del contenitore dall'origine dell'applicazione di esempio
> * Testare l'applicazione multicontenitore in un ambiente Docker locale

Passare alla prossima esercitazione per apprendere come archiviare le immagini del contenitore in Registro Azure Container.

> [!div class="nextstepaction"]
> [Eseguire il push delle immagini in Registro Azure Container][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
