---
title: Installare un'app in un cluster autonomo
description: In questa esercitazione si apprenderà come installare un'applicazione in un cluster autonomo di Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485361"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Esercitazione: Distribuire un'applicazione in un cluster autonomo di Service Fabric

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS e viene distribuita un'applicazione al suo interno.

Questa è la terza di una serie di esercitazioni.  I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" di Service Fabric. Questa esercitazione illustra come creare un'infrastruttura AWS necessaria per ospitare questo cluster autonomo.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Scaricare l'app di esempio
> * Distribuirla nel cluster

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare questa esercitazione:

* [Installare Visual Studio 2019](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio

In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Distribuire l'app nel cluster di Service Fabric

Ora che l'applicazione è stata scaricata, è possibile distribuirla in un cluster direttamente da Visual Studio.

1. Aprire Visual Studio.

2. Selezionare **File** > **Apri**

3. Passare alla cartella in cui è stato clonato il repository git e selezionare Voting.sln

4. Fare clic con il pulsante destro del mouse sul progetto di applicazione `Voting` in Esplora soluzioni e scegliere **Pubblica**

5. Selezionare l'elenco a discesa per **Endpoint connessione** e immettere il nome DNS pubblico di uno dei nodi del cluster.  Ad esempio: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. In Azure un nome di dominio completo (FQDN) non viene fornito automaticamente, ma può facilmente [essere impostato nella pagina della panoramica delle macchine virtuali.](../virtual-machines/create-fqdn.md)

6. Aprire il browser preferito e digitare l'indirizzo del cluster (l'endpoint della connessione; l'app viene distribuita nella porta 8080, ad esempio ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Risposta API dal cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come distribuire un'applicazione nel cluster:

> [!div class="checklist"]
> * Scaricare l'app di esempio
> * Distribuirla nel cluster

Per pulire il cluster, passare alla quarta parte della serie.

> [!div class="nextstepaction"]
> [Pulire le risorse](service-fabric-tutorial-standalone-clean-up.md)