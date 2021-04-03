---
title: Aggiungere un'entità servizio al ruolo di amministratore di Azure Analysis Services | Microsoft Docs
description: Informazioni su come aggiungere un'entità servizio di automazione al ruolo di amministratore di Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100729"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Aggiungere un'entità servizio al ruolo di amministratore del server 

 Per automatizzare le attività di PowerShell, un'entità servizio deve avere privilegi di **amministratore del server** nel server Analysis Services gestito. Questo articolo descrive come aggiungere un'entità servizio al ruolo di amministratore del server in un server Azure Analysis Services. A questo scopo, è possibile usare SQL Server Management Studio o un modello di Resource Manager. 

> [!NOTE]
> Le entità servizio devono essere aggiunte direttamente al ruolo di amministratore del server. L'aggiunta di un'entità servizio a un gruppo di sicurezza e l'aggiunta del gruppo di sicurezza al ruolo di amministratore del server non è supportata. 

## <a name="before-you-begin"></a>Prima di iniziare
Prima di completare questa attività, è necessario aver creato un'entità servizio registrata in Azure Active Directory.

[Creare un'entità servizio - Portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Creare un'entità servizio - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Utilizzare SQL Server Management Studio

È possibile configurare gli amministratori del server con SQL Server Management Studio (SSMS). Per completare questa attività, è necessario avere autorizzazioni di [amministratore del server](analysis-services-server-admins.md) nel server Azure Analysis Services. 

1. In SSMS connettersi al server Azure Analysis Services.
2. In **Proprietà server** > **Sicurezza** fare clic su **Aggiungi**.
3. In **Selezionare un utente o un gruppo** cercare l'app registrata per nome, selezionarla e quindi fare clic su **Aggiungi**.

    ![Cercare l'account dell'entità servizio](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verificare l'ID account dell'entità servizio e quindi fare clic su **OK**.
    
    ![Screenshot che mostra l'ID dell'account dell'entità servizio ed evidenzia il pulsante OK.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Uso di un modello di Resource Manager

È anche possibile configurare gli amministratori del server distribuendo il server Analysis Services usando un modello di Azure Resource Manager. L'identità che esegue la distribuzione deve appartenere al ruolo **collaboratore** per la risorsa nel [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> L'entità servizio deve essere aggiunta usando il formato `app:{service-principal-client-id}@{azure-ad-tenant-id}`.

Il modello di Resource Manager seguente distribuisce un server Analysis Services con un'entità servizio specificata aggiunta al ruolo di amministratore di Analysis Services:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Uso di identità gestite

È anche possibile aggiungere un'identità gestita all'elenco Amministratori di Analysis Services. Ad esempio, si potrebbe avere un'[app per la logica con un'identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) cui si vuole consentire di amministrare il server Analysis Services.

Nella maggior parte delle sezioni del portale di Azure e delle API le identità gestite vengono identificate usando il relativo ID oggetto entità servizio. Tuttavia, Analysis Services richiede che vengano identificate tramite l'ID client. Per ottenere l'ID client per un'entità servizio, è possibile usare l'interfaccia della riga di comando di Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

In alternativa, è possibile usare PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

È quindi possibile usare questo ID client insieme all'ID tenant per aggiungere l'identità gestita all'elenco Amministratori di Analysis Services, come descritto in precedenza.

## <a name="related-information"></a>Informazioni correlate

* [Scaricare il modulo PowerShell di SQL Server](/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)