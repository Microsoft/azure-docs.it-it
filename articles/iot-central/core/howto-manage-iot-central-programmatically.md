---
title: Gestire IoT Central in modo programmatico | Microsoft Docs
description: Questo articolo descrive come creare e gestire IoT Central in modo programmatico. È possibile visualizzare, modificare e rimuovere l'applicazione usando più SDK di linguaggio, ad esempio JavaScript, Python, C# Ruby e Go.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.openlocfilehash: 2feb4d85e7be4076eef8b2796d15b622f6b17c3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658302"
---
# <a name="manage-iot-central-programmatically"></a>Gestire IoT Central in modo programmatico

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire le applicazioni IoT Central dal sito Web [Gestione applicazioni di IoT Central](https://aka.ms/iotcentral), è possibile gestire le applicazioni in modo programmatico usando gli SDK di Azure. I linguaggi supportati includono JavaScript, Python, C#, Ruby e Go.

## <a name="install-the-sdk"></a>Installare l'SDK

La tabella seguente elenca i repository SDK e i comandi di installazione dei pacchetti:

| Repository SDK | Installazione del pacchetto |
| -------------- | ------------ |
| [Azure IotCentralClient SDK per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Microsoft Azure SDK per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK per Ruby - Gestione delle risorse (anteprima)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Pacchetto Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK per Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Versioni dei pacchetti](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Esempi

Il repository [Azure IoT Central ARM SDK samples](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) include esempi di codice per più linguaggi di programmazione che illustrano come creare, aggiornare, elencare ed eliminare applicazioni IoT Central di Azure.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni IoT Central di Azure in modo programmatico, un passaggio successivo suggerito consiste nell'acquisire altre informazioni sul servizio [Azure Resource Manager](../../azure-resource-manager/management/overview.md).