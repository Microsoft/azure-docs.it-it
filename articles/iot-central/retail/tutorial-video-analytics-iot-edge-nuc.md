---
title: Esercitazione - Creare un'istanza di IoT Edge per l'analisi video in Azure IoT Central (Intel NUC)
description: Questa esercitazione mostra come creare un'istanza di IoT Edge per l'analisi video da usare con il modello di applicazione Analisi video - rilevamento movimento e oggetti.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831927"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Esercitazione: Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)

Azure IoT Edge è un servizio completamente gestito che offre funzionalità di Cloud Intelligence a livello locale tramite la distribuzione e l'esecuzione di:

* Logica personalizzata
* Servizi di Azure
* Intelligenza artificiale

In IoT Edge questi servizi vengono eseguiti direttamente su dispositivi IoT multipiattaforma e ciò consente di eseguire una soluzione IoT in modo sicuro e su larga scala nel cloud o offline.

Questa esercitazione mostra come installare e configurare il runtime IoT Edge in un dispositivo Intel NUC.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Aggiornare e configurare IoT Edge
> * Configurare il gateway IoT Edge
> * Connettere una telecamera locale compatibile con ONVIF al dispositivo Intel NUC

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare, completare l'esercitazione precedente [Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) o [Creare un'applicazione Analisi video - rilevamento movimento e oggetti in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).
* Un dispositivo, ad esempio Intel NUC, che esegue Linux, in grado di eseguire contenitori Docker e con una potenza di elaborazione sufficiente per l'esecuzione di analisi video.
* Il [runtime IoT Edge installato](../../iot-edge/how-to-install-iot-edge.md) e in esecuzione nel dispositivo.
* Essere in grado di connettersi al dispositivo IoT Edge dal computer Windows. È necessario il [client SSH PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) o un'utilità equivalente.
* È anche necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una gratuitamente nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Configurare il dispositivo IoT Edge

Se il runtime IoT Edge non è installato nel computer Intel NUC, vedere le istruzioni in [installare il runtime Azure IoT Edge in sistemi Linux basati su Debian](../../iot-edge/how-to-install-iot-edge.md).

Per aggiornare il runtime IoT Edge:

1. Usare l'utilità PuTTY per connettersi al dispositivo IoT Edge.

1. Eseguire i comandi seguenti per aggiornare e controllare la versione del runtime IoT Edge. Al momento della redazione di questo documento, la versione è 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Usare i comandi seguenti per creare le cartelle usate dalla distribuzione con le autorizzazioni necessarie:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Per aggiungere il file di configurazione *state.json* alla cartella */data/storage* nel dispositivo IoT Edge:

1. Usare un editor di testo per aprire il file *state.json* nella cartella *lva-configuration* nel computer locale.

1. Aggiornare i segnaposto `system` e `iotCentral > properties` con valori stringa che descrivono il dispositivo gateway. Sarà possibile visualizzare questi valori in seguito nel dashboard dell'applicazione IoT Central.

1. Aggiornare i segnaposto `iotCentral > appKeys` con i valori annotati nel file *scratchpad.txt* nell'esercitazione precedente. Salvare le modifiche.

1. Usare l'utilità `scp` PuTTY in un prompt dei comandi per copiare il file *state.json* appena modificato nella cartella */data/storage* nel dispositivo IoT Edge. Questo esempio usa `192.168.0.144` come indirizzo IP. Sostituire il valore con l'indirizzo IP del dispositivo IoT Edge:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Configurare IoT Edge per la registrazione e la connessione all'applicazione IoT Central:

1. Usare l'utilità PuTTY per connettersi al dispositivo IoT Edge.

1. Usare un editor di testo, ad esempio `nano`, per aprire il file config.yaml di IoT Edge.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > I file YAML non consentono di usare le tabulazioni per il rientro, ma è possibile usare due spazi. Gli elementi di primo livello non possono contenere spazi vuoti iniziali.

1. Scorrere verso il basso fino a visualizzare `# Manual provisioning configuration`. Impostare come commento le tre righe successive, come illustrato nel frammento di codice seguente:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Scorrere verso il basso fino a visualizzare `# DPS symmetric key provisioning configuration`. Rimuovere il commento dalle tre righe successive, come illustrato nel frammento di codice seguente:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Sostituire `{scope_id}` con il valore di **Ambito ID** annotato nel file *scratchpad.txt* nell'esercitazione precedente.

1. Sostituire `{registration_id}` con *gateway-001*, il dispositivo creato nell'esercitazione precedente.

1. Sostituire `{symmetric_key}` con il valore di **Chiave primaria** per il dispositivo **gateway-001** annotato nel file *scratchpad.txt* nell'esercitazione precedente.

1. Eseguire il comando seguente per riavviare il daemon di IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Per verificare lo stato dei moduli di IoT Edge, eseguire il comando seguente:

    ```bash
    iotedge list
    ```

    L'output del comando precedente mostra cinque moduli in esecuzione. È anche possibile visualizzare lo stato dei moduli in esecuzione nell'applicazione IoT Central.

    > [!TIP]
    > Per verificare lo stato, è possibile eseguire di nuovo questo comando. Potrebbe essere necessario attendere l'avvio dell'esecuzione di tutti i moduli.

Se i moduli IoT Edge non vengono avviati correttamente, vedere [Risolvere i problemi del dispositivo IoT Edge](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Raccogliere il flusso RTSP dalla telecamera

Identificare gli URL di flusso RTSP per le telecamere connesse al dispositivo IoT Edge, ad esempio:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Provare a visualizzare il flusso della telecamera nel computer IoT Edge usando un lettore multimediale, ad esempio VLC.

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'applicazione non è più necessaria, è possibile rimuovere tutte le risorse create come indicato di seguito:

1. Nell'applicazione IoT Central passare alla pagina **Applicazione** nella sezione **Amministrazione**. Selezionare **Elimina**.
1. Nel portale di Azure eliminare il gruppo di risorse **lva-rg**.
1. Nel computer locale arrestare il contenitore Docker **amp-viewer**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto sono stati distribuiti il runtime IoT Edge e i moduli di Analisi video live nel dispositivo gateway Intel NUC.

Per gestire le telecamere, seguire l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Monitorare e gestire un'applicazione Analisi video - rilevamento movimento e oggetti](./tutorial-video-analytics-manage.md)