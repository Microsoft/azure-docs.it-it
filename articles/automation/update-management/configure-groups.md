---
title: Usare i gruppi dinamici con Gestione aggiornamenti di Automazione di Azure
description: Questo articolo illustra come usare i gruppi dinamici con Gestione aggiornamenti di Automazione di Azure.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 318b5498c826b1e29baa35850594cebca72c4f3f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575919"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti consente di specificare come destinazione un gruppo dinamico di macchine virtuali di Azure o non di Azure per le distribuzioni degli aggiornamenti. L'uso di un gruppo dinamico consente di evitare di modificare la distribuzione per aggiornare i computer.

> [!NOTE]
> I gruppi dinamici non funzionano con le macchine virtuali classiche.

È possibile definire gruppi dinamici per macchine virtuali di Azure o non di Azure da **Gestione aggiornamenti** nel portale di Azure. Vedere [gestire gli aggiornamenti per le macchine virtuali](manage-updates-for-vm.md).

Un gruppo dinamico è definito da una query che Automazione di Azure valuta in fase di distribuzione. Anche se la query di un gruppo dinamico recupera un numero elevato di macchine virtuali, Automazione di Azure può elaborare solo un massimo di 1000 macchine virtuali alla volta. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

> [!NOTE]
> Se si prevede di aggiornare più di 1000 macchine virtuali, è consigliabile suddividere gli aggiornamenti tra più pianificazioni. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definire gruppi dinamici per macchine virtuali di Azure

Quando si definisce la query di un gruppo dinamico per macchine virtuali di Azure, per popolare il gruppo dinamico è possibile usare gli elementi seguenti:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/configure-groups/select-groups.png)

Per visualizzare in anteprima i risultati della query del gruppo dinamico, fare clic su **Anteprima**. L'anteprima visualizza l'appartenenza a gruppi in quel momento. Nell'esempio vengono cercate le macchine virtuali con il tag `Role` per il gruppo **BackendServer**. Se questo tag è stato aggiunto a più macchine virtuali, queste vengono aggiunte alle distribuzioni future in quel gruppo.

![Visualizzare in anteprima i gruppi](./media/configure-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definire gruppi dinamici per macchine virtuali non di Azure

Un gruppo dinamico per macchine virtuali non di Azure usa le ricerche salvate, dette anche gruppi di computer. Per informazioni su come creare una ricerca salvata, vedere [Creazione di un gruppo di computer](../../azure-monitor/logs/computer-groups.md#creating-a-computer-group). Dopo aver creato la ricerca salvata, è possibile selezionarla dall'elenco delle ricerche salvate in **Gestione aggiornamenti** nel portale di Azure. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata.

![Screenshot mostra la pagina Seleziona gruppi per non Azure (anteprima) e il riquadro di anteprima sul lato destro.](./media/configure-groups/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile [eseguire query sui log di monitoraggio di Azure](query-logs.md) per analizzare le valutazioni degli aggiornamenti, le distribuzioni e altre attività di gestione correlate. Include query predefinite che consentono di iniziare.
