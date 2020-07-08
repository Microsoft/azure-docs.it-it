---
title: Connettere Qlik Sense ad Azure Cosmos DB e visualizzare i dati
description: Questo articolo descrive i passaggi necessari per connettere Azure Cosmos DB a Qlik Sense e visualizzare i dati personali.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 986db85ddbf741dc459625dbd763b9cbbba1c429
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263348"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Connettere Qlik Sense ad Azure Cosmos DB e visualizzare i dati

Qlik Sense è uno strumento di visualizzazione dei dati che combina dati da origini diverse in una sola visualizzazione. Qlik Sense indicizza tutte le possibili relazioni nei dati in modo da poter avere informazioni dettagliate immediate sui dati. È possibile visualizzare i dati di Azure Cosmos DB con Qlik Sense. Questo articolo descrive i passaggi necessari per connettere Azure Cosmos DB a Qlik Sense e visualizzare i dati personali. 

> [!NOTE]
> La connessione di Qlik Sense ad Azure Cosmos DB è attualmente supportata per l'API SQL e l'API di Azure Cosmos DB per gli account MongoDB.

È possibile connettere Qlik Sense ad Azure Cosmos DB con:

* L'API SQL di Cosmos DB usando il connettore ODBC.

* L'API di Azure Cosmos DB per MongoDB usando il connettore MongoDB di Qlik Sense (attualmente in anteprima).

* L'API di Azure Cosmos DB per MongoDB e l'API SQL con il connettore dell'API REST in Qlik Sense.

* L' API Mongo DB di Cosmos DB usando il connettore gRPC per Qlik Core.
Questo articolo descrive i dettagli della connessione all'API SQL di Cosmos DB tramite il connettore ODBC.

Questo articolo descrive i dettagli della connessione all'API SQL di Cosmos DB tramite il connettore ODBC.

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che le risorse seguenti siano pronte:

* Scaricare [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) oppure configurare Qlik Sense in Azure [installando il prodotto Qlik Sense dal marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Scaricare i [dati sui videogiochi](https://www.kaggle.com/gregorut/videogamesales): questi dati di esempio sono in formato CSV. Archiviare i dati in un account Cosmos DB e visualizzarli in Qlik Sense.

* Creare un account API SQL di Azure Cosmos DB tramite la procedura descritta nella sezione [Creare un account](create-sql-api-dotnet.md#create-account) dell'articolo della guida introduttiva.

* [Creare un database e una raccolta](create-sql-api-java.md#add-a-container): è possibile impostare il valore della velocità effettiva della raccolta su 1000 UR/sec. 

* Caricare i dati di vendita dei videogiochi di esempio nell'account Cosmos DB. È possibile importare i dati usando lo strumento di migrazione dei dati di Azure Cosmos DB; è possibile eseguire un'[importazione sequenziale](import-data.md#SQLSeqTarget) o [in massa](import-data.md#SQLBulkTarget) dei dati. L'importazione dei dati nell'account Cosmos DB richiede circa 3-5 minuti.

* Scaricare, installare e configurare il driver ODBC usando la procedura descritta nell'articolo [Connettersi a Cosmos DB con il driver ODBC](odbc-driver.md). I dati sui videogiochi sono un set di dati semplice e non è necessario modificare lo schema, basta usare lo schema di mapping-raccolta predefinito.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Connettere Qlik Sense a Cosmos DB

1. Aprire Qlik Sense e selezionare **Create new app** (Crea nuova app). Specificare un nome per l'app, quindi selezionare **Create** (Crea).

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Creare una nuova app di Qlik Sense":::

2. Dopo aver creato correttamente la nuova app, selezionare **Open app** (Apri app) e scegliere **Add data from files and other sources** (Aggiungi dati da file e altre origini). 

3. Nelle origini dati selezionare **ODBC** per aprire la finestra di configurazione della nuova connessione. 

4. Passare a **User DSN** (DSN utente) e scegliere la connessione ODBC creata in precedenza. Specificare un nome per la connessione e selezionare **Create** (Crea). 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Creare una nuova connessione":::

5. Dopo aver creato la connessione, è possibile scegliere il database, la raccolta che contiene i dati dei videogiochi e quindi visualizzarli in anteprima.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Scegliere il database e la raccolta"::: 

6. Successivamente, selezionare **Add data** (Aggiungi dati) per caricarli in Qlik Sense. Dopo aver caricato i dati in Qlik Sense, è possibile generare informazioni dettagliate ed eseguire analisi sui dati. È possibile usare le informazioni dettagliate o creare la propria app esplorando le vendite dei videogiochi. La figura seguente mostra un esempio. 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Visualizzare i dati":::

### <a name="limitations-when-connecting-with-odbc"></a>Limitazioni durante la connessione con ODBC 

Cosmos DB è un database distribuito senza schema con i driver modellati in base alle esigenze degli sviluppatori. Il driver ODBC richiede un database con schema da cui dedurre le colonne, i relativi tipi di dati e altre proprietà. La normale query SQL o la sintassi DML con funzionalità relazionali non è applicabile all'API SQL di Cosmos DB perché l'API SQL non è SQL ANSI. Per questo motivo le istruzioni SQL emesse tramite il driver ODBC vengono convertite in sintassi SQL specifica per Cosmos DB che non ha equivalenti per tutti i costrutti. Per evitare questi problemi di conversione, è necessario applicare uno schema quando si configura la connessione ODBC. L'articolo [eseguire la connessione con il driver ODBC](odbc-driver.md) offre suggerimenti e metodi che consentono di configurare lo schema. Assicurarsi di creare questo mapping per ogni raccolta o database all'interno dell'account Cosmos DB.

## <a name="next-steps"></a>Passaggi successivi

Se si usa uno strumento di visualizzazione diverso, ad esempio Power BI, è possibile connettersi a esso usando le istruzioni nel documento seguente:

* [Visualizzare i dati di Cosmos DB con il connettore Power BI](powerbi-visualize.md)
