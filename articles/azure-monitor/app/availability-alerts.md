---
title: Configurare gli avvisi di disponibilità con applicazione Azure Insights | Microsoft Docs
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 19a0e94206691dc741020374bf5c827ba423515d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008354"
---
# <a name="availability-alerts"></a>Avvisi di disponibilità

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Consente di avvisare l'utente se l'applicazione non risponde o se risponde troppo lentamente.

## <a name="enable-alerts"></a>Attivare gli avvisi

Gli avvisi vengono ora abilitati automaticamente per impostazione predefinita, ma per poter configurare completamente l'avviso è necessario innanzitutto creare il test di disponibilità.

![Esperienza di creazione](./media/availability-alerts/create-test.png)

> [!NOTE]
>  con i [nuovi avvisi unificati](../platform/alerts-overview.md), la gravità delle regole di avviso e le preferenze di notifica con [gruppi di azioni](../platform/action-groups.md) **devono essere** configurate nell'esperienza degli avvisi. Se non si esegue la procedura seguente, non si riceveranno notifiche all'interno del portale.

1. Dopo il salvataggio del test di disponibilità, nella scheda Dettagli fare clic sui puntini di sospensione accanto al test appena creato. Fare clic su "Modifica avviso".

   ![Modifica dopo il salvataggio](./media/availability-alerts/edit-alert.png)

2. Impostare il livello di gravità desiderato, la descrizione della regola ed, elemento ancora più importante, il gruppo di azioni con le preferenze di notifica che si vogliono usare per questa regola di avviso.

   ![Modifica dopo il salvataggio](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Gli avvisi di disponibilità creati tramite questa esperienza sono basati sullo stato. Ciò significa che quando viene raggiunto il criterio di avviso, viene generato un singolo avviso quando il sito viene rilevato come non disponibile. Se il sito è ancora inattivo la volta successiva che vengono valutati i criteri di avviso, non verrà generato un nuovo avviso. Quindi, se il sito è inattivo per un'ora e si è configurato un avviso di posta elettronica, si riceverà un messaggio di posta elettronica solo quando il sito è stato disattivato e un messaggio di posta elettronica successivo al backup del sito. Non si riceveranno avvisi continui ricordando che il sito era ancora non disponibile.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Inviare avvisi per X di Y località con segnalazione di errori

Questa regola di avviso è abilitata per impostazione predefinita nell'[esperienza dei nuovi avvisi unificati](../platform/alerts-overview.md), quando si crea un nuovo test di disponibilità. È possibile rifiutarla esplicitamente selezionando l'opzione "classica" o scegliendo di disabilitare la regola di avviso.

> [!NOTE]
> Configurare i gruppi di azioni per ricevere le notifiche quando viene attivato l'avviso seguendo la procedura precedente. Se non si esegue questa procedura, le notifiche all'interno del portale si riceveranno solo quando la regola viene attivata.
>

### <a name="alert-on-availability-metrics"></a>Inviare avvisi per le metriche di disponibilità

Usando i [nuovi avvisi unificati](../platform/alerts-overview.md) è possibile generare avvisi per la disponibilità aggregata segmentata e anche per le metriche di durata dei test:

1. Selezionare una risorsa di Application Insights nell'esperienza delle metriche e quindi selezionare una metrica di disponibilità:

    ![Selezione delle metriche di disponibilità](./media/availability-alerts/select-metric.png)

2. L'opzione Configura avvisi disponibile nel menu consentirà di accedere alla nuova esperienza in cui è possibile selezionare test specifici o località per le quali configurare la regola di avviso. In questa schermata è anche possibile configurare i gruppi di azioni per questa regola di avviso.

### <a name="alert-on-custom-analytics-queries"></a>Inviare avvisi per le query di analisi personalizzate

Usando i [nuovi avvisi unificati](../platform/alerts-overview.md) è possibile inviare avvisi per le [query di log personalizzate](../platform/alerts-unified-log.md). Con le query personalizzate è possibile inviare avvisi per qualsiasi condizione arbitraria che consenta di ottenere il segnale più affidabile di problemi di disponibilità. Questa operazione è applicabile anche se si inviano risultati di disponibilità personalizzati con TrackAvailability SDK.

> [!Tip]
> Le metriche sui dati di disponibilità includono tutti i risultati di disponibilità personalizzati che si possono inviare chiamando l'SDK TrackAvailability. È possibile usare gli avvisi per il supporto delle metriche per inviare avvisi sui risultati di disponibilità personalizzati.
>

## <a name="automate-alerts"></a>Automatizzare gli avvisi

Per automatizzare questo processo con i modelli di Azure Resource Manager, vedere la documentazione [creare un avviso di metrica con gestione risorse modello](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) .

## <a name="troubleshooting"></a>Risoluzione dei problemi

[Articolo sulla risoluzione dei problemi](troubleshoot-availability.md) dedicato.

## <a name="next-steps"></a>Passaggi successivi

* [Test Web in più passaggi](availability-multistep.md)
* [Test Web di ping URL](monitor-web-app-availability.md)
