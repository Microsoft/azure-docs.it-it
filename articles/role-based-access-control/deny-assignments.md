---
title: Informazioni sulle assegnazioni di rifiuto per le risorse di Azure
description: Informazioni sulle assegnazioni di rifiuto nel controllo degli accessi in base al ruolo per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372477"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Informazioni sulle assegnazioni di rifiuto per le risorse di Azure

Simile a un'assegnazione di ruolo, un'*assegnazione di rifiuto* allega un set di azioni di rifiuto a un utente, gruppo o entità servizio in un determinato ambito con lo scopo di rifiutare l'accesso. Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche sulle risorse di Azure, anche se un'assegnazione di ruolo concede loro l'accesso.

Questo articolo descrive il modo in cui le assegnazioni di rifiuto sono definite.

## <a name="how-deny-assignments-are-created"></a>Come vengono create le assegnazioni Deny

Le assegnazioni Deny vengono create e gestite da Azure per proteggere le risorse. I progettisti di Azure e le app gestite di Azure usano le assegnazioni di rifiuto per proteggere le risorse gestite dal sistema. I progetti di Azure e le app gestite di Azure sono l'unico modo per creare le assegnazioni di negazione. Non è possibile creare direttamente le assegnazioni di rifiuto. Per ulteriori informazioni su come i progetti utilizzano le assegnazioni di negazione per bloccare le risorse, vedere informazioni sul [blocco delle risorse nei progetti di Azure](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Non è possibile creare direttamente le assegnazioni di rifiuto.

## <a name="compare-role-assignments-and-deny-assignments"></a>Confrontare le assegnazioni di ruolo e le assegnazioni di rifiuto

Le assegnazioni di negazione seguono un modello simile a quello delle assegnazioni di ruolo, ma presentano anche alcune differenze.

| Funzionalità | Assegnazione di ruolo | Nega assegnazione |
| --- | --- | --- |
| Concedere l'accesso | :heavy_check_mark: |  |
| Rifiutare l'accesso |  | :heavy_check_mark: |
| Può essere creato direttamente | :heavy_check_mark: |  |
| Applicare a un ambito | :heavy_check_mark: | :heavy_check_mark: |
| Escludi entità |  | :heavy_check_mark: |
| Impedisci ereditarietà agli ambiti figlio |  | :heavy_check_mark: |
| Si applicano alle assegnazioni di [amministratore della sottoscrizione classica](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Proprietà dell'assegnazione di rifiuto

 Un'assegnazione di rifiuto ha le seguenti proprietà:

> [!div class="mx-tableFixed"]
> | Proprietà | Obbligatoria | Type | Descrizione |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sì | string | Il nome visualizzato dell'assegnazione di rifiuto. I nomi devono essere univoci per un determinato ambito. |
> | `Description` | No | string | La descrizione dell'assegnazione di rifiuto. |
> | `Permissions.Actions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni di gestione a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotActions` | No | String[] | Una matrice di stringhe che specifica le operazioni di gestione da escludere nell'assegnazione di rifiuto. |
> | `Permissions.DataActions` | Almeno un Actions o un DataActions | String[] | Una matrice di stringhe che specifica le operazioni dati a cui l'assegnazione di rifiuto blocca l'accesso. |
> | `Permissions.NotDataActions` | No | String[] | Una matrice di stringhe che specifica le operazioni dati da escludere nell'assegnazione di rifiuto. |
> | `Scope` | No | string | Una stringa che specifica l'ambito a cui si applica l'assegnazione di rifiuto. |
> | `DoNotApplyToChildScopes` | No | Boolean | Specifica se l'assegnazione di rifiuto è valida per gli ambiti figlio. Il valore predefinito è False. |
> | `Principals[i].Id` | Sì | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui si applica l'assegnazione di rifiuto. Impostare un GUID vuoto `00000000-0000-0000-0000-000000000000` per rappresentare tutte le entità. |
> | `Principals[i].Type` | No | String[] | Matrice di tipi di oggetti rappresentati da entità [i]. ID. impostare su `SystemDefined` per rappresentare tutte le entità. |
> | `ExcludePrincipals[i].Id` | No | String[] | Una matrice degli ID oggetto entità di Azure AD (utente, gruppo, entità servizio o identità gestita) a cui non si applica l'assegnazione di rifiuto. |
> | `ExcludePrincipals[i].Type` | No | String[] | Una matrice di tipi di oggetto rappresentati da ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | No | Boolean | Specifica se questa assegnazione di rifiuto è stata creata da Azure e non può essere modificata o eliminata. Attualmente, tutte le assegnazioni di rifiuto sono protette dal sistema. |

## <a name="the-all-principals-principal"></a>Entità tutte le entità

Per supportare le assegnazioni di negazione, è stata introdotta un'entità definita dal sistema denominata *tutte le entità* . Quest'entità rappresenta tutti gli utenti, gruppi, entità servizio e identità gestite in una directory di Azure AD. Se l'ID entità è un GUID zero `00000000-0000-0000-0000-000000000000` e il tipo di entità è `SystemDefined`, l'entità rappresenta tutte le entità. In Azure PowerShell output, tutte le entità hanno un aspetto simile al seguente:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Tutte le entità possono essere combinate `ExcludePrincipals` con per negare l'accesso a tutte le entità ad eccezione di alcuni utenti. Per tutte le entità sono presenti i vincoli seguenti:

- Può essere usata solo con `Principals` e non può essere usata con `ExcludePrincipals`.
- `Principals[i].Type` deve essere impostato su `SystemDefined`.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: proteggere nuove risorse con i blocchi delle risorse dei progetti di Azure](../governance/blueprints/tutorials/protect-new-resources.md)
* [Elencare le assegnazioni di rifiuto per le risorse di Azure usando il portale di Azure](deny-assignments-portal.md)
