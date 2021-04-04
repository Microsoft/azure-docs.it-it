---
title: Creare un probe personalizzato usando il portale
titleSuffix: Azure Application Gateway
description: Informazioni su come creare un probe personalizzato per un gateway applicazione usando il portale
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95975956"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creare un probe personalizzato per un gateway applicazione con il portale

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell per Azure classico](application-gateway-create-probe-classic-ps.md)

In questo articolo viene aggiunto un probe di integrità personalizzato a un gateway applicazione esistente tramite il portale di Azure. Usando i probe di integrità, applicazione Azure gateway monitora l'integrità delle risorse nel pool back-end.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone già di un gateway applicazione, vedere [Creare un gateway applicazione con il portale](./quick-create-portal.md) per creare un gateway applicazione da usare.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Creazione del probe per lo SKU del gateway applicazione V2

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nell'immettere i valori necessari per la configurazione del probe. Nel secondo passaggio si testa l'integrità back-end usando questa configurazione Probe e si salva il probe. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Immettere le proprietà del probe

1. Accedere al [portale di Azure](https://portal.azure.com). Se non si dispone già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di un mese](https://azure.microsoft.com/free)

2. Nel riquadro Preferiti del portale di Azure fare clic su Tutte le risorse. Fare clic su gateway applicazione nel pannello Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti risorse, è possibile immettere partners.contoso.net nella casella Filtra per nome per accedere con facilità al gateway applicazione.

3. Selezionare **Probe integrità** , quindi selezionare **Aggiungi** per aggiungere un nuovo probe di integrità.

   ![Aggiungi nuovo probe][4]

4. Nella pagina **Aggiungi Probe integrità** compilare le informazioni necessarie per il probe e, al termine, selezionare **OK**.

   |**Impostazione** | **Valore** | **Informazioni dettagliate**|
   |---|---|---|
   |**Nome**|customProbe|Questo valore è un nome descrittivo assegnato al Probe accessibile nel portale.|
   |**Protocollo**|HTTP o HTTPS | Protocollo usato per il probe di integrità. |
   |**Host**|vale a dire contoso.com|Questo valore è il nome dell'host virtuale, diverso dal nome host della macchina virtuale, in esecuzione nel server applicazioni. Il probe viene inviato a \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Selezionare il nome host da impostazioni HTTP back-end**|Sì o No|Imposta l'intestazione *host* nel Probe sul nome host dalle impostazioni http a cui è associato il probe. Requisito speciale in caso di backend multi-tenant, ad esempio servizio app di Azure. [Scopri di più](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Selezionare la porta dalle impostazioni HTTP back-end**| Sì o No|Imposta la *porta* del probe di integrità sulla porta dalle impostazioni http a cui è associato il probe. Se si sceglie No, è possibile immettere una porta di destinazione personalizzata da usare |
   |**Porta**| 1-65535 | Porta personalizzata da usare per i probe di integrità | 
   |**Percorso**|/o qualsiasi percorso valido|Parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/". Per il percorso predefinito http: \/ /contoso.com è sufficiente usare '/' |
   |**Intervallo (sec)**|30|Frequenza con cui viene eseguito il probe per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.|
   |**Timeout (secondi)**|30|Quantità di tempo di attesa del probe prima del timeout. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile. Si noti che il valore di timeout non deve essere superiore al valore ' intervallo ' usato in questa impostazione di probe o al valore ' timeout richiesta ' nell'impostazione HTTP che verrà associato a questo Probe.|
   |**Soglia non integra**|3|Numero di tentativi consecutivi non riusciti da considerare non integri. La soglia può essere impostata su 1 o più.|
   |**USA condizioni di corrispondenza Probe**|Sì o No|Per impostazione predefinita, una risposta HTTP(S) con codice di stato compreso tra 200 e 399 viene considerata integra. È possibile modificare l'intervallo accettabile del codice di risposta back-end o del corpo della risposta back-end. [Scopri di più](./application-gateway-probe-overview.md#probe-matching)|
   |**Impostazioni HTTP**|selezione dall'elenco a discesa|Il probe verrà associato alle impostazioni HTTP selezionate qui e, di conseguenza, eseguirà il monitoraggio dello stato del pool back-end associato all'impostazione HTTP selezionata. Utilizzerà la stessa porta per la richiesta di probe come quella usata nell'impostazione HTTP selezionata. È possibile scegliere solo le impostazioni HTTP che non sono associate ad altri Probe personalizzati. <br>Si noti che per l'associazione sono disponibili solo le impostazioni HTTP che hanno lo stesso protocollo scelto in questa configurazione di probe e hanno lo stesso stato per il *nome host pick dall'opzione di impostazione http back-end* .|
   
   > [!IMPORTANT]
   > Il probe eseguirà il monitoraggio dello stato del back-end solo quando è associato a una o più impostazioni HTTP. Verranno monitorate le risorse back-end dei pool back-end associati alle impostazioni HTTP a cui è associato il probe. La richiesta di probe verrà inviata come \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Testare l'integrità back-end con il probe

Dopo aver immesso le proprietà del probe, è possibile testare l'integrità delle risorse back-end per verificare che la configurazione del probe sia corretta e che le risorse back-end funzionino come previsto.

1. Selezionare **test** e prendere nota del risultato del probe. Il gateway applicazione verifica lo stato di tutte le risorse back-end nei pool back-end associati alle impostazioni HTTP usate per questo Probe. 

   ![Testare l'integrità back-end][5]

2. Se sono presenti risorse di back-end non integre, controllare la colonna **Dettagli** per comprendere il motivo dello stato non integro della risorsa. Se la risorsa è stata contrassegnata come non integra a causa di una configurazione di probe non corretta, selezionare il collegamento **tornare a probe** e modificare la configurazione del probe. In caso contrario, se la risorsa è stata contrassegnata come non integra a causa di un problema con il back-end, risolvere i problemi con la risorsa back-end e quindi testare di nuovo il back-end selezionando il collegamento Torna **a probe** e selezionare **test**.

   > [!NOTE]
   > È possibile scegliere di salvare il probe anche con risorse di back-end non integro, ma non è consigliabile. Questo perché il gateway applicazione non inoltrerà le richieste ai server back-end dal pool back-end, che risultano non integre dal probe. Se non sono presenti risorse integre in un pool back-end, non sarà possibile accedere all'applicazione e verrà ricevuto un errore HTTP 502.

   ![Visualizza risultati Probe][6]

3. Selezionare **Aggiungi** per salvare il probe. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Creazione del probe per lo SKU del gateway applicazione V1

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nel creare il probe. Nel secondo passaggio lo si aggiunge alle impostazioni http back-end del gateway applicazione.

### <a name="create-the-probe"></a><a name="createprobe"></a>Creare il probe

1. Accedere al [portale di Azure](https://portal.azure.com). Se non si dispone già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di un mese](https://azure.microsoft.com/free)

2. Nel riquadro Preferiti del portale di Azure selezionare **Tutte le risorse**. Selezionare il gateway applicazione nella pagina **tutte le risorse** . Se nella sottoscrizione selezionata sono già presenti risorse, è possibile immettere partners.contoso.net nella casella Filtra per nome per accedere con facilità al gateway applicazione.

3. Selezionare **Probe** e quindi selezionare **Aggiungi** per aggiungere un probe.

   ![Pannello Aggiungi probe con informazioni inserite][1]

4. Nel pannello **Aggiungi Probe integrità** compilare le informazioni necessarie per il probe e, al termine, selezionare **OK**.

   |**Impostazione** | **Valore** | **Informazioni dettagliate**|
   |---|---|---|
   |**Nome**|customProbe|Questo valore è un nome descrittivo assegnato al Probe accessibile nel portale.|
   |**Protocollo**|HTTP o HTTPS | Protocollo usato per il probe di integrità. |
   |**Host**|vale a dire contoso.com|Questo valore è il nome dell'host virtuale, diverso dal nome host della macchina virtuale, in esecuzione nel server applicazioni. Il probe viene inviato a (protocollo)://(nome host):(porta da impostazioni)/urlPath.  Questa operazione è applicabile quando si configura multisito nel gateway applicazione. Se il gateway applicazione è configurato per un singolo sito, immettere "127.0.0.1".|
   |**Selezionare il nome host da impostazioni HTTP back-end**|Sì o No|Imposta l'intestazione *host* nel Probe sul nome host della risorsa back-end nel pool back-end associato all'impostazione http a cui è associato il probe. Requisito speciale in caso di backend multi-tenant, ad esempio servizio app di Azure. [Scopri di più](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Percorso**|/o qualsiasi percorso valido|Parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/". Per il percorso predefinito http: \/ /contoso.com è sufficiente usare '/' |
   |**Intervallo (sec)**|30|Frequenza con cui viene eseguito il probe per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.|
   |**Timeout (secondi)**|30|Quantità di tempo di attesa del probe prima del timeout. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile. Si noti che il valore di timeout non deve essere superiore al valore ' intervallo ' usato in questa impostazione di probe o al valore ' timeout richiesta ' nell'impostazione HTTP che verrà associato a questo Probe.|
   |**Soglia non integra**|3|Numero di tentativi consecutivi non riusciti da considerare non integri. La soglia può essere impostata su 1 o più.|
   |**USA condizioni di corrispondenza Probe**|Sì o No|Per impostazione predefinita, una risposta HTTP(S) con codice di stato compreso tra 200 e 399 viene considerata integra. È possibile modificare l'intervallo accettabile del codice di risposta back-end o del corpo della risposta back-end. [Scopri di più](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Il nome host non è uguale al nome del server. Questo valore è il nome dell'host virtuale in esecuzione nel server applicazioni. Il probe viene inviato a \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Aggiungere il probe al gateway

Ora che il probe è stato creato, deve essere aggiunto al gateway. Le impostazioni del probe vengono definite nelle impostazioni HTTP back-end del gateway applicazione.

1. Fare clic su **Impostazioni HTTP** nel gateway applicazione e quindi fare clic sulle impostazioni HTTP back-end correnti presenti nella finestra per visualizzare il pannello di configurazione.

   ![Finestra delle impostazioni HTTP][2]

2. Nella pagina impostazioni **appGatewayBackEndHttpSettings** selezionare la casella di controllo **USA Probe personalizzato** e scegliere il probe creato nella sezione [creare il probe](#createprobe) nell'elenco a discesa **Probe personalizzato** .
   Al termine fare clic su **Salva** per applicare le impostazioni.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare l'integrità delle risorse back-end come determinato dal Probe usando la [visualizzazione integrità back-end](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
