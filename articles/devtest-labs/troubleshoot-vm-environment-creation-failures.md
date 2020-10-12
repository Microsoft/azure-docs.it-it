---
title: Risolvere gli errori di ambiente e VM Azure DevTest Labs
description: Informazioni su come risolvere i problemi di creazione della macchina virtuale (VM) e dell'ambiente in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476479"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Risolvere gli errori di creazione della macchina virtuale (VM) e dell'ambiente in Azure DevTest Labs
DevTest Labs fornisce avvisi se il nome di un computer non è valido o se si sta per violare un criterio Lab. In alcuni casi, viene visualizzato rosso `X` accanto alla macchina virtuale del Lab o allo stato dell'ambiente che informa che si è verificato un errore.  Questo articolo fornisce alcuni trucchi che è possibile usare per individuare il problema sottostante e, possibilmente, evitare il problema in futuro.

## <a name="portal-notifications"></a>Notifiche del portale
Se si usa il portale di Azure, il primo punto da considerare è il **pannello notifiche**.  Il pannello notifiche, disponibile sulla barra dei comandi principale facendo clic sull' **icona**a forma di campana, indica se la creazione dell'ambiente o della macchina virtuale del Lab è stata completata o meno.  Se si è verificato un errore, viene visualizzato il messaggio di errore associato all'errore di creazione. I dettagli forniscono spesso ulteriori informazioni per facilitare la risoluzione del problema. Nell'esempio seguente, la creazione della macchina virtuale non è riuscita a causa di un esaurimento delle memorie centrali. Il messaggio dettagliato indica come risolvere il problema e richiedere un aumento della quota di core.

![Notifica portale di Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM in stato di danneggiamento
Se lo stato della macchina virtuale nel Lab è **danneggiato**, è possibile che la VM sottostante sia stata eliminata dalla pagina della **macchina virtuale** a cui l'utente può accedere dalla pagina delle **macchine virtuali** , non dalla pagina DevTest Labs. Pulire il Lab in DevTest Labs eliminando la macchina virtuale dal Lab. Quindi, ricreare la macchina virtuale nel Lab. 

![VM in stato danneggiato](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Log attività
Esaminare i log attività se si sta esaminando un errore in un momento successivo al tentativo di creazione della macchina virtuale o dell'ambiente. Questa sezione illustra come trovare i log per le macchine virtuali e gli ambienti.

## <a name="activity-logs-for-virtual-machines"></a>Log attività per le macchine virtuali

1. Nella home page per il Lab selezionare la VM per avviare la pagina della **macchina virtuale** .
2. Nella pagina **macchina virtuale** , nella sezione **monitoraggio** del menu a sinistra, selezionare **log attività** per visualizzare tutti i log associati alla macchina virtuale.
3. Negli elementi del log attività selezionare l'operazione non riuscita. In genere, viene chiamata l'operazione non riuscita `Write Virtualmachines` .
4. Nel riquadro destro passare alla scheda JSON. Vengono visualizzati i dettagli nella visualizzazione JSON del log.

    ![Log attività per una macchina virtuale](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Esaminare il log JSON fino a trovare la `statusMessage` Proprietà. Fornisce il messaggio di errore principale e informazioni dettagliate, se applicabile. Il codice JSON seguente è un esempio per l'errore di core racchiuso tra virgolette illustrato in precedenza in questo articolo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Log attività per un ambiente

Per visualizzare il log attività per la creazione di un ambiente, attenersi alla procedura seguente:

1. Nella home page per il Lab selezionare **configurazione e criteri** dal menu a sinistra.
2. nella pagina **configurazione e criteri** selezionare **log attività** nel menu.
3. Cercare l'errore nell'elenco attività nel log e selezionarlo.
4. Nel riquadro destro passare alla scheda JSON e cercare **StatusMessage**.

    ![Log attività ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Log di distribuzione del modello di Gestione risorse
Se l'ambiente o la macchina virtuale è stata creata tramite l'automazione, è possibile esaminare le informazioni sull'errore in un'ultima posizione. Questo è il log di distribuzione del modello Azure Resource Manager. Quando una risorsa lab viene creata tramite l'automazione, viene spesso eseguita tramite una distribuzione del modello di Azure Resource Manager. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)Per Azure Resource Manager modelli di esempio che creano risorse di DevTest Labs, vedere.

Per visualizzare i log di distribuzione del modello Lab, attenersi alla procedura seguente:

1. Avviare la pagina per il gruppo di risorse in cui è presente il Lab.
2. Selezionare **distribuzioni** nel menu a sinistra in **Impostazioni**.
3. Cercare le distribuzioni con stato non riuscito e selezionarlo.
4. Nella pagina **distribuzione** selezionare il collegamento **Dettagli operazione** per l'operazione non riuscita.
5. Vengono visualizzati i dettagli sull'operazione non riuscita nella finestra **Dettagli operazione** .

## <a name="next-steps"></a>Passaggi successivi
Vedere [risoluzione degli errori degli elementi](devtest-lab-troubleshoot-artifact-failure.md)
