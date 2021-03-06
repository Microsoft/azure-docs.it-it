---
title: Delega e ruoli nella gestione dei diritti-Azure AD
description: Informazioni su come delegare la governance degli accessi dagli amministratori IT ai responsabili del reparto e ai responsabili di progetto in modo che possano gestire l'accesso.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204d83b96e3cbe26759d678126d8826d0b2e492e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577849"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delega e ruoli in Azure AD gestione dei diritti

Per impostazione predefinita, gli amministratori globali e gli amministratori utenti possono creare e gestire tutti gli aspetti della gestione dei diritti Azure AD. Tuttavia, gli utenti di questi ruoli potrebbero non essere a conoscenza di tutte le situazioni in cui sono necessari i pacchetti di accesso. In genere si tratta di utenti all'interno dei rispettivi reparti, team o progetti che sanno chi collaborano, usando quali risorse e per quanto tempo. Anziché concedere autorizzazioni senza restrizioni a utenti non amministratori, è possibile concedere agli utenti le autorizzazioni minime necessarie per svolgere il proprio lavoro ed evitare la creazione di diritti di accesso in conflitto o non appropriati.

In questo video viene fornita una panoramica su come delegare la governance di accesso dall'amministratore IT agli utenti che non sono amministratori.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Esempio di delegato

Per comprendere come è possibile delegare la governance di accesso alla gestione dei diritti, è utile prendere in considerazione un esempio. Si supponga che l'organizzazione disponga dei seguenti amministratori e responsabili.

![Delega da amministratore IT ai responsabili](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

In qualità di amministratore IT, Hana ha contattato in ogni reparto, ovvero la muratura in marketing, Mark in Finance e Joe in Legal, responsabile delle risorse del reparto e dei contenuti aziendali critici.

Con la gestione dei diritti, è possibile delegare la governance di accesso a questi non amministratori perché sono quelli che conoscono quali utenti devono accedere, per quanto tempo e per quali risorse. La delega a utenti non amministratori garantisce che le persone corrette gestiscano l'accesso ai propri reparti.

Ecco un modo in cui Hana può delegare la governance degli accessi ai reparti marketing, finanza e legale.

1. Hana consente di creare un nuovo gruppo di sicurezza Azure AD e di aggiungere l'oggetto, Mark e Joe come membri del gruppo.

1. Hana aggiunge tale gruppo al ruolo creatori del catalogo.

    È ora possibile creare cataloghi per i reparti, aggiungere le risorse necessarie ai propri reparti ed eseguire ulteriori deleghe all'interno del catalogo. Non possono vedere i cataloghi reciproci.

1. Per creare un catalogo **Marketing** , che è un contenitore di risorse,

1. Con la proprietà del reparto marketing è possibile aggiungere le risorse a questo catalogo.

1. È possibile aggiungere altri utenti del reparto come proprietari del catalogo per questo catalogo, che consente di condividere le responsabilità di gestione del catalogo.

1. È possibile delegare ulteriormente la creazione e la gestione dei pacchetti di accesso nel catalogo marketing ai Project Manager del reparto marketing. A tale scopo, è possibile assegnarli al ruolo Gestione pacchetti di accesso. Una gestione pacchetti di accesso può creare e gestire i pacchetti di accesso. 

Il diagramma seguente mostra i cataloghi con risorse per gli uffici marketing, Finance e Legal. Usando questi cataloghi, i Project Manager possono creare pacchetti di accesso per i team o i progetti.

![Esempio di delegati di gestione dei diritti](./media/entitlement-management-delegate/elm-delegate.png)

Dopo la delega, il reparto marketing potrebbe avere ruoli simili alla tabella seguente.

| User | Ruolo di lavoro | Ruolo di Azure AD | Ruolo di gestione dei diritti |
| --- | --- | --- | --- |
| Hana | Amministratore IT | Amministratore globale o Amministratore utenti |  |
| Muja | Responsabile marketing | User | Autore del catalogo e proprietario del catalogo |
| Bob | Responsabile marketing | User | Proprietario Catalogo |
| Jessica | project manager marketing | User | Gestione pacchetti di Access |

## <a name="entitlement-management-roles"></a>Ruoli di gestione dei diritti

La gestione dei diritti ha i seguenti ruoli specifici per la gestione dei diritti.

| Ruolo di gestione dei diritti | Descrizione |
| --- | --- |
| Creatore del catalogo | Creare e gestire cataloghi. In genere un amministratore IT che non è un amministratore globale o un proprietario di risorse per una raccolta di risorse. La persona che crea un catalogo diventa automaticamente il primo proprietario del catalogo e può aggiungere altri proprietari del catalogo. Un autore del catalogo non può gestire o vedere cataloghi di cui non è proprietario e non può aggiungere risorse di cui non è proprietario per un catalogo. Se l'autore del catalogo deve gestire un altro catalogo o aggiungere risorse di cui non è proprietario, può richiedere di essere un comproprietario di tale catalogo o risorsa. |
| Proprietario Catalogo | Modificare e gestire cataloghi esistenti. In genere un amministratore IT o proprietari di risorse oppure un utente scelto dal proprietario del catalogo. |
| Gestione pacchetti di Access | Modificare e gestire tutti i pacchetti di accesso esistenti all'interno di un catalogo. |
| Gestione assegnazione pacchetti di Access | Modificare e gestire tutte le assegnazioni dei pacchetti di accesso esistenti. |

Inoltre, il responsabile approvazione scelto e un richiedente di un pacchetto di accesso dispongono dei diritti, anche se non sono ruoli.

| Destra | Descrizione |
| --- | --- |
| Responsabile approvazione | Autorizzazione eseguita da un criterio per approvare o negare le richieste di accesso ai pacchetti, sebbene non possano modificare le definizioni dei pacchetti di accesso. |
| Richiedente | Autorizzazione eseguita da un criterio di un pacchetto di accesso per richiedere il pacchetto di accesso. |

Nella tabella seguente sono elencate le attività che i ruoli di gestione dei diritti possono eseguire.

| Attività | Amministrativi | Creatore del catalogo | Proprietario Catalogo | Gestione pacchetti di Access | Gestione assegnazione pacchetti di Access |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delega a un creatore del catalogo](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Aggiungere un'organizzazione connessa](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Crea un nuovo catalogo](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Aggiungere una risorsa a un catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Aggiungere un proprietario del catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Modificare un catalogo](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Eliminare un catalogo](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delega a una gestione pacchetti di Access](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Rimuovere una gestione pacchetti di accesso](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Creare un nuovo pacchetto di accesso in un catalogo](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Modificare i ruoli delle risorse in un pacchetto di accesso](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Creazione e modifica di criteri](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Assegnare direttamente un utente a un pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Rimuovere direttamente un utente da un pacchetto di accesso](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Visualizza chi ha un'assegnazione a un pacchetto di accesso](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Visualizzare le richieste di un pacchetto di accesso](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Visualizzare gli errori di recapito di una richiesta](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Rielaborare una richiesta](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Annullare una richiesta in sospeso](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Nascondi un pacchetto di accesso](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Eliminare un pacchetto di accesso](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Ruoli necessari per aggiungere risorse a un catalogo

Un amministratore globale può aggiungere o rimuovere qualsiasi gruppo (gruppi di sicurezza creati dal cloud o gruppi di Microsoft 365 creati dal cloud), un'applicazione o un sito di SharePoint online in un catalogo. Un amministratore utente può aggiungere o rimuovere qualsiasi gruppo o applicazione in un catalogo, ad eccezione di un gruppo configurato come assegnabile a un ruolo della directory. Si noti che un amministratore utente può gestire i pacchetti di accesso in un catalogo che include i gruppi configurati come assegnabili a un ruolo della directory.  Per ulteriori informazioni sui gruppi assegnabili ai ruoli, fare riferimento [a creare un gruppo assegnabile al ruolo in Azure Active Directory](../roles/groups-create-eligible.md).

Per un utente che non è un amministratore globale o un amministratore di utenti, per aggiungere gruppi, applicazioni o siti di SharePoint Online a un catalogo, tale utente deve avere *sia* il ruolo della directory Azure ad necessario che il ruolo di gestione dei diritti del proprietario del catalogo. Nella tabella seguente sono elencate le combinazioni di ruoli necessarie per aggiungere risorse a un catalogo. Per rimuovere le risorse da un catalogo, è necessario avere gli stessi ruoli.

| Ruolo della directory di Azure AD | Ruolo di gestione dei diritti | È possibile aggiungere un gruppo di sicurezza | È possibile aggiungere Microsoft 365 gruppo | È possibile aggiungere l'app | È possibile aggiungere il sito di SharePoint Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Amministratore globale](../roles/permissions-reference.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Amministratore utenti](../roles/permissions-reference.md) | n/d |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Amministratore di Intune](../roles/permissions-reference.md) | Proprietario Catalogo | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Amministratore Exchange](../roles/permissions-reference.md) | Proprietario Catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore del servizio Teams](../roles/permissions-reference.md) | Proprietario Catalogo |  | :heavy_check_mark: |  |  |
| [Amministratore SharePoint](../roles/permissions-reference.md) | Proprietario Catalogo |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Amministratore applicazione](../roles/permissions-reference.md) | Proprietario Catalogo |  |  | :heavy_check_mark: |  |
| [Amministratore applicazione cloud](../roles/permissions-reference.md) | Proprietario Catalogo |  |  | :heavy_check_mark: |  |
| User | Proprietario Catalogo | Solo se il proprietario del gruppo | Solo se il proprietario del gruppo | Solo se il proprietario dell'app |  |

Per determinare il ruolo con privilegi minimi per un'attività, è anche possibile fare riferimento ai [ruoli di amministratore per attività di amministrazione in Azure Active Directory](../roles/delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Passaggi successivi

- [Delega della governance di accesso ai creatori di cataloghi](entitlement-management-delegate-catalog.md)
- [Creare e gestire un catalogo di risorse](entitlement-management-catalog-create.md)
