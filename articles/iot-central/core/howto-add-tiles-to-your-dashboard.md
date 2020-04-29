---
title: Aggiungere riquadri al dashboard | Microsoft Docs
description: Come costruttore, informazioni su come configurare il dashboard predefinito dell'applicazione IoT Central di Azure.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310295"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **Dashboard** è la pagina che viene caricata quando gli utenti che hanno accesso all'applicazione passano all'URL dell'applicazione. Se l'applicazione è stata creata da uno dei **modelli di applicazione**, l'applicazione avrà un dashboard predefinito per l'avvio. Se l'applicazione è stata creata dal modello di applicazione dell' **applicazione legacy** , il dashboard sarà vuoto per l'avvio.

> [!NOTE]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali solo per l'utente o condivisi tra tutti gli utenti dell'applicazione. 

## <a name="add-tiles"></a>Aggiunta di riquadri

La schermata seguente mostra il dashboard in un'applicazione creata dal modello di **applicazione personalizzata** . Per personalizzare il dashboard predefinito per l'applicazione, fare clic su **modifica** nella parte superiore sinistra della pagina.

> [!div class="mx-imgBorder"]
> ![Dashboard per le applicazioni basate sul modello "esempio contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Selezionando **Edit (modifica** ) viene aperto il pannello libreria dashboard. La libreria contiene i riquadri e le primitive del dashboard che è possibile usare per personalizzare il dashboard.

> [!div class="mx-imgBorder"]
> ![Libreria dashboard](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Ad esempio, è possibile aggiungere un riquadro di **telemetria** per la temperatura corrente del dispositivo. A tale scopo, procedere nel seguente modo:
1. Selezionare un **modello di dispositivo**
1. Selezionare un' **istanza del dispositivo** per il dispositivo che si vuole visualizzare in un riquadro del dashboard. Verrà quindi visualizzato un elenco delle proprietà del dispositivo che è possibile usare nel riquadro.
1. Per creare il riquadro nel dashboard, fare clic su **temperatura** e trascinarlo nell'area del dashboard. È anche possibile fare clic sulla casella di controllo accanto a **temperatura** e fare clic su **combina**. Lo screenshot seguente mostra la selezione di un modello di dispositivo e di un'istanza del dispositivo e la creazione di un riquadro di telemetria della temperatura nel dashboard.
1. Selezionare **Save (Salva** ) in alto a sinistra per salvare il riquadro nel dashboard.

> [!div class="mx-imgBorder"]
> ![Modulo "Configure Device Details" (Configura dettagli dispositivo) con i dettagli per impostazioni e proprietà](media/howto-add-tiles-to-your-dashboard/device-details.png)

A questo punto, quando un operatore Visualizza il dashboard dell'applicazione predefinito, viene visualizzato il nuovo riquadro con la **temperatura** del dispositivo. Ogni riquadro ha un grafico, un grafico e così via preselezionato che verrà visualizzato quando viene creato il riquadro. Tuttavia, gli utenti possono scegliere di modificare e modificare questa visualizzazione. 

> [!div class="mx-imgBorder"]
> ![Scheda "Dashboard" con visualizzate le impostazioni e le proprietà per il riquadro](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Modifica riquadri

Per modificare un riquadro nel dashboard, fare prima clic su **modifica** nella parte superiore sinistra della pagina, che consente di aprire la modalità di modifica per il dashboard e tutti i relativi riquadri. 

> [!div class="mx-imgBorder"]
> ![Schermata del dashboard con modalità di modifica attivata per un riquadro selezionato](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Fare quindi clic sull'icona a forma di **ingranaggio** nell'angolo in alto a destra del riquadro che si vuole modificare. Qui è possibile modificare gli aspetti del riquadro, inclusi il titolo, la visualizzazione, l'aggregazione e così via.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di aggregazione affiancate](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

È anche possibile modificare la visualizzazione del grafico facendo clic sull'icona del **righello** sul riquadro.

> [!div class="mx-imgBorder"]
> ![Elenco a discesa per le impostazioni di visualizzazione dei riquadri](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipi di sezione

La tabella seguente riepiloga l'uso dei riquadri in Azure IoT Central:
 
| Riquadro | Dashboard | Descrizione
| ----------- | ------- | ------- |
| Contenuto | Dashboard dell'applicazione e del set di dispositivi |I riquadri supportati da Markdown sono riquadri selezionabili che visualizzano l'intestazione e il testo della descrizione. È anche possibile usare questo riquadro come riquadro di collegamento per consentire a un utente di passare a un URL correlato all'applicazione.|
| Immagine | Dashboard dell'applicazione e del set di dispositivi |I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. Usare un riquadro immagine per aggiungere grafica a un dashboard e, facoltativamente, consentire a un utente di passare a un URL pertinente per l'applicazione.|
| Label | Dashboard dell'applicazione |I riquadri delle etichette visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro etichetta per aggiungere informazioni rilevanti al dashboard, quali descrizioni, dettagli del contatto o guida.|
| Mappa | Dashboard dell'applicazione e del dispositivo |I riquadri mappa visualizzano la posizione di un dispositivo su una mappa. È anche possibile visualizzare fino a 100 punti della cronologia della posizione di un dispositivo. Ad esempio, è possibile visualizzare una route campionata in cui si è verificato un dispositivo nella settimana precedente.|
| Grafico a linee | Dashboard dell'applicazione e del dispositivo |I riquadri del grafico a linee visualizzano un grafico di misura aggregata per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee che mostra la temperatura e la pressione medie di un dispositivo nell'ultima ora.|
| Grafico a barre | Dashboard dell'applicazione e del dispositivo |Nei riquadri del grafico a barre viene visualizzato un grafico di misurazioni aggregate per un dispositivo per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre che mostra la temperatura e la pressione medie di un dispositivo nell'ultima ora.|
| Grafico a torta | Dashboard dell'applicazione e del set di dispositivi |Nei riquadri del grafico a torta viene visualizzato un grafico di misurazioni aggregate per un dispositivo per un periodo di tempo.|
| Mappa termica | Dashboard dell'applicazione e del set di dispositivi |I riquadri mappa termica visualizzano le informazioni sul set di dispositivi, rappresentate come colori.|
| Cronologia eventi | Dashboard dell'applicazione e del dispositivo |I riquadri della cronologia eventi visualizzano gli eventi per un dispositivo in un periodo di tempo. Ad esempio, è possibile usarlo per mostrare tutte le variazioni di temperatura per un dispositivo nell'ultima ora.|
| Cronologia dello stato | Dashboard dell'applicazione e del dispositivo |I riquadri cronologia stato visualizzano i valori di misurazione per un periodo di tempo. Ad esempio, è possibile usarlo per mostrare i valori di temperatura per un dispositivo nell'ultima ora.|
| KPI | Dashboard dell'applicazione e del dispositivo | I riquadri KPI visualizzano una telemetria aggregata o una misurazione dell'evento per un periodo di tempo. Ad esempio, è possibile usarlo per mostrare la temperatura massima raggiunta per un dispositivo nell'ultima ora.|
| Ultimo valore noto | Dashboard dell'applicazione e del dispositivo |Ultimi riquadri valore noti Visualizza il valore più recente per una misura di telemetria o di stato. Ad esempio, è possibile usare questo riquadro per visualizzare le misurazioni più recenti di temperatura, pressione e umidità per un dispositivo.|

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinita di Azure IoT Central, è possibile [apprendere come creare un dashboard personale](howto-create-personal-dashboards.md).
