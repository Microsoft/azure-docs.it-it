---
title: Scenari utente di esempio per l'API di Analisi di testo
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra alcuni scenari comuni di integrazione dell'API Analisi del testo nei servizi e nei processi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219247"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Scenari utente di esempio per l'API di Analisi di testo

L'API Analisi del testo è un servizio basato sul cloud che fornisce l'elaborazione avanzata in linguaggio naturale sul testo. Questo articolo descrive alcuni casi d'uso di esempio per l'integrazione dell'API nelle soluzioni e nei processi aziendali. 

## <a name="analyze-survey-results"></a>Analizzare i risultati di un sondaggio

È possibile ottenere informazioni dettagliate dai risultati di un sondaggio condotto su clienti e dipendenti elaborando le risposte di testo non elaborato tramite l'analisi del sentiment. È quindi possibile aggregare i risultati per l'analisi e il completamento e per incrementare il coinvolgimento degli utenti.

![Immagine che illustra come eseguire l'analisi del sentiment sui sondaggi condotti su clienti e dipendenti.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizzare le chiamate dei clienti in ingresso registrate

Estrarre informazioni approfondite dalle chiamate al servizio clienti usando la sintesi vocale, l'analisi del sentiment, e l'estrazione di frasi chiave. Visualizzare i risultati nel dashboard di Power BI o in un portale per conoscere meglio i clienti, evidenziare le tendenze del servizio clienti e aumentare il coinvolgimento dei clienti. Inviare le richieste API come batch per la creazione di report o in tempo reale per gli interventi. Vedere il codice [di esempio su GitHub](https://github.com/rlagh2/callcenteranalytics).

![Immagine che illustra come automatizzare l'acquisizione di informazioni accurate dalle chiamate al servizio clienti usando l'analisi del sentiment](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Elaborare e classificare le richieste di assistenza

Usare l'estrazione di frasi chiave e il riconoscimento di entità per elaborare le richieste di assistenza inviate in formato testuale non strutturato. Usare le entità e le frasi estratte per classificare le richieste per la pianificazione delle risorse e l'analisi delle tendenze.

![Immagine che illustra come usare l'estrazione di frasi chiave e il riconoscimento di entità per classificare i report degli incidenti e le tendenze](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorare i feed di social media del prodotto

Monitorare i commenti e suggerimenti degli utenti sul prodotto nella pagina Twitter o Facebook del prodotto. Usare i dati per analizzare il sentiment dei clienti verso il lancio di nuovi prodotti, estrarre le frasi chiave sulle funzionalità e le richieste di funzionalità o rispondere ai reclami dei clienti in tempo reale. Vedere l'esempio [Modello di Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Immagine che illustra come monitorare i commenti sul prodotto e sull'azienda sui social media usando l'estrazione di frasi chiave](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Classificare e offuscare documenti con informazioni riservate

Usare il riconoscimento delle entità denominato per identificare le informazioni personali e riservate nei documenti. Usare i dati per classificare i documenti o offuscare in modo che possano essere condivisi in modo sicuro.

![Un'immagine che descrive come usare NER per rilevare le informazioni personali e classificare e offuscare i documenti](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Analisi del testo](overview.md)
* [Inviare una richiesta all'API Analisi del testo con la libreria client](quickstarts/text-analytics-sdk.md)
