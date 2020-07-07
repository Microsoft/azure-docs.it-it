---
title: Panoramica dell'acceleratore di soluzioni di manutenzione predittiva - Azure | Microsoft Docs
description: Panoramica dell'acceleratore di soluzioni di manutenzione predittiva di Azure, che consente di stimare il punto in cui è probabile che si verifichi un errore per uno scenario aziendale.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73827422"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Panoramica dell'acceleratore di soluzioni di manutenzione predittiva

L'acceleratore di soluzioni di manutenzione predittiva è una soluzione end-to-end per uno scenario aziendale che consente di stimare il punto in cui è probabile che si verifichino errori. È possibile usare l'acceleratore di soluzioni in modo proattivo per attività come l'ottimizzazione della manutenzione. La soluzione combina i servizi chiave degli acceleratori di soluzioni di Azure IoT, ad esempio Hub IoT e un'area di lavoro di [Azure Machine Learning][lnk-machine-learning]. L'area di lavoro contiene un modello, basato su un set di dati di esempio pubblico, per stimare la vita utile rimanente del motore di un velivolo. La soluzione implementa completamente lo scenario aziendale IoT come punto di partenza per poter pianificare e implementare una soluzione che soddisfi i propri requisiti aziendali.

Il codice dell'acceleratore di soluzioni di manutenzione predittiva è [disponibile su GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici dell'acceleratore di soluzioni:

![Architettura logica][img-architecture]

Gli elementi blu sono servizi di Azure di cui viene effettuato il provisioning nell'area in cui è stato distribuito l'acceleratore di soluzioni. L'elenco di aree in cui è possibile distribuire l'acceleratore di soluzioni viene visualizzato nella [pagina di provisioning][lnk-azureiotsolutions].

L'elemento verde rappresenta un motore di aereo simulato. Altre informazioni su questi dispositivi simulati sono disponibili nella sezione [Dispositivi simulati](#simulated-devices).

Gli elementi grigi rappresentano i componenti che implementano le funzionalità di *gestione del dispositivo*. La versione corrente dell'acceleratore di soluzioni di manutenzione predittiva non effettua il provisioning di queste risorse. Per altre informazioni sulla gestione del dispositivo, fare riferimento all'[acceleratore di soluzioni di monitoraggio remoto][lnk-remote-monitoring].

## <a name="azure-resources"></a>Risorse di Azure

Nel portale di Azure passare al gruppo di risorse con il nome della soluzione scelto per visualizzare le risorse di cui è stato effettuato il provisioning.

![Risorse degli acceleratori][img-resource-group]

Quando si esegue il provisioning dell'acceleratore di soluzioni, viene visualizzato un messaggio di posta elettronica con un collegamento all'area di lavoro di Machine Learning. È anche possibile passare all'area di lavoro di Machine Learning dalla pagina [Acceleratori della soluzione di Microsoft Azure IoT][lnk-azureiotsolutions]. Quando lo stato della soluzione è **Ready** (Pronto), in questa pagina è disponibile un riquadro.

![Modello di Machine Learning][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivi simulati

Nell'acceleratore di soluzioni un dispositivo simulato è un motore di aereo. Il provisioning della soluzione viene effettuato con due motori associati a un singolo aereo. Ogni motore genera quattro tipi di dati di telemetria: il sensore 9, il sensore 11, il sensore 14 e il sensore 15 forniscono i dati necessari per il modello di Machine Learning che calcola la vita utile rimanente per il motore. Ogni dispositivo simulato invia i messaggi di telemetria seguenti all'hub IoT:

*Conteggio dei cicli*. Un ciclo è un volo completato con una durata compresa tra due e dieci ore. Durante il volo, i dati di telemetria vengono acquisiti ogni mezz'ora.

*Telemetria*. Sono presenti quattro sensori che registrano gli attributi del motore. I sensori sono indicati genericamente con l'etichetta Sensore 9, Sensore 11, Sensore 14 e Sensore 15. Questi quattro sensori inviano dati di telemetria sufficienti per ottenere risultati utili dal modello di vita utile rimanente. Il modello usato nell'acceleratore di soluzioni viene creato da un set di dati pubblico che include i dati reali dei sensori del motore. Per altre informazioni sulla modalità di creazione del modello dal set di dati originale, vedere il [modello di manutenzione predittiva in Cortana Intelligence Gallery][lnk-cortana-analytics].

I dispositivi simulati possono gestire i comandi seguenti inviati dall'hub IoT nella soluzione:

| Comando | Descrizione |
| --- | --- |
| StartTelemetry |Controlla lo stato della simulazione.<br/>Avvia il dispositivo che invia i dati di telemetria |
| StopTelemetry |Controlla lo stato della simulazione.<br/>Arresta il dispositivo che invia i dati di telemetria |

L'hub IoT fornisce il riconoscimento dei comandi del dispositivo.

## <a name="azure-stream-analytics-job"></a>Processo di Analisi di flusso di Azure

**Processo: i dati di telemetria** operano sul flusso di dati di telemetria del dispositivo in ingresso usando due istruzioni:

* La prima seleziona tutti i dati di telemetria dai dispositivi e li invia all'archivio BLOB. Da qui vengono visualizzati nell'app Web.
* La seconda calcola i valori medi dei sensori in una finestra temporale scorrevole di due minuti e invia i dati tramite l'hub eventi a un **processore di eventi**.

## <a name="event-processor"></a>Processore di eventi
L'**host processore di eventi** viene eseguito in un processo Web di Azure. Il **processore di eventi** considera i valori medi dei sensori per un ciclo completato e quindi li passa a un modello con training che calcola la vita utile rimanente di un motore. Un'API fornisce l'accesso al modello in un'area di lavoro di Machine Learning che fa parte della soluzione.

## <a name="machine-learning"></a>Machine Learning
Il componente Machine Learning usa un modello derivato dai dati raccolti da veri motori di aerei. È possibile passare all'area di lavoro Machine Learning dal riquadro della soluzione nella pagina [azureiotsolutions.com][lnk-azureiotsolutions] . Quando lo stato della soluzione è **Ready** (Pronto), il riquadro è disponibile.

Per visualizzare il funzionamento dei dati di telemetria raccolti attraverso i servizi degli acceleratori di soluzioni IoT, è disponibile il modello di Azure Machine Learning. Microsoft ha creato un [modello di regressione][lnk_regression_model] basato su dati pubblici<sup>\[[1]\]</sup> e le istruzioni dettagliate su come usare il modello.

L'acceleratore di soluzioni di manutenzione predittiva di Azure IoT usa il modello di regressione creato da questo modello. Il modello viene distribuito nella sottoscrizione di Azure e reso disponibile tramite un'API generata automaticamente. La soluzione include un subset di dati di test per 4 (su un totale di 100) motori e 4 (su un totale di 21) flussi di dati dei sensori. Questi dati sono sufficienti per fornire un risultato esatto dal modello con training.

*\[1 \] A. Saxena e K. Goebel (2008). "Set di dati di simulazione della riduzione del motore di turbofan", data repository della NASA Ames Prognostics ( https://c3.nasa.gov/dashlink/resources/139/) , NASA Ames Research Center, campo Moffett, CA*

## <a name="next-steps"></a>Passaggi successivi
Dopo avere esaminato i componenti chiave dell'acceleratore di soluzioni di manutenzione predittiva, è possibile personalizzarli.

È anche possibile esplorare alcune altre funzionalità degli acceleratori di soluzioni IoT:

* [Domande frequenti sugli acceleratori di soluzioni IoT][lnk-faq]
* [Sicurezza IoT sin dall'inizio][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
