---
title: Creare il runtime di integrazione di Azure in Azure Data FactoryCreate Azure integration runtime in Azure Data Factory
description: Informazioni su come creare il runtime di integrazione di Azure in Azure Data Factory, che viene usato per copiare i dati e inviare le attività di trasformazione.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260761"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Come creare e configurare il runtime di integrazione di Azure
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati in diversi ambienti di rete. Per altre informazioni sul runtime di integrazione, vedere [Integration runtime](concepts-integration-runtime.md) (Runtime di integrazione).

Il runtime di integrazione di Azure fornisce una funzionalità di calcolo completamente gestita per eseguire in modo nativo lo spostamento dei dati e inviare le attività di trasformazione dei dati ai servizi di calcolo, ad esempio HDInsight. È ospitato nell'ambiente Azure e supporta la connessione alle risorse nell'ambiente di rete pubblica con endpoint accessibili pubblicamente.

Questo documento illustra come creare e configurare il runtime di integrazione di Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Runtime di integrazione di Azure predefinito
Per impostazione predefinita, ogni data factory ha un runtime di integrazione di Azure nel back-end che supporta le operazioni negli archivi dati cloud e i servizi di calcolo nella rete pubblica. La località di tale runtime di integrazione di Azure viene risolta automaticamente. Se la proprietà **connectVia** non viene specificata nella definizione del servizio collegato, viene usato il runtime di integrazione di Azure predefinito. È necessario creare in modo esplicito un runtime di integrazione di Azure solo quando si vuole definire in modo esplicito la località del runtime di integrazione o si vogliono raggruppare le esecuzioni di attività in runtime di integrazione diversi a scopo di gestione. 

## <a name="create-azure-ir"></a>Creare il runtime di integrazione di Azure
Il runtime di integrazione può essere creato utilizzando il cmdlet PowerShell **Set-AzDataFactoryV2IntegrationRuntime.** Per creare un runtime di integrazione di Azure, specificare il nome, la località e il tipo nel comando. Ecco un comando di esempio per creare un runtime di integrazione di Azure con la località impostata su "Europa occidentale":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Per il runtime di integrazione di Azure, il tipo deve essere impostato su **Managed**. Non è necessario specificare i dettagli del calcolo perché è completamente gestito in modo elastico nel cloud. Specificare i dettagli del calcolo, ad esempio le dimensioni del nodo e il numero di nodi, quando si vuole creare un runtime di integrazione SSIS di Azure. Per altre informazioni, vedere [Creare e configurare il runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md).

È possibile configurare un runtime di integrazione di Azure esistente per modificarne il percorso usando il cmdlet PowerShell Set-AzDataFactoryV2IntegrationRuntime.You can configure an existing Azure IR to change its location using the Set-AzDataFactoryV2IntegrationRuntime PowerShell cmdlet. Per altre informazioni sulla località di un runtime di integrazione di Azure, vedere [Introduction to integration runtime](concepts-integration-runtime.md) (Introduzione al runtime di integrazione).

## <a name="use-azure-ir"></a>Usare il runtime di integrazione di Azure

Dopo la creazione di un runtime di integrazione di Azure, è possibile farvi riferimento nella definizione del servizio collegato. Segue un esempio di come fare riferimento al runtime di integrazione di Azure creato sopra da un servizio collegato Archiviazione di Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Passaggi successivi
Per creare altri tipi di runtime di integrazione, vedere gli articoli seguenti:

- [Creare un runtime di integrazione self-hostedCreate self-hosted integration runtime](create-self-hosted-integration-runtime.md)
- [Creare il runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md)
 
