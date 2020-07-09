---
title: SDK Griglia di eventi di Azure
description: Vengono descritti gli SDK per la Griglia di eventi di Azure. Questi SDK forniscono gestione, pubblicazione e consumo.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 2b21d8bf2c393b096e9046765e6cfadc76baffbf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119107"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK Griglia di eventi per la gestione e la pubblicazione

La Griglia di eventi fornisce gli SDK che consentono di gestire a livello di codice le risorse e pubblicare eventi.

## <a name="management-sdks"></a>SDK di gestione

Gli SDK di gestione consentono di creare, aggiornare ed eliminare gli argomenti della griglia di eventi e le sottoscrizioni. Attualmente sono disponibili gli SDK seguenti:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Nodo](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDK del piano dati

Gli SDK del piano dati consentono di pubblicare eventi sugli argomenti occupandosi dell'autenticazione, formando l'evento e pubblicando in modo asincrono sull'endpoint specificato. Consentono inoltre di utilizzare eventi proprietari. Attualmente sono disponibili gli SDK seguenti:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Nodo](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Passaggi successivi

* Per le applicazioni di esempio, vedere gli [esempi di codice di Griglia di eventi](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md)
* Per i comandi Griglia di eventi nell'interfaccia della riga di comando di Azure di rete, vedere [Interfaccia della riga di comando di Azure](/cli/azure/eventgrid).
* Per i comandi Griglia di eventi in PowerShell, vedere [PowerShell](/powershell/module/az.eventgrid).
