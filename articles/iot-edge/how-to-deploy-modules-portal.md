---
title: Distribuire moduli da portale di Azure-Azure IoT Edge
description: Usare l'hub Internet delle cose nel portale di Azure per eseguire il push di un modulo di IoT Edge dall'hub Internet al dispositivo IoT Edge, come configurato da un manifesto della distribuzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9248c9578d94b000c04c82b33eeeb089e55a26ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200303"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuire i moduli di Azure IoT Edge dal portale di Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Dopo aver creato i moduli di IoT Edge in base alla propria logica di business, si distribuiscono i moduli nei dispositivi per consentirne l'uso a livello perimetrale. Se si hanno più moduli che interagiscono per raccogliere ed elaborare dati, è possibile distribuirli contemporaneamente e dichiarare le regole di routing che li connettono.

Questo articolo illustra come il portale di Azure consente di creare un manifesto della distribuzione ed eseguire il push della distribuzione a un dispositivo IoT Edge. Per informazioni sulla creazione di una distribuzione destinata a più dispositivi in base ai tag condivisi, vedere [distribuire e monitorare IOT Edge moduli su larga scala](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.
* Un dispositivo IoT Edge.

  Se non si dispone di un dispositivo IoT Edge configurato, è possibile crearne uno in una macchina virtuale di Azure. Per [creare un dispositivo Linux virtuale](quickstart-linux.md) o [creare un dispositivo Windows virtuale](quickstart.md), seguire la procedura descritta in uno degli articoli introduttivi.

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

Nel portale di Azure è disponibile una procedura guidata che consente di creare il manifesto dell'applicazione anziché creare il documento JSON manualmente. Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

>[!NOTE]
>I passaggi descritti in questo articolo riflettono la versione dello schema più recente dell'agente e dell'hub IoT Edge. La versione dello schema 1,1 è stata rilasciata insieme a IoT Edge versione 1.0.10 e Abilita le funzionalità per l'ordine di avvio e la definizione delle priorità del modulo.
>
>Se si esegue la distribuzione in un dispositivo che esegue la versione 1.0.9 o precedente, modificare le **impostazioni di runtime** nel passaggio **moduli** della procedura guidata per usare la versione dello schema 1,0.

### <a name="select-device-and-add-modules"></a>Selezionare il dispositivo e aggiungere i moduli

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Nel riquadro sinistro selezionare **IOT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Sulla barra superiore selezionare **Imposta moduli**.
1. Nella sezione **impostazioni container Registry** della pagina fornire le credenziali per accedere ai registri di contenitori privati contenenti le immagini del modulo.
1. Nella sezione **moduli IOT Edge** della pagina selezionare **Aggiungi**.
1. Scegliere uno dei tre tipi di moduli dal menu a discesa:

   * **Modulo IOT Edge** : specificare il nome del modulo e l'URI dell'immagine del contenitore. Ad esempio, l'URI dell'immagine per il modulo SimulatedTemperatureSensor di esempio è `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Se l'immagine del modulo è archiviata in un registro contenitori privato, aggiungere le credenziali in questa pagina per accedere all'immagine.
   * **Modulo Marketplace** : moduli ospitati in Azure Marketplace. Alcuni moduli del Marketplace richiedono una configurazione aggiuntiva, quindi esaminare i dettagli del modulo nell'elenco dei [moduli IOT Edge di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Modulo di analisi di flusso di Azure** : moduli generati da un carico di lavoro di analisi di flusso di Azure.

1. Dopo aver aggiunto un modulo, selezionare il nome del modulo nell'elenco per aprire le impostazioni del modulo. Specificare i campi facoltativi, se necessario.

   Per ulteriori informazioni sulle impostazioni dei moduli disponibili, vedere [configurazione e gestione dei moduli](module-composition.md#module-configuration-and-management).

   Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](module-composition.md#define-or-update-desired-properties).

1. Ripetere i passaggi da 6 a 8 per aggiungere altri moduli alla distribuzione.
1. Selezionare **Avanti: Route** per passare alla sezione route.

### <a name="specify-routes"></a>Specificare le route

Nella scheda **Route** è possibile definire come vengono passati i messaggi tra i moduli e l'hub IoT. I messaggi vengono costruiti mediante coppie nome/valore. Per impostazione predefinita, la prima distribuzione per un nuovo dispositivo include una route denominata **Route** e definita **da/messages/ \* in $upstream**, il che significa che tutti i messaggi restituiti da qualsiasi modulo vengono inviati all'hub Internet.  

I parametri **Priority** e **time to Live** sono parametri facoltativi che è possibile includere in una definizione di route. Il parametro Priority consente di scegliere le route di cui devono essere elaborati i messaggi prima o le route da elaborare per ultime. La priorità viene determinata impostando un numero 0-9, dove 0 è la priorità più alta. Il parametro time to Live consente di dichiarare per quanto tempo i messaggi in tale route devono essere conservati fino a quando non vengono elaborati o rimossi dalla coda.

Per altre informazioni su come creare route, vedere [dichiarare le route](module-composition.md#declare-routes).

Dopo aver impostato le route, fare clic su **Avanti: verifica + crea** per continuare con il passaggio successivo della procedura guidata.

### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Si noti che sono presenti due moduli dichiarati che non sono stati aggiunti: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Esaminare le informazioni di distribuzione e quindi selezionare **Crea**.

## <a name="view-modules-on-your-device"></a>Visualizzare i moduli nel dispositivo

Dopo aver distribuito i moduli al dispositivo, è possibile visualizzarli tutti nella pagina dei dettagli del dispositivo dell'hub Internet. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuire i moduli da Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) è un marketplace online di applicazioni e servizi in cui è possibile esplorare un'ampia gamma di applicazioni e soluzioni aziendali certificate e ottimizzate per l'esecuzione in Azure, inclusi i [moduli IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

È possibile distribuire un modulo IoT Edge da Azure Marketplace e dall'hub Internet delle cose.

### <a name="deploy-from-azure-marketplace"></a>Distribuire da Azure Marketplace

Esaminare i moduli IoT Edge nel Marketplace e, quando si trova quello che si vuole, è possibile distribuirlo selezionando **Crea** o **Ottieni ora**. Procedere con la procedura guidata di distribuzione che può variare a seconda del modulo IoT Edge selezionato:

1. Accettare le condizioni per l'utilizzo e l'informativa sulla privacy del provider selezionando **Continua**. Potrebbe essere necessario fornire le informazioni di contatto.
1. Scegliere la sottoscrizione e l'hub IoT a cui è collegato il dispositivo di destinazione.
1. Scegliere **Distribuisci in un dispositivo**.
1. Immettere il nome del dispositivo o selezionare **Trova dispositivo** per cercare tra i dispositivi registrati nell'hub.
1. Selezionare **Crea** per continuare il processo standard di configurazione di un manifesto della distribuzione, inclusa l'aggiunta di altri moduli, se lo si desidera. I dettagli per il nuovo modulo, ad esempio URI dell'immagine, opzioni di creazione e proprietà desiderate, sono predefiniti, ma possono essere modificati.

Verificare che il modulo sia distribuito nell'hub Internet delle cose nel portale di Azure. Selezionare il dispositivo, selezionare **imposta moduli** per elencare il modulo nella sezione **moduli IOT Edge** .

### <a name="deploy-from-azure-iot-hub"></a>Eseguire la distribuzione dall'hub Azure

È possibile distribuire rapidamente un modulo da Azure Marketplace sul dispositivo nell'hub Internet delle cose nel portale di Azure.

1. Nel portale di Azure passare all'hub IoT.
1. Nel riquadro sinistro, in **gestione automatica dispositivi**, selezionare **IOT Edge**.
1. Selezionare il dispositivo IoT Edge per ricevere la distribuzione.
1. Sulla barra superiore selezionare **Imposta moduli**.
1. Nella sezione **moduli IOT Edge** fare clic su **Aggiungi** e selezionare **modulo Marketplace** dal menu a discesa.

![Aggiungere un modulo nell'hub Internet](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Scegliere un modulo dalla pagina del **Marketplace del modulo IOT Edge** . Il modulo selezionato viene configurato automaticamente per la sottoscrizione, il gruppo di risorse e il dispositivo. Viene quindi visualizzato nell'elenco dei moduli IoT Edge. Alcuni moduli possono richiedere una configurazione aggiuntiva.

> [!TIP]
> Le informazioni sui moduli IoT Edge dall'hub Azure Internet è limitata. È possibile ottenere altre informazioni sui [moduli IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) in Azure Marketplace.

Selezionare **Avanti: instrada** e continua con la distribuzione come descritto in [specificare le route](#specify-routes) e verificare la [distribuzione](#review-deployment) in precedenza in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-at-scale.md)
