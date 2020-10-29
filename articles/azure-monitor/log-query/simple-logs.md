---
title: Esperienza dei log semplici in monitoraggio di Azure (anteprima) | Microsoft Docs
description: La semplice esperienza dei log consente di creare query di base in monitoraggio di Azure senza interagire direttamente con KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92929301"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Esperienza dei log semplici in monitoraggio di Azure (anteprima)
Monitoraggio di Azure offre un' [esperienza avanzata](get-started-portal.md) per la creazione di [query di log](log-query-overview.md) con il linguaggio KQL. È possibile che non sia necessaria tutta la potenza di KQL, ma è preferibile un'esperienza semplificata per i requisiti di query di base. La semplice esperienza dei log consente di creare query di base senza interagire direttamente con KQL. È anche possibile usare i log semplici come strumento di apprendimento per KQL, in quanto sono necessarie query più sofisticate.

> [!NOTE]
> I log semplici vengono attualmente implementati come test solo per Cosmos DB e Key Vault. Condividi la tua esperienza con Microsoft tramite [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) per aiutarci a determinare se la funzionalità verrà espansa e rilasciata.


## <a name="scope"></a>Ambito
L'esperienza dei log semplici recupera i dati dalla tabella *AzureDiagnostics* , *AzureMetrics* e *AzureActivity* per la risorsa selezionata. 

## <a name="using-simple-logs"></a>Uso di log semplici
Passare a qualsiasi Cosmos DB o Key Vault nella sottoscrizione di Azure con [le impostazioni di diagnostica configurate per raccogliere i log in un'area di lavoro log Analytics](../platform/resource-logs.md#send-to-azure-storage). Fare clic su **log** nel menu **monitoraggio** per aprire la semplice esperienza dei log.

![Screenshot mostra il menu monitoraggio con i log selezionati.](media/simple-logs/menu.png)

Selezionare un **campo** e un **operatore** e specificare un **valore** per il confronto. Fare clic **+** e specificare **e/o** per aggiungere altri criteri.

![Screenshot mostra la ricerca nel riquadro log con i log semplici selezionati.](media/simple-logs/criteria.png)

Fare clic su **Esegui** per visualizzare i risultati della query.

## <a name="view-and-edit-kql"></a>Visualizzare e modificare KQL
Selezionare **editor di query** per aprire il KQL generato dalla query log semplici nell'esperienza log Analytics completa. 

![Editor di query](media/simple-logs/query-editor.png)

È possibile modificare direttamente il KQL e usare altre funzionalità in Log Analytics, ad esempio i filtri, per perfezionare ulteriormente i risultati.

![Modifica KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passaggi successivi

- Completare un'esercitazione sull' [uso di log Analytics nel portale di Azure](get-started-portal.md).
- Completare un'esercitazione sulla [scrittura di query di log](get-started-portal.md).
