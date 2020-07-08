---
title: Come configurare la conservazione nell'ambiente-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare la conservazione nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 524e47e5be142b720687ad48ee9407da23284bd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605058"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurazione della conservazione in Time Series Insights

Questo articolo descrive come configurare il **periodo di conservazione dei dati** e il **comportamento limite di archiviazione superato** in Azure Time Series Insights.

## <a name="summary"></a>Summary

Ogni ambiente Azure Time Series Insights dispone di un'impostazione per configurare il **periodo di conservazione dei dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata della conservazione, che va da 1 a 400 giorni, a seconda della condizione che si verifica per prima.

Ogni ambiente Time Series Insights ha un'impostazione aggiuntiva del **limite di archiviazione superato il comportamento**. Questa impostazione controlla il comportamento di traffico in ingresso e pulizia quando viene raggiunta la capacità massima di un ambiente. È possibile scegliere tra due tipi di comportamento:

- **Eliminare i dati meno recenti** (impostazione predefinita)
- **Sospendere il traffico in ingresso**

Per informazioni dettagliate per comprendere meglio queste impostazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).  

## <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente Time Series Insights esistente. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure. Selezionare l'ambiente Time Series Insights.

1. Sotto l'intestazione **Impostazioni** selezionare **Configurazione archiviazione**.

    [![In impostazioni selezionare Configurazione archiviazione](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selezionare il **periodo di conservazione dei dati (in giorni)** per configurare la conservazione usando la barra del dispositivo di scorrimento o digitare un numero nella casella di testo.

1. Si noti l'impostazione **Capacità**, poiché questa configurazione influisce sulla quantità massima di eventi di dati e sulla capacità di archiviazione totale per l'archiviazione dei dati.

1. Attivare o disattivare l'impostazione **Comportamento limite di archiviazione superato**. Selezionare il comportamento **Purge old data** (Eliminare i dati meno recenti) o **Pause ingress** (Sospendere il traffico in ingresso).

    [![Sospendere il traffico in ingresso: accetta e Salva.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Esaminare la documentazione per conoscere i potenziali rischi di perdita dei dati. Selezionare **Salva** per configurare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).

- Informazioni [su come ridimensionare l'ambiente di Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).
