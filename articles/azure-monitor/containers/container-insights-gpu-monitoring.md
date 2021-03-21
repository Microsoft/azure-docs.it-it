---
title: Configurare il monitoraggio GPU con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio dei cluster Kubernetes con i nodi NVIDIA e AMD con GPU abilitati con informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731868"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Configurare il monitoraggio GPU con informazioni dettagliate sul contenitore

A partire dalla versione dell'agente *ciprod03022019*, l'agente integrato di container Insights ora supporta il monitoraggio dell'utilizzo della GPU (unità di elaborazione grafica) nei nodi del cluster Kubernetes che supportano GPU e monitora i pod/contenitori che richiedono e usano le risorse GPU.

## <a name="supported-gpu-vendors"></a>Fornitori di GPU supportati

Il contenitore Insights supporta il monitoraggio dei cluster GPU dai fornitori di GPU seguenti:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Il contenitore Insights avvia automaticamente il monitoraggio dell'utilizzo della GPU nei nodi e la GPU che richiede i pod e i carichi di lavoro raccogliendo le metriche seguenti a intervalli 60sec e archiviando tali dati nella tabella **InsightMetrics** .

>[!NOTE]
>Dopo il provisioning di un cluster con nodi GPU, verificare che il [driver GPU](../../aks/gpu-cluster.md) sia installato come richiesto da AKS per eseguire carichi di lavoro GPU. Il contenitore Insights raccoglie le metriche GPU tramite Pod driver GPU in esecuzione nel nodo. 

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor|Percentuale di tempo nel periodo di campionamento precedente (60 secondi) durante il quale la GPU era occupata/elaborata attivamente per un contenitore. Il ciclo Duty è un numero compreso tra 1 e 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Ogni contenitore può specificare limiti come una o più GPU. Non è possibile richiedere o limitare una frazione di una GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Ogni contenitore può richiedere una o più GPU. Non è possibile richiedere o limitare una frazione di una GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Quantità di memoria GPU, in byte, disponibile per l'uso per un contenitore specifico. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Quantità di memoria GPU in byte utilizzata da un contenitore specifico. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Numero di GPU in un nodo che può essere usato da Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Numero totale di GPU in un nodo. |

## <a name="gpu-performance-charts"></a>Grafici delle prestazioni GPU 

Il contenitore Insights include grafici preconfigurati per le metriche elencate in precedenza nella tabella come cartella di lavoro GPU per ogni cluster. Per una descrizione delle cartelle di lavoro disponibili per informazioni sul contenitore, vedere [cartelle di lavoro in informazioni dettagliate sul contenitore](../insights/container-insights-reports.md) .

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come distribuire un cluster del servizio contenitore di Azure che include nodi abilitati per la GPU, vedere [usare GPU per carichi di lavoro a elevato utilizzo di calcolo in Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS).

- Altre informazioni sugli [SKU di VM ottimizzate per GPU sono disponibili in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Esaminare il [supporto della GPU in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) per altre informazioni sul supporto sperimentale di Kubernetes per la gestione delle GPU in uno o più nodi di un cluster.
