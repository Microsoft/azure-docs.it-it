---
title: Che cos'è Condivisione dati di Azure?
description: Informazioni su come condividere dati in modo semplice e sicuro con più clienti e partner tramite Condivisione dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: fa90f272a05b7449981ca5d4ccab161fb1e39e9e
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636776"
---
# <a name="what-is-azure-data-share"></a>Che cos'è Condivisione dati di Azure?

Nel mondo moderno i dati vengono visti come asset strategici fondamentali che molte organizzazioni hanno l'esigenza di semplificare e condividere in sicurezza con clienti e partner. Esistono molte soluzioni per rispondere a questa esigenza, tra cui FTP, posta elettronica, API, solo per citarne alcune. Le organizzazioni possono facilmente perdere traccia delle persone con cui hanno condiviso i loro dati. Il provisioning e l'amministrazione della condivisione dei dati tramite FTP o tramite un'infrastruttura API appositamente realizzata comportano spesso costi elevati. L'uso di questi metodi di condivisione su vasta scala è associato a un sovraccarico di gestione. 

Molte organizzazioni sono tenute a essere responsabili dei dati che hanno condiviso. Oltre alla responsabilità, molte vorrebbero avere la possibilità di controllare, gestire e monitorare con semplicità tutti i dati che condividono. Nel mondo moderno, in cui i dati sono destinati a crescere a un ritmo esponenziale, è necessaria una soluzione semplice per condividere i Big Data. I clienti pretendono di avere i dati più aggiornati possibile per avere la certezza di derivarne informazioni significative e tempestive.

Condivisione dati di Azure consente alle organizzazioni di condividere i dati in modo semplice e sicuro con più clienti e partner. Con pochi clic del mouse, è possibile effettuare il provisioning di un nuovo account di condivisione dati, aggiungere set di dati e invitare clienti e partner a condividerli. I provider di dati rimangono sempre in controllo dei dati che hanno condiviso. Con Condivisione dati di Azure, è semplice gestire e monitorare i dati condivisi, quando e con chi. 

Il provider di dati può mantenere il controllo del modo in cui vengono gestiti i dati specificando apposite condizioni per l'utilizzo. Il consumer dei dati deve accettare queste condizioni prima di iniziare a riceverli. I provider di dati possono specificare la frequenza con cui i consumer ricevono gli aggiornamenti. L'accesso a nuovi aggiornamenti può essere revocato in qualsiasi momento dal provider di dati. 

Condivisione dati di Azure consente di ottimizzare le informazioni semplificando la combinazione di dati di terze parti per arricchire gli scenari di analisi e intelligenza artificiale. È possibile sfruttare facilmente le funzionalità degli strumenti di analisi di Azure per preparare, elaborare e analizzare i dati condivisi tramite Condivisione dati di Azure. 

Per condividere e ricevere dati, sia il provider che il consumer di dati devono disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenari per Condivisione dati di Azure

Il servizio Condivisione dati di Azure può essere usato in numerosi settori diversi. Ad esempio, un rivenditore può scegliere di condividere i dati recenti dei punti vendita con i fornitori. Usando Condivisione dati di Azure, può configurare una condivisione contenente questi dati per tutti i fornitori e condividerli su base oraria o giornaliera. 

Condivisione dati di Azure può anche essere usato per stabilire un Marketplace di dati per uno specifico settore. Ad esempio, un ente pubblico o un istituto di ricerca può condividere regolarmente con terze parti e in forma anonima i dati sulla crescita della popolazione. 

Un altro caso d'uso di Condivisione dati di Azure riguarda l'instaurazione di un consorzio di dati. Ad esempio, diversi istituti di ricerca possono condividere i dati con un singolo organismo attendibile. I dati vengono analizzati, aggregati o elaborati con gli strumenti di analisi di Azure e quindi condivisi con le parti interessate. 

## <a name="how-it-works"></a>Funzionamento

Condivisione dati di Azure offre attualmente la condivisione basata su snapshot e la condivisione sul posto. 

Con la condivisione basata su snapshot, i dati vengono spostati dalla sottoscrizione di Azure del provider a quella del consumer. Il provider di dati può effettuare il provisioning di una condivisione dati in cui invitare i destinatari. I consumer dei dati ricevono l'invito alla condivisione dati tramite posta elettronica. Dopo aver accettato l'invito, il consumer di dati può attivare uno snapshot completo dei dati ricevuti in condivisione. Questi dati vengono ricevuti nell'account di archiviazione del consumer. I consumer dei dati ricevono regolari aggiornamenti incrementali per i dati, in modo da avere sempre l'ultima versione. 

I provider di dati possono offrire ai consumer aggiornamenti incrementali per i dati condivisi tramite una pianificazione di snapshot. Le pianificazioni di snapshot vengono offerte su base oraria o giornaliera. Dopo aver accettato e configurato la condivisione dati, il consumer può sottoscrivere una pianificazione di snapshot. Questa opzione è vantaggiosa negli scenari in cui i dati condivisi vengono aggiornati regolarmente e il consumer ha bisogno della versione più aggiornata. 

![flusso di condivisione dati](media/data-share-flow.png)

Dopo aver accettato una condivisione dati, il consumer potrà ricevere i dati in un archivio dati a propria scelta. Se ad esempio il provider condivide i dati tramite Archiviazione BLOB di Azure, il consumer può riceverli in Azure Data Lake Store. Analogamente, se il provider di dati condivide i dati da Azure SQL Data Warehouse, il consumer di dati può scegliere se ricevere i dati in Azure Data Lake Store, in un database SQL di Azure o in Azure SQL Data Warehouse. Nel caso di condivisione da origini basate su SQL, il consumer di dati può anche scegliere se ricevere i dati in formato PARQUET o CSV. 

Con la condivisione sul posto, i provider di dati possono condividere i dati nella posizione in cui risiedono, senza copiarli. Una volta stabilita la relazione di condivisione tramite il flusso di invito, viene creato un collegamento simbolico tra l'archivio dati di origine del provider di dati e l'archivio dati di destinazione del consumer di dati. Il consumer di dati può leggere ed eseguire query sui dati in tempo reale usando il proprio archivio dati. Le modifiche apportate all'archivio dati di origine diventano immediatamente disponibili per il consumer di dati. La condivisione sul posto è attualmente disponibile in anteprima per Esplora dati di Azure.

## <a name="key-capabilities"></a>Funzionalità principali

Condivisione dati di Azure consente ai provider di dati di:

* Condividere dati dall'elenco di [archivi dati supportati](supported-data-stores.md) con clienti e partner esterni all'organizzazione

* Tenere traccia delle persone con cui ha condiviso i dati

* Scelta dello snapshot o della condivisione sul posto

* Controllare la frequenza con cui i consumer ricevono aggiornamenti per i dati

* Consentire ai clienti di eseguire il pull dell'ultima versione dei dati, se necessario, oppure di ricevere automaticamente le modifiche incrementali apportate ai dati a un intervallo predefinito

Condivisione dati di Azure consente ai consumer di dati di: 

* Visualizzare una descrizione del tipo di dati condivisi

* Visualizzare le condizioni per l'utilizzo dei dati

* Accettare o rifiutare un invito di Condivisione dati di Azure

* Accettare i dati condivisi con l'utente in un [archivio dati supportato](supported-data-stores.md)

* Attivare uno snapshot completo o incrementale di una condivisione dati ricevuta da un'organizzazione

* Sottoscrivere una condivisione dati per ricevere la copia più recente dei dati tramite snapshot incrementale

Tutte le principali funzionalità descritte sopra sono supportate tramite il portale di Azure o le API REST. Per altre informazioni sull'uso di Condivisione dati di Azure tramite API REST, vedere la documentazione di riferimento. 

## <a name="supported-regions"></a>Aree supportate

Per l'elenco delle aree di Azure in cui è disponibile Condivisione dati di Azure, visitare la pagina di [prodotti disponibili per area](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) e cercare Condivisione dati di Azure. 

Condivisione dati di Azure non archivia una copia dei dati. I dati vengono archiviati nell'archivio dati sottostante che viene condiviso. Se ad esempio un provider di dati archivia i propri dati in un account di Azure Data Lake Store situato negli Stati Uniti occidentali, i dati verranno archiviati in questa area. Se condivide i dati con un account di archiviazione di Azure situato in Europa occidentale tramite snapshot, i dati vengono in genere trasferiti direttamente a tale account di archiviazione.

Il servizio Condivisione dati di Azure non deve necessariamente essere disponibile nell'area in cui lo si vuole usare. Se ad esempio i dati sono archiviati in un account di archiviazione di Azure situato in un'area in cui Condivisione dati di Azure non è ancora disponibile, è comunque possibile usare il servizio per condividere i dati. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).
