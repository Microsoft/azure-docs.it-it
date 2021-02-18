---
title: Criteri di supporto per i cluster di Azure Red Hat OpenShift 4
description: Informazioni sui requisiti dei criteri di supporto per Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: fced0cfb1836be0f90c606b9a9753dad4200670d
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635626"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Criteri di supporto di Azure Red Hat OpenShift

Alcune configurazioni per i cluster di Azure Red Hat OpenShift 4 possono influenzare l'idoneità al supporto del cluster. Azure Red Hat OpenShift 4 consente agli amministratori del cluster di apportare modifiche ai componenti interni al cluster, ma non tutte le modifiche sono supportate. Il criterio di supporto riportato di seguito condivide le modifiche che violano i criteri e rendono nullo il supporto Microsoft e Red Hat.

> [!NOTE]
> Le funzionalità contrassegnate come "Anteprima della tecnologia" in OpenShift Container Platform non sono supportate in Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Requisiti di configurazione del cluster

* Tutti gli operatori del cluster OpenShift devono rimanere in uno stato gestito. È possibile restituire l'elenco degli operatori del cluster eseguendo `oc get clusteroperators`.
* Il cluster deve avere almeno due nodi del ruolo di lavoro. Non ridimensionare i ruoli di lavoro del cluster a zero o tentare un arresto del cluster normale.
* Non rimuovere o modificare i servizi Prometheus e Alertmanager del cluster.
* Non rimuovere le regole Alertmanager del servizio.
* Non rimuovere o modificare i gruppi di sicurezza di rete.
* Non rimuovere o modificare la registrazione del servizio Azure Red Hat OpenShift (pod MDSD).
* Non rimuovere o modificare il segreto pull del cluster "arosvc.azurecr.io".
* Tutte le macchine virtuali del cluster devono avere accesso diretto a Internet in uscita, almeno agli endpoint di Azure Resource Manager (ARM) e di registrazione dei servizi (Geneva).  Non è supportata alcuna forma d'uso di proxy HTTPS.
* Non modificare la configurazione DNS della rete virtuale del cluster. È necessario usare il resolver DNS di Azure predefinito.
* Non eseguire l'override di alcuno degli oggetti MachineConfig del cluster (ad esempio, la configurazione di kubelet) in alcun modo.
* Non impostare opzioni di unsupportedConfigOverrides. L'impostazione di queste opzioni impedisce gli aggiornamenti della versione secondaria.
* Il servizio Azure Red Hat OpenShift accede al cluster tramite il servizio di collegamento privato.  Non rimuovere o modificare l'accesso al servizio.
* I nodi di calcolo non RHCOS non sono supportati. Ad esempio, non è possibile usare un nodo di calcolo RHEL.

## <a name="supported-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali supportate

Azure Red Hat OpenShift 4 supporta le istanze del nodo di lavoro in macchine virtuali con le dimensioni seguenti:

### <a name="general-purpose"></a>Scopo generico

|Serie|Dimensione|vCPU|Memoria: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Ottimizzate per la memoria

|Serie|Dimensione|vCPU|Memoria: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Ottimizzate per il calcolo

|Serie|Dimensione|vCPU|Memoria: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Nodi master

|Serie|Dimensione|vCPU|Memoria: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
