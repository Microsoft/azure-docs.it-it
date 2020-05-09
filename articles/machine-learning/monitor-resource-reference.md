---
title: Riferimento ai dati di monitoraggio | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Informazioni sui dati e sulle risorse raccolti per Azure Machine Learning e disponibili in monitoraggio di Azure. Monitoraggio di Azure raccoglie e Visualizza i dati relativi all'area di lavoro Azure Machine Learning e consente di visualizzare le metriche, impostare gli avvisi e analizzare i dati registrati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: e9a43f4a7da39869e002e2da9fb9638381e57cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856027"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Riferimento ai dati di monitoraggio di Azure Machine Learning

Informazioni sui dati e sulle risorse raccolti da monitoraggio di Azure dall'area di lavoro Azure Machine Learning. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Log risorse

La tabella seguente elenca le proprietà per i log delle risorse Azure Machine Learning quando vengono raccolti nei log di monitoraggio di Azure o nell'archiviazione di Azure.

### <a name="amlcomputejobevents-table"></a>Tabella AmlComputeJobEvents

| Proprietà | Descrizione |
|:--- |:---|
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Categoria | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| JobId | ID del processo inviato |
| ExperimentId | ID dell'esperimento |
| Experimentname | Nome dell'esperimento |
| CustomerSubscriptionId | SubscriptionId in cui l'esperimento e il processo sono stati inviati |
| WorkspaceName | Nome dell'area di lavoro di Machine Learning |
| ClusterName | Nome del cluster |
| ProvisioningState | Stato dell'invio del processo |
| ResourceGroupName | Nome del gruppo di risorse |
| JobName | Nome del processo |
| ClusterId | ID del cluster |
| EventType | Tipo di evento del processo, ad esempio JobSubmitted, JobRunning, JobFailed, JobSucceeded e così via. |
| ExecutionState | Stato del processo (esecuzione), ad esempio, in coda, in esecuzione, riuscito, non riuscito |
| ErrorDetails | Dettagli dell'errore del processo |
| CreationApiVersion | Versione dell'API usata per creare il processo |
| ClusterResourceGroupName | Nome del gruppo di risorse del cluster |
| TFWorkerCount | Numero di processi di lavoro TF |
| TFParameterServerCount | Conteggio del server dei parametri TF |
| ToolType | Tipo di strumento utilizzato |
| RunInContainer | Flag che descrive se il processo deve essere eseguito all'interno di un contenitore |
| JobErrorMessage | messaggio dettagliato di errore del processo |
| NodeId | ID del nodo creato in cui è in esecuzione il processo |

### <a name="amlcomputeclusterevents-table"></a>Tabella AmlComputeClusterEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Categoria | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ProvisioningState | Stato del provisioning del cluster |
| ClusterName | Nome del cluster |
| ClusterType | Tipo di cluster |
| CreatedBy | Utente che ha creato il cluster |
| CoreCount | Conteggio dei core nel cluster |
| VmSize | Dimensioni della macchina virtuale del cluster |
| VmPriority | Priorità dei nodi creati all'interno di un cluster dedicato/LowPriority |
| ScalingType | Tipo di ridimensionamento manuale/automatico del cluster |
| InitialNodeCount | Numero iniziale di nodi del cluster |
| MinimumNodeCount | Numero minimo di nodi del cluster |
| MaximumNodeCount | Numero massimo di nodi del cluster |
| NodeDeallocationOption | Come deallocare il nodo |
| Editore | Server di pubblicazione del tipo di cluster |
| Offerta | Offerta con cui viene creato il cluster |
| Sku | SKU del nodo/VM creato nel cluster |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| SubnetId | SubnetId del cluster |
| AllocationState | Stato di allocazione cluster |
| CurrentNodeCount | Numero corrente di nodi del cluster |
| TargetNodeCount | Numero di nodi di destinazione del cluster durante la scalabilità verso l'alto o verso il basso |
| EventType | Tipo di evento durante la creazione del cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo di inattività in secondi prima che il cluster venga ridimensionato |
| PreemptedNodeCount | Numero di nodi di cui è stato interrotto il cluster |
| IsResizeGrow | Flag che indica la scalabilità verticale del cluster |
| VmFamilyName | Nome della famiglia di macchine virtuali dei nodi che possono essere creati nel cluster |
| LeavingNodeCount | Uscita dal numero di nodi del cluster |
| UnusableNodeCount | Numero di nodi non utilizzabili del cluster |
| IdleNodeCount | Numero di nodi inattivi del cluster |
| RunningNodeCount | Conteggio dei nodi in esecuzione del cluster |
| PreparingNodeCount | Preparazione del conteggio dei nodi del cluster |
| QuotaAllocated | Quota allocata al cluster |
| QuotaUtilized | Quota utilizzata del cluster |
| AllocationStateTransitionTime | Tempo di transizione da uno stato a un altro |
| ClusterErrorCodes | Codice di errore ricevuto durante la creazione o il ridimensionamento del cluster |
| CreationApiVersion | Versione API usata durante la creazione del cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabella AmlComputeClusterNodeEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Categoria | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ClusterName | Nome del cluster |
| NodeId | ID del nodo del cluster creato |
| VmSize | Dimensioni della macchina virtuale del nodo |
| VmFamilyName | Famiglia di macchine virtuali a cui appartiene il nodo |
| VmPriority | Priorità del nodo creato/LowPriority dedicato |
| Editore | Autore dell'immagine della macchina virtuale, ad esempio Microsoft-dsvm |
| Offerta | Offerta associata alla creazione della macchina virtuale |
| Sku | SKU del nodo o della macchina virtuale creata |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| ClusterCreationTime | Ora di creazione del cluster |
| ResizeStartTime | Ora di inizio/discesa della scalabilità del cluster |
| ResizeEndTime | Ora di fine della scalabilità verticale del cluster |
| NodeAllocationTime | Ora di allocazione del nodo |
| NodeBootTime | Ora di avvio del nodo |
| StartTaskStartTime | Ora in cui l'attività è stata assegnata a un nodo e avviata |
| StartTaskEndTime | Ora di fine dell'attività assegnata a un nodo |
| TotalE2ETimeInSeconds | Tempo totale nodo attivo |

### <a name="metrics"></a>Metriche

Le tabelle seguenti elencano le metriche della piattaforma raccolte per Azure Machine Learning tutte le metriche vengono archiviate nella **area di lavoro di Azure Machine Learning**dello spazio dei nomi.

**Modello**

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Distribuzione del modello non riuscita | Conteggio | Numero di distribuzioni di modelli non riuscite. |
| Distribuzione modello avviata | Conteggio | Numero di distribuzioni di modelli avviate. |
| Distribuzione del modello completata | Conteggio | Numero di distribuzioni di modelli completate correttamente. |
| Registrazione modello non riuscita | Conteggio | Numero di registrazioni del modello non riuscite. |
| Registrazione del modello completata | Conteggio | Numero di registrazioni del modello completate correttamente. |

**Quota**

Le informazioni sulle quote sono destinate solo ai Azure Machine Learning di calcolo.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Core attivi | Conteggio | Numero di core di calcolo attivi. |
| Nodi attivi | Conteggio | Numero di nodi attivi. |
| Core inattivi | Conteggio | Numero di core di calcolo inattivi. |
| Nodi inattivi | Conteggio | Numero di nodi di calcolo inattivi. |
| Uscita da Core | Conteggio | Numero di core in uscita. |
| Uscita da nodi | Conteggio | Numero di nodi in uscita. |
| Core con precedenza | Conteggio | Numero di core con precedenza. |
| Nodi con precedenza | Conteggio | Numero di nodi con precedenza. |
| Percentuale di utilizzo della quota | Percentuale | Percentuale di quota utilizzata. |
| Totale core | Conteggio | Core totali. |
| Totale nodi | Conteggio | Nodi totali. |
| Core inutilizzabili | Conteggio | Numero di core inutilizzabili. |
| Nodi inutilizzabili | Conteggio | Numero di nodi inutilizzabili. |

Di seguito sono riportate le dimensioni che possono essere usate per filtrare le metriche delle quote:

| Dimension | Metriche disponibili con | Descrizione |
| ---- | ---- | ---- |
| Cluster Name | Tutte le metriche della quota | Nome dell'istanza di calcolo. |
| Nome della famiglia di macchine virtuali | Percentuale di utilizzo della quota | Nome della famiglia di macchine virtuali usata dal cluster. |
| Priorità VM | Percentuale di utilizzo della quota | Priorità della macchina virtuale.

**Risorsa**

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| CpuUtilization | Percentuale | Percentuale di utilizzo della CPU per un determinato nodo durante un'esecuzione o un processo. Questa metrica viene pubblicata solo quando un processo è in esecuzione in un nodo. Un processo può usare uno o più nodi. Questa metrica è pubblicata per ogni nodo. |
| GpuUtilization | Percentuale | Percentuale di utilizzo della GPU per un determinato nodo durante un'esecuzione o un processo. Un nodo può avere una o più GPU. Questa metrica è pubblicata per ogni GPU per ogni nodo. |

Di seguito sono riportate le dimensioni che possono essere usate per filtrare le metriche delle risorse:

| Dimension | Descrizione |
| ----- | ----- |
| CreatedTime | |
| DeviceId | ID del dispositivo (GPU). Disponibile solo per GpuUtilization. |
| NodeId | ID del nodo creato in cui è in esecuzione il processo. |
| RunId | ID dell'esecuzione/processo. |

**Correre**

Informazioni sulle esecuzioni di training.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Esecuzioni completate | Conteggio | Numero di esecuzioni completate. |
| Esecuzioni non riuscite | Conteggio | Numero di esecuzioni non riuscite. |
| Esecuzioni avviate | Conteggio | Numero di esecuzioni avviate. |

Di seguito sono riportate le dimensioni che possono essere usate per filtrare le metriche di esecuzione:

| Dimension | Descrizione |
| ---- | ---- |
| ComputeType | Tipo di calcolo utilizzato dall'esecuzione. |
| PipelineStepType | Tipo di [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) utilizzato nell'esecuzione. |
| PublishedPipelineId | ID della pipeline pubblicata utilizzata nell'esecuzione. |
| RunType | Tipo di esecuzione. |

I valori validi per la dimensione RunType sono i seguenti:

| valore | Descrizione |
| ----- | ----- |
| Esperimento | Esecuzioni non pipeline. |
| PipelineRun | Esecuzione di una pipeline, che è l'elemento padre di un StepRun. |
| StepRun | Esecuzione di un passaggio della pipeline. |
| ReusedStepRun | Esecuzione di un passaggio della pipeline che riutilizza un'esecuzione precedente. |

## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio Azure Machine Learning, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con](/azure/azure-monitor/insights/monitor-azure-resource) monitoraggio di Azure.
