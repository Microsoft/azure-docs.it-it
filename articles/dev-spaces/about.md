---
title: Che cos'è Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Informazioni su come Azure Dev Spaces può offrire un'esperienza di sviluppo Kubernetes rapida e iterativa per i team nei cluster del servizio Azure Kubernetes
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori, kubectl, k8s
manager: gwallace
ms.openlocfilehash: fd6f52c71c81f253f3f40f05408e45b6a6c0dbce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199476"
---
# <a name="what-is-azure-dev-spaces"></a>Che cos'è Azure Dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces offre un'esperienza di sviluppo Kubernetes rapida e iterativa per i team nei cluster del servizio Azure Kubernetes (AKS). Azure Dev Spaces consente anche di testare tutti i componenti dell'applicazione ed eseguirne il debug nel servizio Azure Kubernetes con una configurazione minima del computer di sviluppo, senza replicare o simulare le dipendenze.

![Il diagramma mostra due versioni di un'applicazione sviluppate indipendentemente. Le due versioni vengono quindi combinate in un'unica versione in un ambiente di sviluppo Azure Dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Come Azure Dev Spaces semplifica lo sviluppo in Kubernetes

Con Azure Dev Spaces i team possono concentrarsi sullo sviluppo e sulla rapida iterazione della propria applicazione di microservizi, grazie alla possibilità di lavorare direttamente con l'intera architettura o applicazione di microservizi nel servizio Azure Kubernetes. Azure Dev Spaces consente anche di aggiornare parti dell'architettura di microservizi in modo indipendente e in completo isolamento, senza alcun impatto sul resto del cluster del servizio Azure Kubernetes o su altri sviluppatori. Azure Dev Spaces è riservato alle attività di sviluppo e test in ambienti di sviluppo e test di livello inferiore e non è adatto per i cluster del servizio Azure Kubernetes in ambiente di produzione.

Dato che i team possono lavorare con l'intera applicazione e collaborare direttamente nel servizio Azure Kubernetes, Azure Dev Spaces:

* Riduce al minimo la configurazione del computer locale
* Riduce i tempi di formazione per i nuovi sviluppatori nel team
* Aumenta la velocità di un team grazie a una più rapida iterazione
* Riduce il numero di ambienti di sviluppo e integrazione ridondanti, dato che i membri del team possono condividere un cluster
* Elimina la necessità di replicare o simulare le dipendenze
* Migliora la collaborazione fra i team di sviluppo, nonché con i team con cui lavorano, come i team DevOps

Azure Dev Spaces fornisce strumenti per generare asset di Docker e Kubernetes per i progetti. Questi strumenti consentono di aggiungere con facilità applicazioni nuove ed esistenti sia a uno spazio di sviluppo che ad altri cluster del servizio Azure Kubernetes.

Per altre informazioni sul funzionamento di Azure Dev Spaces, vedere [Funzionamento e configurazione di Azure Dev Spaces][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Aree e configurazioni supportate

Azure Dev Spaces è supportato solo dai cluster del servizio Azure Kubernetes [alcune aree][supported-regions]. Azure Dev Spaces supporta l'uso dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o di [Visual Studio Code](https://code.visualstudio.com/download) con l'[estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installata in Linux, macOS oppure Windows 8 o versione successiva per compilare ed eseguire applicazioni nel servizio Azure Kubernetes. Supporta anche l'uso di [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installato in Windows con il carico di lavoro Azure Development.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
