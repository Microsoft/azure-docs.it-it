---
title: Informazioni su Configurazione app di Azure
description: Leggere una panoramica del servizio Configurazione app di Azure. Informazioni sul motivo per potrebbe essere necessario usare Configurazione app e informazioni su come usarlo.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: f38b8de37e84cd8a65c5a5caadf8d3b6b71ce455
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88587073"
---
# <a name="what-is-azure-app-configuration"></a>Informazioni su Configurazione app di Azure

Configurazione app di Azure è un servizio per la gestione centralizzata delle impostazioni delle applicazioni e dei flag di funzionalità. I programmi moderni, specialmente quelli eseguiti nel cloud, hanno in genere molti componenti che sono distribuiti per natura. Disseminare le impostazioni di configurazione tra questi componenti può generare errori difficili da risolvere durante la distribuzione di un'applicazione. Configurazione app consente di archiviare tutte le impostazioni dell'applicazione e di proteggerne gli accessi in un'unica posizione.

## <a name="why-use-app-configuration"></a>Perché usare Configurazione app

Le applicazioni basate sul cloud vengono spesso eseguite in più macchine virtuali o contenitori in più aree e usano più servizi esterni. La creazione di un'applicazione affidabile e scalabile in un ambiente distribuito presenta una sfida significativa.

Diverse metodologie di programmazione consentono agli sviluppatori di far fronte alla crescente complessità legata alla compilazione di queste applicazioni. Ad esempio, [Twelve-Factor App](https://12factor.net/) illustra molti modelli di architettura e procedure consigliate ben testati per l'uso con le applicazioni cloud. Un consiglio fondamentale di questa guida è quello di separare la configurazione dal codice. Le impostazioni di configurazione di un'applicazione devono essere mantenute esterne al relativo eseguibile e lette dall'ambiente di runtime o da un'origine esterna.

Di seguito sono riportati esempi dei tipi di applicazione che traggono vantaggio dall'uso di Configurazione app, che può comunque essere usato da qualsiasi applicazione:

* Microservizi basati sul servizio Azure Kubernetes, sul servizio Service Fabric o su altre app aggiunte a contenitori distribuite in una o più aree geografiche
* App serverless, tra cui Funzioni di Azure o altre app di calcolo senza stato basate su eventi
* Pipeline di distribuzione continua

Configurazione app offre i vantaggi seguenti:

* Un servizio completamente gestito che può essere configurato in pochi minuti
* Mapping e rappresentazioni di chiavi flessibili
* Assegnazione di tag con le etichette
* Riproduzione temporizzata delle impostazioni
* Interfaccia utente dedicata per la gestione dei flag di funzionalità
* Confronto di due set di configurazioni su dimensioni personalizzate
* Sicurezza avanzata grazie alle identità gestite da Azure
* Crittografia di informazioni riservate inattive e in transito
* Integrazione nativa con i framework più diffusi

Configurazione app completa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), che viene usato per archiviare i segreti dell'applicazione. Configurazione app rende più semplice implementare gli scenari seguenti:

* Centralizzare la gestione e la distribuzione dei dati di configurazione gerarchici per ambienti e aree geografiche diversi
* Modificare in modo dinamico le impostazioni applicazione senza la necessità di ridistribuire o riavviare un'applicazione
* Controllare la disponibilità delle funzionalità in tempo reale

## <a name="use-app-configuration"></a>Usare Configurazione app

Il modo più facile di aggiungere un archivio di Configurazione app alla propria applicazione consiste nell'usare una libreria client fornita da Microsoft. I seguenti metodi sono disponibili per connettersi con l'applicazione, a seconda della lingua e del framework scelti

| Linguaggio di programmazione e framework | Come connettersi |
|---|---|
| .NET Core e ASP.NET Core | Provider di Configurazione app per .NET Core |
| .NET Framework e ASP.NET | Generatore di Configurazione app per .NET |
| Java Spring | Client di Configurazione app per Spring Cloud |
| Altro | API REST di Configurazione app |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido per ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Avvio rapido per .NET Core](./quickstart-dotnet-core-app.md)
* [Avvio rapido per .NET Framework](./quickstart-dotnet-app.md)
* [Guida di avvio rapido per Java in Funzioni di Azure](./quickstart-azure-functions-csharp.md)
* [Avvio rapido per Java Spring](./quickstart-java-spring-app.md)
* [Avvio rapido per i flag di funzionalità di ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Avvio rapido per i flag di funzionalità in Spring Boot](./quickstart-feature-flag-spring-boot.md)
