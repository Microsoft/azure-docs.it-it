---
title: Elaborazione degli eventi in tempo reale con Analisi di flusso di AzureReal-time event processing using Azure Stream Analytics
description: Questo articolo descrive l'architettura di riferimento per ottenere processi di elaborazione eventi e analisi in tempo reale usando Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431477"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architettura di riferimento: elaborazione di eventi in tempo reale con Analisi di flusso di Microsoft Azure
L'architettura di riferimento per l'elaborazione di eventi in tempo reale con Analisi dei flussi di Azure è destinata a fornire un progetto generico per la distribuzione in tempo reale di una soluzione di elaborazione dei flussi PaaS con Microsoft Azure.

## <a name="summary"></a>Riepilogo
In genere, le soluzioni di analisi sono basate su funzionalità, ad esempio ETL (estrazione, trasformazione e caricamento) e data warehouse, in cui sono archiviati i dati prima dell'analisi. Le esigenze di modifica, tra cui i dati in arrivo con una maggiore rapidità, spingono al limite il modello esistente. Una soluzione è rappresentata dalla possibilità di analizzare i dati all'interno di flussi in movimento prima dell'archiviazione, e sebbene non si tratti di una nuova funzionalità, l'approccio non è stato adottato ampiamente in tutti i settori industriali verticali. 

Microsoft Azure fornisce un catalogo esteso di tecnologie di analisi in grado di supportare una matrice di scenari di soluzioni e requisiti diversi. Scegliere i servizi Azure da distribuire per una soluzione end-to-end può essere difficile data la vasta gamma di offerte. Questo documento è progettato per descrivere le funzionalità e l'interazione tra i vari servizi Azure che supportano una soluzione di flusso di eventi. Vengono inoltre illustrati alcuni degli scenari in cui i clienti possono trarre vantaggio da questo tipo di approccio.

## <a name="contents"></a>Sommario
* Sunto
* Introduzione all'analisi in tempo reale
* Proposta di valore dei dati in tempo reale in Azure
* Scenari comuni per l’analisi in tempo reale
* Architettura e componenti
  * Origini dati
  * Livello di integrazione dei dati
  * Livello di analisi in tempo reale
  * Livello di archiviazione dei dati
  * Livello presentazione / consumo
* Conclusioni

**Autore:** Charles Feddersen, Solution Architect, Data Insights Center of Excellence, Microsoft Corporation

**Pubblicato:** gennaio 2015

**Revisione:** 1.0

**Download:** [Elaborazione di eventi in tempo reale con Analisi dei flussi di Microsoft Azure](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

