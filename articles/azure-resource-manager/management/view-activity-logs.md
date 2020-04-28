---
title: Visualizzare i log attività di Azure per monitorare le risorse
description: Usare i log attività per esaminare le azioni degli utenti e gli errori. Mostra portale di Azure PowerShell, l'interfaccia della riga di comando di Azure e REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75478136"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Visualizzare i log attività per monitorare le azioni sulle risorse

Con i log attività è possibile determinare:

* le operazioni eseguite sulle risorse nella sottoscrizione
* chi ha avviato l'operazione
* quando si è verificata l'operazione;
* lo stato dell'operazione;
* i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse. Non include le operazioni di lettura (GET). Per un elenco azioni risorsa, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

I log attività vengono conservati per 90 giorni. È possibile eseguire query per qualsiasi intervallo di date, purché la data di inizio non risalga a più di 90 giorni prima.

È possibile recuperare le informazioni dai log attività tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Insights o la [libreria .NET di Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portale di Azure

Per visualizzare i log attività tramite il portale, seguire questa procedura:

1. Scegliere **monitoraggio**dal menu portale di Azure oppure cercare e selezionare **monitoraggio** da qualsiasi pagina.

    ![Selezionare monitoraggio](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selezionare **log attività**.

    ![Selezionare il log attività](./media/view-activity-logs/select-activity-log.png)

1. Viene visualizzato un riepilogo delle operazioni recenti. Un set predefinito di filtri viene applicato alle operazioni. Si noti che le informazioni sul riepilogo includono l'utente che ha avviato l'azione e il momento in cui si è verificato.

    ![Visualizzare riepilogo delle operazioni recenti](./media/view-activity-logs/audit-summary.png)

1. Per eseguire rapidamente un set predefinito di filtri, selezionare **Quick Insights**.

    ![Selezionare Quick Insights](./media/view-activity-logs/select-quick-insights.png)

1. Selezionare una delle opzioni disponibili. Selezionare, ad esempio, **distribuzioni non riuscite** per visualizzare gli errori delle distribuzioni.

    ![Selezionare le distribuzioni non riuscite](./media/view-activity-logs/select-failed-deployments.png)

1. Si noti che i filtri sono stati modificati per concentrarsi sugli errori di distribuzione nelle ultime 24 ore. Vengono visualizzate solo le operazioni che corrispondono ai filtri.

    ![Filtri di visualizzazione](./media/view-activity-logs/view-filters.png)

1. Per concentrare l'attenzione su operazioni specifiche, modificare i filtri o applicarne di nuovi. Ad esempio, l'immagine seguente mostra un nuovo valore per l'**intervallo di tempo** e il **tipo di risorsa** è impostato su account di archiviazione.

    ![Impostare le opzioni di filtro](./media/view-activity-logs/set-filter.png)

1. Per eseguire nuovamente la query in un secondo momento, selezionare **Aggiungi filtri correnti**.

    ![Aggiungere filtri](./media/view-activity-logs/pin-filters.png)

1. Assegnare un nome al filtro.

    ![Assegnare un nome ai filtri](./media/view-activity-logs/name-filters.png)

1. Il filtro è disponibile nel dashboard. Selezionare **Dashboard** nel menu del portale di Azure.

    ![Mostrare il filtro nel dashboard](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Dal portale è possibile visualizzare le modifiche apportate a una risorsa. Tornare alla visualizzazione predefinita in monitoraggio e selezionare un'operazione che comporta la modifica di una risorsa.

    ![Seleziona operazione](./media/view-activity-logs/select-operation.png)

1. Selezionare **cronologia modifiche (anteprima)** e scegliere una delle operazioni disponibili.

    ![Seleziona cronologia modifiche](./media/view-activity-logs/select-change-history.png)

1. Verranno visualizzate le modifiche apportate alla risorsa.

    ![Mostra modifiche](./media/view-activity-logs/show-changes.png)

Per altre informazioni sulla cronologia delle modifiche, vedere [ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per recuperare le voci di log, eseguire il comando **Get-AzLog**. Offrire parametri aggiuntivi per filtrare l'elenco di voci. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per gli ultimi 7 giorni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

L'esempio seguente illustra come usare il log attività per cercare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

In alternativa, è possibile usare funzioni data per specificare l'intervallo di date, ad esempio gli ultimi 14 giorni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

È possibile cercare le azioni eseguite da un determinato utente.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

È possibile filtrare le operazioni non riuscite.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

È possibile concentrarsi su un errore esaminando il messaggio di stato per tale voce.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

È possibile selezionare valori specifici per limitare i dati restituiti.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, è possibile filtrare per tipo di operazione.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

È possibile usare il grafico risorse per visualizzare la cronologia delle modifiche per una risorsa. Per altre informazioni, vedere [ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per recuperare le voci di log, eseguire il comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) con una differenza per indicare l'intervallo di tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

L'esempio seguente illustra come usare il log attività per cercare le operazioni eseguite durante un intervallo di tempo specificato. Le date di inizio e fine vengono specificate in un formato Data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

È possibile cercare le azioni eseguite da un utente specifico, anche per un gruppo di risorse che non esiste più.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

È possibile filtrare le operazioni non riuscite.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

È possibile concentrarsi su un errore esaminando il messaggio di stato per tale voce.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

È possibile selezionare valori specifici per limitare i dati restituiti.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

A seconda dell'ora di inizio specificata, il comando precedente può restituire un lungo elenco di operazioni per il gruppo di risorse. I risultati possono essere filtrati in base all'elemento da cercare specificando i criteri di ricerca. Ad esempio, è possibile filtrare per tipo di operazione.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

È possibile usare il grafico risorse per visualizzare la cronologia delle modifiche per una risorsa. Per altre informazioni, vedere [ottenere le modifiche alle risorse](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>API REST

Le operazioni REST per l'uso del log attività fanno parte delle [Informazioni di riferimento sulle API REST di Azure Insights](/rest/api/monitor/). Per recuperare gli eventi del log attività, vedere [Elencare gli eventi di gestione in una sottoscrizione](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Passaggi successivi

* I log attività di Azure possono essere usati con Power BI per ottenere altre informazioni sulle azioni nella sottoscrizione. Vedere [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Visualizzare e analizzare i log attività di Azure in Power BI e altri strumenti).
* Per informazioni su come impostare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo in Azure](../../role-based-access-control/role-assignments-portal.md).
* Per visualizzare altri dettagli sulle modifiche apportate alle applicazioni dal livello dell'infrastruttura fino alla distribuzione delle applicazioni, vedere [usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure](../../azure-monitor/app/change-analysis.md).
* Per informazioni sui comandi per visualizzare le operazioni di distribuzione, vedere [Visualizzare le operazioni di distribuzione](../templates/deployment-history.md).
* Per informazioni su come impedire operazioni di eliminazione su una risorsa per tutti gli utenti, vedere [Bloccare le risorse con Azure Resource Manager](lock-resources.md).
* Per visualizzare l'elenco delle operazioni disponibili per ogni provider Microsoft Azure Resource Manager, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md)
