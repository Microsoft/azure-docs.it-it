---
title: Come aggiungere un'origine evento dell'hub Internet delle cose-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come aggiungere un'origine evento dell'hub Internet all'ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a0a2f703d9224b8b9dd77c80b2b6a7faee70f5bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81538104"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Aggiungere un'origine evento di un hub IoT in un ambiente Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati dall'hub IoT.

> [!NOTE]
> Le istruzioni in questo articolo si applicano sia agli ambienti di disponibilità generale che a quelli di anteprima di Azure Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

* Creare un [ambiente Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Creare un [hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md).
* L'hub IoT deve ricevere eventi messaggio attivi.
* Creare un gruppo di consumer dedicato nell'hub IoT da usare dall'ambiente Time Series Insights. Tutte le origini evento di Time Series Insights devono avere un proprio gruppo di consumer dedicato non condiviso con altri consumer. Se più lettori utilizzano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori mostrino errori. Per informazioni dettagliate, vedere la guida per gli [sviluppatori dell'hub Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Aggiungere un gruppo di consumer dell'hub IoT

Le applicazioni usano gruppi di consumer per estrarre i dati dall'hub IoT di Azure. Per leggere in modo affidabile i dati dall'hub Internet delle cose, fornire un gruppo di consumer dedicato usato solo da questo ambiente Time Series Insights.

Per aggiungere un nuovo gruppo di consumer all'hub IoT:

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'hub Internet.

1. In **Impostazioni**selezionare **endpoint predefiniti**, quindi selezionare l'endpoint **eventi** .

   [![Nella pagina Endpoint predefiniti selezionare il pulsante Eventi.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. In **Gruppi di consumer** immettere un nome univoco per il gruppo di consumer. Usare questo stesso nome al momento di creare una nuova origine evento nell'ambiente Time Series Insights.

1. Selezionare **Salva**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **Tutte le risorse**. Selezionare l'ambiente Time Series Insights.

1. In **Impostazioni**selezionare **origini eventi**, quindi selezionare **Aggiungi**.

   [![Selezionare le origini eventi e quindi selezionare il pulsante Aggiungi.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. Nel riquadro **Nuova origine evento** per univoco per **Nome dell'origine evento** immettere un nome univoco per questo ambiente Time Series Insights, ad esempio **event-stream**.

1. Per **Origine** selezionare **Hub IoT**.

1. Selezionare un valore per **Opzione di importazione**:

   * Se per una delle sottoscrizioni disponibili esiste già un hub IoT, scegliere **Usare l'hub IoT dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.
   
     [![Selezionare le opzioni nel riquadro Nuova origine evento.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Usare l'hub IoT dalle sottoscrizioni disponibili**:

       [![Riquadro Nuova origine evento - Proprietà da impostare nell'opzione Usare l'hub IoT dalle sottoscrizioni disponibili](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Proprietà | Descrizione |
       | --- | --- |
       | Subscription | Sottoscrizione a cui appartiene l'hub Internet. |
       | Nome dell'hub IoT | Nome dell'hub di Internet delle cose selezionato. |
       | Nome criterio dell'hub IoT | Selezionare i criteri di accesso condiviso. È possibile trovare i criteri di accesso condiviso nella scheda Impostazioni hub Internet. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**. |
       | Chiave criteri hub IoT | La chiave è prepopolata. |

    * Scegliere **Specificare le impostazioni dell'hub IoT manualmente** se l'hub IoT è esterno alle sottoscrizioni o per scegliere opzioni avanzate.

      La tabella seguente illustra le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub IoT manualmente**:

       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | Sottoscrizione a cui appartiene l'hub Internet. |
       | Resource group | Il nome del gruppo di risorse in cui è stato creato questo hub IoT. |
       | Nome dell'hub IoT | Il nome dell'hub IoT. Al momento della creazione, è stato immesso un nome specifico per l'hub IoT. |
       | Nome criterio dell'hub IoT | Criteri di accesso condiviso. È possibile creare i criteri di accesso condiviso nella scheda Impostazioni hub Internet. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. Il criterio di accesso condiviso per l'origine evento *deve* avere le autorizzazioni per la **connessione al servizio**. |
       | Chiave criteri hub IoT | La chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria. |

    * Entrambe le opzioni condividono le opzioni di configurazione seguenti:

       | Proprietà | Descrizione |
       | --- | --- |
       | Gruppo di consumer dell'hub IoT | Il gruppo di consumer per la lettura degli eventi dall'hub IoT. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento. |
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi relativi agli eventi devono essere in questo formato. In caso contrario, non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviato all'hub IoT. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |


1. Aggiungere il nome del gruppo di consumer Time Series Insights dedicato aggiunto all'hub IoT.

1. Selezionare **Crea**.

1. Dopo la creazione dell'origine evento, Time Series Insights inizia automaticamente a trasmettere i dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

* [Definire criteri di accesso ai dati](time-series-insights-data-access.md) per proteggere i dati.

* [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

* Accedere all'ambiente in [Time Series Insights Explorer](https://insights.timeseries.azure.com).
