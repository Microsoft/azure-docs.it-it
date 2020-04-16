---
title: 'Guida introduttiva: Creare un profilo Frontdoor per la disponibilità elevata delle applicazioni'
description: Questa guida introduttiva descrive come creare una frontdoor per un'applicazione Web globale con disponibilità e prestazioni elevate.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521463"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Guida introduttiva: Creare una frontdoor per un'applicazione Web globale a disponibilità elevata

Questa guida introduttiva descrive come creare un profilo di Frontdoor che fornisce disponibilità e prestazioni elevate per un'applicazione Web globale. 

Lo scenario descritto in questa guida introduttiva include due istanze di un'applicazione Web in esecuzione in aree di Azure diverse. Viene creata una configurazione di Frontdoor basata su [back-end ponderati e con la stessa priorità](front-door-routing-methods.md), che consente di instradare il traffico utente al set più vicino di back-end del sito che eseguono l'applicazione. Frontdoor monitora continuamente l'applicazione Web e fornisce il failover automatico nel successivo back-end disponibile quando il sito primario non è disponibile.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure 
Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="prerequisites"></a>Prerequisiti
Per questa guida introduttiva è necessario aver distribuito due istanze di un'applicazione Web in esecuzione in aree di Azure diverse (*Stati Uniti orientali* ed *Europa occidentale*). Entrambe le istanze dell'applicazione Web vengono eseguite in modalità attivo/attivo, vale a dire che entrambe possono ricevere il traffico in qualsiasi momento a differenza di una configurazione attivo-standby, in cui una delle due funge da failover.

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** > **Web** > **App Web**.
2. In **App Web** immettere o selezionare le informazioni seguenti e immettere le impostazioni predefinite nei casi in cui non è specificato alcun valore:

     | Impostazione         | valore     |
     | ---              | ---  |
     | Resource group          | Selezionare **Nuovo** e quindi digitare *myResourceGroupFD1* |
     | Nome           | Immettere un nome univoco per l'app Web  |
     | Stack di runtime          | Selezionare uno stack di runtime per l'app |
     |      Region  |   Stati Uniti occidentali        |
     | Piano di servizio app/Località         | Selezionare **Nuovo**.  Nel piano di servizio app immettere *myAppServicePlanEastUS* e quindi selezionare **OK**.| 
     |SKU e dimensioni  | Selezionare **Modifica dimensioni**. Selezionare **Standard S1 100 ACU totali, 1,75 GB di memoria** |
     
3. Selezionare **Rivedi e crea**.
4. Esaminare le informazioni di riepilogo per l'app Web. Selezionare **Create** (Crea).
5. Dopo circa 5 minuti, quando l'app Web viene distribuita correttamente, viene creato un sito Web predefinito.
6. Ripetere i passaggi da 1 a 3 per creare un secondo sito Web in un'area di Azure diversa con le impostazioni seguenti:

     | Impostazione         | valore     |
     | ---              | ---  |
     | Resource group          | Selezionare **Nuovo** e quindi digitare *myResourceGroupFD2* |
     | Nome           | Immettere un nome univoco per l'app Web  |
     | Stack di runtime          | Selezionare uno stack di runtime per l'app |
     |      Region  |   Europa occidentale      |
     | Piano di servizio app/Località         | Selezionare **Nuovo**.  Nel piano di servizio app immettere *myAppServicePlanWestEuropa* e quindi selezionare **OK**.|   
     |SKU e dimensioni  | Selezionare **Modifica dimensioni**. Selezionare **Standard S1 100 ACU totali, 1,75 GB di memoria** |
    
## <a name="create-a-front-door-for-your-application"></a>Creare una frontdoor per l'applicazione
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Aggiungere un host front-end per la frontdoor
Creare una configurazione di Frontdoor che instrada il traffico utente in base alla latenza più bassa fra i due back-end.

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** > **Rete** > **Frontdoor**.
2. In **Crea una frontdoor** immettere o selezionare le informazioni seguenti e, nel caso non sia specificato alcun valore, immettere le impostazioni predefinite:

     | Impostazione         | valore     |
     | ---              | ---  |
     |Subscription  | Selezionare la sottoscrizione in cui creare la frontdoor.|
     | Resource group          | Selezionare **Nuovo** e quindi digitare *myResourceGroupFD0* |
     | Località del gruppo di risorse  |   Stati Uniti centrali        |
     
     > [!NOTE]
     > Non è necessario creare un nuovo gruppo di risorse in cui distribuire la frontdoor.  È anche possibile selezionare un gruppo di risorse esistente.
     
3. Fare clic su **Avanti: Configurazione**.
4. Fare clic sull'icona '+' nella scheda Front-end/domini.  Per **Nome host** immettere `<Your Initials>frontend`. Questo nome host deve essere globalmente univoco, ma la convalida viene effettuata da Frontdoor.
5. Scegliere **Aggiungi**.

### <a name="b-add-application-backend-and-backend-pools"></a>B. Aggiungere back-end e pool back-end dell'applicazione

Successivamente, è necessario configurare i front-end/domini e uno più back-end dell'applicazione in un pool back-end, per indicare a Frontdoor dove si trova l'applicazione. 

1. Fare clic sull'icona '+' nella scheda Pool back-end per aggiungere un pool back-end, quindi per **Nome** del pool back-end immettere `myBackendPool`.
2. Successivamente, fare clic su **Aggiungi un back-end** per aggiungere i siti Web creati in precedenza.
3. Selezionare **Tipo host back-end** per 'Servizio app', selezionare la sottoscrizione in cui è stato creato il sito Web e quindi scegliere il primo sito Web nell'elenco a discesa **Nome host back-end**.
4. Lasciare i campi restanti così come sono e fare clic su **Aggiungi**.
5. Selezionare **Tipo host back-end** per 'Servizio app', selezionare la sottoscrizione in cui è stato creato il sito Web e quindi scegliere il **secondo** sito Web nell'elenco a discesa **Nome host back-end**.
6. Lasciare i campi restanti così come sono e fare clic su **Aggiungi**.
7. Facoltativamente, si può scegliere di aggiornare le impostazioni di bilanciamento del carico e dei probe di integrità per il pool back-end, ma dovrebbero funzionare anche i valori predefiniti. In entrambi i casi, fare clic su **Aggiungi**.


### <a name="c-add-a-routing-rule"></a>C. Aggiungere una regola di routing
1. Infine, fare clic sull'icona '+' nella scheda Regole di routing per configurare una regola di routing. Questo è necessario per eseguire il mapping dell'host front-end con il pool back-end, che in sostanza consiste nel configurare che, quando `myappfrontend.azurefd.net` riceve una richiesta, deve inoltrarla al pool back-end`myBackendPool`. 
2. Per **Nome** immettere 'LocationRule'.
3. Fare clic su **Aggiungi** per aggiungere la regola di routing per la frontdoor. 
4. Fare clic su **Rivedi e crea**.
5. Esaminare le impostazioni per la creazione della frontdoor. Fare clic su **Crea**

>[!WARNING]
> È **necessario** assicurarsi che ognuno degli host front-end nella frontdoor disponga di una regola di routing con un percorso predefinito ('/\*') associato. Questo vuol dire che tra tutte le regole di gestione deve esisterne almeno una per ognuno degli host front-end definita nel percorso predefinito ('/\*'). In caso contrario, è possibile che il traffico degli utenti finali non venga instradato in modo corretto.

## <a name="view-front-door-in-action"></a>Esaminare Frontdoor in azione
Dopo aver creato una frontdoor, occorreranno alcuni minuti perché la configurazione sia distribuita ovunque a livello globale. Al termine della distribuzione, accedere all'host front-end creato, ovvero aprire un Web browser e raggiungere l'URL `myappfrontend.azurefd.net`. La richiesta verrà automaticamente instradata al back-end più vicino tra quelli specificati nel pool back-end. 

### <a name="view-front-door-handle-application-failover"></a>Esaminare Frontdoor che gestisce il failover dell'applicazione
Se si vuole testare il failover globale istantaneo di Frontdoor, è possibile passare a uno dei siti Web creati e arrestarlo. Base all'impostazione del probe di integrità definita per il pool back-end, verrà eseguito immediatamente il failover del traffico verso l'altra distribuzione del sito Web. È anche possibile testare il comportamento disabilitando il back-end nella configurazione del pool back-end per la frontdoor. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non sono più necessari, eliminare i gruppi di risorse **myResourceGroupFD1**, **myResourceGroupFD2** e **myResourceGroupFD0**:

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata una frontdoor che consente di indirizzare il traffico utente per le applicazioni Web che richiedono massime prestazioni e disponibilità elevata. Per altre informazioni sul routing del traffico, vedere [Metodi di routing di Frontdoor](front-door-routing-methods.md).
