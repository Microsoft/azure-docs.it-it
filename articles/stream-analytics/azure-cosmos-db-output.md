---
title: Azure Cosmos DB output di analisi di flusso di Azure
description: Questo articolo descrive come restituire i dati da analisi di flusso di Azure a Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016578"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB output di analisi di flusso di Azure

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) è un servizio di database distribuito a livello globale che offre scalabilità elastica illimitata in tutto il mondo, query avanzate e indicizzazione automatica su modelli di dati indipendenti dallo schema. Per informazioni sulle opzioni del contenitore di Cosmos DB per Analisi di flusso, vedere l'articolo [Analisi di flusso con Aure Cosmos DB come output](stream-analytics-documentdb-output.md).

L'output di Azure Cosmos DB da Stream Analytics non è attualmente disponibile nelle aree di Azure Cina 21Vianet e Germania (T-Systems International).

> [!Note]
> Analisi di flusso di Azure supporta solo la connessione a Azure Cosmos DB usando l'API SQL.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente.

Nella tabella seguente sono descritte le proprietà per la creazione di un output di Azure Cosmos DB.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Alias per fare riferimento a questo output nella query di Analisi di flusso di Azure. |
| Sink | Azure Cosmos DB. |
| Opzione di importazione | Scegliere **Selezionare Cosmos DB dalle sottoscrizioni correnti** o **Specificare le impostazioni di Cosmos DB manualmente**.
| Account ID | Nome o URI endpoint dell'account Azure Cosmos DB. |
| Chiave account | Chiave di accesso condiviso per l'account Azure Cosmos DB. |
| Database | Nome del database Azure Cosmos DB. |
| Nome contenitore | Nome del contenitore da usare, che deve esistere in Cosmos DB. Esempio:  <br /><ul><li> _ContenitorePersonale_: deve esistere un contenitore denominato "ContenitorePersonale".</li>|
| Document ID |Facoltativa. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.

## <a name="partitioning"></a>Partizionamento

La chiave di partizione è basata sulla clausola PARTITION BY nella query. Il numero di writer di output segue il partizionamento dell'input per le [query completamente parallele](stream-analytics-scale-jobs.md). Analisi di flusso converte la chiave di partizione di output Cosmos DB in una stringa. Se, ad esempio, si dispone di una chiave di partizione con un valore pari a 1 di tipo bigint, viene convertita in "1" di tipo String. Questa conversione viene sempre eseguita indipendentemente dal fatto che la proprietà Partition venga scritta Cosmos DB.

## <a name="output-batch-size"></a>Dimensione del batch di output

Per la dimensione massima dei messaggi, vedere [limiti Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). Le dimensioni batch e la frequenza di scrittura vengono modificate in modo dinamico in base alle risposte di Azure CosmosDB. Non sono presenti limiti predeterminati per Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)
