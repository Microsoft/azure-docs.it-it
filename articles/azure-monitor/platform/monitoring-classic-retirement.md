---
title: Aggiornamento di avvisi classici & monitoraggio in monitoraggio di Azure
description: Descrizione del ritiro dei servizi e delle funzionalità di monitoraggio classici, illustrati in precedenza in portale di Azure in avvisi (versione classica).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 720a0e25f3486f32ffed897e54033fd4b68dace4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848126"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio

Monitoraggio di Azure è ora diventato un servizio di monitoraggio unificato dell'intero stack che supporta metriche unificate e avvisi unificati per tutte le risorse. Per altre informazioni, vedere il [post di blog sul nuovo servizio Monitoraggio di Azure](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Le nuove piattaforme di monitoraggio e avviso di Azure sono state create per essere più veloci, intelligenti ed estendibili, per tenere il passo con la continua crescita del cloud computing e in linea con la filosofia Microsoft di cloud intelligente.

Con la nuova piattaforma di monitoraggio e avviso di Azure, gli avvisi classici in monitoraggio di Azure vengono ritirati, anche se ancora in uso limitato per le risorse che non supportano ancora i nuovi avvisi. La data di ritiro per tali avvisi è stata estesa ulteriormente. Una nuova data verrà annunciata a breve per la migrazione degli avvisi rimanenti, il [cloud di Azure per enti pubblici](../../azure-government/documentation-government-welcome.md)e [Azure Cina 21ViaNet](https://docs.azure.cn/).

 ![Avviso classico nel portale di Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Gli utenti sono invitati a iniziare a usare la nuova piattaforma e ricreare al suo interno gli avvisi.

> [!IMPORTANT]
> Le regole di avviso classiche create nel log attività non verranno deprecate o spostate. È possibile accedere a tutte le regole di avviso classiche create nel log attività e usarle nel loro stato corrente dalla nuova pagina Monitoraggio di Azure - Avvisi. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi del log attività con Monitoraggio di Azure](./alerts-activity-log.md). Analogamente, è possibile accedere agli avvisi sull'integrità dei servizi e usarli nel loro stato corrente dalla nuova sezione Integrità dei servizi. Per informazioni dettagliate, vedere gli [avvisi per le notifiche sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Metriche e avvisi unificati in Application Insights

La nuova piattaforma per le metriche di Monitoraggio di Azure supporterà ora il monitoraggio da Application Insights. Ciò significa che Application Insights verrà collegato ai gruppi di azioni, per offrire un numero di opzioni molto più elevato rispetto alle precedenti funzionalità di chiamate di webhook e di posta elettronica. Gli avvisi possono ora attivare chiamate vocali, Funzioni di Azure, App per la logica, SMS e strumenti di Gestione dei servizi IT, ad esempio ServiceNow e i runbook di automazione. Grazie alle funzionalità di monitoraggio e avviso quasi in tempo reale, la nuova piattaforma consente agli utenti di Application Insights di sfruttare la stessa tecnologia alla base del monitoraggio in altre risorse di Azure e potenziare il monitoraggio dei prodotti Microsoft.

La nuova funzionalità unificata di monitoraggio e avviso per Application Insights comprenderà:

- **Metriche della piattaforma Application Insights**, per fornire popolari metriche predefinite del prodotto Application Insights. Per altre informazioni, vedere l'articolo sull'uso di [metriche della piattaforma per Application Insights nel nuovo servizio Monitoraggio di Azure](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test Web e della disponibilità di Application Insights**, che offrono la possibilità di valutare la velocità di risposta e la disponibilità del server o dell'app Web. Per altre informazioni, vedere l'articolo sull'uso di [test di disponibilità e avvisi per Application Insights nel nuovo servizio Monitoraggio di Azure](../app/monitor-web-app-availability.md).
- **Metriche personalizzate di Application Insights**, che permettono di definire e generare metriche personalizzate per il monitoraggio e gli avvisi. Per altre informazioni, vedere l'articolo sull'uso di [metriche personalizzate per Application Insights nel nuovo servizio Monitoraggio di Azure](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalie errori di Application Insights (parte della funzionalità di rilevamento intelligente)**, per l'invio di notifiche automatiche quasi in tempo reale se si verifica un incremento anomalo della frequenza di chiamate di dipendenze o richieste HTTP non riuscite nell'app Web. Per altre informazioni, vedere questo articolo sull'uso del [rilevamento intelligente-anomalie degli errori](../app/proactive-failure-diagnostics.md).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Metriche e avvisi unificati per altre risorse di Azure

Da marzo 2018, è disponibile la nuova generazione di avvisi e monitoraggio multidimensionale per le risorse di Azure. Ora la nuova piattaforma per le metriche e gli avvisi è più veloce e offre funzionalità quasi in tempo reale. È anche importante notare che gli avvisi della nuova piattaforma per le metriche offrono maggiore granularità, in quanto la nuova piattaforma include l'opzione delle dimensioni, grazie a cui è possibile sezionare e filtrare in base a una specifica combinazione di valori, condizione o operazione. Come tutti gli avvisi nel nuovo servizio Monitoraggio di Azure, i nuovi avvisi delle metriche offrono maggiore estendibilità grazie all'uso dei gruppi di azioni, che consentono di estendere le notifiche oltre la posta elettronica o i webhook, con la possibilità di scegliere anche SMS, funzionalità vocali, funzioni di Azure, runbook di automazione e altro ancora. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](./alerts-metric.md).
Le nuove metriche per le risorse di Azure sono disponibili come:

- **Metriche della piattaforma di Monitoraggio di Azure Standard**, che forniscono popolari metriche precompilate di diversi servizi e prodotti di Azure. Per altre informazioni, vedere l'articolo sulle [metriche supportate in Monitoraggio di Azure](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e quello sugli [avvisi delle metriche supportati in Monitoraggio di Azure](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Metriche personalizzate di Monitoraggio di Azure**, che forniscono metriche da origini definite dall'utente, tra cui l'agente di Diagnostica di Azure. Per altre informazioni, vedere l'articolo sulle [metriche personalizzate in Monitoraggio di Azure](./metrics-custom-overview.md). Usando le metriche personalizzate, è anche possibile pubblicare le metriche raccolte dall'[agente di Diagnostica di Microsoft Azure](./collect-custom-metrics-guestos-resource-manager-vm.md) e dall'[agente InfluxData Telegraf](./collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Ritiro della piattaforma classica di monitoraggio e avviso

Come indicato in precedenza, il monitoraggio e gli avvisi classici meno recenti vengono ritirati; inclusa la chiusura di API correlate, portale di Azure interfaccia e servizi, anche se ancora in uso limitato per le risorse che non supportano ancora i nuovi avvisi. In particolare, queste funzionalità verranno deprecate:

- Metriche e avvisi precedenti (versione classica) per le risorse di Azure attualmente disponibili tramite la [sezione Avvisi (versione classica)](./alerts-classic.overview.md) del portale di Azure, accessibili come risorsa [microsoft.insights/alertrules](/rest/api/monitor/alertrules)
- Piattaforma e metriche personalizzate precedenti (versione classica) per Azure Application Insights, oltre agli avvisi attualmente disponibili tramite la [sezione Avvisi (versione classica)](./alerts-classic.overview.md) del portale di Azure e accessibili come risorsa [microsoft.insights/alertrules](/rest/api/monitor/alertrules)
- Avviso Anomalie errori precedente (versione classica) attualmente disponibile come funzionalità [Rilevamento intelligente in Application Insights](../app/proactive-diagnostics.md) nel portale di Azure, con avvisi configurati visualizzati nella [sezione Avvisi (versione classica)](./alerts-classic.overview.md) del portale di Azure

Ciò significa:

- Il servizio di monitoraggio e avviso classico verrà ritirato e non sarà più disponibile per la creazione di nuove regole di avviso.
- Eventuali regole di avviso che continuano a esistere negli avvisi (versione classica) continueranno a essere eseguite e ad attivare le notifiche.
- Le regole di avviso nel monitoraggio classico & gli avvisi di cui è possibile eseguire la migrazione, verranno automaticamente spostati da Microsoft nell'equivalente nella nuova piattaforma di monitoraggio di Azure in fasi che interessano alcune settimane. Il processo avverrà in modo fluido, senza tempi di inattività e senza alcuna perdita nella copertura del monitoraggio per i clienti.
- Le regole di avviso di cui è stata eseguita la migrazione nella nuova piattaforma di avvisi garantiranno la copertura di monitoraggio come prima, ma genereranno le notifiche con nuovi payload. Qualsiasi indirizzo di posta elettronica, endpoint del webhook o collegamento dell'app per la logica associato alla regola di avviso classica verrà mantenuto in fase di migrazione, ma potrebbe non funzionare correttamente perché il payload degli avvisi sarà diverso nella nuova piattaforma.
- Alcune [regole di avviso classiche che non possono essere migrate automaticamente e che](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) richiedono azioni manuali da parte degli utenti continueranno a essere eseguite.

> [!IMPORTANT]
> Microsoft Azure monitor è stato implementato nello [strumento fasi per eseguire a breve la migrazione volontaria](alerts-using-migration-tool.md) delle regole di avviso classiche alla nuova piattaforma. Ed eseguirlo per forza per tutte le regole di avviso classiche ancora esistenti e di cui è possibile eseguire la migrazione. I clienti dovranno assicurarsi che il payload delle regole di avviso classiche, dispendioso in termini di automazione, sia adattato in modo da gestire il nuovo payload da [Metriche e avvisi unificati in Application Insights](#unified-metrics-and-alerts-in-application-insights) o [Metriche e avvisi unificati per altre risorse di Azure](#unified-metrics-and-alerts-for-other-azure-resources) dopo la migrazione delle regole di avviso classiche. Per ulteriori informazioni, vedere la pagina relativa alla [preparazione alla migrazione della regola di avviso classica](alerts-prepare-migration.md)

Questo articolo verrà continuamente aggiornato con collegamenti e informazioni sulle nuove funzionalità di monitoraggio e avviso di Azure e sulla disponibilità di strumenti di supporto per l'adozione della nuova piattaforma Monitoraggio di Azure.

## <a name="pricing-for-migrated-alert-rules"></a>Prezzi per le regole di avviso migrate

Viene implementato uno strumento di migrazione che consente di eseguire la migrazione degli [avvisi classici](./alerts-classic.overview.md) di monitoraggio di Azure alla nuova esperienza degli avvisi. Le regole di avviso migrate e i corrispondenti gruppi di azioni migrati (email, webhook o LogicApp) rimarranno gratuiti. La funzionalità con gli avvisi classici, inclusa la possibilità di modificare la soglia, il tipo di aggregazione e la granularità di aggregazione continuerà a essere disponibile gratuitamente con la regola di avviso migrata. Tuttavia, se si modifica la regola di avviso migrata in modo da usare una delle nuove funzionalità della piattaforma avvisi, le notifiche o i tipi di azione, viene applicato un addebito corrispondente. Per altre informazioni sui prezzi per le regole di avviso e le notifiche, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Di seguito sono riportati alcuni esempi di casi in cui verrà addebitato un addebito per la regola di avviso:

- Tutte le regole di avviso nuove (non sottoposte a migrazione) create oltre le unità gratuite, nella nuova piattaforma di Monitoraggio di Azure
- Tutti i dati inseriti e conservati oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutti i test Web multitest eseguiti da Application Insights
- Tutte le metriche personalizzate archiviate oltre le unità gratuite incluse in Monitoraggio di Azure
- Eventuali regole di avviso migrate modificate per l'uso di funzionalità di avviso di metrica più recenti, ad esempio frequenza, più risorse/dimensioni, [soglie dinamiche](alerts-dynamic-thresholds.md), modifica di risorse/segnali e così via.
- Tutti i gruppi di azioni migrati che vengono modificati per usare notifiche più recenti o tipi di azione quali SMS, chiamata vocale e integrazione con ITSM.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul nuovo [servizio unificato di Monitoraggio di Azure](../overview.md).
* Leggere le informazioni sui nuovi [avvisi di Azure](./alerts-overview.md).

