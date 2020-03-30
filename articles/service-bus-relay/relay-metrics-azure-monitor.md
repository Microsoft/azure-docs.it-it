---
title: 'Metriche di inoltro di Azure in Monitor di Azure : Documenti Microsoft'
description: Questo articolo fornisce informazioni su come usare Monitoraggio di Azure per monitorare lo stato dell'inoltro di Azure.This article provides information on how you can use Azure Monitor to monitor to state of Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273125"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay metrics in Azure Monitor 
Le metriche del servizio Inoltro di Azure indicano lo stato delle risorse nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse di inoltro, non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti poiché consentono di monitorare lo stato del servizio di inoltro di Azure. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

> [!IMPORTANT]
> Questo articolo riguarda solo la funzionalità Connessioni ibride di Inoltro di Azure e non il servizio Inoltro WCF. 

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche dal [portale di Azure](https://portal.azure.com) o usare le API di Monitoraggio di Azure (REST e .NET) e le soluzioni di analisi come Operations Management Suite e Hub eventi. Per altre informazioni, consultare [Monitoraggio dei dati raccolti da Monitoraggio di Azure](../azure-monitor/platform/data-platform.md).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo approccio viene configurato nelle [Impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md) in Monitoraggio di Azure.

## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e quindi fare clic su **Metriche**. 

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare.

## <a name="billing"></a>Fatturazione

L'uso delle metriche in Monitoraggio di Azure è attualmente gratuito durante la fase di anteprima. Se tuttavia si usano soluzioni aggiuntive per inserire i dati delle metriche, è possibile che la fatturazione venga effettuata da tali soluzioni. Ad esempio, la fatturazione viene effettuata da Archiviazione di Azure se i dati relativi alle metriche vengono archiviati in un account di Archiviazione di Azure. I log di Monitoraggio di Azure vengono inoltre fatturati dai log di Monitoraggio di Azure se si esegue il flusso dei dati delle metriche nei log di Monitoraggio di Azure per l'analisi avanzata.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche del servizio di inoltro di Azure è 1 minuto.

## <a name="connection-metrics"></a>Metriche di connessione

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Numero di connessioni riuscite del listener eseguite al servizio di inoltro di Azure in un periodo specificato. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-ClientError |Numero di errori del client nelle connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-ServerError |Numero di errori del server nelle connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Connessioni mittente - Operazione riuscita |Numero di connessioni riuscite del mittente eseguite in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-ClientError |Numero di errori del client nelle connessioni del mittente in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-ServerError |Numero di errori del server nelle connessioni del mittente in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-TotalRequests |Numero totale di connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-TotalRequests |Richieste di connessione eseguite dai mittenti in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ActiveConnections |Il numero di connessioni attive. Questo valore è un valore temporizzato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ActiveListeners |Numero di listener attivi. Questo valore è un valore temporizzato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerDisconnects |Numero di listener disconnessi in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Disconnessioni mittente |Numero di mittenti disconnessi in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="memory-usage-metrics"></a>Metriche di utilizzo della memoria

| Nome misurazione | Descrizione |
| ------------------- | ----------------- |
|BytesTransferred |Numero di byte trasferiti in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Il servizio di inoltro di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure. L'aggiunta di dimensioni alle metriche è facoltativa. Se non si aggiungono le dimensioni, le metriche vengono specificate a livello di spazio dei nomi. 

|Nome della dimensione|Descrizione|
| ------------------- | ----------------- |
|EntityName| Il servizio di inoltro di Azure supporta le entità di messaggistica nello spazio dei nomi.|

## <a name="next-steps"></a>Passaggi successivi

Vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




