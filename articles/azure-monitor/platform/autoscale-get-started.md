---
title: Introduzione alla scalabilità automatica in Azure
description: Informazioni su come applicare la scalabilità della risorsa app Web, servizio cloud, macchina virtuale o set di scalabilità di macchine virtuali in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: ee36db3f657365036bb68f641be53fd434f1b64b
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694925"
---
# <a name="get-started-with-autoscale-in-azure"></a>Introduzione alla scalabilità automatica in Azure
Questo articolo descrive come configurare l'impostazione di scalabilità automatica per la risorsa nel portale di Microsoft Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/), [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/) e [servizi di gestione API](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Individuare le impostazioni di scalabilità automatica nella sottoscrizione

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

È possibile individuare tutte le risorse per le quali è applicabile la scalabilità automatica in Monitoraggio di Azure. Eseguire i passaggi descritti di seguito per una procedura guidata:

1. Aprire il [portale di Azure.][1]
1. Fare clic sull'icona di Monitoraggio di Azure nel riquadro a sinistra.
  ![Aprire Monitoraggio di Azure][2]
1. Fare clic su **Scalabilità automatica** per visualizzare tutte le risorse per cui è applicabile, nonché il relativo stato corrente di scalabilità automatica.
  ![Individuazione della scalabilità automatica nel Monitoraggio di Azure][3]

È possibile usare il riquadro filtro nella parte superiore per ridurre l'ambito dell'elenco e selezionare le risorse in un gruppo di risorse specifico, i tipi di risorse specifici o una determinata risorsa.

Per ogni risorsa verranno indicati il numero di istanze corrente e lo stato di scalabilità automatica. Lo stato di scalabilità automatica può essere:

- **Non configurato**: non è stata ancora abilitata la scalabilità automatica per questa risorsa.
- **Configurato**: è stata abilitata la scalabilità automatica per questa risorsa.
- **Disattivato**: è stata disattivata la scalabilità automatica per questa risorsa.

## <a name="create-your-first-autoscale-setting"></a>Creare la prima impostazione di scalabilità automatica

Verrà ora illustrata una semplice procedura dettagliata per creare la prima impostazione di scalabilità automatica.

1. Aprire il pannello **Scalabilità automatica** in Monitoraggio di Azure e selezionare una risorsa da ridimensionare (la procedura seguente usa un piano di servizio app associato a un'app Web. È possibile [creare la prima app Web ASP.NET in Azure in 5 minuti][4]).
1. Notare che il numero corrente di istanze per il ruolo è 1. Fare clic su **Abilita scalabilità automatica**.
  ![Impostazione di scalabilità per la nuova app Web][5]
1. Specificare un nome per il set di scalabilità, quindi scegliere **Aggiungi una regola**. Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70 per cento. Lasciare i valori predefiniti e fare clic su **Aggiungi**.
  ![Creare l'impostazione di scalabilità per un'app Web][6]
1. È stata così creata la prima regola di scalabilità. Si noti che l'esperienza utente indica le procedure consigliate e che "È consigliabile includere almeno una regola di riduzione del numero di istanze". A tale scopo, procedere come indicato di seguito:

    a. Fare clic su **Aggiungi regola**.

    b. Impostare **Operatore** a **Minore di**.

    c. Impostare **Soglia** su **20**.

    d. Impostare **Operazione** su **Diminuisci il numero di**.

   A questo punto si avrà un'impostazione di scalabilità che aumenta/riduce il numero di istanze in base all'utilizzo della CPU.
   ![Scalabilità in base alla CPU][8]
1. Fare clic su **Salva**.

Congratulazioni. A questo punto è stata creata la prima impostazione di scalabilità automatica per l'app Web in base all'utilizzo della CPU.

> [!NOTE]
> Gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

## <a name="other-considerations"></a>Altre considerazioni
### <a name="scale-based-on-a-schedule"></a>Scalare in base a una pianificazione
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per giorni specifici della settimana.

1. Fare clic su **Aggiungi una condizione di scalabilità**.
1. L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita.
1. Selezionare **Ripeti in giorni specifici** per la pianificazione.
1. Selezionare i giorni e l'ora di inizio/fine per l'applicazione della condizione di scalabilità.

![Condizione di scalabilità in base alla pianificazione][9]
### <a name="scale-differently-on-specific-dates"></a>Impostare la scalabilità in modo diverso per date specifiche
Oltre alla scalabilità basata sempre sulla CPU, è possibile impostare la scalabilità in modo diverso per le date specifiche.

1. Fare clic su **Aggiungi una condizione di scalabilità**.
1. L'impostazione della modalità e delle regole di scalabilità è uguale alla condizione predefinita.
1. Selezionare **Specificare le date di inizio/fine** per la pianificazione.
1. Selezionare i giorni e l'ora di inizio/fine per l'applicazione della condizione di scalabilità.

![Condizione di scalabilità in base alle date][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visualizzare la cronologia di scalabilità della risorsa
Ogni volta che vengono aumentate o ridotte le prestazioni della risorsa, viene registrato un evento nel log attività. È possibile visualizzare la cronologia della scalabilità della risorsa per le ultime 24 ore passando alla scheda **Cronologia di esecuzione**.

![Cronologia di esecuzione][11]

Per visualizzare la cronologia della scalabilità completa (fino a 90 giorni), selezionare **Fare clic qui per visualizzare altri dettagli**. Si aprirà il log attività con Scalabilità automatica preselezionata per la risorsa e la categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Visualizzare la definizione di scalabilità della risorsa
Scalabilità automatica è una risorsa di Azure Resource Manager. È possibile visualizzare la definizione del piano in JSON passando alla scheda **JSON**.

![Definizione del piano][12]

È possibile apportare modifiche direttamente in JSON, se necessario. Queste modifiche saranno applicate dopo averle salvate.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Disabilitare la scalabilità automatica e ridimensionare le istanze manualmente
A volte può essere opportuno disabilitare l'impostazione di scalabilità corrente e ridimensionare la risorsa manualmente.

Fare clic sul pulsante **Disabilita scalabilità automatica** nella parte superiore.
![Disabilita scalabilità automatica][13]

> [!NOTE]
> Questa opzione disabilita la configurazione. Tuttavia, è possibile ritornare dopo aver abilitato nuovamente la scalabilità automatica.

È ora possibile impostare il numero di istanze da ridimensionare manualmente.

![Impostare la scalabilità manuale][14]

È sempre possibile impostare nuovamente la scalabilità automatica facendo clic su **Abilita scalabilità automatica** e quindi su **Salva**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Instradare il traffico a istanze integre (servizio app)

Quando si aumenta la scalabilità orizzontale a più istanze, il servizio app può eseguire controlli di integrità sulle istanze per instradare il traffico solo alle istanze integre. A tale scopo, aprire il portale per il servizio app, quindi selezionare **controllo integrità** in **monitoraggio**. Selezionare **Abilita** e specificare un percorso URL valido nell'applicazione, ad esempio `/health` o `/api/health` . Fare clic su **Salva**.

Per abilitare la funzionalità con i modelli ARM, impostare la `healthcheckpath` proprietà della `Microsoft.Web/sites` risorsa sul percorso di controllo integrità nel sito, ad esempio: `"/api/health/"` . Per disabilitare la funzionalità, impostare nuovamente la proprietà sulla stringa vuota `""` .

### <a name="health-check-path"></a>Percorso controllo integrità

Il percorso deve rispondere entro un minuto con un codice di stato compreso tra 200 e 299 (inclusi). Se il percorso non risponde entro un minuto o restituisce un codice di stato non compreso nell'intervallo, l'istanza viene considerata "non integra". Il servizio app non segue 30x (301, 302, 307 e così via) reindirizza il percorso del controllo di integrità. questi codici di stato sono considerati non **integri**. Il controllo dell'integrità si integra con le funzionalità di autenticazione e autorizzazione del servizio app. il sistema raggiungerà l'endpoint anche se queste funzionalità di sicurezza sono abilitate. Se si usa il proprio sistema di autenticazione, il percorso di controllo integrità deve consentire l'accesso anonimo. Se nel sito è abilitato solo HTTP **s**, la richiesta Healthcheck verrà inviata tramite http **s**.

Il percorso di controllo integrità deve controllare i componenti critici dell'applicazione. Se, ad esempio, l'applicazione dipende da un database e da un sistema di messaggistica, l'endpoint di controllo integrità deve connettersi a tali componenti. Se l'applicazione non è in grado di connettersi a un componente critico, il percorso deve restituire un codice di risposta a 500 per indicare che l'app non è integra.

#### <a name="security"></a>Sicurezza 

I team di sviluppo di grandi imprese spesso devono rispettare i requisiti di sicurezza per le API esposte. Per proteggere l'endpoint Healthcheck, è necessario prima usare funzionalità come [restrizioni IP](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule), [certificati client](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)o una rete virtuale per limitare l'accesso all'applicazione. È possibile proteggere l'endpoint Healthcheck stesso richiedendo che la `User-Agent` della richiesta in ingresso corrisponda a `ReadyForRequest/1.0` . Non è possibile eseguire lo spoofing del User-Agent perché la richiesta è già stata protetta dalle funzionalità di sicurezza precedenti.

### <a name="behavior"></a>Comportamento

Quando viene fornito il percorso di controllo integrità, il servizio app effettuerà il ping del percorso in tutte le istanze. Se dopo 5 ping non viene ricevuto un codice di risposta con esito positivo, l'istanza viene considerata "non integra". Le istanze non integre verranno escluse dalla rotazione del servizio di bilanciamento del carico se si aumenta la scalabilità orizzontale a due o più istanze e si usa il [livello Basic](../../app-service/overview-hosting-plans.md) o superiore. È possibile configurare il numero necessario di ping non riusciti con l' `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` impostazione dell'app. Questa impostazione dell'app può essere impostata su qualsiasi numero intero compreso tra 2 e 10. Se, ad esempio, è impostato su `2` , le istanze verranno rimosse dal servizio di bilanciamento del carico dopo due ping non riusciti. Inoltre, quando si esegue la scalabilità verticale o orizzontale, il servizio app effettuerà il ping del percorso di controllo integrità per assicurarsi che le nuove istanze siano pronte per le richieste prima di essere aggiunte al servizio di bilanciamento del carico.

> [!NOTE]
> Tenere presente che il piano di servizio app deve essere scalato orizzontalmente a 2 o più istanze ed essere di **livello Basic o superiore** affinché venga eseguita l'esclusione del bilanciamento del carico. Se è presente solo un'istanza, non verrà rimossa dal servizio di bilanciamento del carico anche se non è integro. 

Inoltre, viene effettuato il ping del percorso di controllo integrità quando le istanze vengono aggiunte o riavviate, ad esempio durante le operazioni di scalabilità orizzontale, i riavvii manuali o la distribuzione di codice tramite il sito SCM. Se il controllo di integrità ha esito negativo durante queste operazioni, le istanze con errori non verranno aggiunte al servizio di bilanciamento del carico. In questo modo si impedisce che queste operazioni rifluiscano negativamente sulla disponibilità dell'applicazione.

Quando si usa Healthcheck, è possibile che si verifichi un aumento del carico delle istanze rimanenti integre. Per evitare di sovraccaricare le istanze rimanenti, non verranno escluse più della metà delle istanze. Se, ad esempio, un piano di servizio app viene scalato in orizzontale a 4 istanze e 3 di quelle non integre, al massimo 2 verranno escluse dalla rotazione del LoadBalancer. Le altre 2 istanze (1 integro e 1 non integro) continueranno a ricevere le richieste. Nello scenario peggiore in cui tutte le istanze non sono integre, nessuna verrà esclusa. Se si vuole eseguire l'override di questo comportamento, è possibile impostare l' `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` impostazione dell'app su un valore compreso tra `0` e `100` . Se si imposta questa opzione su un valore più alto, verranno rimosse più istanze non integre (il valore predefinito è 50).

Se i controlli di integrità hanno esito negativo per tutte le app in un'istanza per un'ora, l'istanza verrà sostituita. Al massimo un'istanza verrà sostituita all'ora, con un massimo di tre istanze al giorno per ogni piano di servizio app.

### <a name="monitoring"></a>Monitoraggio

Dopo aver fornito il percorso di controllo integrità dell'applicazione, è possibile monitorare l'integrità del sito usando monitoraggio di Azure. Nel pannello **controllo integrità** nel portale fare clic sulle **metriche** nella barra degli strumenti superiore. Verrà aperto un nuovo pannello in cui è possibile visualizzare lo stato di integrità cronologico del sito e creare una nuova regola di avviso. Per ulteriori informazioni sul monitoraggio dei siti, [vedere la Guida di monitoraggio di Azure](../../app-service/web-sites-monitor.md).

## <a name="moving-autoscale-to-a-different-region"></a>Trasferimento della scalabilità automatica in un'area diversa
Questa sezione descrive come spostare la scalabilità automatica di Azure in un'altra area nella stessa sottoscrizione e nel gruppo di risorse. È possibile usare l'API REST per spostare le impostazioni di scalabilità automatica.
### <a name="prerequisite"></a>Prerequisito
1. Assicurarsi che la sottoscrizione e il gruppo di risorse siano disponibili e che i dettagli in entrambe le aree di origine e di destinazione siano identici.
1. Verificare che la scalabilità automatica di Azure sia disponibile nell' [area di Azure in cui si vuole eseguire lo spostamento](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Spostamento
Usare l' [API REST](/rest/api/monitor/autoscalesettings/createorupdate) per creare un'impostazione di scalabilità automatica nel nuovo ambiente. L'impostazione di scalabilità automatica creata nell'area di destinazione sarà una copia dell'impostazione di scalabilità automatica nell'area di origine.

Non è possibile spostare [le impostazioni di diagnostica](./diagnostic-settings.md) create in associazione all'impostazione di scalabilità automatica nell'area di origine. Sarà necessario ricreare le impostazioni di diagnostica nell'area di destinazione, dopo il completamento della creazione delle impostazioni di autoVendita. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Altre informazioni sullo stato di trasferimento delle risorse tra le aree di Azure
Per altre informazioni sullo spostamento di risorse tra aree e ripristino di emergenza in Azure, vedere [spostare le risorse in un nuovo gruppo di risorse o una nuova sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Passaggi successivi
- [Creare un avviso di log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creare un avviso di log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
