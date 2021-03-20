---
title: Aggiornare il firmware DK di Azure Kinect
description: Informazioni su come aggiornare il firmware del dispositivo Kinect DK di Azure usando lo strumento firmware di Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, aggiornamento, ripristino
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356227"
---
# <a name="update-azure-kinect-dk-firmware"></a>Aggiornare il firmware DK di Azure Kinect

Questo documento fornisce indicazioni su come aggiornare il firmware del dispositivo in Azure Kinect DK.

Azure Kinect DK non aggiorna automaticamente il firmware. È possibile usare [lo strumento firmware Kinect di Azure](azure-kinect-firmware-tool.md) per aggiornare manualmente il firmware alla versione più recente disponibile.

## <a name="prepare-for-firmware-update"></a>Preparare l'aggiornamento del firmware

1. [Scaricare l'SDK](sensor-sdk-download.md).
2. Installare l'SDK.
3. Nel percorso di installazione dell'SDK in (percorso di installazione dell'SDK) \Tools\ è necessario trovare:

    - AzureKinectFirmwareTool.exe
    - Un file firmware. bin nella cartella firmware, ad esempio *AzureKinectDK_Fw_1.5.926614. bin*.

4. Connettere il dispositivo al PC host e anche accenderlo.

> [!IMPORTANT]
> Quando si attiva l'aggiornamento del firmware, è opportuno lasciare la connessione USB e l'alimentatore. La rimozione di una connessione durante l'aggiornamento può comportare lo stato danneggiato del firmware.

## <a name="update-device-firmware"></a>Aggiornare il firmware del dispositivo

1. Aprire un prompt dei comandi nella cartella \Tools\ (percorso di installazione dell'SDK).
2. Aggiornare il firmware usando lo strumento firmware Kinect di Azure

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Esempio:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Attendere il completamento dell'aggiornamento del firmware. Possono essere necessari alcuni minuti a seconda delle dimensioni dell'immagine.

### <a name="verify-device-firmware-version"></a>Verificare la versione del firmware del dispositivo

1. Verificare che il firmware sia aggiornato.

    `AzureKinectFirmwareTool.exe -q`

2. Vedere l'esempio seguente.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Se viene visualizzato l'output precedente, il firmware viene aggiornato.

4. Dopo l'aggiornamento del firmware, è possibile eseguire il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) per verificare che tutti i sensori funzionino come previsto.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Gli aggiornamenti del firmware possono avere esito negativo per diversi motivi. Quando un aggiornamento del firmware non riesce, provare a eseguire i passaggi di mitigazione seguenti:

1. Provare a eseguire il comando di aggiornamento del firmware una seconda volta.

2. Verificare che il dispositivo sia ancora connesso eseguendo una query per la versione del firmware.        AzureKinectFirmareTool.exe

3. Se tutte le altre operazioni hanno esito negativo, seguire la procedura di [ripristino](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) per ripristinare il firmware Factory e riprovare.

Per eventuali problemi aggiuntivi, vedere [pagine del supporto tecnico Microsoft](./index.yml)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Strumento firmware Kinect di Azure](azure-kinect-firmware-tool.md)