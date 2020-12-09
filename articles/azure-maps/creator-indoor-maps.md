---
title: Usare le mappe indoor in Azure Maps Creator (anteprima)
description: Questo articolo presenta i concetti che si applicano ai servizi di creazione di mappe di Azure (anteprima)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903565"
---
# <a name="creator-preview-for-indoor-maps"></a>Creator (anteprima) per le mappe indoor


> [!IMPORTANT]
> I servizi Azure Maps Creator sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questo articolo illustra i concetti e gli strumenti che si applicano a Creator di Mappe di Azure. È consigliabile leggere questo articolo prima di iniziare a usare l'API e l'SDK di Creator di Mappe di Azure.

È possibile usare Creator per sviluppare applicazioni con funzionalità di mapping basate sui dati della pianta di interni. Questo articolo descrive il processo di caricamento, conversione, creazione e uso dei dati della pianta. Il diagramma seguente illustra l'intero flusso di lavoro.

![Flusso di lavoro dei dati della pianta di Creator](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Crea Azure Maps Creator (anteprima) 

Per usare i servizi Creator (anteprima), è necessario creare Azure Maps Creator in un account Azure maps. Per informazioni su come creare il Creator di Mappe di Azure in Mappe di Azure, vedere [Gestire il Creator di Mappe di Azure](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Caricare un pacchetto di disegno

Creator (Preview) raccoglie i dati della mappa interna convertendo un pacchetto di disegno caricato. Il pacchetto di disegno rappresenta una struttura costruita o rimodellata. Per informazioni sui requisiti del pacchetto di disegno, vedere [Requisiti del pacchetto di disegno](drawing-requirements.md).

Usare l' [API di caricamento dei dati di mappe di Azure (anteprima)](/rest/api/maps/data/uploadpreview) per caricare un pacchetto di disegno.  Al completamento del caricamento, l'API Data Upload restituirà un identificatore dei dati utente (`udid`). L'`udid` verrà usato nel passaggio successivo per convertire il pacchetto caricato in dati della pianta di interni.

## <a name="convert-a-drawing-package"></a>Convertire un pacchetto di disegno

Il servizio [Conversione di Mappe di Azure](/rest/api/maps/conversion) converte un pacchetto di disegno caricato in dati della pianta di interni. Inoltre, il servizio di conversione convalida il pacchetto. I problemi di convalida sono classificati in due tipi: errori e avvisi. Se vengono rilevati errori, il processo di conversione avrà esito negativo. Se vengono rilevati avvisi, la conversione avrà esito positivo. Tuttavia, è consigliabile esaminare e risolvere tutti gli avvisi. Un avviso indica che parte della conversione è stata ignorata o risolta automaticamente. La mancata risoluzione degli avvisi può causare errori nei processi successivi. Per altre informazioni, vedere [Avvisi ed errori del pacchetto di disegno](drawing-conversion-error-codes.md).

Quando si verifica un errore, il servizio di conversione fornisce un collegamento all' applicazione Web autonoma [Azure Maps Drawing Error Visualizer](drawing-error-visualizer.md) (Visualizzatore di errori di disegno di Mappe di Azure). È possibile usare il Visualizzatore di errori di disegno per controllare gli [Avvisi ed errori del pacchetto di disegno](drawing-conversion-error-codes.md) che si sono verificati durante il processo di conversione. Dopo aver corretto gli errori, è possibile tentare di caricare e convertire il pacchetto.

## <a name="create-indoor-map-data"></a>Creare dati della pianta di interni

Azure Maps Creator (anteprima) offre tre servizi:

* [Servizio Set di dati](/rest/api/maps/dataset/createpreview).
Usare il servizio Set di dati per creare un set di dati da un pacchetto di disegno convertito.
* [Servizio Set di tessere](/rest/api/maps/tileset/createpreview).
Usare il servizio Set di tessere per creare una rappresentazione basata su vettori di un set di dati. Le applicazioni possono usare un set di tessere per presentare una visualizzazione basata su tessere visiva del set di dati.
* [Servizio Stato della funzionalità](/rest/api/maps/featurestate). Utilizzare il servizio Stato della funzionalità per supportare l'applicazione dello stile alla pianta dinamica. L'applicazione dello stile alla pianta dinamica consente alle applicazioni di riflettere gli eventi in tempo reale sugli spazi forniti dal sistema IoT.

### <a name="datasets"></a>Set di dati

Un set di dati è una raccolta di funzionalità della pianta di interni. Le funzionalità della pianta di interni rappresentano le funzionalità definite in un pacchetto di disegno convertito. Dopo aver creato un set di dati con il [servizio Set di dati](/rest/api/maps/dataset/createpreview), è possibile creare un numero qualsiasi di [tessere](#tilesets) o [set di stati della funzionalità](#feature-statesets).

Il [servizio Set di dati](/rest/api/maps/dataset/createpreview) consente agli sviluppatori, in qualsiasi momento, di aggiungere o rimuovere le funzionalità di un set di dati esistente. Per altre informazioni su come aggiornare un set di dati esistente usando l'API, vedere le opzioni di accodamento in [Servizio Set di dati](/rest/api/maps/dataset/createpreview). Per un esempio di come aggiornare un set di dati, vedere [Manutenzione dei dati](#data-maintenance).

### <a name="tilesets"></a>Set di tessere

Un set di tessere è una raccolta di dati vettoriali che rappresenta un set di tessere della griglia uniformi. Gli sviluppatori possono usare il [servizio Set di tessere](/rest/api/maps/tileset/createpreview) per creare tessere da un set di dati.

Per riflettere diverse fasi del contenuto, è possibile creare più set di tessere dallo stesso set di dati. Ad esempio, è possibile creare un set di tessere con mobili e attrezzature e un'altra tessera senza mobili e attrezzature.  Si può scegliere di generare un set di tessere con gli aggiornamenti di dati più recenti e uno senza tali aggiornamenti.

Oltre ai dati vettoriali, i set di tessere forniscono i metadati per l'ottimizzazione del rendering della mappa. Ad esempio, i metadati del set di tessere includono un livello di ingrandimento minimo e massimo per il set di tessere. I metadati forniscono anche un rettangolo di selezione che definisce l'estensione geografica del set di tessere. Il rettangolo di selezione consente a un'applicazione di impostare a livello di codice il punto centrale corretto. Per altre informazioni sui metadati dei set di tessere, vedere l'[API dell'elenco di set di tessere](/rest/api/maps/tileset/listpreview).

Una volta creato, un set di tessere può essere recuperato tramite il [servizio di rendering V2](#render-v2-service).

Se un set di tessere diventa obsoleto e non è più utile, può essere eliminato. Per altre informazioni su come eliminare il set di tessere, vedere [Manutenzione dei dati](#data-maintenance).

>[!NOTE]
>Un set di tessere è indipendente dal set di dati da cui è stato creato. Se si creano set di tessere da un set di dati e successivamente si aggiorna tale set di dati, il set di tessere non verrà aggiornato. Per riflettere le modifiche in un set di dati, è necessario creare nuovi set di tessere. Analogamente, se si elimina un set di tessere, il set di dati non sarà interessato.

### <a name="feature-statesets"></a>Set di stati della funzionalità

I set di stati della funzionalità sono raccolte di proprietà dinamiche (*stati*) assegnate alle funzionalità del set di dati, ad esempio locali o attrezzature. Un esempio di uno *stato* può essere la temperatura o l'occupazione. Ogni *stato* è una coppia chiave/valore contenente il nome della proprietà, il valore e il timestamp dell'ultimo aggiornamento.

Il [servizio Stato della funzionalità](/rest/api/maps/featurestate/createstatesetpreview) consente di creare e gestire un set di stati della funzionalità per un set di dati. Il set di stati è definito da uno o più *stati*. A ogni funzionalità, ad esempio una stanza, può essere associato uno *stato*.

Il valore di ogni *stato* in un set di stati può essere aggiornato o recuperato dai dispositivi IoT o da altre applicazioni.  Se ad esempio si usa l'[API di aggiornamento dello stato della funzionalità](/rest/api/maps/featurestate/updatestatespreview), i dispositivi che misurano l'occupazione dello spazio possono inserire sistematicamente la modifica dello stato di una stanza.

Un'applicazione può usare un set di stati della funzionalità per eseguire dinamicamente il rendering delle funzionalità in una struttura in base allo stato corrente e allo stile di mappa corrispondente. Per altre informazioni sull'uso del set di stati della funzionalità per applicare uno stile alle funzionalità in una mappa di rendering, vedere [Modulo SDK Web per interni](#indoor-maps-module).

>[!NOTE]
>Analogamente ai set di tessere, la modifica di un set di dati non influisce sul set di stati della funzionalità esistente e l'eliminazione di un set di stati della funzionalità non avrà alcun effetto sul set di dati a cui è collegato.

## <a name="using-indoor-maps"></a>Utilizzo di piante di interni

### <a name="render-v2-service"></a>Servizio di rendering V2

L'API delle [tessere mappa](/rest/api/maps/renderv2/getmaptilepreview) di Azure per il rendering V2 è stata estesa per supportare Creator (Preview) riquadri.

Il servizio di rendering V2-API Get Map Tile di mappe di Azure consente alle applicazioni di richiedere set di tessere. I set di tessere possono quindi essere integrati in un controllo mappa o in un SDK. Per un esempio di un controllo mappa che usa il servizio di rendering V2, vedere [Modulo di piante di interni](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API Web Feature Service

È possibile eseguire query sui set di dati usando l'[API Web Feature Service (WFS)](/rest/api/maps/wfs). Il WFS segue le [funzionalità dell'API Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). L'API WFS consente di eseguire query sulle funzionalità all'interno del set di dati stesso. Ad esempio, è possibile usare il WFS per trovare tutte le sale riunioni di medie dimensioni di una determinata struttura e un livello di piano.

### <a name="indoor-maps-module"></a>Modulo di piante di interni

L'[SDK Web di Mappe di Azure](./index.yml) include il modulo Piante di interni. Questo modulo offre funzionalità estese per la libreria del *controllo mappa* di Mappe di Azure. Il modulo Maps indoor esegue il rendering delle mappe interne create in Creator (Preview). Integra i widget come la *selezione piano*, che consente agli utenti di visualizzare i diversi piani.

Il modulo Piante di interni consente di creare applicazioni Web che integrano i dati delle piante di interni con altri [servizi di Azure Maps](./index.yml). Le configurazioni delle applicazioni più comuni possono includere l'aggiunta di conoscenze a piante di interni da altre mappe quali strade, immagini, meteo e transito.

Inoltre, il modulo Piante di interni supporta l'applicazione dello stile alla mappa dinamica. Per una procedura dettagliata su come implementare l'applicazione dello stile dinamico al set di dati della funzionalità in un'applicazione, vedere [Come usare il modulo Piante di interni](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integrazione di Mappe di Azure

Quando si inizia a sviluppare soluzioni per le piante di interni, è possibile individuare modi per integrare le funzionalità di Mappe di Azure esistenti. Ad esempio, è possibile implementare gli scenari di rilevamento o di sicurezza degli asset usando l'[API Geofence di Mappe di Azure](/rest/api/maps/spatial/postgeofence) con le piante di interni di Creator. L'API Geofence può essere usata per determinare, ad esempio, se un lavoratore accede o esce da aree interne specifiche. Altre informazioni su come connettere Mappe di Azure alla telemetria IoT sono disponibili [qui](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Manutenzione dei dati

 L'API per l'elenco, l'aggiornamento e l'eliminazione delle mappe di Azure (anteprima) consente di elencare, aggiornare ed eliminare i set di impostazioni, riquadri e la funzionalità statesets.

>[!NOTE]
>Ogni volta che si esamina un elenco di elementi e si decide di eliminarli, è necessario considerare l'effetto di tale eliminazione su tutte le API o applicazioni dipendenti. Ad esempio, se è necessario eliminare un set di tessere che è attualmente usato da un'applicazione mediante [Rendering V2 - API Get Map Tile](/rest/api/maps/renderv2/getmaptilepreview), l'eliminazione di tale set di tessere comporterebbe un errore dell'applicazione nell'esecuzione del rendering di quel set di tessere.

### <a name="example-updating-a-dataset"></a>Esempio: Aggiornamento di un set di dati

L'esempio seguente illustra come aggiornare un set di dati, creare un nuovo set di tessere ed eliminare un set di tessere obsoleto.

1. Seguire la procedura descritta nelle sezioni [Caricare un pacchetto di disegno](#upload-a-drawing-package) e [Convertire un pacchetto di disegno](#convert-a-drawing-package) per caricare e convertire il nuovo pacchetto di disegno.

2. Usare l'[API di creazione del set di dati](/rest/api/maps/dataset/createpreview) per accordare i dati convertiti al set di dati esistente.

3. Usare l'[API di creazione del set di tessere](/rest/api/maps/tileset/createpreview) per generare un nuovo set di tessere dal set di dati aggiornato. Salvare il nuovo ID del set di tessere per il passaggio 4.

4. Aggiornare l'identificatore del set di tessere nell'applicazione per abilitare la visualizzazione del set di dati del complesso aggiornato. Se il vecchio set di tessere non è più in uso può essere eliminato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: creazione di una mappa indoor di Creator (Preview)](tutorial-creator-indoor-maps.md)