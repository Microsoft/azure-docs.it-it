---
title: Pulire un cluster autonomo
description: Questa esercitazione illustra come pulire le risorse di AWS o di Azure in un cluster autonomo di Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639021"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Esercitazione: Pulire il cluster autonomo

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS o Azure e viene installata un'applicazione al suo interno.

Questa è la quarta di una serie di esercitazioni. Questa parte dell'esercitazione illustra come pulire le risorse AWS o Azure create per ospitare il cluster di Service Fabric.

Nella quarta parte della serie si apprenderà come:

> [!div class="checklist"]
> * Pulire un cluster di Service Fabric
> * Pulire le risorse di AWS o Azure

## <a name="clean-up-service-fabric-cluster"></a>Pulire un cluster di Service Fabric

1. Eseguire RDP nella macchina virtuale usata per installare Service Fabric.
2. Aprire PowerShell.
3. Sostituire la directory con la cartella estratta nella seconda esercitazione.
4. Per rimuovere le istanze di Service Fabric, eseguire il comando seguente:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Immettere `Y` quando richiesto; in caso di esito positivo l'output avrà un aspetto simile al seguente, con gli indirizzi IP sostituiti in:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>Pulire le risorse AWS

1. Effettuare l'accesso all'account AWS.
2. Accedere alla Console EC2.
3. Selezionare i tre nodi creati nella prima parte dell'esercitazione.
4. Fare clic su **Azioni** > **Stato istanza** > **Termina**.

## <a name="clean-up-azure-resources"></a>Pulire le risorse di Azure

1. Accedere al portale di Azure.
2. Andare alla sezione **Macchine virtuali**.
3. Selezionare le caselle di spunta per i tre nodi creati nella prima parte dell'esercitazione.
4. Fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella quarta parte della serie è stato descritto come pulire le risorse create nei passaggi precedenti.

> [!div class="checklist"]
> * Pulire le risorse

> [!div class="nextstepaction"]
> [Torna all'inizio](service-fabric-tutorial-standalone-create-infrastructure.md)
