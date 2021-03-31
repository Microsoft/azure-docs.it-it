---
title: Impostare i parametri dei servizi collegati in Azure Data Factory
description: Informazioni su come impostare i parametri dei servizi collegati in Azure Data Factory e passare i valori dinamici in fase di esecuzione.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/18/2021
author: dcstwh
ms.author: weetok
ms.openlocfilehash: df26b77f37100ae41b26c013c57cccbfa0d7e205
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595584"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Impostare i parametri dei servizi collegati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È ora possibile impostare i parametri per un servizio collegato e passare i valori dinamici in fase di esecuzione. Se ad esempio si desidera connettersi a database diversi nello stesso server SQL logico, è ora possibile parametrizzare il nome del database nella definizione del servizio collegato. In questo modo si evita di dover creare un servizio collegato per ogni database nel server SQL logico. È possibile parametrizzare anche altre proprietà nella definizione del servizio collegato, ad esempio *Nome utente.*

È possibile usare l'interfaccia utente di Data Factory nell'portale di Azure o un'interfaccia di programmazione per parametrizzare i servizi collegati.

> [!TIP]
> È consigliabile di non impostare i parametri per le password o i segreti. Archiviare invece tutte le stringhe di connessione in Azure Key Vault e impostare i parametri per *Nome segreto*.

> [!Note]
> È presente un bug aperto per usare "-" nei nomi dei parametri, si consiglia di usare i nomi senza "-" fino a quando il bug non viene risolto.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Tipi di servizi collegati supportati

È possibile parametrizzare qualsiasi tipo di servizio collegato.
Quando si crea il servizio collegato nell'interfaccia utente, Data Factory fornisce un'esperienza di parametrizzazione predefinita per i seguenti tipi di servizi collegati. Nel pannello di creazione/modifica del servizio collegato è possibile trovare opzioni per i nuovi parametri e aggiungere contenuto dinamico.

- Amazon Redshift
- Amazon S3
- Archiviazione BLOB di Azure
- Azure Cosmos DB (API SQL)
- Azure Data Lake Storage Gen2
- Database di Azure per MySQL
- Azure Databricks
- Insieme di credenziali chiave di Azure
- Database SQL di Azure
- Istanza gestita di SQL di Azure
- Azure Synapse Analytics 
- Archiviazione tabelle di Azure
- HTTP generico
- REST generico
- MySQL
- Oracle
- SQL Server

Per gli altri tipi di servizi collegati che non si trovano nell'elenco precedente, è possibile parametrizzare il servizio collegato modificando il codice JSON sull'interfaccia utente:

- Nel pannello di creazione/modifica del servizio collegato-> espandere "avanzate" nella parte inferiore della casella di controllo > selezionare "specifica contenuto dinamico in formato JSON"-> specificare il payload JSON del servizio collegato. 
- In alternativa, dopo aver creato un servizio collegato senza parametrizzazione, in [Hub di gestione](author-visually.md#management-hub) -> servizi collegati-> trovare il servizio collegato specifico-> fare clic su "codice" (pulsante " {} ") per modificare il JSON. 

Vedere l' [esempio JSON](#json) per aggiungere la ` parameters` sezione per definire i parametri e fare riferimento al parametro usando ` @{linkedService().paraName} ` .

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

![Aggiungere contenuto dinamico alla definizione del servizio collegato](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Creare un nuovo parametro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
