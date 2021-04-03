---
title: Creare un hub IoT di Azure con un modello (.NET) | Documentazione Microsoft
description: Come usare un modello di Azure Resource Manager per creare un hub IoT con un programma C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144495"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Creare un hub IoT usando un modello di Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

È possibile utilizzare Gestione risorse di Azure per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene mostrato come usare un modello di Azure Resource Manager per creare un hub IoT da un programma C#.

> [!NOTE]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse:  [Azure Resource Manager e classica](../azure-resource-manager/management/deployment-models.md).  In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Visual Studio.
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un [account gratuito][lnk-free-trial] in pochi minuti.
* Un [account di archiviazione di Azure][lnk-storage-account] in cui è possibile archiviare i file del modello di Azure Resource Manager.
* [Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparare il progetto di Visual Studio

1. In Visual Studio creare un progetto desktop classico di Windows Visual C# usando il modello di progetto **App console (.NET Framework)**. Denominare il progetto **CreateIoTHub**.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Gestisci pacchetti NuGet**.

3. In Gestione pacchetti NuGet selezionare **Includi versione preliminare** e nella pagina **Sfoglia** cercare **Microsoft.Azure.Management.ResourceManager**. Selezionare il pacchetto, fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare le licenze.

4. In Gestione pacchetti NuGet cercare **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Fare clic su **Installa**, in **Rivedi modifiche** fare clic su **OK**, quindi fare clic su **I Accept** (Accetto) per accettare la licenza.

5. In Program.cs sostituire le istruzioni **using** esistenti con il codice seguente:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. In Program.cs aggiungere le seguenti variabili statiche sostituendo i valori dei segnaposto. Nella parte precedente di questa esercitazione si è preso nota di **ApplicationId**, **SubscriptionId**, **TenantId** e **Password**. **Il nome dell'account di archiviazione di Azure** è il nome dell'account di archiviazione di Azure in cui vengono archiviati i file del modello di Azure Resource Manager. **Nome gruppo di risorse** è il nome del gruppo di risorse che viene usato quando si crea l'hub IoT. Può essere un gruppo di risorse preesistente o nuovo. **Nome distribuzione** è un nome per la distribuzione, ad esempio **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Inviare un modello per creare un hub IoT

Usare un modello JSON e un file di parametri per creare un hub IoT nel gruppo di risorse. È anche possibile usare un modello di Azure Resource Manager per apportare modifiche a un hub IoT esistente.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi su **Aggiungi** e infine su **Nuovo elemento**. Aggiungere un file JSON denominato **template.json** al progetto.

2. Per aggiungere un hub IoT standard per l'area **Stati Uniti orientali**, sostituire il contenuto di **template.json** con la definizione di risorsa seguente. Per un elenco aggiornato delle aree in cui è supportato l'hub IoT, vedere lo [Stato di Azure][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi su **Aggiungi** e infine su **Nuovo elemento**. Aggiungere un file JSON denominato **parameters.json** al progetto.

4. Sostituire il contenuto di **parameters.json** con le informazioni di parametro seguenti che impostano il nome del nuovo hub IoT su **{iniziali utente}mynewiothub**. Il nome dell'hub IoT deve essere globalmente univoco, quindi deve includere il nome o le iniziali dell'utente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. In **Esplora server** connettersi alla sottoscrizione di Azure e nell'account di archiviazione di Azure creare un contenitore denominato **templates**. Nel pannello **Proprietà** impostare le autorizzazioni **Accesso in lettura pubblico** per il contenitore **templates** su **BLOB**.

6. In **Esplora server** fare clic con il pulsante destro del mouse sul contenitore **templates** e quindi fare clic su **Visualizza contenitore BLOB**. Fare clic sul pulsante **Carica BLOB**, selezionare i due file **parameters.json** e **templates.json** e quindi fare clic su **Apri** per caricare i file JSON nel contenitore **templates**. Gli URL dei BLOB contenenti i dati JSON sono:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Aggiungere il metodo seguente a Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Aggiungere il codice seguente al metodo **CreateIoTHub** per inviare i file di modello e di parametri a Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Aggiungere il codice seguente al metodo **CreateIoTHub** che visualizza lo stato e le chiavi del nuovo hub IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Compilare ed eseguire l'applicazione

È ora possibile completare l'applicazione chiamando il metodo **CreateIoTHub** prima di compilarla ed eseguirla.

1. Aggiungere il codice seguente alla fine del metodo **Main** :

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Fare clic su **Compila** e quindi su **Compila soluzione**. Correggere eventuali errori.

3. Fare clic su **Debug** e quindi su **Avvia debug** per eseguire l'applicazione. Potrebbero occorrere alcuni minuti per l'esecuzione della distribuzione.

4. Per verificare che l'applicazione abbia aggiunto il nuovo hub IoT, visitare il [portale di Azure][lnk-azure-portal] e visualizzare l'elenco delle risorse. In alternativa, usare il cmdlet **Get-AzResource** di PowerShell.

> [!NOTE]
> Questa applicazione di esempio aggiunge un hub IoT Standard S1 che viene addebitato. Al termine, è possibile eliminare l'hub Internet delle cose tramite il [portale di Azure][lnk-azure-portal] o usando il cmdlet di PowerShell **Remove-AzResource** .

## <a name="next-steps"></a>Passaggi successivi
Dopo avere distribuito un hub IoT usando un modello di Azure Resource Manager con un programma C#, può essere opportuno ottenere informazioni più dettagliate:

* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
* Per altre informazioni sulle funzionalità di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager][lnk-azure-rm-overview].
* Per la sintassi e le proprietà JSON da usare nei modelli, vedere i [tipi di risorsa Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md