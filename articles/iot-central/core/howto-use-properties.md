---
title: Usare le proprietà in una soluzione IoT Central di Azure
description: Informazioni su come usare le proprietà di sola lettura e scrivibili in una soluzione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119085"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Usare le proprietà in una soluzione IoT Central di Azure

Questa guida illustra come gli sviluppatori di dispositivi possono usare le proprietà del dispositivo definite in un modello di dispositivo nell'applicazione IoT Central di Azure.

Le proprietà rappresentano valori temporizzati. Ad esempio, un dispositivo potrebbe usare una proprietà per segnalare la temperatura di destinazione che sta tentando di raggiungere. Per impostazione predefinita, le proprietà del dispositivo sono di sola lettura in IoT Central. Le proprietà scrivibili consentono di sincronizzare lo stato tra il dispositivo e l'applicazione IoT Central di Azure.

È anche possibile definire le proprietà del cloud in un'applicazione IoT Central di Azure. I valori delle proprietà cloud non vengono mai scambiati con un dispositivo e non rientrano nell'ambito di questo articolo.

## <a name="define-your-properties"></a>Definire le proprietà

Le proprietà sono campi dati che rappresentano lo stato del dispositivo. Usare le proprietà per rappresentare lo stato durevole del dispositivo, ad esempio lo stato on/off di un dispositivo. Le proprietà possono anche rappresentare le proprietà di base dei dispositivi, ad esempio la versione software del dispositivo. Si dichiarano le proprietà in sola lettura o in scrittura.

Lo screenshot seguente mostra una definizione di proprietà in un'applicazione IoT Central di Azure.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Screenshot che mostra una definizione di proprietà in un'applicazione IoT Central di Azure.":::

Nella tabella seguente vengono illustrate le impostazioni di configurazione per una funzionalità della proprietà.

| Campo           | Descrizione                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome visualizzato    | Nome visualizzato per il valore della proprietà usato nei dashboard e nei moduli.                                                                                                                                                              |
| Nome            | Nome della proprietà. Azure IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere il proprio valore, se necessario. Questo campo deve essere alfanumerico.  Il codice del dispositivo usa questo valore **nome** .           |
| Tipo di funzionalità | Proprietà.                                                                                                                                                                                                                          |
| Tipo semantico   | Tipo semantico della proprietà, ad esempio temperatura, stato o evento. La scelta del tipo semantico determina quali tra i campi seguenti sono disponibili.                                                                       |
| SCHEMA          | Tipo di dati della proprietà, ad esempio double, stringa o vettore. Le scelte disponibili sono determinate dal tipo semantico. Lo schema non è disponibile per i tipi semantici stato ed evento.                                               |
| Scrivibile       | Se la proprietà non è scrivibile, il dispositivo può segnalare i valori delle proprietà in IoT Central di Azure. Se la proprietà è scrivibile, il dispositivo può segnalare i valori delle proprietà in IoT Central di Azure. Azure IoT Central può quindi inviare gli aggiornamenti delle proprietà al dispositivo. |
| Gravità        | Disponibile solo per il tipo semantico evento. I livelli di gravità sono **Errore**, **Informazioni** o **Avviso**.                                                                                                                         |
| Valori di stato    | Disponibile solo per il tipo semantico stato. Definisce i valori di stato possibili, ognuno dei quali ha un nome visualizzato, un nome, un tipo di enumerazione e un valore.                                                                                   |
| Unità            | Unità per il valore della proprietà, ad esempio **mph**, **%** o **&deg; C**.                                                                                                                                                              |
| Unità di visualizzazione    | Unità di visualizzazione da usare nei dashboard e nei moduli.                                                                                                                                                                                    |
| Comment         | Eventuali commenti sulla funzionalità della proprietà.                                                                                                                                                                                        |
| Descrizione     | Descrizione della funzionalità della proprietà.                                                                                                                                                                                          |

Le proprietà possono essere definite anche in un'interfaccia in un modello di dispositivo, come illustrato di seguito:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Questo esempio mostra due proprietà. Queste proprietà sono correlate alla definizione di proprietà nell'interfaccia utente:

* `@type` Specifica il tipo di funzionalità: `Property` . Nell'esempio precedente viene inoltre illustrato il tipo semantico `Temperature` per entrambe le proprietà.
* `name` per la proprietà.
* `schema` Specifica il tipo di dati per la proprietà. Questo valore può essere un tipo primitivo, ad esempio Double, Integer, Boolean o String. Sono supportati anche tipi di oggetto complessi e mappe.
* `writable` Per impostazione predefinita, le proprietà sono di sola lettura. È possibile contrassegnare una proprietà come scrivibile utilizzando questo campo.

I campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

Quando si crea una proprietà, è possibile specificare tipi di schema complessi, ad esempio **Object** e **enum**.

![Screenshot che illustra come aggiungere una funzionalità.](./media/howto-use-properties/property.png)

Quando si seleziona lo **schema** complesso, ad esempio **oggetto**, è necessario definire anche l'oggetto.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Screenshot che illustra come definire un oggetto":::

Nel codice seguente viene illustrata la definizione di un tipo di proprietà dell'oggetto. Questo oggetto dispone di due campi con tipi stringa e Integer.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementare proprietà di sola lettura

Per impostazione predefinita, le proprietà sono di sola lettura. Le proprietà di sola lettura consentono l'aggiornamento del valore della proprietà del report del dispositivo all'applicazione IoT Central di Azure. L'applicazione IoT Central di Azure non può impostare il valore di una proprietà di sola lettura.

Azure IoT Central USA dispositivi gemelli per sincronizzare i valori delle proprietà tra il dispositivo e l'applicazione Azure IoT Central. I valori delle proprietà del dispositivo usano le proprietà segnalate del dispositivo gemello. Per altre informazioni, vedere [dispositivi gemelli](../../iot-hub/tutorial-device-twins.md).

Il frammento di codice seguente di un modello di dispositivo Mostra la definizione di un tipo di proprietà di sola lettura:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Gli aggiornamenti delle proprietà vengono inviati da un dispositivo come payload JSON. Per ulteriori informazioni, vedere [payload](./concepts-telemetry-properties-commands.md).

È possibile usare l'SDK per dispositivi Azure per inviare un aggiornamento della proprietà all'applicazione IoT Central di Azure.

Le proprietà dei dispositivi gemelli possono essere inviate all'applicazione IoT Central di Azure usando la funzione seguente:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Questo articolo usa Node.js per semplicità. Per altri esempi di linguaggio, vedere l'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](tutorial-connect-device.md) .

La vista seguente nell'applicazione IoT Central di Azure Mostra le proprietà che è possibile visualizzare. La visualizzazione rende automaticamente la proprietà del **modello del dispositivo** una _proprietà di sola lettura del dispositivo_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Screenshot che mostra la visualizzazione di una proprietà di sola lettura":::

## <a name="implement-writable-properties"></a>Implementare proprietà scrivibili

Le proprietà scrivibili vengono impostate da un operatore nell'applicazione IoT Central di Azure in un modulo. Azure IoT Central Invia la proprietà al dispositivo. Azure IoT Central prevede un riconoscimento dal dispositivo.

Il frammento di codice seguente da un modello di dispositivo Mostra la definizione di un tipo di proprietà scrivibile:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Per definire e gestire le proprietà scrivibili a cui risponde il dispositivo, è possibile usare il codice seguente:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Il messaggio di risposta deve includere `ac` i `av` campi e. Il campo `ad` è facoltativo. Per esempi, vedere i frammenti di codice seguenti:

* `ac` è un campo numerico che utilizza i valori riportati nella tabella seguente.
* `av` numero di versione inviato al dispositivo.
* `ad` Descrizione della stringa di opzioni.

| Valore | Etichetta | Descrizione |
| ----- | ----- | ----------- |
| `'ac': 200` | Completi | L'operazione di modifica della proprietà è stata completata correttamente. |
| `'ac': 202` o `'ac': 201` | In sospeso | L'operazione di modifica della proprietà è in sospeso o in corso. |
| `'ac': 4xx` | Errore | La modifica della proprietà richiesta non è valida o si è verificato un errore. |
| `'ac': 5xx` | Errore | Si è verificato un errore imprevisto nel dispositivo durante l'elaborazione della modifica richiesta. |

Per altre informazioni sui dispositivi gemelli, vedere [configurare i dispositivi da un servizio back-end](../../iot-hub/tutorial-device-twins.md).

Quando l'operatore imposta una proprietà scrivibile nell'applicazione IoT Central di Azure, l'applicazione usa una proprietà desiderata del dispositivo gemello per inviare il valore al dispositivo. Il dispositivo risponde quindi usando una proprietà segnalata del dispositivo gemello. Quando Azure IoT Central riceve il valore della proprietà segnalata, aggiorna la visualizzazione delle proprietà con lo stato **accettato**.

Nella visualizzazione seguente sono illustrate le proprietà scrivibili. Quando si immette il valore e si seleziona **Salva**, lo stato iniziale è **in sospeso**. Quando il dispositivo accetta la modifica, lo stato diventa **accettato**.

![Screenshot che mostra lo stato in sospeso.](./media/howto-use-properties/status-pending.png)

![Screenshot che mostra la proprietà accettata.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare le proprietà nell'applicazione IoT Central di Azure, vedere:

* [Payloads](concepts-telemetry-properties-commands.md)
* [Creare e connettere un'applicazione client all'applicazione Azure IoT Central](tutorial-connect-device.md)