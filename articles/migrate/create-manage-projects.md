---
title: Creare e gestire i progetto di Azure Migrate
description: Trovare, creare, gestire ed eliminare progetti in Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: 644ac5ecab9f5384b446c3b1ebe762a5d06aaf12
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113429"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Creare e gestire i progetto di Azure Migrate

Questo articolo descrive come creare, gestire ed eliminare progetti [Azure migrate](migrate-services-overview.md) .


## <a name="create-a-project-for-the-first-time"></a>Creare un progetto per la prima volta

La prima volta che si configura Azure Migrate, si crea un progetto e si aggiunge uno strumento di valutazione o migrazione. [Seguire queste istruzioni](how-to-add-tool-first-time.md) per configurare per la prima volta.

## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

Se si dispone già di un progetto Azure Migrate e si desidera creare un progetto aggiuntivo, procedere come segue:  

1. Nel [portale pubblico di Azure](https://portal.azure.com) o in [Azure per enti](https://portal.azure.us)pubblici cercare **Azure migrate**.
2. Nel dashboard Azure Migrate > **Server**selezionare **Cambia** nell'angolo superiore destro.

   ![Modificare il progetto Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui**.

   ![Creazione di un secondo progetto di Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Trovare un progetto

Trovare un progetto come indicato di seguito:

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure migrate**.
2. Nel dashboard Azure Migrate > **Server**selezionare **Cambia** nell'angolo superiore destro.

    ![Passa a un progetto di Azure Migrate esistente](./media/create-manage-projects/switch-project.png)

3. Selezionare la sottoscrizione appropriata e il progetto Azure Migrate.


Se il progetto è stato creato nella [versione precedente](migrate-services-overview.md#azure-migrate-versions) di Azure migrate, trovarlo come segue:

1. Nella [portale di Azure](https://portal.azure.com)cercare **Azure migrate**.
2. Nel dashboard Azure Migrate, se è stato creato un progetto nella versione precedente, viene visualizzato un banner che fa riferimento a progetti precedenti. Selezionare il banner.

    ![Accedi a progetti esistenti](./media/create-manage-projects/access-existing-projects.png)

3. Esaminare l'elenco dei progetti precedenti.


## <a name="delete-a-project"></a>Eliminare un progetto

Elimina come segue:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Nella pagina gruppo di risorse selezionare **Mostra tipi nascosti**.
3. Selezionare il progetto di migrazione che si desidera eliminare e le risorse associate.
    - Il tipo di risorsa è **Microsoft. migrate/migrateprojects**.
    - Se il gruppo di risorse viene usato esclusivamente dal progetto di Azure Migrate, è possibile eliminare l'intero gruppo di risorse.


Tenere presente quanto segue:

- Quando si elimina, vengono eliminati sia il progetto che i metadati relativi alle macchine virtuali individuate.
- Se si usa la versione precedente di Azure Migrate, aprire il gruppo di risorse di Azure in cui è stato creato il progetto. Selezionare il progetto di migrazione che si vuole eliminare (il tipo di risorsa è **progetto di migrazione**).
- Se si usa l'analisi delle dipendenze con un'area di lavoro di Azure Log Analytics:
    - Se è stata collegata un'area di lavoro Log Analytics allo strumento Server Assessment, l'area di lavoro non viene eliminata automaticamente. È possibile utilizzare la stessa area di lavoro Log Analytics per più scenari.
    - Se si desidera eliminare l'area di lavoro Log Analytics, procedere manualmente.

### <a name="delete-a-workspace-manually"></a>Eliminare un'area di lavoro manualmente

1. Passare all'area di lavoro Log Analytics collegata al progetto.

    - Se il progetto Azure migrate non è stato eliminato, è possibile trovare il collegamento all'area di lavoro in **Essentials**  >  **Server Assessment**.
       ![Area di lavoro ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Se il progetto Azure Migrate è già stato eliminato, selezionare **gruppi di risorse** nel riquadro sinistro della portale di Azure e individuare l'area di lavoro.
       
2. [Seguire le istruzioni](../azure-monitor/platform/delete-workspace.md) per eliminare l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere strumenti di [valutazione](how-to-assess.md) o [migrazione](how-to-migrate.md) ai progetti Azure migrate.
