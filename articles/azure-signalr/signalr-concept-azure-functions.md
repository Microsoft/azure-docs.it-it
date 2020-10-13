---
title: Crea app in tempo reale-funzioni di Azure & servizio Azure SignalR
description: Informazioni su come sviluppare un'applicazione Web senza server in tempo reale con il servizio Azure SignalR nell'esempio riportato di seguito.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: cbb1fcf320a78f11045bf9627ffcc438af3e388a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74157614"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Crea app in tempo reale con funzioni di Azure e il servizio Azure SignalR

Poiché il servizio Azure SignalR e funzioni di Azure sono servizi completamente gestiti e altamente scalabili che consentono di concentrarsi sulla creazione di applicazioni anziché sulla gestione dell'infrastruttura, è comune usare i due servizi insieme per fornire comunicazioni in tempo reale in un ambiente senza [Server](https://azure.microsoft.com/solutions/serverless/) .

> [!NOTE]
> Informazioni su come usare SignalR e funzioni di Azure insieme nell'esercitazione interattiva [abilitare gli aggiornamenti automatici in un'applicazione Web usando funzioni di Azure e il servizio SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Integrare le comunicazioni in tempo reale con i servizi di Azure

Funzioni di Azure consente di scrivere codice in [diversi linguaggi](../azure-functions/supported-languages.md), tra cui JavaScript, Python, C# e Java, che vengono attivati ogni volta che si verificano eventi nel cloud. Esempi di questi eventi:

* Richieste dei webhook e HTTP
* Timer periodici
* Eventi dei servizi di Azure, ad esempio:
    - Griglia di eventi
    - Hub eventi
    - Bus di servizio
    - Feed di modifiche di Cosmos DB
    - Archiviazione: BLOB e code
    - Connettori di App per la logica, ad esempio Salesforce e SQL Server

Usando Funzioni di Azure per integrare questi eventi con il servizio Azure SignalR, si ha la possibilità di inviare una notifica a migliaia di client ogni volta che si verificano eventi.

Alcuni scenari comuni per la messaggistica serverless in tempo reale implementabili con Funzioni di Azure e il servizio SignalR includono:

* Visualizzare i dati di telemetria dei dispositivi IoT su una mappa o un dashboard in tempo reale
* Aggiornare i dati in un'applicazione quando i documenti vengono aggiornati in Cosmos DB
* Inviare notifiche in-app quando nuovi ordini vengono creati in Salesforce

## <a name="signalr-service-bindings-for-azure-functions"></a>Associazioni del servizio SignalR per Funzioni di Azure

Le associazioni del servizio SignalR per Funzioni di Azure consentono a un'app di Funzioni di Azure di pubblicare i messaggi ai client connessi al servizio SignalR. I client possono connettersi al servizio usando un client SDK di SignalR che è disponibile in .NET, JavaScript e Java e in altri linguaggi presto disponibili.

### <a name="an-example-scenario"></a>Scenario di esempio

Un esempio di come usare le associazioni del servizio SignalR è usare Funzioni di Azure per integrarlo con Azure Cosmos DB e il servizio SignalR per inviare i messaggi in tempo reale quando i nuovi eventi vengono visualizzati in un feed di modifiche di Cosmos DB.

![Cosmos DB, Funzioni di Azure, servizio SignalR](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Viene apportata una modifica in una raccolta di Cosmos DB
2. L'evento di modifica viene propagato al feed di modifiche di Cosmos DB
3. Funzioni di Azure viene attivato dall'evento di modifica tramite il trigger di Cosmos DB
4. L'associazione di output per il servizio SignalR pubblica un messaggio per il servizio SignalR
5. Il servizio SignalR pubblica il messaggio per tutti i client connessi

### <a name="authentication-and-users"></a>Autenticazione e utenti

Il servizio SignalR consente di trasmettere messaggi a tutti i client o solo a un sottoinsieme di client, ad esempio quelli che appartengono a un singolo utente. Le associazioni del servizio SignalR per Funzioni di Azure possono essere combinate con l'autenticazione del servizio app per autenticare gli utenti con i provider, ad esempio Azure Active Directory, Facebook e Twitter. È quindi possibile inviare messaggi direttamente a questi utenti autenticati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene fornita una panoramica su come usare Funzioni di Azure con il servizio SignalR per abilitare una vasta gamma di scenari di messaggistica serverless in tempo reale.

Per informazioni dettagliate su come usare funzioni di Azure e il servizio SignalR insieme, vedere le risorse seguenti:

* [Sviluppo e configurazione di funzioni di Azure con il servizio SignalR](signalr-concept-serverless-development-config.md)
* [Abilitare gli aggiornamenti automatici in un'applicazione Web con Funzioni di Azure e il Servizio SignalR](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

Per altre informazioni, seguire una di queste guide introduttive.

* [Guida introduttiva senza server del servizio Azure SignalR-C #](signalr-quickstart-azure-functions-csharp.md)
* [Guida introduttiva al servizio Azure SignalR serverless - JavaScript](signalr-quickstart-azure-functions-javascript.md)
