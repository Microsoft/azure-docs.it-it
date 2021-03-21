---
title: JobConfig.jsdi analisi di flusso di Azure sui campi
description: Questo articolo elenca i campi supportati per la JobConfig.jsdi analisi di flusso di Azure nel file usato per creare processi in Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020486"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>JobConfig.jsdi analisi di flusso di Azure sui campi

I campi seguenti sono supportati nell' *JobConfig.jssu* file usato per [creare un processo di analisi di flusso di Azure usando Visual Studio Code](quick-create-visual-studio-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nome|Tipo|Obbligatoria|valore|
|----|----|--------|-----|
|Impostazioni locali|stringa|No|Impostazioni locali dei dati del processo di analisi di flusso. Il valore deve corrispondere al nome di un oggetto supportato. Se non è specificato alcun valore, il valore predefinito è "en-US".|
|OutputErrorPolicy|stringa|No|Indica i criteri da applicare agli eventi che arrivano all'output e che non possono essere scritti nella risorsa di archiviazione esterna a causa di un formato non valido (valori di colonna mancanti, valori di colonna di tipo o dimensione errata). -Arresta o Elimina|
|EventsLateArrivalMaxDelayInSeconds|numero intero|No|Ritardo massimo tollerabile in secondi in cui potrebbero essere inclusi gli eventi che arrivano in ritardo. L'intervallo supportato è compreso tra-1 e 1814399 (20.23:59:59 giorni) e-1 viene usato per specificare un'attesa indefinita. Se la proprietà è assente, viene interpretato come un valore pari a-1.|
|EventsOutOfOrderMaxDelayInSeconds|numero intero|No|Il ritardo massimo tollerabile in secondi per cui è possibile modificare gli eventi non ordinati in modo da essere nuovamente nell'ordine.|
|EventsOutOfOrderPolicy|stringa|No|Indica i criteri da applicare agli eventi che non arrivano in ordine nel flusso di eventi di input. -Regola o Elimina|
|Streaming|numero intero|Sì|Specifica il numero di unità di streaming utilizzate dal processo di streaming.|
|CompatibilityLevel|stringa|No|Controlla alcuni comportamenti di runtime del processo di streaming. -I valori accettabili sono "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|boolean|No|Impostare true per consentire a questo processo di comunicare con altri servizi di Azure usando un'identità del Azure Active Directory gestito.|
|GlobalStorage. AccountName|stringa|No|L'account di archiviazione globale viene usato per archiviare il contenuto correlato al processo di analisi di flusso, ad esempio snapshot dei dati di riferimento SQL.|
|GlobalStorage. AccountKey|stringa|No|Chiave corrispondente per l'account di archiviazione globale.|
|DataSourceCredentialDomain|stringa|No|Proprietà riservata per l'archiviazione locale delle credenziali.|
|ScriptType|string|Sì|Proprietà riservata per indicare il tipo del file di origine. Il valore accettabile è "JobConfig" per JobConfig.json.|
|Tag|Coppie chiave-valore JSON|No|I tag sono coppie nome-valore che consentono di classificare le risorse e visualizzare dati di fatturazione consolidati tramite l'applicazione dello stesso tag a più risorse e gruppi di risorse. I nomi di tag non fanno distinzione tra maiuscole e minuscole e i valori dei tag fanno distinzione tra maiuscole|

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)
* [Eseguire test locali delle query di Analisi di flusso con dati di esempio con Visual Studio Code](visual-studio-code-local-run.md)
* [Testare le query di analisi di flusso in locale rispetto all'input del flusso Live usando Visual Studio Code](visual-studio-code-local-run-live-input.md) 
* [Distribuire un processo di analisi di flusso di Azure tramite ci/CD NPM Package](./cicd-overview.md)