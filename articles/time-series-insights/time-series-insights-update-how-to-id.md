---
title: Procedure consigliate per la scelta di un ID di serie temporali-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle procedure consigliate per la scelta di un ID di serie temporale in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083521"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Procedure consigliate per la scelta di un ID serie temporale

Questo articolo riepiloga l'importanza dell'ID della serie temporale per l'ambiente di Azure Time Series Insights anteprima e le procedure consigliate per sceglierne una.

## <a name="choose-a-time-series-id"></a>Scegliere un ID serie temporale

La selezione di un ID Time Series appropriato è fondamentale. Scegliere un ID serie temporale è come scegliere una chiave di partizione per un database. È obbligatorio quando si crea un ambiente di anteprima Time Series Insights. 

> [!IMPORTANT]
> Gli ID delle serie temporali sono:
> * Una proprietà con *distinzione tra maiuscole* e minuscole: le lettere e i caratteri vengono usati in ricerche, confronti, aggiornamenti e durante il partizionamento.
> * Una proprietà non *modificabile* : una volta creata, non può essere modificata.

> [!TIP]
> Se l'origine evento è un hub Internet delle cose, l'ID della serie temporale sarà probabilmente ***iothub-Connection-Device-ID***.

Di seguito sono riportate le principali procedure consigliate:

* Selezionare una chiave di partizione con molti valori distinti (ad esempio, centinaia o migliaia). In molti casi, potrebbe trattarsi dell'ID del dispositivo, dell'ID del sensore o dell'ID tag in JSON.
* L'ID serie temporale deve essere univoco a livello di nodo foglia del [modello serie temporale](./time-series-insights-update-tsm.md).
* Il limite di caratteri per la stringa del nome di proprietà dell'ID della serie temporale è 128. Per il valore della proprietà dell'ID della serie temporale, il limite di caratteri è 1.024.
* Se manca un valore di proprietà univoco per l'ID della serie temporale, questo viene considerato come un valore null e segue la stessa regola del vincolo di univocità.
* È anche possibile selezionare fino a *tre* proprietà chiave come ID della serie temporale. La combinazione sarà una chiave composta che rappresenta l'ID della serie temporale.  
  > [!NOTE]
  > Le tre proprietà chiave devono essere stringhe.
  > È necessario eseguire una query su questa chiave composta anziché su una proprietà alla volta.

## <a name="select-more-than-one-key-property"></a>Selezionare più di una proprietà chiave

Negli scenari seguenti viene descritto come selezionare più di una proprietà chiave come ID della serie temporale.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Esempio 1: ID Time Series con una chiave univoca

* Sono presenti flotte legacy di asset. Ogni ha una chiave univoca.
* Una flotta viene identificata in modo univoco dalla proprietà **DeviceID**. Per un'altra flotta, la proprietà Unique è **ObjectID**. Nessuna delle due flotte contiene la proprietà univoca dell'altra flotta. In questo esempio si selezionano due chiavi, **DeviceID** e **ObjectID**, come chiavi univoche.
* Accettiamo valori null e la mancanza della presenza di una proprietà nel payload dell'evento viene conteggiata come valore null. Questo è anche il modo appropriato per gestire l'invio di dati a due origini eventi in cui i dati in ogni origine evento hanno un ID di serie temporale univoco.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Esempio 2: ID Time Series con una chiave composta

* È necessario che più proprietà siano univoche nella stessa flotta di asset. 
* Sei un produttore di Smart Build e Distribuisci sensori in ogni chat room. In ogni stanza, in genere si hanno gli stessi valori per **sensorId**. Esempi sono **sensor1**, **SENSOR2**e **Sensor3**.
* La compilazione presenta numeri di piano e spazio sovrapposti tra i siti nella proprietà **flrRm**. Questi numeri hanno valori quali **1a**, **2B**e **3A**.
* Si dispone di una proprietà, **location**, che contiene valori quali **Redmond**, **Barcelona**e **Tokyo**. Per creare l'univocità, è necessario definire le tre proprietà seguenti come chiavi ID della serie temporale: **sensorId**, **flrRm**e **location**.

Evento RAW di esempio:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Nella portale di Azure è quindi possibile immettere la chiave composta come indicato di seguito: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [modellazione dei dati](./time-series-insights-update-tsm.md).

* Pianificare l' [ambiente di Azure Time Series Insights anteprima](./time-series-insights-update-plan.md).