---
title: 'Emulatore Azure Cosmos: download e note sulla versione'
description: Ottenere le note sulla versione dell'emulatore Azure Cosmos per diverse versioni e le informazioni sul download.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: ab31daafe496f1aaad2bbc8075b2514d9e57e997
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84687175"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulatore Azure Cosmos - Note sulla versione e informazioni di download

Questo articolo include le note sulla versione dell'emulatore Azure Cosmos con un elenco di aggiornamenti delle funzionalità apportati in ogni versione. Inoltre, include la versione più recente dell'emulatore disponibile per il download.

## <a name="download"></a>Download

| | |
|---------|---------|
|**Download MSI**|[Area download Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Operazioni preliminari**|[Sviluppare in locale con l'emulatore Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Note sulla versione

### <a name="2111"></a>2.11.1

- In questa versione sono stati corretti un paio di bug relativi a Esplora dati dell'emulatore. In alcuni casi, quando si usa Esplora dati dell'emulatore tramite un Web browser, non è possibile connettersi all'endpoint dell'emulatore Cosmos e a tutte le azioni correlate. Ad esempio la creazione di un database o di un contenitore restituirà un errore. Il secondo problema risolto è relativo alla creazione di un elemento da un file JSON usando l'azione di caricamento di Esplora dati.

### <a name="2110"></a>2.11.0

- Questa versione introduce il supporto per la velocità effettiva con provisioning a scalabilità automatica. Queste nuove funzionalità includono la possibilità di impostare un livello massimo personalizzato di velocità effettiva con provisioning in unità richiesta (UR/sec) e di abilitare la scalabilità automatica su database e contenitori esistenti, oltre a offrire il supporto a livello di codice tramite Azure Cosmos DB SDK.
- Correzione di un errore per cui durante l'esecuzione di query su una quantità elevata di documenti (oltre 1 GB), l'emulatore genera un errore con codice di stato interno 500.

### <a name="292"></a>2.9.2

- Questa versione corregge un bug e abilita al tempo stesso il supporto per l'endpoint MongoDb versione 3.2. Aggiunge inoltre il supporto per la generazione di tracce ETL a scopo di risoluzione dei problemi usando WPR invece di LOGMAN.

### <a name="291"></a>2.9.1

- Questa versione corregge alcuni problemi del supporto per l'API di query e ripristina la compatibilità con i sistemi operativi precedenti, ad esempio Windows Server 2012.

### <a name="290"></a>2.9.0

- In questa versione è stata aggiunta l'opzione per impostare la coerenza del prefisso e aumentare i limiti massimi per utenti e autorizzazioni.

### <a name="272"></a>2.7.2

- Questa versione aggiunge il supporto per il server MongoDB versione 3.6 all'emulatore Cosmos. Per avviare un endpoint MongoDB destinato alla versione 3.6 del servizio, avviare l'emulatore dalla riga di comando con privilegi di amministratore con l'opzione "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Questa versione corregge una regressione che impedisce agli utenti di eseguire query sull'account API SQL dall'emulatore quando vengono usati client basati su .NET Core o x86.

### <a name="246"></a>2.4.6

- Questa versione offre le stesse funzionalità incluse nella versione di luglio 2019 del servizio Azure Cosmos, con le eccezioni indicate in [Sviluppare in locale con l'emulatore Azure Cosmos](local-emulator.md). Vengono inoltre corretti diversi bug correlati all'arresto dell'emulatore quando viene richiamato tramite la riga di comando e gli override degli indirizzi IP interni per i client SDK che usano la connettività in modalità diretta.

### <a name="243"></a>2.4.3

- Avvio del servizio MongoDB disattivato per impostazione predefinita. Per impostazione predefinita è abilitato solo l'endpoint SQL. L'utente deve avviare l'endpoint manualmente usando l'opzione "/EnableMongoDbEndpoint" della riga di comando dell'emulatore. Adesso è come tutti gli altri endpoint di servizio, ad esempio Gremlin, Cassandra e Table.
- Risolto un bug nell'emulatore quando si inizia con "/AllowNetworkAccess" in cui gli endpoint Gremlin, Cassandra e Table non gestiscono correttamente le richieste provenienti da client esterni.
- Aggiungere porte di connessione diretta alle impostazioni delle regole del Firewall.

### <a name="240"></a>2.4.0

- Risolto un problema con l'emulatore relativo al mancato avvio se nel computer host sono presenti app di monitoraggio di rete, come Pulse Client.
