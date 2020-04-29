---
title: Usare l'estensione Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play anteprima | Microsoft Docs
description: Installare l'estensione Azure per l'interfaccia della riga di comando di Azure e usarla per interagire con i dispositivi Plug and Play connessi all'hub Internet.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770450"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installare e usare l'estensione Azure per l'interfaccia della riga di comando di Azure

[L'interfaccia della](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) riga di comando di Azure è uno strumento da riga di comando multipiattaforma open source per la gestione delle risorse di Azure, ad esempio l'hub Internet. L'interfaccia della riga di comando di Azure è disponibile in Windows, Linux e MacOS. L'interfaccia della riga di comando di Azure è preinstallata anche nella [Azure cloud Shell](https://shell.azure.com). L'interfaccia della riga di comando di Azure consente di gestire le risorse dell'hub Azure, le istanze del servizio Device provisioning e gli hub collegati senza installare alcuna estensione.

L'estensione Azure per l'interfaccia della riga di comando di Azure è uno strumento da riga di comando per l'interazione con e il test dei dispositivi Plug and Play anteprima. È possibile usare l'estensione per:

- Connettersi a un dispositivo.
- Visualizzare i dati di telemetria inviati dal dispositivo.
- Usare le proprietà del dispositivo.
- Chiamare i comandi del dispositivo.

Questo articolo illustra come:

- Installare e configurare l'estensione Azure per l'interfaccia della riga di comando di Azure.
- Usare l'estensione per interagire con e testare i dispositivi.
- Usare l'estensione per gestire le interfacce nel repository del modello.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installare l'estensione Azure per l'interfaccia della riga di comando di Azure

### <a name="step-1---install-the-azure-cli"></a>Passaggio 1: installare l'interfaccia della riga di comando di Azure

Seguire le [istruzioni di installazione](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure nell'ambiente in uso. Per usare tutti i comandi seguenti, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.73 o versione successiva. Usare il comando `az -–version` per verificare.

### <a name="step-2---install-iot-extension"></a>Passaggio 2: installare l'estensione Internet

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra diverse modalità per installare l'estensione. Il modo più semplice è quello di eseguire `az extension add --name azure-iot`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-iot` per visualizzare informazioni dettagliate sull'estensione IoT. Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Usare l'estensione Azure per l'interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti

Per accedere alla sottoscrizione di Azure, eseguire il comando seguente:

```azurecli
az login
```

> [!NOTE]
> Se si usa Azure cloud Shell, l'accesso viene eseguito automaticamente e non è necessario eseguire il comando precedente.

Per usare l'estensione Azure per l'interfaccia della riga di comando di Azure, è necessario:

- Un hub IoT di Azure. Sono disponibili diversi modi per aggiungere un hub Internet alla sottoscrizione di Azure, ad esempio [creare un hub Internet delle cose usando l'interfaccia della](../iot-hub/iot-hub-create-using-cli.md)riga di comando di Azure. È necessaria la stringa di connessione dell'hub Internet per l'esecuzione dei comandi dell'estensione Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un dispositivo registrato nell'hub Internet. È possibile usare il comando dell'interfaccia della riga di comando di Azure seguente per registrare un dispositivo `{YourIoTHubName}` , `{YourDeviceID}` assicurarsi di sostituire i segnaposto e con i valori:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Per alcuni comandi è necessaria la stringa di connessione per un repository del modello aziendale. Un repository di modelli per l'azienda viene creato quando si esegue [la prima onboarding nel portale di Azure Certified for](howto-onboard-portal.md)Internet. Una terza parte può condividere la stringa di connessione del repository del modello con l'utente per consentire l'accesso alle interfacce e ai modelli.

### <a name="interact-with-a-device"></a>Interagire con un dispositivo

È possibile usare l'estensione per visualizzare e interagire con gli Plug and Play dispositivi connessi a un hub Internet. L'estensione funziona con il dispositivo gemello digitale che rappresenta il dispositivo Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Elencare i dispositivi e le interfacce

Elencare tutti i dispositivi in un hub Internet:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Elencare tutte le interfacce registrate da un Plug and Play dispositivo:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Proprietà

Elencare tutte le proprietà e i valori delle proprietà per un'interfaccia in un dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Impostare il valore di una proprietà di lettura/scrittura:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Un esempio di file di payload per impostare la proprietà **Name** nell'interfaccia del **sensore** di un dispositivo su **Contoso** è simile al seguente:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Comandi:

Elencare tutti i comandi per un'interfaccia in un dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Senza il `--repo-login` parametro, questo comando usa il repository del modello pubblico.

Richiamare un comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventi gemelli digitali

Esegui il monitoraggio di tutti gli Plug and Play eventi gemelli digitali da un dispositivo e un'interfaccia specifici che passano al gruppo di consumer dell'hub eventi **$default** :

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitora tutti gli eventi Plug and Play i dispositivi gemelli digitali da un dispositivo e un'interfaccia specifici che passano da un gruppo di consumer specifico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gestire le interfacce in un repository di modelli

I comandi seguenti usano il repository del modello Plug and Play. Per usare un repository del modello aziendale, aggiungere `--login` l'argomento con la stringa di connessione del repository del modello.

Elencare le interfacce nel repository di modelli di Plug and Play di Internet pubblico:

```azurecli
az iot pnp interface list
```

Mostra un'interfaccia nel repository di modelli Plug and Play di Internet pubblico:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Creare un'interfaccia nel repository del modello aziendale Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un'interfaccia nel repository del modello pubblico.

Aggiornare un'interfaccia nel repository del modello aziendale Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un'interfaccia nel repository del modello pubblico.

Pubblicare un'interfaccia dal repository del modello aziendale Plug and Play al repository del modello pubblico. Questa operazione rende l'interfaccia non modificabile:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare le interfacce nel repository del modello pubblico.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gestire i modelli di funzionalità del dispositivo in un repository di modelli

I comandi seguenti usano il repository del modello Plug and Play. Per usare un repository del modello aziendale, aggiungere `--login` l'argomento con la stringa di connessione del repository del modello.

Elencare i modelli di funzionalità del dispositivo nel repository del modello pubblico Plug and Play:

```azurecli
az iot pnp capability-model list
```

Mostra un modello di funzionalità del dispositivo nel repository del modello pubblico Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Creare un modello di funzionalità del dispositivo in un archivio del modello aziendale Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile creare direttamente un modello nel repository del modello pubblico.

Aggiornare un modello di funzionalità del dispositivo nel repository del modello aziendale Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Non è possibile aggiornare direttamente un modello nel repository del modello pubblico.

Pubblicare un modello di funzionalità del dispositivo dal repository del modello aziendale Plug and Play al repository del modello pubblico. Questa operazione rende il modello non modificabile:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Solo i partner Microsoft possono pubblicare modelli nel repository del modello pubblico.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come installare e usare l'estensione Azure per l'interfaccia della riga di comando di Azure per interagire con i dispositivi Plug and Play. Un passaggio successivo suggerito consiste nell'apprendere come [gestire i modelli](./howto-manage-models.md).
