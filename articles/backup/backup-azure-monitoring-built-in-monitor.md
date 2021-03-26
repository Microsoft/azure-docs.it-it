---
title: Monitorare i carichi di lavoro protetti di backup di Azure
description: Questo articolo illustra le funzionalità di monitoraggio e notifica per i carichi di lavoro di backup di Azure usando il portale di Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 83ed5af00bb61d7a8929e710b52e60c33c0f479b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559214"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitoraggio dei carichi di lavoro di backup di Azure

Backup di Azure offre più soluzioni di backup in base al requisito di backup e alla topologia dell'infrastruttura (in locale rispetto ad Azure). Tutti gli utenti o gli amministratori di backup dovrebbero vedere cosa accade in tutte le soluzioni e possono prevedere di ricevere notifiche in scenari importanti. Questo articolo descrive in dettaglio le funzionalità di monitoraggio e notifica fornite dal servizio backup di Azure.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Elementi di backup nell'insieme di credenziali di servizi di ripristino

È possibile monitorare tutti gli elementi di backup tramite un insieme di credenziali di servizi di ripristino. Passando alla sezione **elementi di backup** nell'insieme di credenziali si apre una visualizzazione che fornisce il numero di elementi di backup di ogni tipo di carico di lavoro associato all'insieme di credenziali. Quando si fa clic su una riga, viene aperta una visualizzazione dettagliata che elenca tutti gli elementi di backup del tipo di carico di lavoro specificato, con informazioni sull'ultimo stato di backup per ogni elemento, sul punto di ripristino più recente disponibile e così via.

![Elementi di backup dell'insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> Per gli elementi di cui è stato eseguito il backup in Azure con DPM, l'elenco mostrerà tutte le origini dati protette (sia disco che online) usando il server DPM. Se la protezione viene arrestata per l'origine dati con i dati di backup conservati, l'origine dati sarà ancora elencata nel portale. È possibile passare ai dettagli dell'origine dati per verificare se i punti di ripristino sono presenti nel disco, in linea o in entrambi. Inoltre, le origini dati per cui viene arrestata la protezione online ma vengono conservati i dati, la fatturazione per i punti di ripristino online continua fino a quando i dati non vengono eliminati completamente.
>
> Per visualizzare gli elementi di backup nel portale dell'insieme di credenziali di servizi di ripristino, la versione di DPM deve essere DPM 1807 (5.1.378.0) o DPM 2019 (versione 10.19.58.0 o successiva).

## <a name="backup-jobs-in-recovery-services-vault"></a>Processi di backup nell'insieme di credenziali di servizi di ripristino

Backup di Azure offre funzionalità di monitoraggio e avviso predefinite per i carichi di lavoro protetti da backup di Azure. Nelle impostazioni dell'insieme di credenziali di servizi di ripristino, la sezione **monitoraggio** include processi e avvisi incorporati.

![Monitoraggio incorporato dell'insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

I processi vengono generati quando vengono eseguite operazioni quali la configurazione del backup, il backup, il ripristino, l'eliminazione del backup e così via.

I processi dalle seguenti soluzioni di backup di Azure sono illustrati di seguito:

- Backup di macchine virtuali di Azure
- Backup di file di Azure
- Backup del carico di lavoro di Azure, ad esempio SQL e SAP HANA
- Agente di Servizi di ripristino di Microsoft Azure (MARS)

I processi da System Center Data Protection Manager (SC-DPM), server di Backup di Microsoft Azure (MAB) non vengono visualizzati.

> [!NOTE]
> I carichi di lavoro di Azure, ad esempio SQL e SAP HANA backup nelle VM di Azure, hanno un numero elevato di processi di backup. Ad esempio, i backup del log possono essere eseguiti ogni 15 minuti. Per questi carichi di lavoro di database, vengono visualizzate solo le operazioni attivate dall'utente. Le operazioni di backup pianificate non vengono visualizzate.

## <a name="backup-alerts-in-recovery-services-vault"></a>Avvisi di backup nell'insieme di credenziali di servizi di ripristino

> [!NOTE]
> La visualizzazione degli avvisi negli insiemi di credenziali non è attualmente supportata nel centro di backup. È necessario passare a un singolo insieme di credenziali per visualizzare gli avvisi per l'insieme di credenziali.

Gli avvisi sono essenzialmente scenari in cui gli utenti vengono informati in modo che possano intraprendere le azioni rilevanti. La sezione **avvisi di backup** Mostra gli avvisi generati dal servizio backup di Azure. Questi avvisi sono definiti dal servizio e l'utente non può creare avvisi personalizzati.

### <a name="alert-scenarios"></a>Scenari di avviso

Gli scenari seguenti sono definiti dal servizio come scenari di avviso.

- Errori di backup/ripristino
- Backup completato con avvisi per l'agente di Servizi di ripristino di Microsoft Azure (MARS)
- Arrestare la protezione con Mantieni dati/arresta la protezione con Elimina dati

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Gli avvisi delle seguenti soluzioni di backup di Azure sono illustrati qui

- Backup di macchine virtuali di Azure
- Backup di File di Azure
- Backup del carico di lavoro di Azure, ad esempio SQL, SAP HANA
- Agente di Servizi di ripristino di Microsoft Azure (MARS)

> [!NOTE]
> Gli avvisi di System Center Data Protection Manager (SC-DPM), server di Backup di Microsoft Azure (MAB) non vengono visualizzati qui.

### <a name="consolidated-alerts"></a>Avvisi consolidati

Per le soluzioni di backup del carico di lavoro di Azure, ad esempio SQL e SAP HANA, i backup del log possono essere generati molto spesso, fino a ogni 15 minuti in base ai criteri. È anche possibile che anche gli errori di backup del log siano molto frequenti (fino a ogni 15 minuti). In questo scenario, l'utente finale verrà sovraccaricato se viene generato un avviso per ogni occorrenza di errori. Viene quindi inviato un avviso per la prima occorrenza e se gli errori successivi sono dovuti alla stessa causa principale, non vengono generati ulteriori avvisi. Il primo avviso viene aggiornato con il numero di errori. Tuttavia, se l'avviso viene disattivato dall'utente, l'occorrenza successiva attiverà un altro avviso e verrà considerato come primo avviso per tale occorrenza. Questo è il modo in cui backup di Azure esegue il consolidamento degli avvisi per i backup SQL e SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Eccezioni quando non viene generato un avviso

Quando un avviso non viene generato in caso di errore, si verificano alcune eccezioni. Ad esempio:

- L'utente ha annullato esplicitamente il processo in esecuzione
- Il processo ha esito negativo perché è in corso un altro processo di backup (non è necessario agire da qui perché è sufficiente attendere il completamento del processo precedente)
- Il processo di backup della VM non riesce perché la macchina virtuale di Azure di cui è stato eseguito il backup non esiste più
- [Avvisi consolidati](#consolidated-alerts)

Le eccezioni sopra riportate sono progettate dal comprendere che il risultato di queste operazioni (principalmente attivato dall'utente) viene visualizzato immediatamente nei client Portal/PS/CLI. Quindi, l'utente è immediatamente consapevole e non è necessaria una notifica.

### <a name="alert-types"></a>Tipi di avviso

In base alla gravità dell'avviso, gli avvisi possono essere definiti in tre tipi:

- **Critico**: in linea di principio, qualsiasi errore di backup o ripristino (pianificato o attivato dall'utente) provocherebbe la generazione di un avviso e verrebbe visualizzato come un avviso critico e anche per operazioni distruttive come l'eliminazione del backup.
- **Avviso**: se l'operazione di backup ha esito positivo ma con pochi avvisi, vengono elencate come avvisi di avviso. Gli avvisi di avviso sono attualmente disponibili solo per i backup dell'agente di backup di Azure.
- **Informativo**: attualmente, nessun avviso informativo viene generato dal servizio backup di Azure.

## <a name="notification-for-backup-alerts"></a>Notifica per gli avvisi di backup

> [!NOTE]
> La configurazione della notifica può essere eseguita solo tramite il portale di Azure. Il supporto per PS/CLI/API REST/Azure Resource Manager modello non è supportato.

Quando viene generato un avviso, viene inviata una notifica agli utenti. Backup di Azure fornisce un meccanismo di notifica incorporato tramite posta elettronica. È possibile specificare singoli indirizzi di posta elettronica o liste di distribuzione per ricevere una notifica quando viene generato un avviso. È anche possibile scegliere se ricevere una notifica per ogni singolo avviso o raggrupparli in un digest orario e quindi ricevere una notifica.

![Notifica di posta elettronica incorporate dell'insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando viene configurata la notifica, si riceverà un messaggio di benvenuto o introduttivo. Questo conferma che backup di Azure può inviare messaggi di posta elettronica a questi indirizzi quando viene generato un avviso.<br>

Se la frequenza è stata impostata su un digest orario e un avviso viene generato e risolto entro un'ora, non farà parte del digest orario imminente.

> [!NOTE]
>
> - Se viene eseguita un'operazione distruttiva, ad esempio **Arresta la protezione con l'eliminazione dei dati** , viene generato un avviso e viene inviato un messaggio di posta elettronica ai proprietari, agli amministratori e ai coamministratori della sottoscrizione anche se le notifiche non sono configurate per l'insieme di credenziali di servizi di ripristino.
> - Per configurare la notifica per i processi riusciti, utilizzare [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Inattivazione degli avvisi

Per disattivare o risolvere un avviso attivo, è possibile selezionare l'elemento di elenco corrispondente all'avviso che si desidera disattivare. Verrà visualizzata una schermata in cui sono visualizzate informazioni dettagliate sull'avviso, con un pulsante di **disattivazione** nella parte superiore. Se si seleziona questo pulsante, lo stato dell'avviso verrà modificato su **inattivo**. È anche possibile disattivare un avviso facendo clic con il pulsante destro del mouse sull'elemento dell'elenco corrispondente a tale avviso e selezionando **Disattiva**.

![Inattivazione avviso di insieme di credenziali RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Avvisi di monitoraggio di Azure per backup di Azure (anteprima)

Backup di Azure fornisce anche avvisi tramite monitoraggio di Azure, per consentire agli utenti di avere un'esperienza coerente per la gestione degli avvisi in diversi servizi di Azure, incluso il backup. Con gli avvisi di monitoraggio di Azure è possibile indirizzare gli avvisi a qualsiasi canale di notifica supportato da backup di Azure, ad esempio posta elettronica, ITSM, webhook, app per la logica e così via.

Questa funzionalità è attualmente disponibile per i database di Azure per il server PostgreSQL, i BLOB di Azure e Azure Managed Disks. Gli avvisi vengono generati per gli scenari seguenti ed è possibile accedervi passando a un insieme di credenziali per il backup e facendo clic sulla voce di menu **Alerts** :

- Elimina dati di backup
- Errore di backup (per ottenere avvisi per un errore di backup, è necessario registrare il flag AFEC denominato **EnableAzureBackupJobFailureAlertsToAzureMonitor** tramite il portale di anteprima)
- Ripristino non riuscito (per ottenere avvisi per un errore di ripristino, è necessario registrare il flag AFEC denominato **EnableAzureBackupJobFailureAlertsToAzureMonitor** tramite il portale di anteprima)

Per altre informazioni sugli avvisi di monitoraggio di Azure, vedere [Panoramica degli avvisi in Azure](../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Passaggi successivi

[Monitorare i carichi di lavoro di backup di Azure con monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md)