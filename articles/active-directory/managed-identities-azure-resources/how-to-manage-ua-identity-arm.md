---
title: Creare & eliminare un'identità gestita assegnata dall'utente usando Azure Resource Manager
description: Istruzioni dettagliate su come creare ed eliminare identità gestite assegnate dall'utente mediante Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184877"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Creare, elencare ed eliminare un'identità gestita assegnata dall'utente usando Azure Resource Manager


Le identità gestite per le risorse di Azure offrono servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene creata un'identità gestita assegnata dall'utente mediante Azure Resource Manager.

Non è possibile elencare ed eliminare un'identità gestita assegnata dall'utente mediante un modello di Azure Resource Manager.  Vedere gli articoli seguenti per creare ed elencare un'identità gestita assegnata dall'utente:

- [Elencare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Eliminare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione Panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="template-creation-and-editing"></a>Creazione e modifica del modello

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di Azure Resource Manager offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

- Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
- Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) o dallo [stato attuale della distribuzione](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
- Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) di Visual Studio per creare e distribuire un modello. 

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Per creare un'identità gestita assegnata dall'utente, usare il modello seguente. Sostituire il valore `<USER ASSIGNED IDENTITY NAME>` con i propri valori:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure mediante un modello di Azure Resource Manager, vedere [Configure managed identities for Azure resources on an Azure VM using a templates](qs-configure-template-windows-vm.md) (Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure mediante modelli).


