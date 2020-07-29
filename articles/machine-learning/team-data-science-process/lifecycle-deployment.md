---
title: Fase di distribuzione del ciclo di vita del processo di data science per i team
description: Obiettivi, attività e risultati finali per la fase di distribuzione dei progetti di data science
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76720487"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fase di distribuzione del ciclo di vita del processo di data science per i team

Questo articolo descrive gli obiettivi, le attività e i risultati finali associati alla distribuzione del processo di data science per i team. Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Informazioni commerciali**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Obiettivo
Distribuire modelli con una pipeline di dati in un ambiente di produzione o di simil-produzione per l'accettazione da parte di un utente finale. 

## <a name="how-to-do-it"></a>Come eseguirla
Attività principali descritte in questa fase:

**Rendere operativo il modello**: distribuire il modello e la pipeline in un ambiente di produzione o di simil-produzione per l'uso da parte dell'applicazione.

### <a name="operationalize-a-model"></a>Rendere operativo un modello
Quando si ottiene un set di modelli con prestazioni ottimali, è possibile renderli operativi per l'uso da parte di altre applicazioni. In base ai requisiti aziendali, le previsioni vengono eseguite in tempo reale o in batch. Per distribuire i modelli, è possibile esporli con un'interfaccia API aperta. L'interfaccia consente al modello di essere usato facilmente da diverse applicazioni, ad esempio:

   * Siti Web online
   * Fogli di calcolo 
   * Dashboard
   * Applicazioni line-of-business 
   * Applicazioni back-end 

Per esempi su come rendere operativi i modelli con un servizio Web di Azure Machine Learning, vedere [Pubblicare un servizio Web di Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). È anche opportuno creare dati di telemetria e monitorare il modello di produzione e la pipeline di dati distribuiti. Questa procedura consente allo stato del sistema successivo di segnalare eventuali problemi e risolverli.  

## <a name="artifacts"></a>Artifacts

* Dashboard di stato che mostra l'integrità del sistema e le metriche chiave
* Report di modellazione finale con informazioni dettagliate sulla distribuzione
* Documento finale sull'architettura della soluzione


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Informazioni commerciali](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Vengono fornite procedure dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
