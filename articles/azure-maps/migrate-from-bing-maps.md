---
title: 'Esercitazione: Eseguire la migrazione da Bing Maps a Mappe di Azure | Mappe di Microsoft Azure'
description: Esercitazione su come eseguire la migrazione da Bing Maps a Mappe di Microsoft Azure. Vengono fornite indicazioni dettagliate su come passare alle API e agli SDK di Mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388958"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Esercitazione: Eseguire la migrazione da Bing Maps a Mappe di Azure

Questa guida fornisce informazioni dettagliate su come eseguire la migrazione di applicazioni Web, per dispositivi mobili e basate su server da Bing Maps alla piattaforma Mappe di Azure. La guida include esempi di codice comparativi, suggerimenti e procedure consigliate per la migrazione a Mappe di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Confronto generale tra funzionalità equivalenti di Bing Maps disponibili in Mappe di Azure.
> * Quali differenze di licenza prendere in considerazione.
> * Come pianificare la migrazione.
> * Dove trovare risorse tecniche e il supporto.

## <a name="prerequisites"></a>Prerequisiti

1. Accedere al [portale di Azure](https://portal.azure.com). Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
2. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Panoramica della piattaforma Mappe di Azure

Mappe di Azure offre agli sviluppatori di tutti i settori potenti funzionalità geospaziali che includono i dati di mappe più aggiornati disponibili per fornire contesto geografico alle applicazioni Web e per dispositivi mobili. Mappe di Azure è un set di API REST conforme all'API Azure One per mappe, ricerca, pianificazione percorso, traffico, fusi orari, geofencing, dati di mappe, dati meteo e molti altri servizi affiancati da SDK Web e Android per rendere lo sviluppo semplice, flessibile e portabile in più piattaforme. [Mappe di Azure è disponibile anche in Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Confronto generale tra piattaforme

La tabella seguente fornisce un elenco generale delle funzionalità di Bing Maps, con il relativo supporto per tali funzionalità in Mappe di Azure. L'elenco non include funzionalità aggiuntive di Mappe di Azure come accessibilità, API di geofencing, servizi per il traffico, operazioni spaziali, accesso diretto alle tessere mappa e servizi batch.

| Funzionalità di Bing Maps                     | Supporto di Mappe di Azure |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Pianificata            |
| UWP SDK                               | N/D                 |
| WPF SDK                               | N/D                 |
| API REST del servizio                     | ✓                  |
| Suggerimenti automatici                           | ✓                  |
| Indicazioni stradali (anche per camion)          | ✓                  |
| Matrice di distanze                       | ✓                  |
| Altitudini                            | ✓ (anteprima)        |
| Immagini - Mappa statica                  | ✓                  |
| Metadati di immagini                      | ✓                  |
| Isocrone                            | ✓                  |
| Informazioni dettagliate locali                        | ✓                  |
| Ricerca locale                          | ✓                  |
| Riconoscimento della posizione                  | ✓                  |
| Località (geocodifica diretta/inversa) | ✓                  |
| Percorsi di itinerari ottimizzati            | Pianificata            |
| Allineamento alle strade                         | ✓                  |
| Servizi dati spaziali (SDS)           | Partial            |
| Fuso orario                             | ✓                  |
| Incidenti stradali                     | ✓                  |
| Mappe basate sulla configurazione             | N/D                |

Bing Maps offre autenticazione di base basata su chiavi. Mappe di Azure fornisce sia l'autenticazione basata su chiavi di base, sia l'autenticazione di Azure Active Directory, estremamente sicura.

## <a name="licensing-considerations"></a>Considerazioni sulla gestione delle licenze

Quando si esegue la migrazione da Bing Maps a Mappe di Azure, è necessario considerare le informazioni seguenti in relazione alla gestione delle licenze.

* Mappe di Azure addebita i costi per l'utilizzo delle mappe interattive in base al numero di tessere mappa caricate, mentre Bing Maps li addebita per il caricamento del controllo mappa (sessioni). Per ridurre i costi per gli sviluppatori, Mappe di Azure memorizza automaticamente nella cache le tessere mappa. Viene generata una transazione di Mappe di Azure ogni 15 tessere mappa caricate. Gli Azure Maps SDK interattivi usano tessere da 512-pixel e generano in media una o meno transazioni per visualizzazione pagina.

* Mappe di Azure consente di archiviare in Azure i dati della sua piattaforma. I dati possono anche essere memorizzati in una cache altrove per un massimo di sei mesi, in base alle [condizioni per l'utilizzo](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Ecco alcune risorse legate alle licenze di Mappe di Azure:

-   [Pagina dei prezzi di Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Condizioni per l'utilizzo di Mappe di Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (incluse nelle condizioni per l'utilizzo di Microsoft Online Services)
-   [Scegliere il piano tariffario corretto in Mappe di Azure](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Piano di migrazione suggerito

Di seguito è riportato un esempio di un piano di migrazione generale.

1.  Fare un inventario degli SDK e dei servizi di Bing Maps usati dall'applicazione e verificare che Mappe di Azure fornisca SDK e servizi alternativi a cui eseguire la migrazione.
2.  Creare una sottoscrizione di Azure (se non se ne ha già una) all'indirizzo <https://azure.com>
3.  Creare un account di Mappe di Azure ([documentazione](./how-to-manage-account-keys.md)) e una chiave di autenticazione per Azure Active Directory ([documentazione](./how-to-manage-authentication.md)).
4.  Eseguire la migrazione del codice dell'applicazione.
5.  Testare l'applicazione dopo la migrazione.
6.  Distribuire l'applicazione di cui è stata eseguita la migrazione in ambiente di produzione.

## <a name="create-an-azure-maps-account"></a>Creare un account di Mappe di Azure

Per creare un account Mappe di Azure e ottenere l'accesso alla piattaforma Mappe di Azure, seguire questa procedura:

1. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
2. Accedere al [portale di Azure](https://portal.azure.com/).
3. Creare un [account Mappe di Azure](./how-to-manage-account-keys.md).
4. [Ottenere la chiave della sottoscrizione di Mappe di Azure](./how-to-manage-authentication.md#view-authentication-details) o configurare l'autenticazione di Azure Active Directory per una maggiore sicurezza.

## <a name="azure-maps-technical-resources"></a>Risorse tecniche di Mappe di Azure

Ecco un elenco di risorse tecniche utili per Mappe di Azure.

* Panoramica: <https://azure.com/maps>
* Documentazione: <https://aka.ms/AzureMapsDocs>
* Esempi di codice di Web SDK: <https://aka.ms/AzureMapsSamples>
* Forum per sviluppatori: <https://aka.ms/AzureMapsForums>
* Video: <https://aka.ms/AzureMapsVideos>
* Blog: <https://aka.ms/AzureMapsBlog>
* Feedback su Mappe di Azure (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Supporto per la migrazione

Gli sviluppatori possono richiedere supporto per la migrazione nei [forum](/answers/topics/azure-maps.html) o tramite una delle numerose opzioni di supporto di Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nuova terminologia

L'elenco seguente contiene i termini comuni di Bing Maps e i relativi termini di Mappe di Azure corrispondenti.

| Termine di Bing Maps                    | Termine di Mappe di Azure                                                |
|-----------------------------------|----------------------------------------------------------------|
| Aerea                            | Satellitare o aerea                                            |
| Indicazioni                        | Anche definite pianificazione percorso                             |
| Entità                          | Geometrie o caratteristiche                                         |
| `EntityCollection`                | Origine dati o livello                                           |
| `Geopoint`                        | Posizione                                                       |
| `GeoXML`                          | File XML nel modulo di I/O spaziale                             |
| Overlay sul terreno                    | Livello immagine                                                    |
| Ibrido (in riferimento al tipo di mappa) | Satellite con strade                                           |
| Casella informativa                           | Popup                                                          |
| Location                          | Posizione                                                       |
| `LocationRect`                    | Rettangolo di selezione                                                   |
| Tipo mappa                          | Stile mappa                                                      |
| Barra di navigazione                    | Selezione stile mappa, controllo dello zoom, controllo del pitch, controllo della bussola |
| Puntina da disegno                           | Livello bolle, livello simboli o marcatore HTML                      |

## <a name="clean-up-resources"></a>Pulire le risorse

Non sono presenti risorse che richiedono la pulizia.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come eseguire la migrazione dell'applicazione Bing Maps, vedere questi articoli:

> [!div class="nextstepaction"]
> [Eseguire la migrazione di un'app Web](migrate-from-bing-maps-web-app.md)
