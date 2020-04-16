---
title: Mapping data flow Visual Monitoring
description: Informazioni su come monitorare visivamente i flussi di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: b06000d6b3cd7a429b7ad0fc9c453595f21bd893
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418474"
---
# <a name="monitor-data-flows"></a>Monitorare i flussi di dati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Al termine della compilazione e del debug del flusso di dati, è necessario definire l'esecuzione del flusso di dati in base a una pianificazione nel contesto di una pipeline. È possibile pianificare la pipeline da Azure Data Factory tramite trigger. In alternativa, è possibile usare l'opzione Trigger Now (Attiva ora) nel generatore di pipeline di Azure Data Factory per attivare una singola esecuzione in modo da testare il flusso di dati nel contesto della pipeline.

Quando si esegue la pipeline, è possibile monitorarla insieme a tutte le attività che contiene, inclusa l'attività del flusso di dati. Fare clic sull'icona di monitoraggio nel pannello dell'interfaccia utente di Azure Data Factory. Verrà visualizzata una schermata simile alla seguente. Le icone evidenziate permettono di esaminare più in dettaglio le attività nella pipeline, tra cui l'attività del flusso di dati.

![Monitoraggio del flusso di dati](media/data-flow/mon001.png "Monitoraggio dei flussi di dati")

Vedrai le statistiche a questo livello, inclusi i tempi di esecuzione e lo stato. L'ID esecuzione a livello di attività è diverso da quello a livello di pipeline. L'ID esecuzione al livello precedente è per la pipeline. Se si fa clic sul simbolo di occhiali, vengono visualizzati ulteriori dettagli sull'esecuzione del flusso di dati.

![Monitoraggio del flusso di dati](media/data-flow/mon002.png "Monitoraggio dei flussi di dati")

Quando ci si trova nella visualizzazione grafica di monitoraggio dei nodi, è possibile osservare una versione semplificata di tipo solo visualizzazione del grafico del flusso di dati.

![Monitoraggio del flusso di dati](media/data-flow/mon003.png "Monitoraggio dei flussi di dati")

## <a name="view-data-flow-execution-plans"></a>Visualizzare i piani di esecuzione del flusso di dati

Quando il flusso di dati viene eseguito in Spark, Azure Data Factory determina i percorsi del codice ottimali in base all'intero flusso di dati. Inoltre, i percorsi di esecuzione possono verificarsi in nodi con scalabilità orizzontale e partizioni di dati diversi. Di conseguenza, il grafico di monitoraggio rappresenta la progettazione del flusso, tenendo conto del percorso di esecuzione delle trasformazioni. Quando si fa clic sui singoli nodi, vengono visualizzati "raggruppamenti" che rappresentano il codice che è stato eseguito contemporaneamente nel cluster. Le tempistiche e i conteggi visualizzati rappresentano i gruppi invece dei singoli passaggi nella progettazione.

![Monitoraggio del flusso di dati](media/data-flow/mon004.png "Monitoraggio dei flussi di dati")

* Quando si fa clic sull'area aperta nella finestra di monitoraggio, le statistiche nel riquadro inferiore mostrano le tempistiche e i numeri di righe per ogni sink e le trasformazioni che hanno generato i dati sink per la derivazione della trasformazione.

* Quando si selezionano singole trasformazioni, si riceverà un feedback aggiuntivo sul pannello di destra che mostra le statistiche delle partizioni, i conteggi delle colonne, l'asimmetria (come sono uniformemente i dati distribuiti tra le partizioni) e la curtosi (quanto sono appuntiti i dati).

* Quando si fa clic sul sink nella visualizzazione del nodo, viene visualizzata la derivazione della colonna. Esistono tre metodi diversi in cui vengono accumulate le colonne lungo il flusso di dati per raggiungere il sink. ovvero:

  * Calcolato: si usa la colonna per l'elaborazione condizionale o all'interno di un'espressione nel flusso di dati, ma non si atterra nel sink
  * Derivato: la colonna è una nuova colonna generata nel flusso, ovvero non presente nell'oggetto Origine
  * Mappato: la colonna ha avuto origine dall'origine e la colonna viene mappata a un campo sink
  * Stato del flusso di dati: lo stato corrente dell'esecuzione
  * Tempo di avvio del cluster: quantità di tempo per acquisire l'ambiente di calcolo JIT Spark per l'esecuzione del flusso di dati
  * Numero di trasformazioni: numero di passaggi di trasformazione eseguiti nel flussoNumber of transforms: How many transformation steps are being executed in your flow
  
![Monitoraggio del flusso di dati](media/data-flow/monitornew.png "Monitoraggio del flusso di dati nuovo")  
  
## <a name="monitor-icons"></a>Icona di monitoraggio

Questa icona indica che i dati della trasformazione sono già stati memorizzati nella cache del cluster e di conseguenza le tempistiche e il percorso di esecuzione sono già stati presi in considerazione:

![Monitoraggio del flusso di dati](media/data-flow/mon004.png "Monitoraggio dei flussi di dati")

Nella trasformazione verranno anche visualizzate alcune icone a forma di cerchio verde. Questa rappresentano un conteggio del numero di sink in cui vengono trasmessi i dati.
