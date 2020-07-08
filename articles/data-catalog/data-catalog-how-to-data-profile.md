---
title: Come utilizzare origini dati di profiling dati in Azure Data Catalog
description: Articolo sulle procedure che illustra come includere profili dati a livello di tabella e di colonna durante la registrazione delle origini dati in Azure Data Catalog e come usare i profili dati per comprendere le origini dati.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 698da509be2f375925459bbdd4a35d21c6ff4407
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84171226"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Come profilare origini dati in Azure Data Catalog

## <a name="introduction"></a>Introduzione

**Microsoft Azure Data Catalog** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, **Azure Data Catalog** è tutto per aiutare gli utenti a individuare, comprendere e usare le origini dati e ad aiutare le organizzazioni a ottenere più valore dai dati esistenti. Quando un'origine dati viene registrata con **Azure Data Catalog**, i relativi metadati vengono copiati e indicizzati dal servizio, ma non è tutto.

La funzione di **profiling dati** di **Azure Data Catalog** esamina i dati delle origini dati supportate nel catalogo e raccoglie statistiche e informazioni sui dati. È facile includere un profilo degli asset di dati. Quando si registra un asset di dati, scegliere **Includi profilo dati** nello strumento di registrazione delle origini dati.

## <a name="what-is-data-profiling"></a>Informazioni sul profiling dati

Il profiling dati esamina i dati nell'origine dati di cui è in corso la registrazione e raccoglie statistiche e informazioni sui dati. Durante l'individuazione delle origini dati, le statistiche consentono di determinare l'idoneità dei dati per la risoluzione del problema aziendale.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Le origini dati seguenti supportano il profiling dati:

* Viste e tabelle di SQL Server, inclusi database SQL di Azure e Azure SQL Data Warehouse.
* Viste e tabelle di Oracle
* Viste e tabelle di Teradata
* Tabelle Hive

Includendo i profili dati durante la registrazione degli asset di dati gli utenti possono rispondere a domande sulle origini dati, ad esempio:

* Può essere usata per risolvere il problema aziendale?
* I dati sono conformi a standard o modelli particolari?
* Quali sono alcune delle anomalie dell'origine dati?
* Quali sono i possibili problemi legati all'integrazione di questi dati nell'applicazione?

> [!NOTE]
> È anche possibile aggiungere della documentazione a un asset per descrivere come integrare i dati in un'applicazione. Vedere l'articolo relativo alla [documentazione delle origini dati](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Come includere un profilo dati durante la registrazione di un'origine dati

È facile includere un profilo dell'origine dati. Quando si registra un'origine dati, nel pannello **Oggetti da registrare** dello strumento di registrazione delle origini dati scegliere **Includi profilo dati**.

![Casella di controllo Includi profilo dati](media/data-catalog-data-profile/data-catalog-register-profile.png)

Per altre informazioni su come registrare le origini dati, vedere [Come registrare le origini dati](data-catalog-how-to-register.md) e [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Applicazione di filtri su asset di dati che includono profili dati

Per trovare asset di dati che includono un profilo dati, è possibile specificare `has:tableDataProfiles` o `has:columnsDataProfiles` come termini di ricerca.

> [!NOTE]
> Selezionando **Includi profilo dati** nello strumento di registrazione dell'origine dati, è possibile includere le informazioni del profilo a livello di tabella e a livello di colonna. Tuttavia, l'API del catalogo dati consente la registrazione degli asset di dati con un solo set di informazioni sul profilo.
>

## <a name="viewing-data-profile-information"></a>Visualizzazione delle informazioni sul profilo dati

Dopo aver individuato un'origine dati adatta con un profilo, è possibile visualizzare i dettagli relativi al profilo dati. Per visualizzare il profilo dati, selezionare un asset di dati e scegliere **Profilo dati** nella finestra del portale di Azure Data Catalog.

![Scheda profilo dati](media/data-catalog-data-profile/data-catalog-view.png)

Un profilo dati in **Azure Data Catalog** include informazioni sul profilo a livello di tabella e di colonna, ad esempio:

### <a name="object-data-profile"></a>Profilo dati dell'oggetto

* Numero di righe
* Dimensioni della tabella
* Ultimo aggiornamento dell'oggetto

### <a name="column-data-profile"></a>Profilo dati della colonna

* Tipo di dati della colonna
* Numero di valori distinct
* Numero di righe con valori NULL
* Deviazione minima, massima, media e standard per i valori di colonna

## <a name="summary"></a>Summary

Il profiling dati fornisce statistiche e informazioni sugli asset di dati registrati per consentire di determinare l'idoneità dei dati per la risoluzione di problemi aziendali. Oltre che annotare e documentare le origini dati, i profili dati permettono agli utenti di comprendere meglio i dati.

## <a name="see-also"></a>Vedere anche

* [Come registrare le origini dati](data-catalog-how-to-register.md)
* [Introduzione al Catalogo dati di Azure](data-catalog-get-started.md)
