---
title: Guida di riferimento per le parti di Progettazione viste in monitoraggio di Azure | Microsoft Docs
description: Usando Progettazione viste in monitoraggio di Azure, è possibile creare visualizzazioni personalizzate che vengono visualizzate nel portale di Azure e contengono un'ampia gamma di visualizzazioni sui dati nell'area di lavoro Log Analytics. Questo articolo è una guida di riferimento per le impostazioni delle parti di visualizzazione disponibili nelle viste personalizzate.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7b670cafa4d643e37fae068a4c0033fc97a96ccd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92166591"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Guida di riferimento per le parti di visualizzazione di Progettazione viste in monitoraggio di Azure
Utilizzando Progettazione viste in monitoraggio di Azure, è possibile creare un'ampia gamma di visualizzazioni personalizzate nell'portale di Azure che consentono di visualizzare i dati nell'area di lavoro di Log Analytics. Questo articolo è una guida di riferimento per le impostazioni delle parti di visualizzazione disponibili nelle viste personalizzate.

Per altre informazioni su Progettazione visualizzazioni, vedere:

* [Progettazione viste](view-designer.md): offre una panoramica di Progettazione viste e delle procedure per la creazione e la modifica di viste personalizzate.
* [Informazioni di riferimento sul riquadro](view-designer-tiles.md): offre informazioni di riferimento sulle impostazioni relative a ogni riquadro disponibile nelle viste personalizzate.


I tipi di riquadro di Progettazione viste disponibili sono descritti nella tabella seguente:

| Tipo di vista | Descrizione |
|:--- |:--- |
| [Elenco di query](#list-of-queries-part) |Visualizza un elenco di query di log. È possibile selezionare ogni query per visualizzarne i risultati. |
| [Numero ed elenco](#number-and-list-part) |Nell'intestazione viene visualizzato un singolo numero che mostra il conteggio dei record da una query di log. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo. |
| [Due numeri ed elenco](#two-numbers-and-list-part) |L'intestazione Visualizza due numeri che mostrano i conteggi dei record da query di log separate. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo. |
| [Grafico ad anello ed elenco](#donut-and-list-part) |L'intestazione presenta un singolo numero che riepiloga una colonna di valori in una query di log. L'anello visualizza graficamente i risultati dei tre record principali. |
| [Due sequenze temporali ed elenco](#two-timelines-and-list-part) |L'intestazione presenta i risultati di due query di log nel corso del tempo sotto forma di istogrammi, con un callout in cui viene visualizzato un singolo numero che riepiloga una colonna di valori in una query di log. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo. |
| [Informazioni](#information-part) |L'intestazione presenta testo statico e un collegamento opzionale. L'elenco visualizza uno o più elementi con titolo e testo statici. |
| [Grafico a linee, callout ed elenco](#line-chart-callout-and-list-part) |L'intestazione presenta un grafico a linee con più serie provenienti da una query di log nel corso del tempo e un callout con un valore riepilogato. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo. |
| [Grafico a linee ed elenco](#line-chart-and-list-part) |L'intestazione presenta un grafico a linee con più serie provenienti da una query di log nel corso del tempo. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo. |
| [Stack dei grafici a linee](#stack-of-line-charts-part) |Visualizza tre grafici a linee separati con più serie provenienti da una query di log nel corso del tempo. |

Le sezioni successive descrivono nei dettagli i tipi di riquadro e le relative proprietà.

> [!NOTE]
> Le parti nelle viste sono basate su [query di log](../log-query/log-query-overview.md) nell'area di lavoro log Analytics. Attualmente non supportano [query tra risorse](../log-query/cross-workspace-query.md) per recuperare dati da Application Insights.

## <a name="list-of-queries-part"></a>Parte relativa all'elenco delle query
Nella parte elenco di query viene visualizzato un elenco di query di log. È possibile selezionare ogni query per visualizzarne i risultati. Per impostazione predefinita, la vista include una singola query ed è possibile aggiungerne altre facendo clic su **+ Query**.

![Screenshot della parte visualizzazione elenco di query in Progettazione viste di monitoraggio di Azure.](media/view-designer-parts/view-list-queries.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo |Testo visualizzato nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Filtri preselezionati |Elenco delimitato da virgole delle proprietà da includere nel riquadro sinistro del filtro quando si seleziona una query. |
| Modalità di rendering |Vista iniziale che viene aperta quando si seleziona la query. È possibile selezionare qualsiasi vista disponibile dopo l'apertura della query. |
| **Query** | |
| Query di ricerca |Query da eseguire. |
| Nome descrittivo | Nome descrittivo che viene visualizzato. |

## <a name="number-and-list-part"></a>Parte relativa a Numero ed elenco
Nell'intestazione viene visualizzato un singolo numero che mostra il conteggio dei record da una query di log. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo.

![Screenshot delle parti di visualizzazione numero ed elenco in Progettazione viste di monitoraggio di Azure.](media/view-designer-parts/view-number-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo visualizzato nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Titolo** | |
| Legenda |Testo che viene visualizzato nella parte superiore dell'intestazione. |
| Query |Query da eseguire per l'intestazione. Viene visualizzato il numero di record restituiti dalla query. |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Vengono visualizzate le prime due proprietà per i primi dieci record nei risultati. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. Le barre vengono create automaticamente in base al valore relativo della colonna numerica.<br><br>Usare il comando `Sort` nella query per ordinare i record nell'elenco. Per eseguire la query e restituire tutti i record, è possibile selezionare **Visualizza tutto**. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Separatore di nome e valore |Delimitatore di singoli caratteri da usare per analizzare la proprietà di testo in più valori. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Parte relativa a Due numeri ed elenco
L'intestazione contiene due numeri che visualizzano un conteggio di record da query di log separate. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo.

![Visualizzazione Due numeri ed elenco](media/view-designer-parts/view-two-numbers-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo visualizzato nella parte superiore della vista. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Navigazione nel titolo** | |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Titolo** | |
| Legenda |Testo che viene visualizzato nella parte superiore dell'intestazione. |
| Query |Query da eseguire per l'intestazione. Viene visualizzato il numero di record restituiti dalla query. |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Vengono visualizzate le prime due proprietà per i primi dieci record nei risultati. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. Le barre vengono create automaticamente in base al valore relativo della colonna numerica.<br><br>Usare il comando `Sort` nella query per ordinare i record nell'elenco. Per eseguire la query e restituire tutti i record, è possibile selezionare **Visualizza tutto**. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Operazione |Operazione da eseguire per il grafico sparkline. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Separatore di nome e valore |Delimitatore di singoli caratteri da usare per analizzare la proprietà di testo in più valori. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="donut-and-list-part"></a>Parte relativa a Grafico ad anello ed elenco
L'intestazione presenta un singolo numero che riepiloga una colonna di valori in una query di log. L'anello visualizza graficamente i risultati dei tre record principali.

![Vista Grafico ad anello ed elenco](media/view-designer-parts/view-donut-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Testo che viene visualizzato nella parte superiore dell'intestazione. |
| Sottotitolo |Testo che viene visualizzato sotto il titolo nella parte superiore dell'intestazione. |
| **Grafico ad anello** | |
| Query |Query da eseguire per il grafico ad anello. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Grafico ad anello** |**> Centro** |
| Testo |Testo che viene visualizzato sotto il valore all'interno del grafico ad anello. |
| Operazione |Operazione da eseguire sulla proprietà value per riepilogarla come valore singolo.<ul><li>Somma: aggiunge i valori di tutti i record.</li><li>Percentuale: rapporto tra i record restituiti in base ai valori in **Valori dei risultati usati nell'operazione relativa al centro** e il totale dei record nella query.</li></ul> |
| I valori dei risultati usati nell'operazione centrale |Facoltativamente, fare clic sul segno più (+) per aggiungere uno o più valori. I risultati della query saranno limitati ai record con i valori di proprietà specificati. Se non vengono aggiunti valori, nella query vengono inclusi tutti i record. |
| **Opzioni aggiuntive** |**&gt; Colors** (Colori) |
| Colore 1<br>Colore 2<br>Colore 3 |Selezionare il colore per ognuno dei valori visualizzati nel grafico ad anello. |
| **Opzioni aggiuntive** |**> Mappa colori avanzata** |
| Valore campo |Digitare il nome di un campo da visualizzare con un colore diverso, se incluso nell'anello. |
| Colore |Selezionare il colore da assegnare al campo univoco. |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Viene visualizzato il numero di record restituiti dalla query. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Operazione |Operazione da eseguire per il grafico sparkline. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Separatore di nome e valore |Delimitatore di singoli caratteri da usare per analizzare la proprietà di testo in più valori. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Parte relativa a Due sequenze temporali ed elenco
L'intestazione presenta i risultati di due query di log nel corso del tempo sotto forma di istogrammi, con un callout in cui viene visualizzato un singolo numero che riepiloga una colonna di valori in una query di log. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo.

![Vista Due sequenze temporali ed elenco](media/view-designer-parts/view-two-timelines-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Navigazione nel titolo** | |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Primo grafico<br>Secondo grafico** | |
| Legenda |Testo che viene visualizzato sotto il callout per la prima serie. |
| Colore |Colore da usare per le colonne della serie. |
| Query |Query da eseguire per la prima serie. Il numero di record in ogni intervallo di tempo viene rappresentato dalle colonne del grafico. |
| Operazione |Operazione da eseguire sulla proprietà value per riepilogarla come valore singolo per il callout.<ul><li>Somma: somma dei valori di tutti i record.</li><li>Media: media dei valori di tutti i record.</li><li>Ultimo esempio: valore dell'ultimo intervallo incluso nel grafico.</li><li>Primo esempio: valore del primo intervallo incluso nel grafico.</li><li>Conteggio: numero di tutti i record restituiti dalla query.</li></ul> |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Viene visualizzato il numero di record restituiti dalla query. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Operazione |Operazione da eseguire per il grafico sparkline. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="information-part"></a>Parte relativa alle informazioni
L'intestazione presenta testo statico e un collegamento opzionale. L'elenco visualizza uno o più elementi con titolo e testo statici.

![Visualizzazione delle informazioni](media/view-designer-parts/view-information.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Colore |Colore di sfondo dell'intestazione. |
| **Intestazione** | |
| Immagine |File di immagine che viene visualizzato nell'intestazione. |
| Label |Testo che viene visualizzato nell'intestazione. |
| **Intestazione** |**&gt; Collegamento** |
| Label |Testo del collegamento. |
| URL |URL del collegamento. |
| **Elementi informazioni** | |
| Titolo |Testo che viene visualizzato per il titolo di ogni elemento. |
| Content |Testo che viene visualizzato per ogni elemento. |

## <a name="line-chart-callout-and-list-part"></a>Parte relativa a Grafico a linee, callout ed elenco
L'intestazione presenta un grafico a linee con più serie provenienti da una query di log nel corso del tempo e un callout con un valore riepilogato. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo.

![Vista Grafico a linee, callout ed elenco](media/view-designer-parts/view-line-chart-callout-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Testo che viene visualizzato nella parte superiore dell'intestazione. |
| Sottotitolo |Testo che viene visualizzato sotto il titolo nella parte superiore dell'intestazione. |
| **Grafico a linee** | |
| Query |Query da eseguire per il grafico a linee. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. Questa query usa in genere la parola chiave *measure* per riepilogare i risultati. Se la query usa la parola chiave *interval*, l'asse X del grafico usa questo intervallo di tempo. Se la query non include la parola chiave *interval*, l'asse X usa intervalli orari. |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Grafico a linee** |**> Callout** |
| Titolo del callout |Testo che viene visualizzato sopra il valore del callout. |
| Nome della serie |Il valore della proprietà per la serie da usare per il valore di callout. Se non viene fornita alcuna serie, vengono usati tutti i record prodotti dalla query. |
| Operazione |Operazione da eseguire sulla proprietà value per riepilogarla come valore singolo per il callout.<ul><li>Media: media dei valori di tutti i record.</li><li>Conteggio: numero di tutti i record restituiti dalla query.</li><li>Ultimo esempio: valore dell'ultimo intervallo incluso nel grafico.</li><li>Max: valore massimo derivante dagli intervalli inclusi nel grafico.</li><li>Min: valore minimo derivante dagli intervalli inclusi nel grafico.</li><li>Somma: somma dei valori di tutti i record.</li></ul> |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questo collegamento per usare una scala logaritmica per l'asse Y. |
| Unità |Specificare le unità per i valori restituiti dalla query. Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori. Il tipo di *Unità* specifica la categoria dell'unità e definisce i valori del tipo *Unità corrente* disponibili. Se si seleziona un valore in *Converti in*, i valori numerici vengono convertiti dal tipo *Unità corrente* al tipo *Converti in*. |
| Etichetta personalizzata |Testo che viene visualizzato per l'asse Y accanto all'etichetta per il tipo *Unità*. Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo *Unità*. |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Viene visualizzato il numero di record restituiti dalla query. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Operazione |Operazione da eseguire per il grafico sparkline. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Separatore di nome e valore |Delimitatore di singoli caratteri da usare per analizzare la proprietà di testo in più valori. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="line-chart-and-list-part"></a>Parte relativa a Grafico a linee ed elenco
L'intestazione presenta un grafico a linee con più serie provenienti da una query di log nel corso del tempo. L'elenco visualizza i primi dieci risultati di una query, con un grafico indicante il valore relativo di una colonna numerica o il suo variare nel tempo.

![Vista Grafico a linee ed elenco](media/view-designer-parts/view-line-chart-callout-list.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| Usa icona |Selezionare questo collegamento per visualizzare l'icona. |
| **Intestazione** | |
| Titolo |Testo che viene visualizzato nella parte superiore dell'intestazione. |
| Sottotitolo |Testo che viene visualizzato sotto il titolo nella parte superiore dell'intestazione. |
| **Grafico a linee** | |
| Query |Query da eseguire per il grafico a linee. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. Questa query usa in genere la parola chiave *measure* per riepilogare i risultati. Se la query usa la parola chiave *interval*, l'asse X del grafico usa questo intervallo di tempo. Se la query non include la parola chiave *interval*, l'asse X usa intervalli orari. |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Grafico a linee** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questo collegamento per usare una scala logaritmica per l'asse Y. |
| Unità |Specificare le unità per i valori restituiti dalla query. Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori. Il tipo di *Unità* specifica la categoria dell'unità e definisce i valori del tipo *Unità corrente* disponibili. Se si seleziona un valore in *Converti in*, i valori numerici vengono convertiti dal tipo *Unità corrente* al tipo *Converti in*. |
| Etichetta personalizzata |Testo che viene visualizzato per l'asse Y accanto all'etichetta per il tipo *Unità*. Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo *Unità*. |
| **Elenco** | |
| Query |Query da eseguire per l'elenco. Viene visualizzato il numero di record restituiti dalla query. |
| Nascondi grafico |Selezionare questo collegamento per disabilitare il grafico a destra della colonna numerica. |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Colore |Colore delle barre o dei grafici sparkline. |
| Operazione |Operazione da eseguire per il grafico sparkline. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Separatore di nome e valore |Delimitatore di singoli caratteri da usare per analizzare la proprietà di testo in più valori. Per altre informazioni, vedere [Impostazioni comuni](#sparklines). |
| Navigazione click-through | Azione intrapresa quando si fa clic su un elemento nell'elenco.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Elenco** |**> Titoli di colonna** |
| Name |Testo che viene visualizzato nella parte superiore della prima colonna. |
| Valore |Testo che viene visualizzato nella parte superiore della seconda colonna. |
| **Elenco** |**&gt; Thresholds** (Soglie) |
| Abilitare le soglie |Selezionare questo collegamento per abilitare le soglie. Per altre informazioni, vedere [Impostazioni comuni](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stack dei grafici a linee
Lo stack presenta tre grafici a linee separati con più serie provenienti da una query di log nel corso del tempo, come mostrato di seguito:

![Stack dei grafici a linee](media/view-designer-parts/view-stack-line-charts.png)

| Impostazione | Descrizione |
|:--- |:--- |
| **Generale** | |
| Titolo gruppo |Testo che viene visualizzato nella parte superiore del riquadro. |
| Nuovo gruppo |Selezionare questo collegamento per creare un nuovo gruppo nella vista, partendo dalla vista corrente. |
| Icona |File di immagine che viene visualizzato accanto al risultato nell'intestazione. |
| **Grafico 1<br>Grafico 2<br>Grafico 3** |**&gt; Header** (Intestazione) |
| Titolo |Testo che viene visualizzato nella parte superiore del grafico. |
| Sottotitolo |Testo che viene visualizzato sotto il titolo nella parte superiore del grafico. |
| **Grafico 1<br>Grafico 2<br>Grafico 3** |**Grafico a linee** |
| Query |Query da eseguire per il grafico a linee. La prima proprietà è un valore di testo, mentre la seconda è un valore numerico. Questa query usa in genere la parola chiave *measure* per riepilogare i risultati. Se la query usa la parola chiave *interval*, l'asse X del grafico usa questo intervallo di tempo. Se la query non include la parola chiave *interval*, l'asse X usa intervalli orari. |
| Navigazione click-through | Azione intrapresa quando si fa clic sull'intestazione.  Per altre informazioni, vedere [Impostazioni comuni](#click-through-navigation). |
| **Grafico** |**> Asse Y** |
| Usa scala logaritmica |Selezionare questo collegamento per usare una scala logaritmica per l'asse Y. |
| Unità |Specificare le unità per i valori restituiti dalla query. Queste informazioni vengono usate per visualizzare le etichette del grafico che indicano i tipi di valore e, facoltativamente, per convertire i valori. Il tipo di *Unità* specifica la categoria dell'unità e definisce i valori del tipo *Unità corrente* disponibili. Se si seleziona un valore in *Converti in*, i valori numerici vengono convertiti dal tipo *Unità corrente* al tipo *Converti in*. |
| Etichetta personalizzata |Testo che viene visualizzato per l'asse Y accanto all'etichetta per il tipo *Unità*. Se non viene specificata alcuna etichetta, viene visualizzato solo il tipo *Unità*. |

## <a name="common-settings"></a>Impostazioni comuni
Le sezioni seguenti descrivono le impostazioni comuni a più parti di visualizzazione.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separatore di nome e valore
Il separatore di nome e valore è il delimitatore dei singoli caratteri da usare per analizzare la proprietà text da una query di elenco in più valori. Se si specifica un delimitatore, è possibile specificare nomi per ogni campo, separati dallo stesso delimitatore nella casella **nome** .

Si consideri, ad esempio, una proprietà denominata *Sede* nella quale vengono inclusi valori come *Redmond-Building 41* e *Bellevue-Building12*. È possibile specificare un trattino (-) come separatore di nome e valore e *City-Building* come nome. Ciò comporta l'analisi di ciascun valore in due proprietà chiamate *City* e *Building*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navigazione click-through
Navigazione click-through definisce l'azione che verrà intrapresa quando si fa clic su un'intestazione o un elemento elenco in una visualizzazione.  Verrà aperta una query nel [log Analytics](../log-query/log-query-overview.md) o verrà avviata un'altra visualizzazione.

La tabella seguente illustra le impostazioni relative alla navigazione click-through.

| Impostazione           | Descrizione |
|:--|:--|
| Ricerca log (automatica) | Query di log da eseguire quando si seleziona un elemento di intestazione.  Si tratta della stessa query di log su cui si basa l'elemento.
| Ricerca log        | Query di log da eseguire quando si seleziona un elemento in un elenco.  Digitare la query nella casella **Query di spostamento**.   Usare *{selected item}* per includere la sintassi per l'elemento selezionato dall'utente.  Se, ad esempio, la query include una colonna denominata *Computer* e la query di navigazione è *{selected item}*, viene eseguita una query del tipo *Computer="MyComputer"* quando si seleziona un computer. Se la query di navigazione è *Type=Event {selected item}* viene eseguita la query *Type=Event Computer="MyComputer"*. |
| Visualizzazione              | Visualizzazione da aprire quando si seleziona un elemento intestazione o un elemento in un elenco.  Selezionare il nome di una visualizzazione nell'area di lavoro nella casella **Nome visualizzazione**. |



### <a name="sparklines"></a><a name="sparklines"></a>Grafici sparkline
Un grafico sparkline è un grafico a linee di piccole dimensioni che mostra le variazioni nel tempo del valore di una voce di elenco. Per le parti di visualizzazione con un elenco è possibile scegliere se visualizzare una barra orizzontale con indicato il valore relativo di una colonna numerica oppure un grafico sparkline con indicate le variazioni del valore nel tempo.

La tabella seguente illustra le impostazioni di un grafico sparkline:

| Impostazione | Descrizione |
|:--- |:--- |
| Abilita grafici sparkline |Selezionare questo collegamento per visualizzare un grafico sparkline al posto di una barra orizzontale. |
| Operazione |Se sono abilitati i grafici sparkline, questa è l'operazione da eseguire su ogni proprietà nell'elenco per calcolare i valori per il grafico sparkline.<ul><li>Ultimo esempio: ultimo valore per la serie nell'intervallo di tempo.</li><li>Max: valore massimo per la serie nell'intervallo di tempo.</li><li>Min: valore minimo per la serie nell'intervallo di tempo.</li><li>Somma: somma dei valori per la serie nell'intervallo di tempo.</li><li>Riepilogo: usa lo stesso comando `measure` della query nell'intestazione.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Soglie
Se si usano le soglie, è possibile visualizzare un'icona colorata accanto a ogni elemento in un elenco. Le soglie offrono un indicatore visivo rapido di elementi che superano un determinato valore o rientrano in un intervallo specifico. È, ad esempio, possibile visualizzare un'icona verde per gli elementi con un valore accettabile, gialla se il valore è compreso in un intervallo che indica un possibile problema e rosso se il valore supera un valore di errore.

Quando si abilitano le soglie, è necessario specificarne una o più. Se il valore di un elemento è superiore al valore di soglia, ma inferiore al valore di soglia successivo, viene usato il colore corrispondente. Se l'elemento è superiore al valore di soglia massimo, viene usato un altro colore. 

A ogni soglia è associato il valore **Default** (Predefinito). Questo è il colore che viene impostato se non viene superato nessun altro valore. È possibile aggiungere o rimuovere le soglie selezionando il pulsante **Aggiungi** (+) o **Elimina** (x).

La tabella seguente illustra le impostazioni relative alle soglie:

| Impostazione | Descrizione |
|:--- |:--- |
| Abilitare le soglie |Selezionare questo collegamento per visualizzare un'icona colorata a sinistra di ogni valore. L'icona indica l'integrità del valore in relazione alle soglie specificate. |
| Name |Nome del valore di soglia. |
| Soglia |Valore per la soglia. Il colore di integrità di ogni elemento dell'elenco è impostato sul colore del valore di soglia massimo superato dal valore dell'elemento. Se nessun valore di soglia viene superato, viene usato un colore predefinito. |
| Colore |Colore che indica il valore di soglia. |

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [query di log](../log-query/log-query-overview.md) per supportare le query nelle parti di visualizzazione.
