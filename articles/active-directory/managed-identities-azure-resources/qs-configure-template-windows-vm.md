---
title: Configurare le identità gestite in macchine virtuali di Azure tramite un modello - Azure AD
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite un modello di Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d38d3ab8cbbfb1f409b368cddc1df854baa8e0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266885"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite i modelli

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice.

Questo articolo illustra, tramite il modello di distribuzione di Azure Resource Manager, come eseguire queste identità gestite per le operazioni delle risorse di Azure su una macchina virtuale di Azure:

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'uso del modello di distribuzione di Azure Resource Manager, consultare la [sezione Panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="azure-resource-manager-templates"></a>Modelli di Azure Resource Manager

Analogamente al portale di Azure e all'esecuzione dello script, i modelli di gestione di [Azure Resource Manager](../../azure-resource-manager/management/overview.md) offrono la possibilità di distribuire risorse nuove o modificate definite da un gruppo di risorse di Azure. Diverse opzioni sono disponibili per la modifica e la distribuzione dei modelli, sia in locale che basati sul portale incluso quanto segue:

   - Usare un [modello personalizzato di Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), che consente di creare un modello nuovo o di usare come base un modello comune esistente o un [modello di avvio rapido](https://azure.microsoft.com/documentation/templates/).
   - Derivazione da un gruppo di risorse esistente, tramite l'esportazione di un modello da una [distribuzione originale](../../azure-resource-manager/templates/export-template-portal.md) o dallo [stato attuale della distribuzione](../../azure-resource-manager/templates/export-template-portal.md).
   - Usare un [editor JSON, ad esempio il codice di Visual Studio,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) locale e di caricarlo e distribuirlo tramite PowerShell o l'interfaccia della riga di comando.
   - Usare il [progetto del gruppo di risorse di Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) di Visual Studio per creare e distribuire un modello.  

Indipendentemente dall'opzione scelta, la sintassi dei modelli è la stessa durante la distribuzione iniziale e la ridistribuzione. L'abilitazione di un'identità gestita assegnata dal sistema o dall'utente in una macchina virtuale nuova o esistente viene eseguita allo stesso modo. Per impostazione predefinita Azure Resource Manager esegue inoltre un [aggiornamento incrementale](../../azure-resource-manager/templates/deployment-modes.md) per le distribuzioni.

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

In questa sezione si abiliterà e disabiliterà un'identità gestita assegnata dal sistema tramite un modello di Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale di Azure oppure in una macchina virtuale esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM.

2. Per abilitare l'identità gestita assegnata dal sistema, caricare il modello in un editor, individuare la risorsa interessata `Microsoft.Compute/virtualMachines` nella sezione `resources` e aggiungere la proprietà `"identity"` allo stesso livello della proprietà `"type": "Microsoft.Compute/virtualMachines"`. Usare la sintassi seguente:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Al termine, verranno aggiunte le sezioni seguenti alla sezione `resource` del modello, che avrà un aspetto simile a questo:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Assegnare un ruolo identità gestita assegnata dal sistema della macchina virtuale

Dopo avere abilitato l'identità gestita assegnata dal sistema sulla macchina virtuale, è possibile autorizzare, ad esempio, un ruolo di accesso **Lettore** al gruppo di risorse in cui è stato creata.

Per assegnare un ruolo all'identità assegnata dal sistema della macchina virtuale, all'account deve essere assegnato il ruolo [Amministratore Accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator).

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM.

2. Caricare il modello in un' [editor](#azure-resource-manager-templates) e aggiungere le informazioni seguenti per assegnare alla macchina virtuale l'accesso come **Lettore** al gruppo di risorse in cui è stato creata.  La struttura del modello può variare a seconda dell'editor e del modello di distribuzione scelto.

   Sotto la sezione `parameters` aggiungere quanto segue:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Sotto la sezione `variables` aggiungere quanto segue:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Sotto la sezione `resources` aggiungere quanto segue:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Disabilitare un'identità gestita assegnata dal sistema in una macchina virtuale di Azure

Per rimuovere l'identità gestita assegnata dal sistema da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachines`la risorsa interessata`resources` all'interno della sezione. Se si dispone di una macchina virtuale con solo un'identità gestita assegnata dal sistema, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.  

   **Microsoft.Compute/virtualMachines versione API 2018-06-01**

   Se la macchina virtuale ha identità gestite assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme ai valori dizionario `userAssignedIdentities`.

   **Microsoft.Compute/virtualMachines versione API 2018-06-01**

   Se `apiVersion` è `2017-12-01` e la macchina virtuale ha identità gestite assegnate sia dal sistema sia dall'utente, rimuovere `SystemAssigned` dal tipo di identità e mantenere `UserAssigned` insieme alla matrice `identityIds` delle identità gestite assegnate dall'utente.  

L'esempio seguente mostra come rimuovere un'identità gestita assegnata dal sistema da una macchina virtuale senza identità gestite assegnate dall'utente:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

In questa sezione si assegna un'identità gestita assegnata dall'utente a una macchina virtuale di Azure tramite il modello di Azure Resource Manager.

> [!Note]
> Per creare un'identità gestita assegnata dall'utente usando un modello di Azure Resource Manager, vedere [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Assegnare a una macchina virtuale di Azure un'identità gestita assegnata all'utente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Nell'elemento `resources` aggiungere la voce seguente per assegnare un'identità gestita assegnata dall'utente alla macchina virtuale.  Assicurarsi di sostituire `<USERASSIGNEDIDENTITY>` con il nome dell'identità gestita assegnata dall'utente che è stata creata.

   **Microsoft.Compute/virtualMachines versione API 2018-06-01**

   Se `apiVersion` è `2018-06-01`, le identità gestite assegnate dall'utente vengono archiviate nel formato dizionario `userAssignedIdentities` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione `variables` del modello.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines versione API 2017-12-01**

   Se `apiVersion` è `2017-12-01`, le identità gestite assegnate dall'utente vengono archiviate nella matrice `identityIds` e il valore `<USERASSIGNEDIDENTITYNAME>` deve essere archiviato in una variabile definita nella sezione `variables` del modello.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Al termine, verranno aggiunte le sezioni seguenti alla sezione `resource` del modello, che avrà un aspetto simile a questo:

   **Microsoft.Compute/virtualMachines versione API 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]   
   ```
   **Microsoft.Compute/virtualMachines versione API 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Se si accede ad Azure localmente o tramite il portale di Azure, usare un account che sia associato alla sottoscrizione di Azure che contiene la VM.

2. Caricare il modello in un [editor](#azure-resource-manager-templates) e individuare `Microsoft.Compute/virtualMachines`la risorsa interessata`resources` all'interno della sezione. Se si dispone di una macchina virtuale con solo un'identità gestita assegnata dall'utente, è possibile disabilitarla modificando il tipo di identità e impostandolo su `None`.

   L'esempio seguente illustra come rimuovere tutte le identità gestite assegnate dall'utente da una macchina virtuale senza identità gestite assegnate dal sistema:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines versione API 2018-06-01**

   Per rimuovere da una macchina virtuale una singola identità gestita assegnata dall'utente, rimuoverla dal dizionario `useraAssignedIdentities`.

   Se si dispone di un'identità gestita assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.

   **Microsoft.Compute/virtualMachines versione API 2017-12-01**

   Per rimuovere una singola identità gestita assegnata dall'utente da una macchina virtuale, rimuoverla dalla matrice `identityIds`.

   Se si dispone di un'identità gestita assegnata dal sistema, mantenerla nel valore `type` del valore `identity`.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md).