---
title: Inviare avvisi da Azure Application Insights | Microsoft Docs
description: Esercitazione per l'invio di avvisi in risposta agli errori dell'applicazione con Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 6b8cc951301ec315f758478eda1c0736882e66f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627680"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorare e inviare avvisi sullo stato di integrità dell'applicazione con Azure Application Insights

Azure Application Insights consente di monitorare l'applicazione e inviare avvisi quando l'applicazione non è disponibile, si verificano errori o si riscontrano problemi di prestazioni.  Questa esercitazione illustra il processo di creazione di test per controllare in modo continuativo la disponibilità dell'applicazione.

Si apprenderà come:

> [!div class="checklist"]
> * Creare test di disponibilità per controllare in modo continuativo la risposta dell'applicazione
> * Inviare un messaggio e-mail agli amministratori quando si verifica un problema

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

Creare una [risorsa Application Insights](../app/create-new-resource.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Creare un test di disponibilità

I test di disponibilità in Application Insights consentono di testare automaticamente l'applicazione da diverse località in tutto il mondo.   In questa esercitazione verrà eseguito un test con un URL per verificare che l'applicazione Web sia disponibile.  È anche possibile creare una procedura dettagliata completa per verificarne il funzionamento dettagliato. 

1. Selezionare **Application Insights** e quindi selezionare la sottoscrizione in uso.  

2. Selezionare **Disponibilità** nel menu **Analisi** e quindi fare clic su **Crea test**.

    ![Aggiungere un test di disponibilità](media/tutorial-alert/add-test-001.png)

3. Digitare un nome per il test e lasciare le altre impostazioni predefinite.  Questa selezione attiverà le richieste per l'URL dell'applicazione ogni 5 minuti da cinque diverse aree geografiche.

4. Selezionare **Avvisi** per aprire l'elenco a discesa **Avvisi**, in cui è possibile definire i dettagli sulla modalità di risposta in caso di esito negativo del test. Scegliere **Near real time** e impostare lo stato su **Abilitato**.

    Digitare un indirizzo e-mail a cui inviare gli avvisi quando vengono soddisfatti i criteri di avviso.  Se si vuole, è possibile digitare l'indirizzo di un webhook da chiamare quando vengono soddisfatti i criteri di avviso.

    ![Creare un test](media/tutorial-alert/create-test-001.png)

5. Tornare al pannello di test, selezionare i puntini di sospensione e modificare l'avviso per immettere la configurazione per l'avviso near real time.

    ![Modificare l'avviso](media/tutorial-alert/edit-alert-001.png)

6. Impostare le località con errori su un valore superiore o uguale a 3. Creare un [gruppo di azioni](../alerts/action-groups.md) per configurare gli utenti che ricevono una notifica quando la soglia avvisi viene violata.

    ![Interfaccia utente per salvare l'avviso](media/tutorial-alert/save-alert-001.png)

7. Dopo aver configurato l'avviso, fare clic sul nome del test per visualizzare i dettagli da ogni località. Il formato dei test può essere sia un grafico a linee che un grafico a dispersione per visualizzare l'esito positivo o gli errori per un intervallo di tempo specificato.

    ![Dettagli del test](media/tutorial-alert/test-details-001.png)

8. È possibile eseguire il drill-down nei dettagli di qualsiasi test facendo clic sul relativo punto nel grafico a dispersione. Verrà avviata la visualizzazione dei dettagli delle transazioni end-to-end. L'esempio seguente mostra i dettagli per una richiesta non riuscita.

    ![Risultato del test](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come creare avvisi per gli eventuali problemi, passare all'esercitazione successiva per imparare ad analizzare il modo in cui gli utenti interagiscono con l'applicazione.

> [!div class="nextstepaction"]
> [Comprendere gli utenti](./tutorial-users.md)

