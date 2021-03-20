---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050448"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi, ad esempio metadati, configurazioni e condizioni. L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Usare i dispositivi gemelli per:

* Archiviare i metadati dei dispositivi dal back-end della soluzione.

* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni (ad esempio, il metodo di connettività usato) dall'app per dispositivi.

* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata (come gli aggiornamenti del firmware e della configurazione) tra un'app per dispositivi e un'app di back-end.

* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

I dispositivi gemelli sono progettati per la sincronizzazione e per l'esecuzione di query sulle configurazioni e le condizioni dei dispositivi. Altre informazioni su quando usare i dispositivi gemelli sono reperibili in [Informazioni sui dispositivi gemelli](../articles/iot-hub/iot-hub-devguide-device-twins.md).

I dispositivi gemelli vengono archiviati in un hub IoT e contengono gli elementi seguenti:

* **Tag**. Metadati dei dispositivi accessibili solo dal back-end della soluzione.

* **Proprietà desiderate**. Oggetti JSON modificabili dal back-end della soluzione e osservabili dall'app per dispositivi.

* **Proprietà segnalate**. Oggetti JSON modificabili dall'app per dispositivi e leggibili dal back-end della soluzione.

I tag e le proprietà non possono contenere matrici, ma gli oggetti possono essere annidati.

Nella figura seguente viene illustrata l'organizzazione di un dispositivo gemello:

![Immagine che illustra le funzionalità del dispositivo gemello](./media/iot-hub-selector-twin-get-started/twin.png)

Il back-end della soluzione può anche eseguire query sui dispositivi gemelli in base a tutti i dati sopra indicati.
Per altre informazioni sui dispositivi gemelli, vedere [Informazioni sui dispositivi gemelli](../articles/iot-hub/iot-hub-devguide-device-twins.md). Per altre informazioni sulle query, vedere [Linguaggio di query di hub IoT](../articles/iot-hub/iot-hub-devguide-query-language.md).


Questa esercitazione illustra come:

* Creare un'app back-end che aggiunge tag a un dispositivo gemello e un'app per dispositivo simulato che segnala il proprio canale di connettività come proprietà segnalata nel dispositivo gemello.

* Eseguire query sui dispositivi dall'app back-end con filtri sui tag e sulle proprietà creati in precedenza.
