---
title: Creare un controller di dati di Azure Arc nell'portale di Azure
description: Creare un controller di dati di Azure Arc nell'portale di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030867"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Creare un controller di dati di Azure Arc nell'portale di Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introduzione

È possibile usare la portale di Azure per creare un controller di dati di Azure Arc.

Molte delle esperienze di creazione per Azure Arc iniziano nella portale di Azure anche se la risorsa da creare o gestire è esterna all'infrastruttura di Azure. Il modello di esperienza utente in questi casi, soprattutto quando non esiste una connettività diretta tra Azure e l'ambiente, consiste nell'usare il portale di Azure per generare uno script che può quindi essere scaricato ed eseguito nel proprio ambiente per stabilire una connessione sicura ad Azure. Ad esempio, i server abilitati per Azure Arc seguono questo modello per [creare i server abilitati per Arc](../servers/onboard-portal.md).

Quando si usa la modalità di connessione indiretta di Azure Arc Enabled Data Services, è possibile usare la portale di Azure per generare un notebook che può quindi essere scaricato ed eseguito in Azure Data Studio sul cluster Kubernetes. 

Quando si usa la modalità di connessione diretta, è possibile effettuare il provisioning del controller dati direttamente dalla portale di Azure. Altre informazioni sulle modalità di [connettività](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Usare il portale di Azure per creare un controller di dati di Azure Arc

Attenersi alla procedura seguente per creare un controller di dati di Azure ARC usando il portale di Azure e Azure Data Studio.

1. Per prima cosa, accedere al [marketplace portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  I risultati della ricerca nel Marketplace verranno filtrati per mostrare il "Azure Arc data controller".
1. Se il primo passaggio non ha immesso i criteri di ricerca. Immettere nei risultati della ricerca, fare clic su "Azure Arc data controller".
1. Selezionare il riquadro del controller di dati di Azure dal Marketplace.
1. Fare clic sul pulsante **Crea**.
1. Selezionare la modalità di connettività indiretta. Altre informazioni sulle [modalità di connettività e sui requisiti](./connectivity.md). 
1. Esaminare i requisiti per creare un controller di dati di Azure Arc e installare i prerequisiti software mancanti, ad esempio Azure Data Studio e kubectl.
1. Fare clic sul pulsante **Next: data controller Details** .
1. Scegliere una sottoscrizione, un gruppo di risorse e una località di Azure esattamente come per qualsiasi altra risorsa da creare nel portale di Azure. In questo caso, il percorso di Azure selezionato sarà quello in cui verranno archiviati i metadati relativi alla risorsa.  La risorsa stessa verrà creata in base a qualsiasi infrastruttura scelta. Non è necessario che si trovi nell'infrastruttura di Azure.
1. Immettere un nome per il controller dati.

1. Selezionare un profilo di configurazione della distribuzione.
1. Fare clic sul pulsante **Apri in Azure studio** .
1. Nella schermata successiva verrà visualizzato un riepilogo delle selezioni e un notebook generato.  Per scaricare il notebook, è possibile fare clic sul pulsante **Scarica Notebook di provisioning** .

   > [!IMPORTANT]
   > In Azure Red Hat OpenShift o Red Hat OpenShift container Platform è necessario applicare il vincolo del contesto di sicurezza prima di creare il controller di dati. Seguire le istruzioni in [applicare un vincolo del contesto di sicurezza per Azure Arc Enabled Data Services in OpenShift](how-to-apply-security-context-constraint.md).

1. Aprire il notebook in Azure Data Studio e fare clic sul pulsante **Esegui tutto** nella parte superiore.
1. Seguire i prompt e le istruzioni nel notebook per completare la creazione del controller di dati.

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome "Arc".  Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire ' Arc ' con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).