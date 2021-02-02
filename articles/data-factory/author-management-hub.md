---
title: Hub di gestione
description: Gestire le connessioni, la configurazione del controllo del codice sorgente e le proprietà di creazione globali nell'hub di gestione Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: dcstwh
ms.author: weetok
manager: anandsub
ms.date: 02/01/2021
ms.openlocfilehash: c3366b7ba0eb0b49d4d5b89481b7bed843e52c8e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428999"
---
# <a name="management-hub-in-azure-data-factory"></a>Hub di gestione in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

L'hub di gestione, a cui si accede tramite la scheda *Gestisci* nel Azure Data Factory UX, è un portale che ospita le azioni di gestione globali per il data factory. Qui è possibile gestire le connessioni agli archivi dati e ai calcoli esterni, alla configurazione del controllo del codice sorgente e alle impostazioni del trigger.

## <a name="manage-connections"></a>Gestire le connessioni

### <a name="linked-services"></a>Servizi collegati

I servizi collegati definiscono le informazioni di connessione per Azure Data Factory per connettersi agli archivi dati esterni e agli ambienti di calcolo. Per altre informazioni, vedere [concetti relativi ai servizi collegati](concepts-linked-services.md). La creazione, la modifica e l'eliminazione di servizi collegati vengono eseguite nell'hub di gestione.

![Gestisci servizi collegati](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Runtime di integrazione

Un runtime di integrazione è un'infrastruttura di calcolo usata da Azure Data Factory per fornire funzionalità di integrazione dei dati in ambienti di rete diversi. Per ulteriori informazioni, vedere Concetti relativi a [Integration Runtime](concepts-integration-runtime.md). Nell'hub di gestione è possibile creare, eliminare e monitorare i runtime di integrazione.

![Gestire i runtime di integrazione](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Gestione del controllo del codice sorgente

### <a name="git-configuration"></a>Configurazione git

È possibile visualizzare o modificare tutte le informazioni relative a git nelle impostazioni di configurazione di git nell'hub di gestione. 

Sono elencate anche le ultime informazioni sui commit pubblicate e possono essere utili per comprendere il commit preciso, che è stato pubblicato/distribuito negli ambienti. Può essere utile anche quando si esegue la correzione rapida nell'ambiente di produzione.

Per ulteriori informazioni, vedere informazioni sul [controllo del codice sorgente in Azure Data Factory](source-control.md).

![Gestisci repository git](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Modello di parametrizzazione

Per eseguire l'override dei parametri del modello di Gestione risorse generati durante la pubblicazione dal ramo di collaborazione, è possibile generare o modificare un file di parametri personalizzati. Per ulteriori informazioni, vedere l'articolo relativo all' [utilizzo di parametri personalizzati nel modello di gestione risorse](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template). Il modello di parametrizzazione è disponibile solo quando si lavora in un repository git. Se il *arm-template-parameters-definition.js* nel file non esiste nel ramo di lavoro, la modifica del modello predefinito lo genererà.

![Gestire parametri personalizzati](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Gestione della creazione

### <a name="triggers"></a>Trigger

I trigger determinano quando deve essere avviata l'esecuzione di una pipeline. Attualmente i trigger possono essere in base a una pianificazione del clock del muro, operare su un intervallo periodico o dipendere da un evento. Per ulteriori informazioni, vedere l'articolo relativo all' [esecuzione del trigger](concepts-pipeline-execution-triggers.md#trigger-execution). Nell'hub di gestione è possibile creare, modificare, eliminare o visualizzare lo stato corrente di un trigger.

![Screenshot che mostra dove creare, modificare, eliminare o visualizzare lo stato corrente di un trigger.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Parametri globali

I parametri globali sono costanti in un data factory che possono essere utilizzati da una pipeline in qualsiasi espressione. Per ulteriori informazioni, vedere informazioni sui [parametri globali](author-global-parameters.md).

![Crea parametri globali](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare un repository git](source-control.md) nel file ADF


