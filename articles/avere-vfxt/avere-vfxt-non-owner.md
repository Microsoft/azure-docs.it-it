---
title: Soluzione alternativa per i non proprietari di Avere vFXT - Azure
description: Soluzione alternativa per consentire agli utenti senza autorizzazioni di proprietario della sottoscrizione di distribuire Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76153276"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizzare i non proprietari a distribuire Avere vFXT

Queste istruzioni forniscono una soluzione alternativa per consentire a un utente senza privilegi di proprietario della sottoscrizione di creare un sistema Avere vFXT per Azure.

(Il metodo consigliato per distribuire il sistema Avere vFXT consiste nell'avere un utente con privilegi di proprietario che esegue la procedura di creazione, come spiegato in [Preparare la creazione di Avere vFXT](avere-vfxt-prereqs.md).)  

La soluzione alternativa prevede la creazione di un ruolo di accesso aggiuntivo che offre agli utenti autorizzazioni sufficienti per installare il cluster. Il ruolo deve essere creato dal proprietario di una sottoscrizione e il proprietario deve assegnarlo agli utenti appropriati.

Il proprietario di una sottoscrizione deve inoltre [accettare le condizioni d'uso](avere-vfxt-prereqs.md) per l'immagine del marketplace di Avere vFXT.

> [!IMPORTANT]
> Tutti questi passaggi devono essere eseguiti da un utente con privilegi di proprietario della sottoscrizione che verrà usata per il cluster.

1. Copiare le righe seguenti e salvarle in un file (ad esempio, `averecreatecluster.json`). Usare il proprio ID sottoscrizione nell'istruzione `AssignableScopes`.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Eseguire questo comando per creare il ruolo:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Esempio:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Assegnare questo ruolo all'utente che creerà il cluster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Al termine del processo, il ruolo assegna a qualsiasi utente le autorizzazioni seguenti per la sottoscrizione:

* Creare e configurare l'infrastruttura di rete
* Creare il controller del cluster
* Eseguire gli script di creazione del cluster dal controller del cluster per creare il cluster
