---
title: Creare avvisi del log attività per i Lab in Azure DevTest Labs
description: Questo articolo illustra la procedura per creare avvisi del log attività per Lab in Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588759"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Creare avvisi del log attività per i Lab in Azure DevTest Labs
Questo articolo illustra come creare avvisi del log attività per i Lab in Azure DevTest Labs, ad esempio quando viene creata una macchina virtuale o quando viene eliminata una macchina virtuale.

## <a name="create-alerts"></a>Creare avvisi
In questo esempio viene creato un avviso per tutte le operazioni amministrative in un Lab con un'azione che invia un messaggio di posta elettronica ai proprietari della sottoscrizione. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca del portale di Azure digitare **monitoraggio**, quindi selezionare **monitoraggio** dall'elenco risultati. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Cerca monitoraggio":::        
1. Selezionare **avvisi** nel menu a sinistra e quindi selezionare **nuova regola di avviso** sulla barra degli strumenti. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Pagina degli avvisi":::    
1. Nella pagina **Crea regola di avviso** fare clic su **Seleziona risorsa**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Selezionare la risorsa per l'avviso":::        
1. Selezionare **DevTest Labs** per **filtro per tipo di risorsa**, selezionare il Lab nell'elenco e quindi fare clic su **fine**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Selezionare il Lab come risorsa":::
1. Tornare alla pagina **Crea regola di avviso** , fare clic su **Seleziona condizione**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Collegamento selezionare la condizione":::    
1. Nella pagina **Configura logica** per i segnali selezionare un segnale supportato da DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Seleziona segnale":::
1. Filtrare in base a **livello di evento** (dettagliato, informativo, avviso, errore, critico, tutti), **stato** (non riuscito, avviato, riuscito) e **chi ha iniziato** l'evento. 
1. Selezionare **fine** per completare la configurazione della condizione. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Configurare la logica del segnale-completato":::
1. È stato specificato per l'ambito (Lab) e la condizione per l'avviso. A questo punto, è necessario specificare un gruppo di azione con le azioni da eseguire quando viene soddisfatta la condizione. Tornare alla pagina **Crea regola di avviso** , scegliere **Seleziona gruppo di azioni**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Collegamento Seleziona gruppo di azioni":::
1. Fare clic sul collegamento **Crea gruppo di azioni** sulla barra degli strumenti. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Collegamento Crea gruppo di azioni":::
1. Nella pagina **Aggiungi gruppo di azione** , attenersi alla seguente procedura:
    1. Immettere un **nome** per il gruppo di azioni.
    1. Immettere un **nome breve** per il gruppo di azioni. 
    1. Selezionare il **gruppo di risorse** in cui si desidera creare l'avviso. 
    1. Immettere un **nome per l'azione**. 
    1. Selezionare il **tipo di azione** (in questo esempio, **email Azure Resource Manager role**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Pagina Aggiungi gruppo di azioni":::
    1. Nella pagina **ruolo Azure Resource Manager di posta elettronica** selezionare il ruolo. In questo esempio è **proprietario**. Quindi selezionare **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Seleziona il ruolo":::            
    1. Selezionare **OK** nella pagina **Aggiungi gruppo di azioni**. 
1. A questo punto, nella pagina **Crea regola di avviso** , immettere un nome per la regola di avviso e quindi fare clic su **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Crea regola di avviso-completato":::

## <a name="view-alerts"></a>Visualizzare gli avvisi 
1. Gli avvisi vengono visualizzati negli **avvisi** per tutte le operazioni amministrative (in questo esempio). Gli avvisi potrebbero richiedere qualche minuto per la visualizzazione. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="L'acquisizione schermo visualizza gli avvisi nel dashboard.":::
1. Se si seleziona numero in una colonna (ad esempio, **avvisi totali**), vengono visualizzati gli avvisi che sono stati generati. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Tutti gli avvisi":::
1. Se si seleziona un avviso, vengono visualizzati i relativi dettagli. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Dettagli avviso":::
1. In questo esempio si riceve anche un messaggio di posta elettronica con contenuto come illustrato nell'esempio seguente: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Messaggio di avviso":::

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla creazione di gruppi di azioni con tipi di azione diversi, vedere [creare e gestire gruppi di azioni nella portale di Azure](../azure-monitor/alerts/action-groups.md).
- Per altre informazioni sui log attività, vedere  [log attività di Azure](../azure-monitor/essentials/activity-log.md).
- Per informazioni sull'impostazione degli avvisi nei log attività, vedere [avvisi sul log attività](../azure-monitor/alerts/activity-log-alerts.md).

