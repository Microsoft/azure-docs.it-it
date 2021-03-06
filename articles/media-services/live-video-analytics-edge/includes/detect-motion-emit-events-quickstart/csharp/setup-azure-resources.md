---
ms.openlocfilehash: 6b1df49ed0f2c543706d7584a1ade5dc5554a564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750361"
---
Per questa esercitazione sono necessarie le risorse di Azure seguenti:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) installato

Per questo argomento di avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

1. Aprire [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare **Bash** come ambiente.

    ![Selezione dell'ambiente](../../../media/quickstarts/env-selector.png)
1. Eseguire il comando seguente.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Dopo il completamento dello script, verranno visualizzate tutte le risorse necessarie nella sottoscrizione. Il totale di 12 risorse verrà configurato dallo script:
    1. **Endpoint di streaming** : consente di riprodurre l'asset AMS registrato.
    1. **Macchina virtuale** : si tratta di una macchina virtuale che fungerà da dispositivo perimetrale.
    1. **Disco** : si tratta di un disco di archiviazione collegato alla macchina virtuale per archiviare i supporti e gli artefatti.
    1. **Gruppo di sicurezza di rete** : viene usato per filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure.
    1. **Interfaccia di rete** : consente a una macchina virtuale di Azure di comunicare con Internet, Azure e altre risorse.
    1. **Connessione Bastion** : consente di connettersi alla macchina virtuale usando il browser e la portale di Azure.
    1. **Indirizzo IP pubblico** : consente alle risorse di Azure di comunicare con Internet e con servizi di Azure pubblici
    1. **Rete virtuale** : consente a molti tipi di risorse di Azure, ad esempio la macchina virtuale, di comunicare in modo sicuro tra loro, con Internet e con le reti locali. Altre informazioni sulle [reti virtuali](../../../../../virtual-network/virtual-networks-overview.md)
    1. **Hub** di Internet delle cose: funge da Hub messaggi centrale per la comunicazione bidirezionale tra l'applicazione internet, IOT Edge moduli e i dispositivi gestiti.
    1. **Account del servizio multimediale** : consente di gestire e trasmettere in streaming contenuti multimediali in Azure.
    1. **Account** di archiviazione: è necessario avere un account di archiviazione primario ed è possibile avere un numero qualsiasi di account di archiviazione secondari associati all'account di servizi multimediali. Per altre informazioni, vedere [Account di archiviazione di Azure con account di Servizi multimediali di Azure](../../../../latest/storage-account-concept.md).
    1. **Registro contenitori** : consente di archiviare e gestire le immagini del contenitore Docker privato e gli artefatti correlati.

1. Al termine dello script, selezionare le parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file nella directory *~/clouddrive/lva-sample*. I file rilevanti per questo avvio rapido sono:

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - Questo file contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo perimetrale.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa questo file per l'esecuzione del codice di esempio.
     
    Questi file saranno necessari per configurare l'ambiente di sviluppo in Visual Studio Code nella sezione successiva. È consigliabile copiarli in un file locale per il momento.
    
    ![Impostazioni app](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Se si verificano problemi con le risorse di Azure create, vedere la **[guida alla risoluzione dei problemi](../../../troubleshoot-how-to.md#common-error-resolutions)** più comuni rilevati.