---
title: Registrare il ticket di supporto per Azure Stack Edge Pro Azure Data Box Gateway | Microsoft Docs
description: Informazioni su come registrare la richiesta di supporto per i problemi relativi a Azure Stack Edge Pro o Data Box Gateway ordini.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: f76652600f42d7e82914836537935ac9a74decb4
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436796"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>Aprire un ticket di supporto per Azure Stack Edge Pro e Azure Data Box Gateway

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-databox-gateway-sku.md)]

Questo articolo si applica a Azure Stack Edge Pro e Azure Data Box Gateway entrambi gestiti dal servizio Pro/Azure Data Box Gateway di Azure Stack Edge. Se si verificano problemi con il servizio, è possibile creare una richiesta di servizio per il supporto tecnico. In questo articolo viene descritto:

* Come creare una richiesta di supporto
* Come gestire una richiesta di supporto relativa al ciclo di vita all'interno del portale.

## <a name="create-a-support-request"></a>Creare una richiesta di supporto

Per creare una richiesta di supporto, attenersi alla procedura seguente:

1. Passare a Azure Stack Edge Pro o Data Box Gateway ordine. Passare alla sezione **Supporto e risoluzione dei problemi** e quindi selezionare **Nuova richiesta di supporto**.

2. In **Nuova richiesta di supporto**, nella scheda **Informazioni di base**, seguire questa procedura:

    1. Nell'elenco a discesa **Tipo di problema** selezionare **Tecnico**.
    2. Scegliere la propria **sottoscrizione**.
    3. In **Servizio** selezionare **Servizi personali**. Nell'elenco a discesa selezionare **Azure stack Edge Pro e data box gateway**.
    4. Selezionare la **Risorsa**. Corrisponde al nome dell'ordine.
    5. Fornire un breve **Riepilogo** del problema riscontrato. 
    6. Selezionare il **Tipo di problema**.
    7. A seconda del tipo di problema selezionato, scegliere un **Sottotipo del problema** corrispondente.
    8. Selezionare **Avanti: Soluzioni >>** .

        ![Nozioni di base](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. Nella scheda **Dettagli** seguire questa procedura:

    1. Specificare la data e l'ora di inizio del problema.
    2. Fornire una **Descrizione** per il problema.
    3. In **Caricamento file** fare clic sull'icona della cartella per cercare eventuali altri file da caricare.
    4. Selezionare **Condividi informazioni diagnostica**.
    5. In base alla sottoscrizione effettuata, viene automaticamente popolato un **piano di supporto** .
    6. Nell'elenco a discesa selezionare il tipo di **Gravità**.
    7. Selezionare il **Metodo di contatto preferito**.
    8. Il campo **Ore per la risposta** viene selezionato automaticamente in base al piano di sottoscrizione effettuato.
    9. Fornire la lingua preferita per il supporto.
    10. In **Informazioni di contatto** specificare nome, posta elettronica, numero di telefono, contatto facoltativo, Paese. Il supporto tecnico Microsoft usa queste informazioni per contattare l'utente per altre informazioni, la diagnostica e la risoluzione. 
    11. Selezionare **Avanti: Rivedi e crea >>** .

        ![Problema](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. Nella scheda **Rivedi e crea** esaminare le informazioni relative al ticket di supporto. Selezionare **Create** (Crea). 

    ![Problema 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Dopo aver creato il ticket di supporto, un tecnico del supporto contatterà il prima possibile per procedere con la richiesta.

## <a name="get-hardware-support"></a>Ottenere il supporto hardware

Queste informazioni si applicano solo al dispositivo Azure Stack. Di seguito è illustrato il processo a cui attenersi per segnalare problemi hardware:

1. Aprire un ticket di supporto dal portale di Azure per un problema hardware. In **Tipo di problema** selezionare **Hardware di Azure Stack**. In **Sottotipo del problema** scegliere **Errore hardware**.

    ![Problema hardware](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Dopo avere creato il ticket di supporto, si verrà presto contattati da un tecnico del supporto per procedere con la richiesta.

2. Se supporto tecnico Microsoft determina che si tratta di un problema hardware, viene eseguita una delle azioni seguenti:

    * Viene inviata un'unità sostituibile sul campo per il componente hardware in cui si è verificato l'errore. Attualmente, le unità di alimentazione e le unità SSD sono le uniche FRU supportate.
    * Solo le unità sostituibili sul campo vengono sostituite entro il giorno lavorativo successivo. Per tutti gli altri componenti, è necessaria la spedizione di un'unità di sostituzione completa del sistema.

3. Se è stato determinato che la sostituzione di una FRU è necessaria entro l'ora locale di 1 PM (dal lunedì al venerdì), un tecnico in sede invia il giorno lavorativo successivo alla propria sede per eseguire una sostituzione della FRU. Una sostituzione completa del sistema richiede in genere molto più tempo perché le parti vengono spedite dalla nostra fabbrica e possono essere soggette a ritardi di trasporto e di dogana.

## <a name="manage-a-support-request"></a>Gestire una richiesta di supporto

Dopo la creazione del ticket di supporto, sarà possibile gestire il ciclo di vita del ticket dal portale.

### <a name="to-manage-your-support-requests"></a>Per gestire le richieste di supporto

1. Per visualizzare la pagina Guida e supporto, passare a **Sfoglia -> Guida e supporto**.

    ![Gestire le richieste di supporto](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. Verrà visualizzato l'elenco tabulare **Richieste di supporto recenti** in **Guida e supporto**.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Selezionare e fare clic su una richiesta di supporto. È possibile visualizzare lo stato e i dettagli della richiesta. Fare clic su **+ Nuovo messaggio** se si vuole seguire la richiesta.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [risolvere i problemi relativi a Azure stack Edge Pro](azure-stack-edge-troubleshoot.md).
Informazioni sulla [Risoluzione dei problemi relativi a Data Box Gateway](../databox-gateway/data-box-gateway-troubleshoot.md).