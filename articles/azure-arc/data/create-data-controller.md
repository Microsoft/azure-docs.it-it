---
title: Creare un controller dei dati
description: Creare un controller di dati di Azure Arc in un cluster Kubernetes multinodo tipico che è già stato distribuito.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cd41f740d3cdb8130199a5dda6e73209637d5142
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745935"
---
# <a name="create-the-azure-arc-data-controller"></a>Creare il controller di dati di Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Panoramica della creazione del controller di dati di Azure Arc

È possibile creare servizi dati abilitati per Azure Arc su più tipi diversi di cluster Kubernetes e servizi Kubernetes gestiti usando più approcci diversi.

Attualmente, l'elenco supportato dei servizi e delle distribuzioni di Kubernetes è il seguente:

- Servizio Azure Kubernetes
- Motore del servizio Kubernetes di Azure (motore AKS) in Azure Stack
- Servizio Azure Kubernetes in Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- OpenShift container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Motore di Google Cloud Kubernetes (GKE)
- Kubernetes upstream e open source in genere distribuito tramite kubeadm

> [!IMPORTANT]
> * La versione minima supportata di Kubernetes è la v 1.17. Per ulteriori informazioni, vedere [problemi noti](./release-notes.md#known-issues) . 
> * La versione minima supportata di OCP è 4,3.
> * Vedere i [requisiti di connettività](connectivity.md) per comprendere la connettività necessaria tra l'ambiente e Azure.
> * Per informazioni dettagliate su come configurare l'archiviazione permanente, vedere la [Guida alla configurazione dell'archiviazione](storage-configuration.md) .
> * Se si usa il servizio Azure Kubernetes, le dimensioni della VM del nodo di lavoro del cluster devono essere almeno **Standard_D8s_v3** e usare **dischi Premium.** Il cluster non deve essere esteso a più zone di disponibilità. 
> * Se si usa un'altra distribuzione o servizio Kubernetes, è necessario assicurarsi di avere una dimensione minima del nodo di 8 GB di RAM e 4 core e una capacità totale di 32 GB di RAM disponibile in tutti i nodi Kubernetes. Ad esempio, è possibile avere 1 nodo a 32 GB di RAM e 4 core oppure avere 2 nodi con 16 GB di RAM e 4 core ciascuno.

> [!NOTE]
> Se si usa Red Hat OpenShift container Platform in Azure, è consigliabile usare la versione più recente disponibile.

A seconda dell'opzione scelta, alcuni strumenti saranno _necessari_, ma è consigliabile [installare tutti gli strumenti client](./install-client-tools.md) prima di iniziare a creare il controller dati di Azure Arc.

Indipendentemente dall'opzione scelta, durante il processo di creazione sarà necessario fornire le seguenti informazioni:

- **Nome controller dati** : nome descrittivo per il controller dati, ad esempio "controller dati di produzione", "controller dati di Seattle".
- **Nome utente del controller dati** : qualsiasi nome utente per l'utente amministratore del controller di dati.
- **Password del controller dei dati** : password dell'utente amministratore del controller di dati.
- **Nome dello spazio dei nomi Kubernetes** : il nome dello spazio dei nomi Kubernetes in cui si vuole creare il controller dati.
- **Modalità** di connettività: la modalità di connettività determina il grado di connettività dall'ambiente dei servizi dati abilitati per Azure Arc ad Azure. L'anteprima supporta attualmente solo modalità connesse e direttamente connesse.  Per informazioni, vedere [modalità di connettività](./connectivity.md). 
- **ID sottoscrizione** di Azure: il GUID della sottoscrizione di Azure in cui si vuole creare la risorsa del controller dei dati in Azure.
- **Nome del gruppo di risorse di Azure** : nome del gruppo di risorse in cui si vuole creare la risorsa del controller dei dati in Azure.
- **Località di Azure** : località di Azure in cui verranno archiviati i metadati delle risorse del controller di dati in Azure. Per un elenco delle aree disponibili, vedere [infrastruttura globale/prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili più opzioni per la creazione del controller di dati di Azure Arc:

> **Si desidera fare semplicemente una prova?**  
> Inizia rapidamente a usare [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.
> 
- [Creare un controller di dati con [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Creare un controller dati con Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Creare un controller dati dal portale di Azure tramite un notebook di Jupyter in Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Creare un controller di dati con strumenti Kubernetes, ad esempio kubectl o OC](create-data-controller-using-kubernetes-native-tools.md)
- [Creare un controller dati con Azure Arc Jumpstart per un'esperienza accelerata di una distribuzione di prova](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
