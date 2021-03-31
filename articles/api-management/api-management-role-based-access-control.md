---
title: Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure | Microsoft Docs
description: Informazioni su come usare i ruoli predefiniti e creare ruoli personalizzati in Gestione API di Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: fc41d2904d5a6676f1e9e1e2d178985bef0a9aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095540"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure

Gestione API di Azure si basa sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per consentire la gestione degli accessi con granularità fine per i servizi e le entità di gestione API (ad esempio, API e criteri). Questo articolo offre una panoramica dei ruoli predefiniti e personalizzati in Gestione API. Per altre informazioni sulla gestione degli accessi nel portale di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Ruoli predefiniti

In Gestione API sono attualmente disponibili tre ruoli predefiniti e altri due ne verranno aggiunti prossimamente. Questi ruoli possono essere assegnati ad ambiti diversi, tra cui una sottoscrizione, un gruppo di risorse e una singola istanza di Gestione API. Ad esempio, se si assegna il ruolo "lettore del servizio gestione API" a un utente a livello di gruppo di risorse, l'utente ha accesso in lettura a tutte le istanze di gestione API all'interno del gruppo di risorse. 

La tabella seguente contiene descrizioni brevi dei ruoli predefiniti. È possibile assegnare questi ruoli tramite il portale di Azure o altri strumenti, tra cui Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), l'[interfaccia della riga di comando di Azure ](../role-based-access-control/role-assignments-cli.md) e l'[API REST](../role-based-access-control/role-assignments-rest.md). Per informazioni dettagliate su come assegnare i ruoli predefiniti, vedere [assegnare i ruoli di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).

| Ruolo          | Accesso in lettura<sup>[1]</sup> | Accesso in scrittura<sup>[2]</sup> | Creazione, eliminazione e ridimensionamento di servizi, VPN e configurazione dominio personalizzato | Accesso al portale di pubblicazione legacy | Descrizione
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Collaboratore servizio Gestione API | ✓ | ✓ | ✓ | ✓ | Utente con privilegi avanzati. Dispone di accesso CRUD completo a servizi ed entità di Gestione API (ad esempio, API e criteri). Dispone di accesso al portale di pubblicazione legacy. |
| Lettore del servizio gestione API | ✓ | | || Dispone di accesso in sola lettura a servizi ed entità Gestione API. |
| Operatore del servizio gestione API | ✓ | | ✓ | | Può gestire i servizi di Gestione API, ma non le entità.|
| Editor del servizio gestione API<sup>*</sup> | ✓ | ✓ | |  | Può gestire le entità di Gestione API, ma non i servizi.|
| Gestione contenuto gestione API<sup>*</sup> | ✓ | | | ✓ | Può gestire il portale per sviluppatori. Accesso in sola lettura a servizi ed entità.|

<sup>[1] Accesso in lettura a servizi ed entità di Gestione API (ad esempio, API e criteri).</sup>

<sup>[2] Accesso in scrittura a servizi ed entità di Gestione API, tranne per le operazioni seguenti: creazione, eliminazione e ridimensionamento di istanze; configurazione di VPN; configurazione di domini personalizzati.</sup>

<sup>\* Il ruolo Editor del servizio sarà disponibile dopo la migrazione di tutte le interfacce utente di amministrazione dal portale di pubblicazione esistente al portale di Azure. Il ruolo Gestione contenuto sarà disponibile dopo il refactoring del portale di pubblicazione per contenere solo le funzionalità relative alla gestione del portale per sviluppatori.</sup>  

## <a name="custom-roles"></a>Ruoli personalizzati

Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati per fornire una gestione degli accessi più granulare per le entità di Gestione API. È ad esempio possibile creare un ruolo personalizzato con accesso in sola lettura a un servizio di Gestione API, ma che ha accesso in scrittura solo a un'API specifica. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/custom-roles.md). 

> [!NOTE]
> Per poter visualizzare un'istanza di gestione API nella portale di Azure, un ruolo personalizzato deve includere l' ```Microsoft.ApiManagement/service/read``` azione.

Quando si crea un ruolo personalizzato, è più semplice iniziare con uno dei ruoli predefiniti. Modificare gli attributi per aggiungere **Actions**, **NotActions** o **AssignableScopes** e quindi salvare le modifiche come nuovo ruolo. L'esempio seguente inizia con il ruolo "lettore servizio gestione API" e crea un ruolo personalizzato denominato "Calculator API Editor". È possibile assegnare il ruolo personalizzato a un'API specifica. Di conseguenza, questo ruolo può accedere solo a tale API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Nell'articolo [Operazioni di provider di risorse con Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) è disponibile l'elenco delle autorizzazioni che possono essere concesse a livello di Gestione API.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul controllo degli accessi in base al ruolo in Azure, vedere gli articoli seguenti:
  * [Introduzione alla gestione degli accessi nel portale di Azure](../role-based-access-control/overview.md)
  * [Assegnare i ruoli di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md)
  * [Ruoli personalizzati in RBAC di Azure](../role-based-access-control/custom-roles.md)
  * [Operazioni dei provider di risorse di Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
