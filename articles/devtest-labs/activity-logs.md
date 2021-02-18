---
title: Log attività in Azure DevTest Labs | Microsoft Docs
description: Questo articolo illustra la procedura per visualizzare i log attività per Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582764"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Visualizzare i log attività per i Lab in Azure DevTest Labs 
Dopo aver creato uno o più Lab, è probabile che si voglia monitorare come e quando si accede, modificare e gestire i Lab e da chi. Azure DevTest Labs USA monitoraggio di Azure, in particolare i **log attività**, per fornire informazioni su tali operazioni sui Lab. 

Questo articolo illustra come visualizzare i log attività per un Lab in Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Visualizzare il log attività per un Lab

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nella sezione **DEVOPS**. Selezionare * (stella) accanto a **DevTest Labs** nella sezione **DEVOPS**. Questa azione aggiunge **DevTest Labs** al menu di spostamento a sinistra in modo che sia facilmente accessibile la volta successiva. Selezionare quindi **DevTest Labs** nel menu di spostamento a sinistra.

    ![Tutti i servizi - Selezionare DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Nell'elenco di lab selezionare il proprio lab.
1. Nella home page per il Lab selezionare **configurazioni e criteri** dal menu a sinistra. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Selezionare configurazione e criteri nel menu a sinistra.":::
1. Nella pagina **configurazione e criteri** selezionare **log attività** nel menu a sinistra in **Gestisci**. Verranno visualizzate le voci per le operazioni eseguite nel Lab. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Log attività":::    
1. Selezionare un evento per visualizzarne i dettagli. Nella pagina **Riepilogo** vengono visualizzate informazioni quali il nome dell'operazione, il timestamp e l'utente che ha eseguito l'operazione. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Arresta evento VM-Riepilogo":::        
1. Passare alla scheda **JSON** per visualizzare altri dettagli. Nell'esempio seguente è possibile visualizzare il nome della macchina virtuale e l'operazione eseguita nella macchina virtuale (arrestata).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Arresta evento VM-JSON":::           
1. Passare alla scheda **cronologia modifiche (anteprima)** per visualizzare la cronologia delle modifiche. Nell'esempio seguente viene visualizzata la modifica apportata alla macchina virtuale. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Arresta evento VM-cronologia modifiche":::             
1. Selezionare la modifica nell'elenco cronologia modifiche per visualizzare altri dettagli sulla modifica. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Arresta evento VM-modifica dettagli":::             

Per altre informazioni sui log attività, vedere [log attività di Azure](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'impostazione degli **avvisi** nei log attività, vedere [creare avvisi](create-alerts.md).
- Per altre informazioni sui log attività, vedere  [log attività di Azure](../azure-monitor/essentials/activity-log.md).

