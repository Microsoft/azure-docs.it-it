---
title: Sportello anteriore di Azure-Caching | Microsoft Docs
description: Questo articolo consente di comprendere come il front-end di Azure monitora l'integrità dei backend
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e521711cdf488f00b56e2805ee0aaa6ee8412958
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056959"
---
# <a name="caching-with-azure-front-door"></a>Memorizzazione nella cache con lo sportello anteriore di Azure
Il documento seguente specifica il comportamento di Frontdoor di Azure con regole di routing che hanno abilitato la memorizzazione nella cache. La porta anteriore è una rete per la distribuzione di contenuti (CDN) moderna e, insieme a bilanciamento del carico e accelerazione sito dinamico, supporta anche i comportamenti di memorizzazione nella cache come qualsiasi altra rete CDN.

## <a name="delivery-of-large-files"></a>Recapito di file di grandi dimensioni
Il front-end di Azure recapita file di grandi dimensioni senza copertura per le dimensioni dei file. Frontdoor di Azure usa una tecnica chiamata suddivisione degli oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, Frontdoor recupera piccole parti del file dal back-end. Dopo aver ricevuto una richiesta di file completo o intervallo di byte, un ambiente Frontdoor richiede il file dal back-end in blocchi da 8 MB.

</br>Quando il blocco arriva all'ambiente Frontdoor, viene memorizzato nella cache e reso immediatamente disponibile all'utente. Frontdoor esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua finché non viene scaricato l'intero file (se richiesto), non sono disponibili tutti gli intervalli di byte (se richiesto) o il client non termina la connessione.

</br>Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Frontdoor memorizza nella cache tutti i blocchi alla loro ricezione e pertanto l'intero file non deve essere memorizzato nella cache di Frontdoor. Le richieste successive del file o di intervalli di byte vengono soddisfatte dalla cache. Se non tutti i blocchi vengono memorizzati nella cache, viene usata la prelettura per richiedere i blocchi dal back-end. Questa ottimizzazione si basa sulla capacità del server di back-end di supportare le richieste di intervallo di byte. Se il server di back-end non supporta le richieste di intervallo di byte, questa ottimizzazione non è efficace.

## <a name="file-compression"></a>Compressione dei file
Frontdoor comprime in modo dinamico il contenuto sull'edge, offrendo così una risposta più rapida ai client. Tutti i file sono idonei alla compressione. Il file, tuttavia, deve essere un tipo MIME che sia idoneo per l'elenco di compressione. Frontdoor attualmente non supporta modifiche a questo elenco. L'elenco corrente consiste in:</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- application/javascript
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Inoltre, la dimensione del file deve essere compresa tra 1 KB e 8 MB.

Questi profili supportano le codifiche di compressione seguenti:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se una richiesta supporta la compressione gzip e la compressione Brotli, la compressione Brotli ha la precedenza.</br>
Quando una richiesta per una risorsa indica la compressione e i risultati della richiesta non sono presenti nella cache, Frontdoor esegue la compressione della risorsa direttamente nel server POP. In seguito, il file compresso viene gestito nella cache. L'elemento risultante viene restituito con un transfer-encoding: suddiviso in blocchi.

## <a name="query-string-behavior"></a>Comportamento di memorizzazione della stringa di query
Frontdoor consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non ha importanza.
- **Ignora stringhe di query**: modalità predefinita. In questa modalità Frontdoor passa la stringa di query dal richiedente al back-end alla prima richiesta ed esegue la memorizzazione nella cache dell'asset. Tutte le richieste successive dell'asset gestite dall'ambiente Frontdoor ignoreranno le stringhe di query finché l'asset memorizzato nella cache non sarà scaduto.

- **Memorizza nella cache tutti gli URL univoci**: in questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta inviata da back-end per una richiesta di `www.example.ashx?q=test1` viene memorizzata nella cache nell'ambiente Frontdoor e restituita per le memorizzazioni nella cache successive con la stessa stringa di query. Una richiesta di `www.example.ashx?q=test2` viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).

## <a name="cache-purge"></a>Ripulire la cache
Frontdoor memorizzerà nella cache gli asset fino alla scadenza della durata TTL dell'asset. Dopo la scadenza della durata TTL dell'asset, quando un client richiede l'asset dall'ambiente Frontdoor, questo recupera una nuova copia aggiornata dell'asset per soddisfare la richiesta del client e aggiornare la cache.
</br>La procedura consigliata per assicurarsi che gli utenti ottengano sempre la copia più recente degli asset consiste nel versioning di questi ultimi per ogni aggiornamento e nella relativa pubblicazione come nuovi URL. Frontdoor recupera immediatamente i nuovi asset per le richieste client successive. A volte si desidera ripulire il contenuto memorizzato nella cache da tutti i nodi periodici e forzare il recupero dei nuovi asset aggiornati. Ciò potrebbe essere dovuto agli aggiornamenti all'applicazione Web o a un aggiornamento rapido di asset che contengono informazioni non corrette.

</br>Selezionare gli asset che si desidera ripulire dai nodi periferici. Se si desidera ripulire tutti gli asset, fare clic sulla casella di controllo Elimina tutto. In alternativa digitare il percorso di ogni asset che si vuole ripulire nella casella di testo Percorso. I formati seguenti sono supportati nel percorso.
1. **Eliminazione a percorso singolo**: Ripulisci singoli asset specificando il percorso completo dell'asset (senza il protocollo e il dominio), con l'estensione di file, ad esempio/Pictures/strasbourg.png;
2. **Wildcard purge**: (Eliminazione dei caratteri jolly) l'asterisco (\*) può essere usato come carattere jolly. Eliminare tutte le cartelle, le sottocartelle e i file in un endpoint con/ \* nel percorso o eliminare tutte le sottocartelle e i file in una cartella specifica specificando la cartella seguita da/ \* , ad esempio/Pictures/ \* .
3. **Root domain purge**: (Eliminazione del dominio radice) consente di eliminare la radice dell'endpoint inserendo "/" nel percorso.

Le pulizie della cache su Frontdoor non fanno distinzione tra maiuscole e minuscole. Inoltre, risultano indipendenti dalla stringa di query, vale a dire che l'eliminazione di un indirizzo Web eliminerà tutte le variazioni delle stringhe di query. 

## <a name="cache-expiration"></a>Ora di scadenza della cache
L'ordine delle intestazioni seguente viene usato per determinare quanto tempo un elemento rimane memorizzato nella cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Scadenza\<http-date>

Intestazioni di risposta Cache-Control che indicano che la risposta non verrà memorizzata nella cache, ad esempio cache-Control: private, cache-Control: No-cache e cache-Control: No-Store viene rispettato. Tuttavia, se sono presenti più richieste in elaborazione in un POP per lo stesso indirizzo Web, esse possono condividere la risposta. Se non è presente alcun controllo cache, il comportamento predefinito è che AFD memorizza nella cache la risorsa per l'intervallo di tempo X, dove X viene selezionato in modo casuale da 1 a 3 giorni.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni della richiesta seguenti non verranno inoltrate a un back-end quando si usa la memorizzazione nella cache.
- Content-Length
- Transfer-encoding

## <a name="cache-duration"></a>Durata cache

La durata della cache può essere configurata sia nella finestra di progettazione front door che nel motore regole. La durata della cache impostata in frontdoor designer è la durata minima della cache. Questo override non funzionerà se l'intestazione di controllo della cache dall'origine ha un valore TTL maggiore di quello di sostituzione. 

La durata della cache impostata tramite il motore regole è un vero e proprio override della cache, vale a dire che utilizzerà il valore di sostituzione indipendentemente dall'intestazione della risposta di origine.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
