---
title: Informazioni dettagliate prestazioni query-database di Azure per MariaDB
description: Questo articolo descrive la funzionalità Informazioni dettagliate prestazioni query nel database di Azure per MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527845"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Informazioni dettagliate sulle prestazioni delle query nel Database di Azure per MariaDB

**Si applica a:** Database di Azure per MariaDB 10,2

Informazioni dettagliate prestazioni query consente di identificare rapidamente quali sono le query a esecuzione più lunga, come cambiano nel corso del tempo e le attese che ne compromettono le prestazioni.

## <a name="common-scenarios"></a>Scenari comuni

### <a name="long-running-queries"></a>Query a esecuzione prolungata

- Identificazione delle query con il tempo di esecuzione più lungo nelle ultime X ore
- Identificazione delle prime N query in attesa delle risorse
 
### <a name="wait-statistics"></a>Statistiche attesa

- Informazioni sulla natura delle attese per una query
- Informazioni sulle tendenze per le attese di risorse e su dove esiste la contesa di risorse

## <a name="permissions"></a>Autorizzazioni

Autorizzazioni **Proprietario** oppure **Collaboratore** necessarie per visualizzare il testo delle query in Informazioni dettagliate prestazioni query. **Lettore** può visualizzare i grafici e tabelle ma non il testo della query.

## <a name="prerequisites"></a>Prerequisiti

Per il funzionamento di Informazioni dettagliate prestazioni query, in [Query Store](concepts-query-store.md) devono essere presenti i dati.

## <a name="viewing-performance-insights"></a>Visualizzazione delle informazioni dettagliate sulle prestazioni

La visualizzazione [Informazioni dettagliate prestazioni query](concepts-query-performance-insight.md) nel portale di Azure permette di sfogliare le informazioni chiave del Query Store.

Nella pagina del portale del database di Azure per il server MariaDB selezionare **informazioni dettagliate prestazioni query** nella sezione **prestazioni intelligenti** della barra dei menu.

### <a name="long-running-queries"></a>Query a esecuzione prolungata

La scheda **query con esecuzione prolungata** Mostra le prime 5 query per durata media per esecuzione, aggregate in intervalli di 15 minuti. È possibile visualizzare altre query selezionando l'elenco a discesa **numero di query** . I colori del grafico possono cambiare per un ID di query specifico quando si esegue questa operazione.

È possibile fare clic e trascinare nel grafico per limitare l'elenco a un intervallo di tempo specifico. In alternativa, usare le icone zoom avanti e indietro per visualizzare rispettivamente un periodo di tempo più piccolo o più grande.

![Informazioni dettagliate sulle prestazioni delle query a esecuzione prolungata](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Statistiche attesa 

> [!NOTE]
> Le statistiche di attesa sono destinate alla risoluzione dei problemi di prestazioni delle query. È consigliabile attivarla solo a scopo di risoluzione dei problemi. <br>Se viene visualizzato il messaggio di errore nel portale di Azure "*problema riscontrato per ' Microsoft. DBforMariaDB '; non è possibile soddisfare la richiesta. Se il problema persiste o è imprevisto, contattare il supporto tecnico con queste informazioni ".* durante la visualizzazione delle statistiche di attesa, usare un periodo di tempo inferiore.

Le statistiche di attesa forniscono una visualizzazione degli eventi di attesa che si verificano durante l'esecuzione di una query specifica. Per altre informazioni sui tipi di evento Wait, vedere la [documentazione del motore MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selezionare la scheda **Statistiche di attesa** per visualizzare le corrispondenti visualizzazioni in attesa nel server.

Le query visualizzate nella visualizzazione delle statistiche di attesa sono raggruppate in base alle query che mostrano le attese più grandi durante l'intervallo di tempo specificato.

![Informazioni dettagliate prestazioni query attese statistiche](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [monitoraggio e l'ottimizzazione](concepts-monitoring.md) nel database di Azure per MariaDB.