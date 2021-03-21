---
title: Aggiungere un'origine evento di hub eventi-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come aggiungere un'origine evento di hub eventi di Azure all'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461135"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Aggiungere un'origine evento dell'hub eventi all'ambiente Azure Time Series Insights

Questo articolo descrive come usare il portale di Azure per aggiungere all'ambiente Azure Time Series Insights un'origine evento che legge dati da Hub eventi di Azure.

> [!NOTE]
> I passaggi descritti in questo articolo si applicano sia agli ambienti Azure Time Series Insights generazione 1 che Azure Time Series Insights generazione 2.

## <a name="prerequisites"></a>Prerequisiti

- Creare un ambiente di Azure Time Series Insights come descritto in [creare un ambiente Azure Time Series Insights](./tutorial-set-up-environment.md).
- Creare un hub eventi. Leggere [creare uno spazio dei nomi di hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
- L'hub eventi deve ricevere eventi messaggio attivi. Informazioni su come [inviare eventi a hub eventi di Azure usando il .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Creare un gruppo di consumer dedicato nell'hub eventi da cui l'ambiente di Azure Time Series Insights può utilizzare. Ogni origine evento Azure Time Series Insights deve avere un proprio gruppo di consumer dedicato che non è condiviso con altri consumer. Se più lettori utilizzano eventi dello stesso gruppo di consumer, è probabile che tutti i lettori mostrino errori. È previsto un limite di 20 gruppi di consumer per hub eventi. Per informazioni dettagliate, vedere la [Guida alla programmazione di hub eventi](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Aggiungere un gruppo di consumer all'hub eventi

Le applicazioni usano gruppi di consumer per estrarre i dati da Hub eventi di Azure. Per leggere in modo affidabile i dati dall'hub eventi, fornire un gruppo di consumer dedicato usato solo da questo ambiente Azure Time Series Insights.

Per aggiungere un nuovo gruppo di consumer nell'hub eventi:

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'istanza di hub eventi dal riquadro **Panoramica** dello spazio dei nomi dell'hub eventi. Selezionare **entità > Hub eventi** o trovare l'istanza in **nome**.

    [![Aprire lo spazio dei nomi dell'hub eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Nell'istanza dell'hub eventi selezionare **entità > gruppi di consumer**. Quindi selezionare **+ gruppo di consumer** per aggiungere un nuovo gruppo di consumer.

   [![Hub eventi: aggiungere un gruppo di consumer](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   In caso contrario, selezionare un gruppo di consumer esistente e passare alla sezione successiva.

1. Nella pagina **Gruppi di consumer** immettere un nuovo valore univoco per **Nome**.  Utilizzare questo stesso nome quando si crea una nuova origine evento nell'ambiente Azure Time Series Insights.

1. Selezionare **Crea**.

## <a name="add-a-new-event-source"></a>Aggiungere una nuova origine evento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente di Azure Time Series Insights esistente. Nel menu a sinistra selezionare **tutte le risorse** e quindi selezionare l'ambiente Azure Time Series Insights.

1. Selezionare **origini eventi**, quindi selezionare **Aggiungi**.

   [![In Origini eventi selezionare il pulsante Aggiungi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Immettere un valore per **nome origine evento** univoco per questo ambiente Azure Time Series Insights, ad esempio `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. In **Origine** selezionare **Hub eventi**.

1. Selezionare i valori appropriati in **Opzione di importazione**:

   - Se in una delle sottoscrizioni è presente un hub eventi esistente, selezionare **Usare un hub eventi dalle sottoscrizioni disponibili**. Questa opzione rappresenta l'approccio più semplice.

     [![Selezionare un'opzione di importazione dell'origine evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - La tabella seguente descrive le proprietà obbligatorie per l'opzione **Usare un hub eventi dalle sottoscrizione disponibili**:

       [![Dettagli relativi a sottoscrizione e hub eventi](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Proprietà | Descrizione |
       | --- | --- |
       | Subscription | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi. |
       | Nome dell'hub eventi | Nome dell'istanza di hub eventi desiderata. |
       | Valore criterio Hub eventi | Selezionare i criteri di accesso condiviso desiderati. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Pre-popolato dal valore dei criteri dell'hub eventi selezionato. |

   - Se l'hub eventi è esterno alle sottoscrizioni o se si vogliono scegliere opzioni avanzate, selezionare **Specificare le impostazioni dell'hub eventi manualmente**.

       La tabella seguente descrive le proprietà obbligatorie se si seleziona l'opzione **Specificare le impostazioni dell'hub eventi manualmente**:

       | Proprietà | Descrizione |
       | --- | --- |
       | ID sottoscrizione | Sottoscrizione a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Resource group | Il gruppo di risorse a cui appartiene l'istanza e lo spazio dei nomi dell'hub eventi desiderato. |
       | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi a cui appartiene l'istanza dell'hub eventi. |
       | Nome dell'hub eventi | Nome dell'istanza di hub eventi desiderata. |
       | Valore criterio Hub eventi | Selezionare i criteri di accesso condiviso desiderati. È possibile creare i criteri di accesso condiviso nella scheda **Configura** dell'hub eventi. Ogni criterio di accesso condiviso ha un nome, le autorizzazioni impostate e le chiavi di accesso. I criteri di accesso condiviso per l'origine evento *devono* disporre di autorizzazioni di **lettura**. |
       | Chiave criteri hub eventi | Chiave di accesso condiviso usata per autenticare l'accesso allo spazio dei nomi del bus di servizio di Azure. Immettere qui la chiave primaria o secondaria. |

   - Entrambe le opzioni condividono le opzioni di configurazione seguenti:

       | Proprietà | Descrizione |
       | --- | --- |
       | Gruppo di consumer dell'hub eventi | Gruppo di consumer per la lettura degli eventi dall'hub eventi. È consigliabile usare un gruppo di consumer dedicato per la propria origine evento. |
       | Formato di serializzazione eventi | Attualmente JSON è l'unico formato di serializzazione disponibile. I messaggi di evento devono essere in questo formato o non è possibile leggere i dati. |
       | Nome della proprietà Timestamp | Per determinare questo valore, è necessario comprendere il formato dei dati del messaggio inviati all'hub eventi. Questo valore è il **nome** della proprietà evento specifica nei dati del messaggio che si vuole usare come timestamp dell'evento. Il valore fa distinzione tra maiuscole e minuscole. Se lasciato vuoto, come timestamp dell'evento viene usato il **tempo di accodamento dell'evento** nell'origine evento. |

1. Aggiungere il nome del gruppo di consumer Azure Time Series Insights dedicato aggiunto all'hub eventi.

1. Selezionare **Crea**.

   Dopo la creazione dell'origine evento, Azure Time Series Insights avvia automaticamente il flusso dei dati nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

- [Definire criteri di accesso ai dati](./concepts-access-policies.md) per proteggere i dati.

- [Inviare eventi](time-series-insights-send-events.md) all'origine evento.

- Accedere all'ambiente in [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).
