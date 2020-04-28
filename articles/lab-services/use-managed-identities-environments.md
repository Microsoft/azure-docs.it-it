---
title: Usare le identità gestite di Azure per creare ambienti in DevTest Labs | Microsoft Docs
description: Informazioni su come usare le identità gestite in Azure per distribuire gli ambienti in un Lab in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73931148"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Usare le identità gestite di Azure per distribuire gli ambienti in un Lab 
In qualità di proprietario del Lab, è possibile usare un'identità gestita per distribuire gli ambienti in un Lab. Questa funzionalità è utile negli scenari in cui l'ambiente contiene o contiene riferimenti alle risorse di Azure, ad esempio insiemi di credenziali delle chiavi, raccolte di immagini condivise e reti esterne al gruppo di risorse dell'ambiente. Consente la creazione di ambienti sandbox che non sono limitati al gruppo di risorse dell'ambiente.

> [!NOTE]
> Attualmente, per Lab è supportata una singola identità assegnata dall'utente. 

## <a name="prerequisites"></a>Prerequisiti
- [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Usare il portale di Azure
In questa sezione, in qualità di proprietario del Lab, usare il portale di Azure per aggiungere un'identità gestita dall'utente al Lab. 

1. Nella pagina Lab selezionare **configurazione e criteri**. 
1. Selezionare **Identity (identità** ) nella sezione **Settings (impostazioni** ).
1. Per aggiungere un'identità assegnata a un utente, selezionare **Aggiungi** sulla barra degli strumenti. 
1. Selezionare un' **identità** da un elenco a discesa pre-popolato.
1. Selezionare **OK**.

    ![Aggiungi identità gestita dall'utente](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. Nell'elenco viene visualizzata l'identità gestita dall'utente aggiunta. 

    ![Identità gestita dall'utente nell'elenco](./media/use-managed-identities-environments/identity-in-list.png)

Una volta salvato, il Lab utilizzerà questa identità durante la distribuzione di tutti gli ambienti Lab. È anche possibile accedere alla risorsa di identità in Azure selezionando l'identità dall'elenco. 

Il proprietario del Lab non deve eseguire alcuna operazione speciale durante la distribuzione di un ambiente purché l'identità aggiunta al lab disponga delle autorizzazioni per le risorse esterne a cui l'ambiente deve accedere. 

Per modificare l'identità gestita dall'utente assegnata al Lab, rimuovere prima l'identità collegata al Lab, quindi aggiungerne un'altra al Lab. Per rimuovere un'identità collegata al Lab, selezionare **... (puntini**di sospensione) e fare clic su **Rimuovi**. 

![Identità gestita dall'utente nell'elenco](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>USA API

1. Dopo aver creato un'identità, prendere nota dell'ID risorsa dell'identità. Dovrebbe essere simile all'esempio seguente: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Eseguire un metodo PUT HTTPS per aggiungere una nuova `ServiceRunner` risorsa al Lab in modo analogo all'esempio seguente. La risorsa di Service Runner è una risorsa proxy per gestire e controllare le identità gestite in DevTest Labs. Il nome del Runner del servizio può essere qualsiasi nome valido, ma è consigliabile usare il nome della risorsa di identità gestita. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Ad esempio: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Una volta aggiunta l'identità assegnata all'utente al Lab, il servizio Azure DevTest Labs lo utilizzerà durante la distribuzione di Azure Resource Manager ambienti. Se ad esempio è necessario il modello di Gestione risorse per accedere a un'immagine della raccolta di immagini condivise esterna, assicurarsi che l'identità aggiunta al lab disponga delle autorizzazioni minime necessarie per la risorsa della raccolta immagini condivisa. 
