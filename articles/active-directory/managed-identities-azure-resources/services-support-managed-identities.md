---
title: Servizi di Azure che supportano identità gestite-Azure AD
description: Elenco di servizi che supportano le identità gestite per le risorse di Azure e l'autenticazione di Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45a248300cd61a0e57b9f35c8905d7c335069a27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991280"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servizi che supportano le identità gestite per le risorse di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Con un'identità gestita è possibile eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. È in corso l'integrazione dell'autenticazione di Azure AD e delle identità gestite per le risorse di Azure in Azure. Controllare spesso gli aggiornamenti.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servizi di Azure che supportano le identità gestite per le risorse di Azure

I servizi di Azure seguenti supportano le identità gestite per le risorse di Azure:

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Anteprima | Anteprima | 
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Anteprima | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per le macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Set di scalabilità delle macchine virtuali di Azure

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Anteprima | Anteprima | Anteprima |
| Assegnata dall'utente | ![Disponibile][check] | Anteprima | Anteprima | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per il set di scalabilità di macchine virtuali di Azure (nelle aree in cui è disponibile):

- [Azure portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md)
- [Modelli di Gestione risorse di Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Servizio app di Azure

| Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check] |

Vedere l'elenco seguente per configurare l'identità gestita per il Servizio app di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | Non disponibile |

Vedere l'elenco seguente per usare un'identità gestita con i [progetti di Azure](../../governance/blueprints/overview.md):

- [Assegnazione portale di Azure-Blueprint](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST-assegnazione progetto](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Funzioni di Azure

Tipo di identità gestita |Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check]  | ![Disponibile][check]  | ![Disponibile][check]  |

Vedere l'elenco seguente per configurare l'identità gestita per Funzioni di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interfaccia della riga di comando di Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modello di Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>App per la logica di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |


Vedere l'elenco seguente per configurare l'identità gestita per le App per la logica di Azure (nelle aree in cui è disponibile):

- [Azure portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Modello di Azure Resource Manager](https://docs.microsoft.com/azure/logic-apps/logic-apps-azure-resource-manager-templates-overview)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Azure Data Factory V2 (nelle aree in cui è disponibile):

- [Azure portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gestione API di Azure

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | ![Disponibile][check] | Non disponibile | ![Disponibile][check] |
| Assegnata dall'utente | Anteprima | Anteprima | Non disponibile | Anteprima |

Vedere l'elenco seguente per configurare l'identità gestita per Gestione API di Azure (nelle aree in cui è disponibile):

- [Modello di Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Istanze di Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | Linux: anteprima<br>Windows: non disponibile | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Linux: anteprima<br>Windows: non disponibile | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per Istanze di Azure Container (nelle aree in cui è disponibile):

- [Interfaccia della riga di comando di Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modello di Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Attività di Registro Azure Container

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | Non disponibile |
| Assegnata dall'utente | Anteprima | Non disponibile | Non disponibile | Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per le attività di Container Registry di Azure (nelle aree in cui sono disponibili):

- [Interfaccia della riga di comando di Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
L' [identità gestita per le applicazioni Service Fabric](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) è in anteprima e disponibile in tutte le aree geografiche.

Tipo di identità gestita | Disponibile a livello generale<br>Aree globali di Azure | Azure Government | Azure Germania | 21Vianet per Azure Cina |
| --- | :-: | :-: | :-: | :-: |
| Assegnata dal sistema | ![Disponibile][check] | Non disponibile | Non disponibile | non disponibile |
| Assegnata dall'utente | ![Disponibile][check] | Non disponibile | Non disponibile |Non disponibile |

Vedere l'elenco seguente per configurare l'identità gestita per le applicazioni Service Fabric di Azure in tutte le aree:
- [Modello di Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servizi di Azure che supportano l'autenticazione di Azure AD

I servizi seguenti supportano l'autenticazione di Azure AD e sono stati testati con i servizi client che usano le identità gestite per le risorse di Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Per configurare l'accesso ai Azure Resource Manager, vedere l'elenco seguente:

- [Assegnare l'accesso tramite portale di Azure](howto-assign-access-portal.md)
- [Assegnare l'accesso tramite PowerShell](howto-assign-access-powershell.md)
- [Assegnare l'accesso tramite l'interfaccia della riga di comando Azure](howto-assign-access-CLI.md)
- [Assegnare l'accesso tramite Azure Resource Manager modello](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Disponibile][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://management.microsoftazure.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://management.chinacloudapi.cn` | ![Disponibile][check] |

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Disponibile][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania |  `https://vault.microsoftazure.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://vault.azure.cn` | ![Disponibile][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-sql"></a>SQL di Azure 

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Disponibile][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Disponibile][check] |
| Azure Germania | `https://database.cloudapi.de/` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://database.chinacloudapi.cn/` | ![Disponibile][check] |

### <a name="azure-event-hubs"></a>Hub eventi di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Disponibile][check] |
| Azure Government |  | Non disponibile |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |

### <a name="azure-service-bus"></a>Bus di servizio di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Disponibile][check] |
| Azure Government |  | ![Disponibile][check] |
| Azure Germania |   | Non disponibile |
| 21Vianet per Azure Cina |  | Non disponibile |









### <a name="azure-storage-blobs-and-queues"></a>BLOB e code di archiviazione di Azure

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Disponibile][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Disponibile][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | ID risorsa | Stato |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Disponibile][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Disponibile][check] |
| Azure Germania | `https://*.asazure.cloudapi.de` | ![Disponibile][check] |
| 21Vianet per Azure Cina | `https://*.asazure.chinacloudapi.cn` | ![Disponibile][check] |

> [!Note]
> Microsoft Power BI supporta anche le [identità gestite](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).


[check]: media/services-support-managed-identities/check.png "Disponibile"
