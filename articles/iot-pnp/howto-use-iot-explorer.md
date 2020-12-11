---
title: Installare e usare Esplora risorse di Azure | Microsoft Docs
description: Installare lo strumento Azure Internet Explorer e usarlo per interagire con le Plug and Play dispositivi connessi all'hub. Sebbene questo articolo sia incentrato sull'uso dei dispositivi Plug and Play, è possibile usare lo strumento con qualsiasi dispositivo connesso all'hub.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030316"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installare e usare Azure Internet Explorer

Azure Internet Explorer è uno strumento grafico per l'interazione con i dispositivi e connessi all'hub Internet delle cose. Questo articolo è incentrato sull'uso dello strumento per testare i dispositivi Plug and Play. Dopo aver installato lo strumento nel computer locale, è possibile usarlo per connettersi a un hub. È possibile usare lo strumento per visualizzare i dati di telemetria inviati dai dispositivi, usare le proprietà del dispositivo e richiamare i comandi.

Questo articolo illustra come:

- Installare e configurare lo strumento Azure Internet Explorer.
- Usare lo strumento per interagire e testare i dispositivi Plug and Play.

Per informazioni più generali sull'uso dello strumento, vedere il [file Leggimi](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)di GitHub.

Per usare lo strumento Azure Internet Explorer, è necessario:

- Un hub IoT di Azure. Sono disponibili diversi modi per aggiungere un hub Internet alla sottoscrizione di Azure, ad esempio [la creazione di un hub Internet delle cose usando l'interfaccia della riga di comando di Azure](../iot-hub/iot-hub-create-using-cli.md). Per eseguire lo strumento Azure Internet Explorer è necessaria la stringa di connessione dell'hub Internet. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Un dispositivo registrato nell'hub IoT. È possibile usare Esplora risorse per creare e gestire le registrazioni dei dispositivi nell'hub Internet delle cose.

## <a name="install-azure-iot-explorer"></a>Installare Azure Internet Explorer Explorer

Passare ad [Azure Internet Explorer releases](https://github.com/Azure/azure-iot-explorer/releases) ed espandere l'elenco degli asset per la versione più recente. Scaricare e installare la versione più recente dell'applicazione.

>[!Important]
> Eseguire l'aggiornamento alla versione 0.13. x per risolvere i modelli da qualsiasi repository basato su [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Usare Esplora risorse di Azure

Per un dispositivo, è possibile connettere il proprio dispositivo o usare uno dei dispositivi simulati di esempio. Per alcuni dispositivi simulati di esempio scritti in linguaggi diversi, vedere la Guida introduttiva all' [applicazione per la connessione di un dispositivo Plug and Play dispositivo a un hub](quickstart-connect-device.md) .

### <a name="connect-to-your-hub"></a>Connettersi all'hub

La prima volta che si esegue Azure Internet Explorer, viene richiesta la stringa di connessione dell'hub. Dopo aver aggiunto la stringa di connessione, selezionare **Connetti**. È possibile usare le impostazioni dello strumento per passare a un altro hub delle cose aggiornando la stringa di connessione.

La definizione del modello per un dispositivo Plug and Play viene archiviata sia nel repository pubblico, nel dispositivo connesso o in una cartella locale. Per impostazione predefinita, lo strumento Cerca la definizione del modello nel repository pubblico e nel dispositivo connesso. È possibile aggiungere e rimuovere origini oppure configurare la priorità delle origini nelle **Impostazioni**:

Per aggiungere un'origine:

1. Vai a **Home/Plug and Play le impostazioni**
2. Selezionare **Aggiungi** e scegliere l'origine, da un repository o da una cartella locale.

Per rimuovere un'origine:

1. Vai a **Home/Plug and Play le impostazioni**
2. Individuare l'origine che si desidera rimuovere.
3. Selezionare **X** per rimuoverlo.

Modificare le priorità di origine:

È possibile trascinare e rilasciare una delle origini della definizione del modello in un rango diverso nell'elenco.

### <a name="view-devices"></a>Visualizzare i dispositivi

Una volta che lo strumento si connette all'hub Internet, Visualizza la pagina di elenco dei **dispositivi** in cui sono elencate le identità del dispositivo registrate con l'hub Internet. Per visualizzare altre informazioni, è possibile selezionare qualsiasi voce nell'elenco.

Nella pagina dell'elenco dei **dispositivi** è possibile:

- Selezionare **nuovo** per registrare un nuovo dispositivo con l'hub. Immettere quindi un ID dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e abilitare la connessione all'hub.
- Selezionare un dispositivo e quindi fare clic su **Elimina** per eliminare un'identità del dispositivo. Esaminare i dettagli del dispositivo prima di completare questa azione per assicurarsi di eliminare l'identità del dispositivo corretta.

## <a name="interact-with-a-device"></a>Interagire con un dispositivo

Nella pagina elenco **dispositivi** selezionare un valore nella colonna **ID dispositivo** per visualizzare la pagina dei dettagli del dispositivo registrato. Per ogni dispositivo sono presenti due sezioni: **Device** e **Digital gemelle**.

### <a name="device"></a>Dispositivo

Questa sezione include le schede **identità dispositivo**,  **dispositivo gemello**, **telemetria**, **metodo diretto**, **messaggio da cloud a dispositivo**, **identità modulo**  .

- È possibile visualizzare e aggiornare le informazioni sull' [identità del dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md) nella scheda **identità dispositivo** .
- È possibile accedere alle informazioni sul [dispositivo gemello](../iot-hub/iot-hub-devguide-device-twins.md) nella scheda del **dispositivo gemello** .
- Se un dispositivo è connesso e invia attivamente dati, è possibile visualizzare i dati di [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) nella scheda **telemetria** .
- È possibile chiamare un [metodo diretto](../iot-hub/iot-hub-devguide-direct-methods.md) sul dispositivo nella scheda **metodo diretto** .
- È possibile inviare un [messaggio da cloud a dispositivo](../iot-hub/iot-hub-devguide-messages-c2d.md) nella scheda **messaggi da cloud a dispositivo** .
- È possibile accedere alle informazioni del [modulo gemello](../iot-hub/iot-hub-devguide-module-twins.md) .

### <a name="iot-plug-and-play-components"></a>Componenti Plug and Play

Se il dispositivo è connesso all'hub usando un **ID modello**, lo strumento Mostra la scheda **componenti plug and Play** , in cui è possibile visualizzare l' **ID modello**.

Se l' **ID del modello** è disponibile in una delle origini configurate, ovvero il repository pubblico o la cartella locale, viene visualizzato l'elenco dei componenti. Selezionando un componente vengono visualizzate le proprietà, i comandi e i dati di telemetria disponibili.

Nella pagina **componente** è possibile visualizzare le proprietà di sola lettura, aggiornare le proprietà scrivibili, richiamare i comandi e visualizzare i messaggi di telemetria prodotti da questo componente.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Visualizzare i componenti in Azure IoT Explorer":::

#### <a name="properties"></a>Proprietà

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Visualizzare le proprietà in Azure Internet Explorer":::

È possibile visualizzare le proprietà di sola lettura definite in un'interfaccia nella scheda **Proprietà (sola lettura)** . È possibile aggiornare le proprietà scrivibili definite in un'interfaccia nella scheda **Proprietà (scrivibile)** :

1. Passare alla scheda **Proprietà (scrivibile)** .
1. Fare clic sulla proprietà che si desidera aggiornare.
1. Immettere il nuovo valore per la proprietà.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare la modifica.

Dopo aver inviato una modifica, è possibile tenere traccia dello stato di aggiornamento: **sincronizzazione**, **esito positivo** o **errore**. Al termine della sincronizzazione, viene visualizzato il nuovo valore della proprietà nella colonna delle **proprietà restituita** . Se si passa ad altre pagine prima del completamento della sincronizzazione, lo strumento continua a notificare il completamento dell'aggiornamento. È anche possibile usare il centro notifiche dello strumento per visualizzare la cronologia delle notifiche.

#### <a name="commands"></a>Comandi:

Per inviare un comando a un dispositivo, passare alla scheda **comandi** :

1. Nell'elenco dei comandi espandere il comando che si desidera attivare.
1. Immettere i valori necessari per il comando.
1. Visualizzare in anteprima il payload da inviare al dispositivo.
1. Inviare il comando.

#### <a name="telemetry"></a>Telemetria

Per visualizzare i dati di telemetria per l'interfaccia selezionata, passare alla relativa scheda di **telemetria** .

#### <a name="known-issues"></a>Problemi noti

Per un elenco delle funzionalità di Internet delle cose supportate dalla versione più recente dello strumento, vedere [elenco delle funzionalità](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come installare e usare Azure Internet Explorer Explorer per interagire con i dispositivi Plug and Play. Un passaggio successivo suggerito consiste nell'apprendere come [installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md).
