---
title: Integrazione di partner di immagini
description: Questo articolo descrive l'integrazione del partner di immagini.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: 1bc723892e29ccc24a7612cfbe50d4b274cdacdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183795"
---
# <a name="imagery-partner-integration"></a>Integrazione di partner di immagini

Questo articolo descrive come usare il componente Azure FarmBeats Translator per inviare dati di immagini a FarmBeats. I dati di immagini agricoli possono essere generati da varie origini, ad esempio fotocamere multispettrali, satelliti e droni. I partner di immagini agricole possono integrarsi con FarmBeats per fornire ai clienti mappe personalizzate generate per le proprie farm.

I dati, una volta disponibili, possono essere visualizzati tramite il tasto di scelta rapida FarmBeats e possono essere usati potenzialmente per la fusione dei dati e la creazione di modelli di Machine Learning/intelligenza artificiale (ML/AI) dalle aziende agricole o dagli integratori di sistemi dei clienti.

FarmBeats offre la possibilità di:

- Definire i tipi di immagine, l'origine e il formato di file personalizzati usando le API/ExtendedType.
- Inserire i dati delle immagini da varie origini tramite le API/scene e/SceneFile.

Le informazioni seguenti sono incentrate sul recupero di qualsiasi forma di immagine nel sistema FarmBeats.

Quando si seleziona la sezione relativa alle **Immagini drone** , viene visualizzata una finestra popup che mostra un'immagine ad alta risoluzione della orthomosaic drone. È possibile accedere al software del partner, che consente di pianificare i voli drone e ottenere dati non elaborati. Si continuerà a usare il software del partner per la pianificazione dei percorsi e il orthomosaic dell'immagine.

I partner drone devono consentire ai clienti di collegare il proprio account cliente alla propria istanza di FarmBeats in Azure.

Per collegare FarmBeats, è necessario usare le credenziali seguenti nel software del partner drone:

- Endpoint API
- ID tenant
- ID client
- Segreto client

## <a name="api-development"></a>Sviluppo di API

Le API contengono la documentazione tecnica di Swagger. Per informazioni sulle API e sulle richieste o risposte corrispondenti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Authentication

FarmBeats USA Microsoft Azure [Active Directory](../../app-service/overview-authentication-authorization.md) (Azure ad).  Il Servizio app di Azure offre supporto di autorizzazione e autenticazione integrato. 

Per altre informazioni su Azure AD, vedere [Azure Active Directory](../../app-service/overview-authentication-authorization.md).   

FarmBeats Datahub usa l'autenticazione con token di connessione, che richiede le credenziali seguenti:

- ID client
- Segreto client
- ID tenant

Usando le credenziali precedenti, il chiamante può richiedere un token di accesso, che deve essere inviato nelle richieste API successive, nella sezione header, come indicato di seguito:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

L'esempio di codice Python seguente recupera il token di accesso. È quindi possibile usare il token per le chiamate API successive a FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

## <a name="http-request-headers"></a>Intestazioni di richiesta HTTP

Di seguito sono riportate le intestazioni di richiesta di uso più frequente che devono essere specificate quando si effettua una chiamata API a FarmBeats Datahub.

**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type  | Formato della richiesta (Content-Type: application/<format>). Il formato per l’API FarmBeats Datahub è JSON. Content-Type: application/json
Autorizzazione | Specifica il token di accesso necessario per effettuare una chiamata API. Authorization: Bearer <Access-Token>
Accept  | Formato della risposta. Il formato per l’API FarmBeats Datahub è JSON. Accept: application/json


## <a name="api-requests"></a>Richieste API

Per eseguire una richiesta API REST, combinare:

- Metodo HTTP (GET, POST e PUT).
- URL del servizio API.
- URI della risorsa (per eseguire query, inviare dati, aggiornare o eliminare).
- Una o più intestazioni della richiesta HTTP.

Facoltativamente, è possibile includere parametri di query sulle chiamate GET da filtrare, di cui limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consente di ottenere l'elenco dei dispositivi:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

La maggior parte delle chiamate GET, POST e PUT richiede un corpo della richiesta in formato JSON.

La seguente richiesta di esempio serve a creare un dispositivo. Questo esempio include un JSON di input con il corpo della richiesta.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che offre una rappresentazione testuale semplice di strutture di dati arbitrarie. Per altre informazioni, vedere l' [organizzazione JSON](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Inserire immagini in FarmBeats

Quando il partner dispone di credenziali per connettersi a FarmBeats datahub, il partner esegue i passaggi seguenti nel componente Translator.

1.  Creare un nuovo tipo esteso per i campi seguenti, in base al tipo di immagine da caricare:

    - **Origine della scena**: ad esempio, drone_partner_name
    - **Tipo di scena**: ad esempio, drone
    - **Tipo di file di scena**: ad esempio, indice clorofilla
    - **Tipo di contenuto del file di scena**: ad esempio, image/TIFF

2.  Chiamare l'API/Farms per ottenere l'elenco di farm dal sistema FarmBeats di Azure.
3.  Fornire al cliente la possibilità di scegliere una singola farm dall'elenco di farm.

    Il sistema partner deve mostrare la farm all'interno del software partner per eseguire la pianificazione del percorso e la raccolta di immagini e voli drone.

4.  Chiamare l'API/scene e fornire i dettagli necessari per creare una nuova scena con un ID scena univoco.
5.  Ricevere un URL di firma di accesso condiviso BLOB per caricare le immagini necessarie in FarmBeats datahub nel contesto della farm scelta nel sistema FarmBeats.

Ecco un flusso dettagliato sulle chiamate API.

### <a name="step-1-extendedtype"></a>Passaggio 1: ExtendedType

Archiviare l'API/ExtendedType per verificare se il tipo e l'origine file sono disponibili in FarmBeats. A tale scopo, chiamare GET sull'API/ExtendedType.

Di seguito sono riportati i valori definiti dal sistema:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Questo passaggio è una singola installazione. L'ambito di questo nuovo tipo di scena è limitato alla sottoscrizione in cui è installato Azure FarmBeats.

Ad esempio, per aggiungere SceneSource: "SlantRange", è necessario inserire l'ID dell'API/ExtendedType con il payload di input della chiave "SceneSource".

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Il campo verde è la nuova aggiunta ai valori di origine della scena definiti dal sistema.

### <a name="step-2-get-farm-details"></a>Passaggio 2: ottenere i dettagli della farm

Le scene (file con estensione TIFF o CSV) si trovano nel contesto di una farm. Per ottenere i dettagli della farm, è necessario eseguire un'operazione GET sull'API/farm. L'API restituisce l'elenco delle farm disponibili in FarmBeats. È possibile selezionare la farm per cui si desidera inserire i dati.

OTTENERE la risposta/Farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Passaggio 3: creare un ID scena (POST-chiamata)

Creare una nuova scena (file con estensione TIFF o CSV) con le informazioni fornite, che forniscono la data, la sequenza e l'ID della farm a cui è associata la scena. I metadati associati alla scena possono essere definiti in proprietà, che include la durata e il tipo di misura.

Con la creazione di una nuova scena viene creato un nuovo ID di scena, associato alla farm. Dopo aver creato l'ID della scena, l'utente può usare lo stesso per creare un nuovo file (con estensione TIFF o CSV) e archiviare il contenuto del file.

Payload di input di esempio per la chiamata POST sull'API/scene:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Risposta API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Creare un file di scena**

L'ID scena restituito nel passaggio 3 è l'input per il file della scena. Il file della scena restituisce un token URL di firma di accesso condiviso, che è valido per 24 ore.

Se l'utente richiede una modalità a livello di codice per il caricamento di un flusso di immagini, è possibile usare l'SDK dell'archiviazione BLOB per definire un metodo usando l'ID, la posizione e l'URL del file della scena.

Payload di input di esempio per la chiamata POST sull'API/SceneFile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Risposta API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

La chiamata POST all'API/SceneFile restituisce un URL di caricamento della firma di accesso condiviso, che può essere usato per caricare il file CSV o TIFF usando la libreria o il client di archiviazione BLOB di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dettagli di integrazione basata sull'API REST, vedere [API REST](rest-api-in-azure-farmbeats.md).