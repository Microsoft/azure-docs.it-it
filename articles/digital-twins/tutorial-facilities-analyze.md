---
title: 'Esercitazione: Analizzare gli eventi in Time Series Insights - Gemelli digitali di Azure | Microsoft Docs'
description: Informazioni su come visualizzare e analizzare gli eventi dagli spazi di Gemelli digitali di Azure con Azure Time Series Insights seguendo i passaggi descritti in questa esercitazione.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933811"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Esercitazione: Visualizzare e analizzare gli eventi dei Gemelli digitali di Azure usando Time Series Insights

Dopo aver distribuito l'istanza di Gemelli digitali di Azure, avere effettuato il provisioning degli spazi e avere implementato una funzione personalizzata per il monitoraggio di condizioni specifiche, è possibile visualizzare gli eventi e i dati provenienti dagli spazi per identificare tendenze e anomalie.

Nella [prima esercitazione](tutorial-facilities-setup.md) è stato configurato il grafico spaziale di un edificio immaginario, con una stanza contenente sensori relativi a movimento, anidride carbonica e temperatura. Nella [seconda esercitazione](tutorial-facilities-udf.md) è stato effettuato il provisioning del grafico e di una funzione definita dall'utente. La funzione monitora i valori dei sensori e attiva le notifiche per condizioni appropriate, ovvero se la stanza è vuota e la temperatura e i livelli di anidride carbonica sono normali.

Questa esercitazione illustra come integrare le notifiche e i dati provenienti dall'installazione di Gemelli digitali di Azure on Azure Time Series Insights. È quindi possibile visualizzare i valori dei sensori nel tempo. È possibile cercare le tendenze, ad esempio la stanza più usata e l'orario di maggiore affollamento durante la giornata. È anche possibile rilevare le anomalie, ad esempio la stanza con aria più viziata o la più calda oppure un'area dell'edificio che invia valori di temperatura costantemente elevati, che indicano un guasto nell'impianto di condizionamento dell'aria.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Trasmettere dati con Hub eventi di Azure
> * Eseguire analisi con Time Series Insights

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che siano già state effettuate le attività di [configurazione](tutorial-facilities-setup.md) e [provisioning](tutorial-facilities-udf.md) dell'installazione di Gemelli digitali di Azure. Prima di procedere, assicurarsi di avere:

- Un [account Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'istanza di Gemelli digitali in esecuzione.
- Gli [esempi C# di Gemelli digitali](https://github.com/Azure-Samples/digital-twins-samples-csharp) scaricati ed estratti nel computer di lavoro.
- [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo per eseguire l'esempio. Eseguire `dotnet --version` per verificare se è installata la versione corretta.

> [!TIP]
> Se si esegue il provisioning di una nuova istanza, usare un nome istanza di Gemelli digitali univoco.

## <a name="stream-data-by-using-event-hubs"></a>Trasmettere dati con Hub eventi

Usare il servizio [Hub eventi](../event-hubs/event-hubs-about.md) per creare una pipeline per lo streaming dei dati. Questa sezione illustra come creare l'hub eventi da usare come connettore tra Gemelli digitali di Azure e le istanze di Time Series Insights.

### <a name="create-an-event-hub"></a>Creare un hub eventi

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Cercare e selezionare **Hub eventi**. Selezionare **Create** (Crea).

    [![Creare uno spazio dei nomi di Hub eventi](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. In **Nome** immettere un nome per lo spazio dei nomi di Hub eventi. Scegliere **Standard** per **Piano tariffario** e indicare **Sottoscrizione**, **Gruppo di risorse** usato per l'istanza di Gemelli digitali e **Località**. Selezionare **Create** (Crea).

1. Nella distribuzione dello spazio dei nomi di Hub eventi, selezionare il riquadro **Panoramica**, quindi selezionare **Vai alla risorsa**.

    [![Spazio dei nomi di Hub eventi dopo la distribuzione](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. Nel riquadro **Panoramica** dello spazio dei nomi di Hub eventi selezionare il pulsante **Hub eventi** nella parte superiore.
    [![Pulsante Hub eventi](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. In **Nome** immettere un nome per l'hub eventi e selezionare **Crea**.

   Una volta completata la distribuzione, l'hub eventi sarà presente nel riquadro **Hub eventi** dello spazio dei nomi di Hub eventi con lo stato **Attivo**. Selezionare l'hub eventi per aprire il relativo riquadro **Panoramica**.

1. Selezionare il pulsante **Gruppo di consumer** nella parte superiore e immettere un nome, ad esempio **tsievents** per il gruppo di consumer. Selezionare **Create** (Crea).

    [![Gruppo di consumer dell'hub eventi](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Una volta creato, il gruppo di consumer verrà visualizzato nell'elenco nella parte inferiore del riquadro **Panoramica** dell'hub eventi.

1. Aprire il riquadro **Criteri di accesso condivisi** per l'hub eventi e selezionare il pulsante **Aggiungi**. Immettere **ManageSend** come nome del criterio, assicurarsi che tutte le caselle di controllo siano selezionate e selezionare **Crea**.

    [![Stringhe di connessione dell'hub eventi](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Verificare che si stia creando un criterio di firma di accesso condiviso per l'istanza dell'hub eventi invece che per lo spazio dei nomi.

1. Aprire i criteri **ManageSend** creati e copiare i valori di **Stringa di connessione - chiave primaria** e **Stringa di connessione - chiave secondaria** in un file temporaneo. Questi valori saranno necessari per creare un endpoint per l'hub eventi nella sezione successiva.

### <a name="create-an-endpoint-for-the-event-hub"></a>Creare un endpoint per l'hub eventi

1. Nella finestra di comando assicurarsi di trovarsi nella cartella **occupancy-quickstart\src** dell'esempio di Gemelli digitali di Azure.

1. Aprire il file **actions\createEndpoints.yaml** nell'editor. Sostituire i contenuti con quanto segue:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Sostituire i segnaposto `Primary_connection_string_for_your_event_hub` con il valore di **Stringa di connessione - chiave primaria** per l'hub eventi. Assicurarsi che il formato della stringa di connessione sia il seguente:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Sostituire i segnaposto `Secondary_connection_string_for_your_event_hub` con il valore di **Stringa di connessione - chiave secondaria** per l'hub eventi. Assicurarsi che il formato della stringa di connessione sia il seguente: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Sostituire i segnaposto `Name_of_your_Event_Hub` con il nome dell'hub eventi.

    > [!IMPORTANT]
    > Immettere tutti i valori senza virgolette. Verificare che sia presente almeno uno spazio dopo i due punti nel file YAML. È anche possibile convalidare i contenuti del file YAML usando qualsiasi validator YAML online, come [questo strumento](https://onlineyamltools.com/validate-yaml).

1. Salvare e chiudere il file. Immettere il comando seguente nella finestra di comando e accedere con il proprio account Azure, quando richiesto.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Vengono creati due endpoint per l'hub eventi.

   [![Endpoint per Hub eventi](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Eseguire analisi con Time Series Insights

1. Nel riquadro sinistro del [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**. 

1. Cercare e selezionare una risorsa **Time Series Insights** con disponibilità generale (GA). Selezionare **Create** (Crea).

1. In **Nome** immettere un nome per l'istanza di Time Series Insights e quindi selezionare un'opzione in **Sottoscrizione**. In **Gruppo di risorse** selezionare il gruppo di risorse usato per l'istanza di Gemelli digitali e quindi selezionare un'opzione in **Località**. Selezionare **Avanti: Origine evento** o la scheda **Origine evento**.

    [![Selezioni per la creazione di un'istanza Time Series Insights](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. Nella scheda **Origine evento** immettere un **Nome**, selezionare **Hub eventi** per **Tipo di origine** e assicurarsi che gli altri valori siano selezionati correttamente per fare riferimento all'hub eventi creato. Selezionare **ManageSend** per **Nome criterio di accesso Hub eventi** e quindi selezionare il gruppo di consumer creato nella sezione precedente per **Gruppo di consumer dell'Hub eventi**. Selezionare **Rivedi e crea**.

    [![Selezioni per la creazione di un'origine evento](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. Nel riquadro **Rivedi e crea** riquadro, esaminare le informazioni immesse e selezionare **Crea**.

1. Nel riquadro di distribuzione selezionare la risorsa Time Series Insights creata. Si aprirà il riquadro **Panoramica** per l'ambiente Time Series Insights.

1. Selezionare il pulsante **Vai all'ambiente** nella parte superiore. Se viene visualizzato un avviso di accesso ai dati, aprire il riquadro **Criteri di accesso ai dati** per l'istanza di Time Series Insights, selezionare **Aggiungi**, selezionare **Collaboratore** come ruolo e selezionare l'utente appropriato.

1. Il pulsante **Vai all'ambiente** consente di visualizzare lo [strumento di esplorazione di Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Se non viene visualizzato alcun evento, simulare eventi dei dispositivi passando al progetto **device-connectivity** dell'esempio di Gemelli digitali ed eseguendo `dotnet run`.

1. Dopo che alcuni eventi simulati sono stati generati, tornare allo strumento di esplorazione di Time Series Insights e selezionare il pulsante di aggiornamento nella parte superiore. Verranno visualizzati i grafici analitici creati per i dati dei sensori simulati. 

    [![Grafico nello strumento di esplorazione di Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. Nello strumento di esplorazione di Time Series Insights è quindi possibile generare grafici e mappe termiche per diversi eventi e dati relativi a stanze, sensori e altre risorse. A sinistra, usare le caselle di riepilogo a discesa **MISURA** e **DIVIDI PER** in modo da creare le visualizzazioni personalizzate. 

   Selezionare, ad esempio, **Eventi** per **MISURA** e **DigitalTwins-SensorHardwareId** per **DIVIDI PER** in modo da generare una mappa termica per ognuno dei sensori. La mappa termica sarà simile all'immagine seguente:

   [![Mappa termica nello strumento di esplorazione di Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si non si vuole esplorare ulteriormente Gemelli digitali di Azure, è possibile eliminare le risorse create in questa esercitazione:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutte le risorse** nel menu a sinistra, selezionare il gruppo di risorse di Gemelli digitali e quindi fare clic su **Elimina**.

    > [!TIP]
    > Se si sono riscontrati problemi durante l'eliminazione dell'istanza di Gemelli digitali, è stato reso disponibile un aggiornamento del servizio con la correzione. Riprovare a eliminare l'istanza.

2. Se necessario, eliminare le applicazioni di esempio nel computer di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per altre informazioni sui grafici di intelligenza spaziale e sui modelli a oggetti in Gemelli digitali di Azure.

> [!div class="nextstepaction"]
> [Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale](concepts-objectmodel-spatialgraph.md)
