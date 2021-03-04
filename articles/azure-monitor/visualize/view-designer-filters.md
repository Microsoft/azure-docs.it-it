---
title: Filtri nelle viste di monitoraggio di Azure | Microsoft Docs
description: Un filtro in una vista di monitoraggio di Azure consente agli utenti di filtrare i dati nella vista in base al valore di una proprietà specifica senza modificare la vista stessa.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 988bb1467e7bf2cca31151d85c57677b235cab30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043304"
---
# <a name="filters-in-azure-monitor-views"></a>Filtri nelle viste di monitoraggio di Azure
Un **filtro** in una [vista di monitoraggio di Azure](view-designer.md) consente agli utenti di filtrare i dati nella vista in base al valore di una proprietà specifica senza modificare la vista stessa.  È ad esempio possibile consentire agli utenti della vista di filtrare solo i dati di un computer o un set di computer specifico.  È possibile creare più filtri in una singola vista per consentire agli utenti di filtrare in base a più proprietà.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.

## <a name="using-a-filter"></a>Uso di un filtro
Fare clic sull'intervallo di tempo della data nella parte superiore di una visualizzazione per aprire l'elenco a discesa in cui è possibile modificare l'intervallo di tempo per la visualizzazione.

![Screenshot del menu a discesa intervallo di tempo per una visualizzazione in monitoraggio di Azure, che mostra il pulsante di opzione per gli ultimi 7 giorni selezionati.](media/view-designer-filters/filters-example-time.png)

Fare clic su **+** per aggiungere un filtro utilizzando filtri personalizzati definiti per la visualizzazione. Selezionare un valore per il filtro dall'elenco a discesa. Continuare ad aggiungere filtri facendo clic sul pulsante **+** . 


![Screenshot della finestra di dialogo per l'aggiunta di un filtro personalizzato in monitoraggio di Azure. È in corso la selezione della proprietà computer nel menu a discesa Seleziona proprietà.](media/view-designer-filters/filters-example-custom.png)

Se si rimuovono tutti i valori per un filtro, il filtro non verrà più applicato.


## <a name="creating-a-filter"></a>Creazione di un filtro

Creare un filtro dalla scheda **Filtri** quando si [modifica una vista](view-designer.md).  Il filtro è globale per la vista e si applica a tutte le parti della vista.  

![Impostazioni dei filtri](media/view-designer-filters/filters-settings.png)

La tabella seguente descrive le impostazioni per un filtro.

| Impostazione | Descrizione |
|:---|:---|
| Nome campo | Nome del campo usato per filtrare.  Questo campo deve corrispondere al campo riepiloga nella **query per i valori**. |
| Esegui query per valori | Query da eseguire per popolare l'elenco a discesa relativo al filtro per l'utente.  Questa query deve usare [riepiloga](/azure/kusto/query/summarizeoperator) o [Distinct](/azure/kusto/query/distinctoperator) per fornire valori univoci per un determinato campo e deve corrispondere al **nome del campo**.  È possibile usare l'opzione [sort](/azure/kusto/query/sortoperator) per ordinare i valori visualizzati dall'utente. |
| Tag | Nome del campo usato nelle query che supportano il filtro e visualizzato dall'utente. |

### <a name="examples"></a>Esempio

La tabella seguente include alcuni esempi di filtri comuni.  

| Nome campo | Esegui query per valori | Tag |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computers (Computer) |
| EventLevelName | Event &#124; distinct EventLevelName | Gravità |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Gravità |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificare le query della vista

Affinché un filtro abbia effetto, è necessario modificare le query nella vista per filtrare i valori selezionati.  Se non si modifica alcuna query nella vista, i valori selezionati dall'utente non avranno alcun effetto.

La sintassi per l'uso di un valore di filtro in una query è: 

`where ${filter name}`  

Se, ad esempio, nella vista è presente una query che restituisce eventi e utilizza un filtro denominato _computer_, è possibile utilizzare la query seguente.

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Se si aggiunge un altro filtro denominato Severity, è possibile usare la query seguente per applicare entrambi i filtri.

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>Passaggi successivi
* Leggere altre informazioni sulle [parti di visualizzazione](view-designer-parts.md) che è possibile aggiungere alla vista personalizzata.
