---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 30b8facfef6d90a444bd61d0ce041ed7dfef324e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82132249"
---
## <a name="automatic-device-management"></a>Gestione automatica dei dispositivi
La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. La gestione automatica dei dispositivi consente di gestire un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e permette all'hub IoT di aggiornare i dispositivi quando rientrano nell'ambito.  Si compone delle [configurazioni automatiche del dispositivo](../articles/iot-hub/iot-hub-auto-device-config.md) e delle [distribuzioni automatiche di IoT Edge](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge consente la distribuzione basata su cloud di codice specifico per soluzioni o servizi di Azure nei dispositivi locali. I dispositivi IoT Edge possono aggregare dati da altri dispositivi per eseguire operazioni di elaborazione e analisi prima che i dati vengano inviati al cloud. Per altre informazioni, vedere [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente di IoT Edge
La parte del runtime di IoT Edge responsabile della distribuzione e del monitoraggio dei moduli.

## <a name="iot-edge-device"></a>Dispositivo IoT Edge
Nei dispositivi IoT Edge è installato il runtime di Iot Edge e nei dettagli del dispositivo quest'ultimo è contrassegnato come **Dispositivo IoT Edge**. Altre informazioni sono disponibili in [Distribuire Azure IoT Edge in un dispositivo simulato in Linux: anteprima](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Distribuzione automatica di IoT Edge
Una distribuzione automatica di IoT Edge configura un set di destinazione di dispositivi IoT Edge per eseguire un set di moduli IoT Edge. Ogni distribuzione garantisce continuamente che tutti i dispositivi conformi alla condizione di destinazione eseguano il set di moduli specificato, anche quando vengono creati nuovi dispositivi o vengono modificati in modo che corrispondano alla condizione di destinazione. Ogni dispositivo IoT Edge riceve solo la distribuzione con priorità più alta tra quelle di cui soddisfa la condizione di destinazione. Altre informazioni sulla [distribuzione automatica di IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto della distribuzione di IoT Edge
Documento JSON contenente le informazioni da copiare in uno o più moduli gemello dei dispositivi IoT Edge per distribuire un set di moduli, route e le proprietà desiderate del modulo associato.

## <a name="iot-edge-gateway-device"></a>Dispositivo gateway IoT Edge
Un dispositivo IoT Edge con dispositivo downstream, che può essere un dispositivo IoT Edge o non IoT Edge.

## <a name="iot-edge-hub"></a>Hub di IoT Edge
La parte del runtime di IoT Edge responsabile delle comunicazioni da modulo a modulo e delle comunicazioni upstream (verso l'hub IoT) e downstream (dall'hub IoT). 

## <a name="iot-edge-leaf-device"></a>Dispositivo foglia IoT Edge
Un dispositivo IoT Edge senza dispositivo downstream. 

## <a name="iot-edge-module"></a>Modulo IoT Edge
Un modulo IoT Edge è un contenitore Docker che è possibile distribuire su dispositivi IoT Edge. Esegue un'attività specifica, come inserire un messaggio da un dispositivo, trasformare un messaggio o inviare un messaggio a un hub IoT. Comunica inoltre con altri moduli e invia dati al runtime di IoT Edge. [Informazioni sui requisiti e gli strumenti per lo sviluppo di moduli IOT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identità del modulo IoT Edge
Un record nel registro delle identità del modulo dell'hub IoT con informazioni dettagliate sull'esistenza e le credenziali di sicurezza che devono essere usate da un modulo per eseguire l'autenticazione con un hub Edge o l'hub IoT.

## <a name="iot-edge-module-image"></a>Immagine del modulo IoT Edge
L'immagine Docker usata dal runtime di IoT Edge per creare istanze del modulo.

## <a name="iot-edge-module-twin"></a>Modulo IoT Edge gemello
Un documento Json contenuto nell'hub IoT in cui vengono archiviate le informazioni di stato relative a un'istanza del modulo.

## <a name="iot-edge-priority"></a>Priorità di IoT Edge
Quando due distribuzioni di IoT Edge hanno come destinazione lo stesso dispositivo, viene applicata la distribuzione con priorità più elevata. Se due distribuzioni hanno la stessa priorità, viene applicata la distribuzione con la data di creazione successiva. Per altre informazioni, vedere [Priorità](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Runtime di IoT Edge
Il runtime di IoT Edge include tutti i componenti distribuiti da Microsoft per essere installati in un dispositivo IoT Edge. Include l'agente Edge, l'hub di Edge e il daemon di sicurezza di IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge imposta i moduli in un unico dispositivo
Operazione che copia il contenuto di un file manifesto di IoT Edge in un modulo gemello del dispositivo. L'API sottostante è del tipo generico "applica configurazione", ovvero si limita ad accettare un file manifesto di IoT Edge come input.

## <a name="iot-edge-target-condition"></a>Condizione di destinazione di IoT Edge
In una distribuzione IoT Edge la condizione di destinazione è qualsiasi condizione booleana nei tag dei dispositivi gemelli per selezionare i dispositivi di destinazione della distribuzione, ad esempio **tag. Environment = prod**. La condizione di destinazione viene costantemente valutata in modo da includere tutti i nuovi dispositivi che soddisfano i requisiti o rimuovere i dispositivi che non sono più disponibili. Per altre informazioni, vedere [Condizione di destinazione](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)