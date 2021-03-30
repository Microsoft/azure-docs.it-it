---
title: Uso di applicazioni in Azure Blockchain Workbench
description: Esercitazione su come usare i contratti applicazione nell'anteprima di Azure Blockchain Workbench.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324050"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Esercitazione: uso di applicazioni in Azure blockchain Workbench

È possibile usare Blockchain Workbench per creare ed eseguire operazioni su contratti. È anche possibile visualizzare i dettagli dei contratti, come lo stato e la cronologia delle transazioni.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo contratto
> * Eseguire un'azione su un contratto

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una distribuzione di Blockchain Workbench. Per altre informazioni, vedere [distribuzione di Azure blockchain Workbench](deploy.md) per informazioni dettagliate sulla distribuzione
* Un'applicazione blockchain distribuita in Blockchain Workbench. Vedere [Creare un'applicazione blockchain in Azure Blockchain Workbench](create-app.md)

[Aprire Blockchain Workbench](deploy.md#blockchain-workbench-web-url) nel browser.

![Blockchain Workbench](./media/use/workbench.png)

È necessario accedere come membro di Blockchain Workbench. Se non è elencata alcuna applicazione, si è un membro di Blockchain Workbench, ma di nessuna delle applicazioni. L'amministratore di Blockchain Workbench può assegnare membri alle applicazioni.

## <a name="create-new-contract"></a>Creare un nuovo contratto

Per creare un nuovo contratto, è necessario essere un membro specificato come **iniziatore** di contratti. Per informazioni sulla definizione dei ruoli applicazione e degli iniziatori del contratto, vedere i [flussi di lavoro nella panoramica della configurazione](configuration.md#workflows). Per informazioni sull'assegnazione di membri ai ruoli applicazione, vedere [Aggiungere un membro all'applicazione](manage-users.md#add-member-to-application).

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto che si vuole creare. Viene visualizzato un elenco dei contratti attivi.

2. Per creare un nuovo contratto, selezionare **Nuovo contratto**.

    ![Pulsante Nuovo contratto](./media/use/contract-list.png)

3. Viene visualizzato il riquadro **Nuovo contratto**. Specificare i valori dei parametri iniziali. Selezionare **Crea**.

    ![Riquadro Nuovo contratto](./media/use/new-contract.png)

    Il nuovo contratto creato viene visualizzato nell'elenco con gli altri contratti attivi.

    ![Elenco dei contratti attivi](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Eseguire operazioni sui contratti

A seconda dello stato in cui si trova il contratto, i membri possono eseguire azioni per la transizione allo stato successivo del contratto. Le azioni sono definite come [transizioni](configuration.md#transitions) all'interno di un [stato](configuration.md#states). I membri che appartengono a un ruolo applicazione o istanza consentito per la transizione possono eseguire l'azione. 

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto su cui si vuole eseguire l'azione.
2. Selezionare il contratto nell'elenco. I dettagli sul contratto vengono visualizzati in sezioni diverse. 

    ![Dettagli sul contratto](./media/use/contract-details.png)

    | Sezione  | Descrizione  |
    |---------|---------|
    | Stato | Elenca lo stato di avanzamento corrente all'interno delle fasi del contratto |
    | Dettagli | Valori correnti del contratto |
    | Azione | Informazioni dettagliate sull'ultima azione |
    | Attività | Cronologia delle transazioni del contratto |
    
3. Nella sezione **Azione** selezionare **Intervieni**.

4. I dettagli sullo stato corrente del contratto vengono visualizzati in un riquadro. Scegliere l'azione che si vuole eseguire nell'elenco a discesa. 

    ![Scegliere un'azione](./media/use/choose-action.png)

5. Selezionare **Intervieni** per avviare l'azione.
6. Se sono necessari parametri per l'azione, specificare i valori per l'azione.

    ![Intervieni](./media/use/take-action.png)

7. Selezionare **Intervieni** per eseguire l'azione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Controllo delle versioni dell'applicazione Azure Blockchain Workbench](version-app.md)
