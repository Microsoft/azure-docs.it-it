---
title: Set di dati pubblici per l'analisi di AzurePublic data sets for Azure analytics
description: Informazioni sui set di dati pubblici che è possibile usare per prototipi e test di servizi e soluzioni di analisi di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 0fe20c90cf857ce09a83c6ac53e6fe77eb512153
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528200"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Set di dati pubblici per test e prototipi

In questo elenco di set di dati pubblici è possibile trovare dati da usare per prototipi e test di servizi e soluzioni di archiviazione e analisi.

## <a name="us-government-and-agency-data"></a>Dati del governo e delle agenzie degli Stati Uniti

| Origine dati | Informazioni sui dati | Informazioni sui file |
|---|---|---|
| [Dati del US Gov](https://catalog.data.gov/dataset) | Oltre 250.000 set di dati che coprono l'agricoltura, il clima, i consumatori, gli ecosistemi, l'istruzione, l'energia, la finanza, la salute, il governo locale, la produzione, il settore marittimo, l'oceano, la sicurezza pubblica e la scienza e la ricerca negli Stati Uniti. | File di diverse dimensioni e vari formati, tra cui HTML, XML, CSV, JSON, Excel e molti altri. È possibile filtrare i set di dati disponibili in base al formato di file. |
| [Dati di censimento degli Stati Uniti](https://www.census.gov/data.html) | Dati statistici sulla popolazione degli Stati Uniti. | I set di dati sono disponibili in vari formati. |
| [Dati di scienze della Terra della NASA](https://earthdata.nasa.gov/) | Oltre 32.000 raccolte di dati riguardanti agricoltura, atmosfera, biosfera, clima, criosfera, dimensioni umane, idrosfera, superficie terrestre, oceani, interazioni Terra-Sole e altro ancora. | I set di dati sono disponibili in vari formati. |
| [Ritardi nei voli e altri dati sui trasporti delle compagnie aeree](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Il Bureau of Transportation Statistics (BTS) del Dipartimento dei Trasporti degli Stati Uniti (DOT) tiene traccia delle prestazioni puntuali dei voli nazionali operati da grandi vettori aerei. Informazioni di riepilogo sul numero di voli puntuali, in ritardo, annullati o deviati sono riportate... in tabelle di riepilogo pubblicate nel sito Web". | I file sono disponibili in formato CSV. |
| [Incidenti stradali mortali: Fatality Analysis Reporting System (FARS) degli Stati Uniti](https://www.nhtsa.gov/FARS) | "FARS è un censimento a livello nazionale che fornisce a NHTSA, Congresso e pubblico americano dati annuali sugli incidenti stradali mortali nel traffico di veicoli a motore". | "È possibile creare online dati personalizzati sugli incidenti mortali usando il sistema di query FARS oppure scaricare tutti i dati FARS dal 1975 a oggi dal sito FTP". |
| [Dati chimici tossici - Dati EPA Toxicity&trade;ForeCaster (ToxCast)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "I dati high-throughput più aggiornati disponibili al pubblico dell'EPA sulla tossicità di migliaia di sostanze chimiche. Questi dati vengono generati tramite l'iniziativa di ricerca ToxCast dell'EPA". | I set di dati sono disponibili in vari formati, tra cui fogli di calcolo, pacchetti R e file di database MySQL. |
| [Dati su sostanze chimiche tossiche: NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "La sfida Tox21 Data Challenge 2014 è stata progettata per consentire agli scienziati di comprendere il potenziale delle sostanze chimiche e dei composti sottoposti a test tramite l'iniziativa Toxicology in the 21st Century per interrompere i percorsi biologici in modi che potrebbero determinare effetti tossici". | I set di dati sono disponibili in formato SMILES e SDF. Includono "dati sulle attività di analisi e sulle strutture chimiche della raccolta di circa 10.000 composti (Tox21 10K) di Tox21". |
| [Dati su biotecnologia e genomi del National Center for Biotechnology Information (NCBI)](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Più set di dati riguardanti geni, genomi e proteine. | I set di dati sono in formato di testo, XML, BLAST e di altro tipo. È disponibile un'app BLAST. |

## <a name="other-statistical-and-scientific-data"></a>Altri dati statistici e scientifici

| Origine dati | Informazioni sui dati | Informazioni sui file |
|---|---|---|
| [Dati relativi ai taxi di New York City](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "I record relativi alle corse dei taxi includono campi contenenti data e ora di partenza e di arrivo, luogo di partenza e di arrivo, distanze delle corse, dettaglio delle tariffe, tipi di tariffa, tipi di pagamento e numero di passeggeri segnalato dal tassista". | I set di dati sono disponibili in file CSV per singolo mese. |
| [Set di dati di Microsoft Research: analisi scientifica dei dati per la ricerca](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Più set di dati riguardanti l'interazione uomo-computer, audio/video, data mining/recupero informazioni, posizione geospaziale, elaborazione del linguaggio naturale e robotica/visione artificiale. | I set di dati sono disponibili in vari formati, compressi per il download. |
| [Dati di Open Science Data Cloud](https://www.opensciencedatacloud.org/projects/) | "Open Science Data Cloud offre alla comunità scientifica risorse per l'archiviazione, la condivisione e l'analisi di set di dati scientifici a livello di terabyte e petabyte".| I set di dati sono disponibili in vari formati. |
| [Dati sul clima globale: WorldClim](https://worldclim.org/) | "WorldClim è un set di livelli di dati sul clima globale a griglia con risoluzione spaziale di circa 1 km2. Questi dati possono essere usati per la mappatura e la modellazione spaziale". | I file contengono dati geospaziali. Per altre informazioni, vedere [Data format](https://worldclim.org/formats1) (Formato dei dati). |
| [Dati sulla società umana: The GDELT Project](https://www.gdeltproject.org/data.html) | "The GDELT Project è il database aperto relativo alla società umana più grande e completo e con la risoluzione più elevata che sia mai stato creato". | I file di dati non elaborati sono disponibili in formato CSV. |
| [Dati di stima dei clic per annunci pubblicitari per l'apprendimento automatico di Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Il più grande set di dati per l'apprendimento automatico mai rilasciato al pubblico". Per altre informazioni, vedere [Criteo's 1 TB Click Prediction Dataset](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/) (Set di dati di stima dei clic di 1 TB di Criteo). | |
| [Set di dati di text mining ClueWeb09 di Lemur Project](https://www.lemurproject.org/clueweb09.php/) | "Il set di dati ClueWeb09 è stato creato per supportare la ricerca sul recupero informazioni e le tecnologie per il linguaggio umano correlate. È costituito da circa 1 miliardo di pagine Web in 10 lingue raccolte nei mesi di gennaio e febbraio 2009". | Vedere [Dataset Information](https://www.lemurproject.org/clueweb09/datasetInformation.php) (Informazioni sul set di dati).|

## <a name="online-service-data"></a>Dati di servizi online

| Origine dati | Informazioni sui dati | Informazioni sui file |
|---|---|---|
| [GitHub Archive](https://www.githubarchive.org/) | "Il progetto GitHub Archive ha lo scopo di registrare la sequenza temporale pubblica [degli eventi] di GitHub, archiviarla e renderla facilmente accessibile per analisi aggiuntive". | Scaricare gli archivi di eventi con codifica JSON in formato Gzip (con estensione gz) da un client Web. |
| [Dati sull'attività di GitHub del progetto GHTorrent](http://ghtorrent.org/) | "Il progetto GHTorrent è un'iniziativa che ha lo scopo di creare un mirror di dati offline, scalabile e disponibile per query offerto tramite l'API REST GitHub. GHTorrent monitora la sequenza temporale pubblica degli eventi di GitHub. Per ogni evento, recupera il contenuto e le dipendenze, in modo esauriente". | I dump del database MySQL sono in formato CSV. |
| [Dump dei dati di Stack Overflow](https://archive.org/details/stackexchange) | "Si tratta di un dump, reso anonimo, di tutti contenuti creati dagli utenti nella rete Stack Exchange [che include Stack Overflow]". | "Ogni sito [come Stack Overflow] viene formattato come archivio separato costituito da file XML compressi tramite 7-Zip, con compressione bzip2. L'archivio di ogni sito include post, utenti, voti, commenti, cronologia dei post e relativi collegamenti". |
