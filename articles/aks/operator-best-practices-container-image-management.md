---
title: Procedure consigliate per l'operatore - Gestione delle immagini del contenitore nel servizio Azure Kubernetes
description: Procedure consigliate per l'operatore del cluster per la gestione e la protezione delle immagini del contenitore nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105120"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore nel servizio Azure Kubernetes

La sicurezza delle immagini del contenitore e del contenitore è una priorità importante durante lo sviluppo e l'esecuzione di applicazioni in Azure Kubernetes Service (AKS). I contenitori con immagini di base obsolete o runtime di applicazione senza patch introducono un rischio per la sicurezza e un possibile vettore di attacco. 

Riduci al minimo i rischi grazie all'integrazione e all'esecuzione di strumenti di analisi e correzione nei contenitori in fase di compilazione e Runtime. Prima di intercettare la vulnerabilità o un'immagine di base obsoleta, maggiore è la protezione del cluster. 

In questo articolo, *"containers"* significa:
* Immagini del contenitore archiviate in un registro contenitori.
* Contenitori in esecuzione.

Questo articolo illustra in particolare come proteggere i contenitori nel servizio Azure Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Analizza e monitora e aggiorna le vulnerabilità delle immagini.
> * Attiva e Ridistribuisci automaticamente le immagini del contenitore quando viene aggiornata un'immagine di base.

È anche possibile leggere le procedure consigliate per la [sicurezza dei cluster][best-practices-cluster-security] e la [sicurezza dei pod][best-practices-pod-security].

È anche possibile usare la [sicurezza del contenitore nel centro sicurezza][security-center-containers] per analizzare i contenitori per individuare eventuali vulnerabilità. L' [integrazione di container Registry di Azure][security-center-acr] con il Centro sicurezza consente di proteggere le immagini e il registro di sistema dalle vulnerabilità.

## <a name="secure-the-images-and-run-time"></a>Proteggere le immagini e il runtime

> **Indicazioni sulle procedure consigliate** 
>
> Analizza le immagini del contenitore per individuare eventuali vulnerabilità. Distribuisci solo immagini convalidate. Aggiornare regolarmente le immagini di base e il runtime dell'applicazione. Ridistribuire i carichi di lavoro nel cluster AKS.

Quando si adottano carichi di lavoro basati su contenitori, è opportuno verificare la sicurezza delle immagini e del runtime usati per creare applicazioni personalizzate. In che modo è possibile evitare di introdurre vulnerabilità di sicurezza nelle distribuzioni? 
* Includere nel flusso di lavoro di distribuzione un processo per analizzare le immagini del contenitore usando strumenti come [Twistlock][twistlock] o [Aqua][aqua].
* Consente di distribuire solo le immagini verificate.

![Analizzare e correggere le immagini del contenitore, convalidarle e distribuirle](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Ad esempio, è possibile usare una pipeline di integrazione continua e distribuzione continua (CI/CD) per automatizzare le analisi, la verifica e le distribuzioni di immagini. Registro Azure Container include queste funzionalità di analisi delle vulnerabilità.

## <a name="automatically-build-new-images-on-base-image-update"></a>Compilare automaticamente nuove immagini all'aggiornamento dell'immagine di base

> **Indicazioni sulle procedure consigliate** 
>
> Quando si usano immagini di base per le immagini dell'applicazione, usare l'automazione per creare nuove immagini quando viene aggiornata l'immagine di base. Poiché le immagini di base aggiornate includono generalmente correzioni per la sicurezza, aggiornare tutte le immagini del contenitore di applicazioni downstream.

Ogni volta che viene aggiornata un'immagine di base, è necessario aggiornare anche le immagini del contenitore downstream. Integrare questo processo di compilazione in pipeline di convalida e distribuzione, ad esempio [Azure Pipelines][azure-pipelines] o Jenkins. Queste pipeline assicurano che le applicazioni continuino a essere eseguite sulle immagini basate su aggiornate. Una volta convalidate le immagini del contenitore dell'applicazione, è possibile aggiornare le distribuzioni del servizio Azure Kubernetes in modo che eseguano le immagini più recenti e sicure.

Le immagini del contenitore possono essere aggiornate automaticamente anche da Attività del Registro Azure Container quando l'immagine di base viene aggiornata. Con questa funzionalità, è possibile compilare alcune immagini di base e mantenerle aggiornate con correzioni di bug e sicurezza.

Per altre informazioni sugli aggiornamenti delle immagini di base, vedere [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Tasks][acr-base-image-update].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere i contenitori. Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Automatizzare la compilazione di immagini in caso di aggiornamento dell'immagine di base con ACR Tasks][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md