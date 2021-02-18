---
title: Log Analytics di diagnostica di Windows Virtual Desktop (classico)-Azure
description: Come usare log Analytics con la funzionalità di diagnostica desktop virtuale di Windows (classica).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bfe8026f099c341b68a1aa1fc5e3fac425e29403
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578895"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Usare Log Analytics per la funzionalità di diagnostica in desktop virtuale di Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../diagnostics-log-analytics.md).

Desktop virtuale Windows offre una funzionalità di diagnostica che consente all'amministratore di identificare i problemi usando un'unica interfaccia. Questa funzionalità registra le informazioni di diagnostica ogni volta che un utente assegna un ruolo desktop virtuale Windows utilizza il servizio. Ogni log contiene informazioni sul ruolo di desktop virtuale di Windows incluso nell'attività, i messaggi di errore visualizzati durante la sessione, le informazioni sul tenant e le informazioni sull'utente. La funzionalità di diagnostica crea log attività per le azioni utente e amministrative. Ogni log attività rientra in tre categorie principali:

- Attività di sottoscrizione di feed: quando un utente tenta di connettersi al feed tramite Desktop remoto Microsoft applicazioni.
- Attività di connessione: quando un utente tenta di connettersi a un desktop o RemoteApp tramite Desktop remoto Microsoft applicazioni.
- Attività di gestione: quando un amministratore esegue operazioni di gestione nel sistema, ad esempio la creazione di pool host, l'assegnazione di utenti ai gruppi di app e la creazione di assegnazioni di ruolo.

Le connessioni che non raggiungono Desktop virtuale Windows non vengono visualizzate nei risultati della diagnostica perché il servizio dei ruoli di diagnostica fa parte del servizio Desktop virtuale Windows. Problemi di connessione di desktop virtuali Windows possono verificarsi quando l'utente riscontra problemi di connettività di rete.

## <a name="why-you-should-use-log-analytics"></a>Perché usare Log Analytics

Si consiglia di usare Log Analytics per analizzare i dati di diagnostica nel client di Azure che va oltre la risoluzione dei problemi per singolo utente. Poiché è possibile eseguire il pull dei contatori delle prestazioni delle macchine virtuali in Log Analytics si dispone di uno strumento per raccogliere informazioni per la distribuzione.

## <a name="before-you-get-started"></a>Prima di iniziare

Prima di poter usare Log Analytics con la funzionalità di diagnostica, è necessario [creare un'area di lavoro](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Dopo aver creato l'area di lavoro, seguire le istruzioni in [connettere i computer Windows a monitoraggio di Azure](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) per ottenere le informazioni seguenti:

- ID dell'area di lavoro
- Chiave primaria dell'area di lavoro

Queste informazioni saranno necessarie in un secondo momento nel processo di installazione.

## <a name="push-diagnostics-data-to-your-workspace"></a>Eseguire il push dei dati di diagnostica nell'area di lavoro

È possibile eseguire il push dei dati di diagnostica dal tenant desktop virtuale di Windows nel Log Analytics per l'area di lavoro. È possibile configurare questa funzionalità immediatamente quando si crea il tenant per la prima volta collegando l'area di lavoro al tenant oppure è possibile configurarlo in un secondo momento con un tenant esistente.

Per collegare il tenant all'area di lavoro Log Analytics durante la configurazione del nuovo tenant, eseguire il cmdlet seguente per accedere a desktop virtuale Windows con l'account utente TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Se si intende collegare un tenant esistente anziché un nuovo tenant, eseguire invece questo cmdlet:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

È necessario eseguire questi cmdlet per ogni tenant a cui si vuole collegare Log Analytics.

>[!NOTE]
>Se non si vuole collegare l'area di lavoro Log Analytics quando si crea un tenant, eseguire il `New-RdsTenant` cmdlet.

## <a name="cadence-for-sending-diagnostic-events"></a>Cadenza per l'invio di eventi di diagnostica

Gli eventi di diagnostica vengono inviati al Log Analytics al termine dell'operazione.

## <a name="example-queries"></a>Query di esempio

Le query di esempio seguenti mostrano come la funzionalità di diagnostica genera un report per le attività più frequenti nel sistema:

Nel primo esempio vengono illustrate le attività di connessione avviate dagli utenti con client desktop remoto supportati:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

Questa query di esempio seguente mostra le attività di gestione da parte degli amministratori nei tenant:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Interrompi invio dati a Log Analytics

Per arrestare l'invio di dati da un tenant esistente a Log Analytics, eseguire il cmdlet seguente e impostare stringhe vuote:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

È necessario eseguire questo cmdlet per ogni tenant da cui si vuole interrompere l'invio dei dati.

## <a name="next-steps"></a>Passaggi successivi

Per esaminare gli scenari di errore comuni che possono essere identificati dalla funzionalità di diagnostica, vedere [identificare e diagnosticare i problemi](diagnostics-role-service-2019.md#common-error-scenarios).
