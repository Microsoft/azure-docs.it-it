---
title: Esercitazione-failover manuale di un hub Azure Microsoft Docs
description: "Esercitazione: informazioni su come eseguire un failover manuale dell'hub delle cose in un'area diversa e verificare che funzioni e quindi restituirla all'area originale e controllarla nuovamente."
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 3b6bc972f5c26c78ffff81f5bab8c2812cf2cb11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622914"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Esercitazione: Eseguire il failover manuale per un hub IoT

Il failover manuale è una funzionalità del servizio hub IoT che consente ai clienti di eseguire il [failover](https://en.wikipedia.org/wiki/Failover) delle attività dell'hub da un'area primaria all'area geografica associata di Azure corrispondente. Il failover manuale può essere eseguito in caso di emergenza a livello di area o in eseguito a un'interruzione del servizio estesa. È anche possibile eseguire un failover pianificato per testare le funzionalità di ripristino di emergenza, anche se è consigliabile usare un hub IoT di test, invece di uno in esecuzione nell'ambiente di produzione. La funzionalità di failover manuale viene offerta ai clienti senza costi aggiuntivi per gli hub IoT creati dopo il 18 maggio 2017.

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
> * Creare un hub IoT usando il portale di Azure. 
> * Eseguire un failover. 
> * Vedere l'hub in esecuzione nella località secondaria.
> * Eseguire un failback per ritornare alle operazioni dell'hub IoT nella località primaria. 
> * Verificare che l'hub venga eseguito correttamente nella località corretta.

Per altre informazioni sul failover manuale e sul failover avviato da Microsoft con l'hub IoT, vedere [Ripristino di emergenza tra aree](iot-hub-ha-dr.md#cross-region-dr).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Fare clic su **+ Crea una risorsa** e selezionare **Internet delle cose**, quindi **Hub IoT**.

   ![Screenshot che illustra la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selezionare la scheda **Informazioni di base**. Compilare i campi seguenti.

    **Sottoscrizione**: selezionare la sottoscrizione di Azure che si vuole usare.

    **Gruppo di risorse**: fare clic su **Crea nuovo** e specificare **ManlFailRG** come nome del gruppo di risorse.

    **Area**: selezionare un'area nelle vicinanze. In questa esercitazione viene usato `West US 2`. Un failover può essere eseguito solo tra aree geografiche associate di Azure. L'area geografica associata a Stati Uniti occidentali 2 è WestCentralUS.
    
   **Nome hub IoT**: specificare un nome per l'hub IoT. Il nome dell'hub deve essere univoco a livello globale. 

   ![Screenshot che mostra il riquadro Informazioni di base per la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Fare clic su **Rivedi e crea**. (Usa le impostazioni predefinite per dimensioni e scalabilità.) 

4. Verificare le informazioni e quindi fare clic su **Crea** per creare l'hub IoT. 

   ![Screenshot che mostra l'ultimo passaggio per la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Eseguire un failover manuale

Si noti che è previsto un limite di due failover e due failback al giorno per un hub IoT.

1. Fare clic su **Gruppi di risorse** e quindi selezionare il gruppo di risorse **ManlFailRG**. Fare clic sull'hub nell'elenco delle risorse. 

1. In **Impostazioni** nel riquadro dell'hub IoT fare clic su **Failover**.

   ![Screenshot che mostra il riquadro delle proprietà dell'hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

1. Nel riquadro Failover manuale vengono visualizzate le informazioni relative a **Posizione corrente** e **Failover location** (Posizione di failover). La posizione corrente indica sempre la posizione in cui l'hub è attualmente attivo. La posizione di failover è l'[area geografica associata di Azure](../best-practices-availability-paired-regions.md) standard, che è abbinata alla posizione corrente. Non è possibile modificare i valori della località. Per questa esercitazione, la posizione corrente è `West US 2`, mentre quella di failover è `West Central US`.

   ![Screenshot che mostra il riquadro Failover manuale](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Nella parte superiore del riquadro Failover manuale fare clic su **Avvio failover**. 

1. Nel riquadro di conferma specificare il nome dell'hub IoT per confermare che sia quello di cui si vuole eseguire il failover. Per avviare il failover, fare quindi clic su **Failover**.

   La quantità di tempo impiegato per eseguire il failover manuale è proporzionale al numero di dispositivi registrati per l'hub. Ad esempio, in presenza di 100.000 dispositivi potrebbero essere richiesti 15 minuti, ma per cinque milioni di dispositivi l'operazione potrebbe richiedere un'ora o più.

   ![Screenshot che mostra il riquadro di conferma Failover manuale](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Durante l'esecuzione del processo di failover manuale, viene visualizzato un banner che indica che è in corso un failover manuale. 

   ![Screenshot che mostra il failover manuale in corso](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se si chiude il riquadro dell'hub IoT e lo si apre di nuovo facendo clic su di esso nel riquadro del gruppo di risorse, viene visualizzato un banner che indica che è in corso il failover manuale dell'hub. 

   ![Screenshot che mostra il failover dell'hub IoT in corso](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Al termine, le aree corrente e di failover nella pagina Failover manuale vengono invertite e l'hub diventa di nuovo attivo. In questo esempio la posizione corrente è ora `WestCentralUS`, mentre quella di failover è `West US 2`. 

   ![Screenshot che mostra che il failover è stato completato](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   La pagina di panoramica mostra anche un banner che indica che il failover è stato completato e che l'hub IoT è in esecuzione nell'area `West Central US`.

   ![Screenshot che mostra che il failover è stato completato nella pagina di panoramica](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Eseguire il failback 

Dopo aver eseguito un failover manuale, è possibile ritrasferire le operazioni dell'hub nell'area primaria originale, operazione nota come failback. Se è appena stato eseguito un failover, è necessario attendere circa un'ora prima di poter richiedere un failback. Se si prova a eseguire il failback in un periodo di tempo più breve, viene visualizzato un messaggio di errore.

Il failback viene eseguito esattamente come un failover manuale. Questa è la procedura: 

1. Per eseguire un failback, tornare al riquadro Hub Iot per l'hub Iot.

2. In **Impostazioni** nel riquadro dell'hub IoT fare clic su **Failover**. 

3. Nella parte superiore del riquadro Failover manuale fare clic su **Avvio failover**. 

4. Nel riquadro di conferma specificare il nome dell'hub IoT per confermare che sia quello di cui si vuole eseguire il failback. Per avviare quindi il failback, fare clic su OK. 

   ![Screenshot della richiesta di failback manuale](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Vengono visualizzati i banner, come descritto nella sezione relativa all'esecuzione di un failover. Dopo aver completato il failback, `West US 2` è di nuovo la posizione corrente, mentre `West Central US` è la posizione di failover, come impostato in origine.

## <a name="clean-up-resources"></a>Pulire le risorse 

Per rimuovere le risorse create per questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT e il gruppo di risorse stesso. 

1. Fare clic su **Gruppi di risorse**. 

2. Individuare e selezionare il gruppo di risorse **ManlFailRG**. Fare clic su di essa per aprirla. 

3. Fare clic su **Elimina gruppo di risorse**. Quando richiesto, immettere il nome del gruppo di risorse e fare clic su **Elimina** per confermare. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare ed eseguire un failover manuale e come richiedere un failback eseguendo le attività seguenti:

> [!div class="checklist"]
> * Creare un hub IoT usando il portale di Azure. 
> * Eseguire un failover. 
> * Vedere l'hub in esecuzione nella località secondaria.
> * Eseguire un failback per ritornare alle operazioni dell'hub IoT nella località primaria. 
> * Verificare che l'hub venga eseguito correttamente nella località corretta.

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
> [Gestire lo stato di un dispositivo IoT](tutorial-device-twins.md)
