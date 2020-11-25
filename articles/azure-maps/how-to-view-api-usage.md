---
title: Visualizzare le metriche di utilizzo dell'API Maps di Azure | Mappe Microsoft Azure
description: Informazioni su come visualizzare le metriche di utilizzo dell'API di Azure Maps, ad esempio richieste totali, errori totali e disponibilità. Vedere come filtrare i dati e suddividere i risultati.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/06/2018
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c9b732bd25e7ef8aa084c98d5b059d422f86a4b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003515"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Visualizzare le metriche di utilizzo dell'API Mappe di Azure

Questo articolo illustra come visualizzare le metriche di utilizzo dell'API, per l'account Azure Maps, nella [portale di Azure](https://portal.azure.com). Le metriche vengono visualizzate in un pratico formato grafico in riferimento a un periodo di tempo personalizzabile.

## <a name="view-metric-snapshot"></a>Visualizzare lo snapshot delle metriche

È possibile visualizzare alcune metriche comuni nella pagina **Panoramica** dell'account di Mappe. Questa pagina attualmente include i valori *Richieste totali*, *Totale errori* e *Disponibilità* riferiti a un periodo di tempo selezionabile.

![Cenni preliminari sulle metriche di utilizzo di Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Passare alla sezione successiva, se è necessario personalizzare questi grafici per un'analisi specifica.

## <a name="view-detailed-metrics"></a>Visualizzare metriche dettagliate

1. Accedere alla sottoscrizione di Azure nel [portale](https://portal.azure.com).

2. Fare clic sulla voce di menu **Tutte le risorse** sul lato sinistro e passare all'*account di Mappe di Azure*.

3. Dopo aver aperto l'account di Mappe fare clic sul menu **Metriche** a sinistra.

4. Nel riquadro **metrica** scegliere una delle opzioni seguenti:

   1. **Disponibilità**, che illustra la disponibilità *Media* dell'API in un periodo di tempo.
   2. **Utilizzo**, che illustra il funzionamento del *Conteggio* per l'account.

      ![Riquadro metriche di utilizzo di mappe di Azure](media/how-to-view-api-usage/portal-metrics.png)

5. È possibile in seguito selezionare l'*Intervallo di tempo* facendo clic su **Last 24 hours (Automatic)** (Ultime 24 ore - Automatico). Per impostazione predefinita, l'intervallo di tempo è impostato su 24 ore. Dopo aver fatto clic, verranno visualizzati tutti gli intervalli di tempo selezionabili. È possibile selezionare *Granularità temporale* e scegliere di visualizzare l'ora come *locale* oppure *GMT* nello stesso elenco a discesa. Fare clic su **Applica**.

    ![Intervallo di tempo delle metriche di Maps di Azure](media/how-to-view-api-usage/time-range.png)

6. Una volta aggiunta la metrica, è possibile **aggiungere un filtro** dalle proprietà rilevanti per tale metrica. Quindi, selezionare il valore della proprietà che si desidera visualizzare nel grafico.

    ![Filtro metriche di utilizzo di mappe di Azure](media/how-to-view-api-usage/filter.png)

7. È anche possibile **applicare la suddivisione** per la metrica in base alla proprietà della metrica selezionata. Consente di suddividere il grafico in più grafici, per ogni valore della proprietà. Nell'immagine seguente il colore di ogni grafico corrisponde al valore della proprietà visualizzato nella parte inferiore del grafico.

    ![Suddivisione delle metriche di utilizzo di Azure Maps](media/how-to-view-api-usage/splitting.png)

8. È anche possibile osservare più metriche sullo stesso grafico, semplicemente facendo clic sul pulsante **Aggiungi metrica** nella parte superiore.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle API di Mappe di Azure per le quali si vuole tenere traccia dell'utilizzo:
> [!div class="nextstepaction"] 
> [Procedure per l'SDK Web di Azure Maps](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Procedure di Azure Maps Android SDK](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentazione dell'API REST di Mappe di Azure](/rest/api/maps)