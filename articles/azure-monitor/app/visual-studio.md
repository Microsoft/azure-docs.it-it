---
title: Eseguire il debug in Visual Studio con applicazione Azure Insights
description: Diagnostica e analisi delle prestazioni delle app Web durante il debug e nell'ambiente di produzione.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2507dbf7bb8294c949f434d5fa96ccc0af9a7eb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563539"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Eseguire il debug delle applicazioni con Azure Application Insights in Visual Studio
In Visual Studio 2015 e versioni successive è possibile analizzare le prestazioni e diagnosticare i problemi nell'app Web ASP.NET sia durante il debug che nell'ambiente di produzione, usando i dati di telemetria di [Azure Application Insights](./app-insights-overview.md).

Se l'app web ASP.NET è stata creata con Visual Studio 2017 o versioni successive, include già Application Insights SDK. In caso contrario, se non è ancora stato fatto, [aggiungere Application Insights all'app](./asp-net.md).

Per monitorare l'app quando è in produzione, in genere si visualizzano i dati di telemetria di Application Insights nel [portale di Azure](https://portal.azure.com), che permette di impostare gli avvisi e applicare strumenti di monitoraggio efficaci. Per il debug, invece, è anche possibile cercare e analizzare i dati di telemetria in Visual Studio. È possibile usare Visual Studio per analizzare i dati di telemetria sia dal sito di produzione che dalle esecuzioni di debug nel computer di sviluppo. Nel secondo caso, è possibile analizzare le esecuzioni di debug anche se non è ancora stato configurato l'SDK per l'invio dei dati di telemetria al portale di Azure. 

## <a name="debug-your-project"></a><a name="run"></a> Eseguire il debug del progetto
Per eseguire l'app Web in modalità di debug locale, premere F5. Aprire pagine diverse per generare alcuni dati di telemetria.

In Visual Studio viene visualizzato un conteggio degli eventi registrati dal modulo di Application Insights nel progetto.

![In Visual Studio il pulsante Application Insights viene visualizzato durante il debug.](./media/visual-studio/appinsights-09eventcount.png)

Fare clic su questo pulsante per cercare nei dati di telemetria. 

## <a name="application-insights-search"></a>Ricerca in Application Insights
La finestra di ricerca di Application Insights mostra gli eventi che sono stati registrati. Se è stato eseguito l'accesso ad Azure durante la configurazione di Application Insights, è possibile cercare gli stessi eventi nel portale di Azure.

![Fare clic con il pulsante destro del mouse sul progetto e scegliere Application Insights, Cerca.](./media/visual-studio/34.png)

> [!NOTE] 
> Dopo aver selezionato o deselezionato i filtri, fare clic sul pulsante di ricerca alla fine del campo di ricerca di testo.
>

La ricerca di testo libero funziona in tutti i campi degli eventi. Ad esempio, è possibile cercare parte dell'URL di una pagina, il valore di una proprietà, come la città del client, o parole specifiche in un log di traccia.

Fare clic su qualsiasi evento per visualizzarne le proprietà dettagliate.

Per le richieste all'app Web, è possibile fare clic per visualizzare il codice.

![In Dettagli richiesta fare clic per visualizzare il codice](./media/visual-studio/31.png)

È anche possibile aprire gli elementi correlati per diagnosticare le richieste non riuscite o le eccezioni.

![In Dettagli richiesta scorrere fino agli elementi correlati](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Visualizzare le eccezioni e le richieste non riuscite
I report delle eccezioni vengono visualizzati nella finestra di ricerca. In alcuni tipi di applicazioni ASP.NET meno recenti è necessario [configurare il monitoraggio delle eccezioni](./asp-net-exceptions.md) per visualizzare le eccezioni gestite dal framework.

Fare clic su un'eccezione per ottenere un'analisi dello stack. Se il codice dell'app è aperto in Visual Studio, è possibile fare clic nell'analisi dello stack per visualizzare la relativa riga del codice.

![Screenshot mostra l'oggetto about in una traccia dello stack.](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Visualizzare i riepiloghi delle richieste ed eccezioni nel codice
Nella riga CodeLens sopra ogni metodo del gestore viene visualizzato un conteggio delle richieste e delle eccezioni registrate da Application Insights nelle ultime 24 ore.

![Screenshot mostra un'eccezione in una finestra di dialogo di contesto.](./media/visual-studio/21.png)

> [!NOTE] 
> CodeLens mostra i dati di Application Insights unicamente se l'[app è configurata per l'invio dei dati di telemetria al portale di Application Insights](./asp-net.md).
>

[Altre informazioni su Application Insights in CodeLens](./visual-studio-codelens.md)

## <a name="local-monitoring"></a>Monitoraggio locale
(Da Visual Studio 2015 Update 2) Se l'SDK non è stato configurato per l'invio della telemetria al portale di Application Insights (e quindi non è presente nessuna chiave di strumentazione in ApplicationInsights.config), la finestra di diagnostica visualizza la telemetria dalla sessione di debug più recente. 

Questo è consigliabile se è già stata pubblicata una versione precedente dell'app. Si vuole però evitare di combinare la telemetria delle sessioni di debug con la telemetria nel portale di Application Insights dell'app pubblicata.

È utile anche se si vuole eseguire il debug di alcuni [dati di telemetria personalizzati](./api-custom-events-metrics.md) prima di inviarli al portale.

* *Inizialmente, Application Insights è stato interamente configurato per inviare i dati di telemetria al portale. Ora però si vuole fare in modo che i dati di telemetria vengano visualizzati solo in Visual Studio.*
  
  * Nelle impostazioni della finestra di ricerca è disponibile un'opzione per cercare la diagnostica locale anche se l'app invia la telemetria al portale.
  * Per arrestare l'invio dei dati di telemetria al portale, impostare come commento la riga `<instrumentationkey>...` da ApplicationInsights.config. Quando si è pronti per inviare di nuovo i dati di telemetria al portale, rimuovere il commento.


## <a name="next-steps"></a>Passaggi successivi

 * **[Uso del portale di Application Insights](./overview-dashboard.md)**. Visualizzare i dashboard, strumenti avanzati di diagnostica e di analisi, avvisi, una mappa attiva delle dipendenze dell'applicazione e i dati di telemetria esportati. 

