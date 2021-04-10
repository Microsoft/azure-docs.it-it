---
title: Strumenti per l'inserimento di dati
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e le utilità di inserimento dati preinstallati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519372"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Strumenti di inserimento dati della macchina virtuale per data science

Come uno dei primi passaggi tecnici di un progetto data science o intelligenza artificiale, è necessario identificare i set di dati da usare e portarli nell'ambiente di analisi. Il Data Science Virtual Machine (DSVM) fornisce gli strumenti e le librerie per portare i dati da origini diverse all'archiviazione dei dati analitici localmente in DSVM o in una piattaforma di dati nel cloud o in locale.

Ecco alcuni strumenti di spostamento dei dati disponibili in DSVM.

## <a name="adlcopy"></a>AdlCopy

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per copiare i dati dall'archiviazione BLOB di Azure in Azure Data Lake Store. Consente anche di copiare dati tra due account di Azure Data Lake Store.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di più BLOB dall'archiviazione BLOB di Azure in Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e digitare `adlcopy` per visualizzare la guida.    |
| Collegamenti agli esempi      | [Con AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| Strumenti correlati in DSVM      | AzCopy, interfaccia della riga di comando di Azure     |

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento di gestione per Azure. Contiene anche i verbi di comando per spostare i dati dalle piattaforme dati di Azure, ad esempio archiviazione BLOB di Azure e Azure Data Lake Store.     |
| Versioni di DSVM supportate      | Windows, Linux     |
| Usi tipici      | Importazione ed esportazione di dati da e verso archiviazione di Azure e Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e digitare `az` per visualizzare la guida.    |
| Collegamenti agli esempi      | [Utilizzare l'interfaccia della riga di comando di Azure](/cli/azure)     |
| Strumenti correlati in DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Strumento per copiare dati da e verso file locali, archiviazione BLOB di Azure, file e tabelle.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Copia di file nell'archivio BLOB di Azure e copia di BLOB tra account.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e digitare `azcopy` per visualizzare la guida.    |
| Collegamenti agli esempi      | [AzCopy in Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| Strumenti correlati in DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Utilità di migrazione dati a Azure Cosmos DB

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Strumento per importare dati da diverse origini in Azure Cosmos DB, un database NoSQL nel cloud. Queste origini includono file JSON, file CSV, SQL, MongoDB, archiviazione tabelle di Azure, Amazon DynamoDB e raccolte di API SQL Azure Cosmos DB.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di file da una macchina virtuale a CosmosDB, importazione di dati da archiviazione tabelle di Azure a CosmosDB e importazione di dati da un database di Microsoft SQL Server a CosmosDB.     |
|  Come usarla o eseguirla?    |   Per utilizzare la versione della riga di comando, aprire un prompt dei comandi e digitare `dt` . Per utilizzare lo strumento GUI, aprire un prompt dei comandi e digitare `dtui` .    |
| Collegamenti agli esempi      | [CosmosDB importare dati](../../cosmos-db/import-data.md)      |
| Strumenti correlati in DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Interfaccia utente grafica per l'interazione con i file archiviati nel cloud di Azure. |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione ed esportazione di dati da DSVM.    |
|  Come usarla o eseguirla?    | Cercare "Azure Storage Explorer" nel menu Start. |
| Collegamenti agli esempi      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Strumento di SQL Server per copiare i dati tra SQL Server e un file di dati.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di un file CSV in una tabella SQL Server e esportazione di una tabella SQL Server in un file.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e digitare `bcp` per visualizzare la guida.    |
| Collegamenti agli esempi      | [utilità bcp](/sql/tools/bcp-utility)      |
| Strumenti correlati in DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per montare un contenitore di archiviazione BLOB di Azure nel file system Linux.      |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      | Lettura e scrittura in BLOB in un contenitore.      |
|  Come usarlo ed eseguirlo    |   Eseguire _blobfuse_ in un terminale.    |
| Collegamenti agli esempi      | [blobfuse in GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Strumenti correlati in DSVM      | Interfaccia della riga di comando di Azure      |