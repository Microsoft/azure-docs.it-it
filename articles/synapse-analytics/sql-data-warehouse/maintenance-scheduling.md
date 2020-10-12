---
title: Pianificazioni di manutenzione per il pool SQL sinapsi
description: La pianificazione della manutenzione consente ai clienti di pianificare gli eventi di manutenzione pianificati necessari usati da Azure sinapsi Analytics per implementare nuove funzionalità, aggiornamenti e patch.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 1c5bc4400e99fb1c24e321e623aaee523b9c7383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85210984"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usare le pianificazioni della manutenzione per gestire gli aggiornamenti e la manutenzione dei servizi

La funzionalità pianificazione della manutenzione integra le notifiche di manutenzione pianificata per l'integrità dei servizi, il monitoraggio Integrità risorse controllo e il servizio di pianificazione della manutenzione per il pool SQL sinapsi (data warehouse) in Azure sinapsi Analytics.

È consigliabile usare la pianificazione della manutenzione per scegliere un intervallo di tempo quando è consigliabile ricevere nuove funzionalità, aggiornamenti e patch. È necessario scegliere una finestra di manutenzione primaria e secondaria entro un periodo di sette giorni, ogni finestra deve essere compresa in intervalli di giorni distinti.

È ad esempio possibile pianificare una finestra primaria da sabato 22:00 a domenica 01:00, quindi pianificare una finestra secondaria di mercoledì 19:00 a 22:00. Se non è possibile eseguire la manutenzione durante la finestra di manutenzione primaria, la manutenzione verrà ritentata durante la finestra di manutenzione secondaria. La manutenzione del servizio può avvenire occasionalmente durante le finestre primarie e secondarie. Per garantire il completamento rapido di tutte le operazioni di manutenzione, DW400c e livelli di data warehouse inferiori possono completare la manutenzione al di fuori di una finestra di manutenzione designata.

Per tutte le istanze di data warehouse appena create viene applicata una pianificazione di manutenzione definita dal sistema durante la distribuzione. È possibile modificare la pianificazione non appena la distribuzione viene completata.

Anche se una finestra di manutenzione può essere compresa tra tre e otto ore, questo non significa che il data warehouse sarà offline per la durata. La manutenzione può essere eseguita in qualsiasi momento all'interno di tale finestra ed è necessario aspettarsi una singola disconnessione durante tale periodo che dura ~ 5 -6 minuti perché il servizio distribuisce nuovo codice nel data warehouse. DW400c e Lower possono riscontrare più brevi perdite di connettività in momenti diversi durante la finestra di manutenzione. Quando viene avviata la manutenzione, tutte le sessioni attive verranno annullate e verrà eseguito il rollback delle transazioni di cui non è stato eseguito il commit. Per ridurre al minimo i tempi di inattività delle istanze, assicurarsi che non siano presenti transazioni con esecuzione prolungata nel data warehouse prima del periodo di manutenzione scelto.

Tutte le operazioni di manutenzione devono terminare entro le finestre di manutenzione specificate, a meno che non sia necessario distribuire un aggiornamento sensibile al tempo. Se il data warehouse è sospeso durante una manutenzione pianificata, verrà aggiornato durante l'operazione di ripresa. Si riceverà una notifica subito dopo il completamento della manutenzione del data warehouse.

## <a name="alerts-and-monitoring"></a>Avvisi e monitoraggio

L'integrazione con le notifiche sull'integrità dei servizi e il monitoraggio per il controllo dell'integrità delle risorse consente ai clienti di essere aggiornati sulle attività di manutenzione imminenti. Questa automazione sfrutta i vantaggi di monitoraggio di Azure. È possibile decidere come si vuole ricevere una notifica degli eventi di manutenzione imminenti. Inoltre, è possibile scegliere i flussi automatizzati che consentiranno di gestire i tempi di inattività e ridurre al minimo l'effetto operativo.

Una notifica di anticipo di 24 ore precede tutti gli eventi di manutenzione che non sono relativi al DWC400c e ai livelli inferiori.

> [!NOTE]
> Nel caso in cui sia necessario distribuire un aggiornamento critico del tempo, i tempi di notifica avanzati possono essere notevolmente ridotti.

Se è stata ricevuta una notifica di avanzamento, la manutenzione avrà luogo, ma non sarà possibile eseguire la manutenzione durante il periodo di tempo della notifica, si riceverà una notifica di annullamento. La manutenzione verrà quindi ripresa durante il successivo periodo di manutenzione pianificato.

Tutti gli eventi di manutenzione attivi verranno visualizzati nella sezione **Integrità dei servizi - Manutenzione pianificata**. La cronologia dell'integrità dei servizi include un record completo degli eventi precedenti. La manutenzione può essere monitorata tramite il dashboard del portale di controllo dell'integrità dei servizi di Azure durante un evento attivo.

### <a name="maintenance-schedule-availability"></a>Disponibilità della pianificazione della manutenzione

Anche se la pianificazione della manutenzione non è disponibile nell'area selezionata, è possibile visualizzare e modificare la pianificazione di manutenzione in qualsiasi momento. Quando la pianificazione della manutenzione diventa disponibile nella propria area geografica, la pianificazione identificata diventerà immediatamente attiva nel pool SQL sinapsi.

## <a name="view-a-maintenance-schedule"></a>Visualizzare una pianificazione della manutenzione

Per impostazione predefinita, tutte le istanze di data warehouse appena create hanno una finestra di manutenzione primaria e secondaria di otto ore applicata durante la distribuzione. Come indicato in precedenza, è possibile modificare le finestre non appena la distribuzione è completa. Nessuna manutenzione verrà eseguita al di fuori delle finestre di manutenzione specificate senza preavviso.

Per visualizzare la pianificazione di manutenzione applicata al pool SQL sinapsi, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il pool di sinapsi SQL che si vuole visualizzare.
3. Il pool SQL sinapsi selezionato verrà aperto nel pannello panoramica. La pianificazione di manutenzione applicata al data warehouse viene visualizzata sotto la **pianificazione della manutenzione**.

![Pannello Panoramica](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Modificare una pianificazione della manutenzione

Una pianificazione della manutenzione può essere aggiornata o modificata in qualsiasi momento. Se per l'istanza selezionata è in corso un ciclo di manutenzione attivo, le impostazioni verranno salvate. Diventeranno attive durante il successivo periodo di manutenzione identificato. [Altre informazioni](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sul monitoraggio del data warehouse durante un evento di manutenzione attiva.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificazione delle finestre primarie e secondarie

Le finestre primarie e secondarie devono avere intervalli di giorni separati. Un esempio è una finestra primaria da martedì a giovedì e una finestra secondaria da sabato a domenica.

Per modificare la pianificazione di manutenzione per il pool SQL sinapsi, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il pool di sinapsi SQL che si vuole aggiornare. La pagina viene aperta nel pannello Panoramica.
Aprire la pagina per le impostazioni della pianificazione di manutenzione selezionando il collegamento **Riepilogo pianificazione manutenzione** nel pannello panoramica. In alternativa, selezionare l'opzione **Pianificazione della manutenzione** nel menu delle risorse a sinistra.

    ![Opzioni del pannello Panoramica](./media/maintenance-scheduling/maintenance-change-option.png)

3. Scegliere l'intervallo di date preferito per la finestra di manutenzione primaria usando le opzioni nella parte superiore della pagina. Con questa selezione si stabilisce se la finestra primaria è prevista nei giorni feriali o durante il fine settimana. La selezione aggiornerà di conseguenza i valori dell'elenco a discesa.
Durante l'anteprima alcune aree potrebbero non supportare ancora il set completo di opzioni **Giorno**.

   ![Pannello delle impostazioni di manutenzione](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Scegliere le finestre di manutenzione primarie e secondarie desiderate usando gli elenchi a discesa seguenti:
   - **Giorno**: giorno preferito per eseguire la manutenzione durante la finestra selezionata.
   - **Ora di inizio**: orario di inizio preferito per la finestra di manutenzione.
   - **Intervallo di tempo**: durata preferita della finestra temporale.

   L'area **Riepilogo della pianificazione** nella parte inferiore del pannello viene aggiornata in base ai valori selezionati.
  
5. Selezionare **Salva**. Viene visualizzato un messaggio, che conferma che la nuova pianificazione è ora attiva.

   Se si salva una pianificazione in un'area che non supporta la pianificazione della manutenzione, viene visualizzato il seguente messaggio. Le impostazioni vengono salvate e diventeranno attive quando la funzione sarà disponibile nell'area selezionata.

   ![Messaggio sulla disponibilità in base all'area geografica](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sulla creazione, visualizzazione e gestione degli avvisi con Monitoraggio di Azure.
- [Altre informazioni](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sulle azioni webhook per le regole di avviso relative ai log.
- [Altre informazioni](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Creazione e gestione di gruppi di azioni.
- [Scopri di più](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) su integrità dei servizi di Azure.
