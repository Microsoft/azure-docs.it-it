---
title: Che cos'è il controllo degli accessi in base al ruolo di Azure?
description: Verrà fornita una panoramica del controllo degli accessi in base al ruolo di Azure. Usare le assegnazioni di ruolo per controllare l'accesso alle risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3846a4669cc2a77862e73dbb8e7743b19740e8a4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996501"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Che cos'è il controllo degli accessi in base al ruolo di Azure?

La gestione dell'accesso per le risorse cloud è una funzione essenziale per qualsiasi organizzazione che usa il cloud. Il controllo degli accessi in base al ruolo di Azure consente di gestire gli utenti autorizzati ad accedere alle risorse di Azure, le operazioni che possono eseguire su tali risorse e le aree a cui hanno accesso.

Il controllo degli accessi in base al ruolo di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) che garantisce una gestione con granularità fine degli accessi delle risorse di Azure.

## <a name="what-can-i-do-with-azure-rbac"></a>Che cosa è possibile fare con il controllo degli accessi in base al ruolo di Azure?

Di seguito sono riportati alcuni esempi di ciò che è possibile fare con il controllo degli accessi in base al ruolo di Azure:

- Consentire a un utente di gestire le macchine virtuali in una sottoscrizione e un altro utente a gestire le reti virtuali
- Consentire a un gruppo di amministratori di database di gestire database SQL all'interno di una sottoscrizione
- Consentire a un utente di gestire tutte le risorse in un gruppo di risorse, ad esempio le macchine virtuali, i siti Web e le subnet
- Consentire a un'applicazione di accedere a tutte le risorse in un gruppo di risorse

## <a name="how-azure-rbac-works"></a>Funzionamento del controllo degli accessi in base al ruolo di Azure

Per controllare l'accesso alle risorse tramite il controllo degli accessi in base al ruolo di Azure è necessario creare assegnazioni di ruolo. Questo è un concetto chiave da comprendere: si tratta di come vengono applicate le autorizzazioni. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito.

### <a name="security-principal"></a>Entità di sicurezza

Un'*entità di sicurezza* è un oggetto che rappresenta un utente, un gruppo, un'entità servizio o un'identità gestita che richiede l'accesso alle risorse di Azure.

![Entità di sicurezza per un'assegnazione di ruolo](./media/overview/rbac-security-principal.png)

- Utente: un soggetto che dispone di un profilo in Azure Active Directory. È anche possibile assegnare ruoli agli utenti in altri tenant. Per informazioni sugli utenti in altre organizzazioni, vedere [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Gruppo: un set di utenti creato in Azure Active Directory. Quando si assegna un ruolo a un gruppo, tutti gli utenti all'interno di tale gruppo dispongono del ruolo appropriato. 
- Entità servizio: un'identità di sicurezza utilizzata da applicazioni o servizi per accedere a specifiche risorse di Azure. È possibile pensare a questo elemento come a un'*identità utente* (nome utente e password o certificato) per un'applicazione.
- Identità gestita: un'identità in Azure Active Directory che viene gestita automaticamente da Azure. Le [identità gestite](../active-directory/managed-identities-azure-resources/overview.md) vengono in genere usate durante lo sviluppo di applicazioni cloud per gestire le credenziali per l'autenticazione ai servizi di Azure.

### <a name="role-definition"></a>Definizione di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, generalmente chiamata *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, I ruoli possono essere di livello superiore, ad esempio quello di proprietario, o specifici, ad esempio quello di lettore di macchina virtuale.

![Definizione di ruolo per un'assegnazione di ruolo](./media/overview/rbac-role-definition.png)

Azure include diversi [ruoli predefiniti](built-in-roles.md) che è possibile usare. Di seguito sono elencati quattro fondamentali ruoli predefiniti. I primi tre si applicano a tutti i tipi di risorse.

- [Proprietario](built-in-roles.md#owner): ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.
- [Collaboratore](built-in-roles.md#contributor): può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri.
- [Lettore](built-in-roles.md#reader): può visualizzare le risorse di Azure esistenti.
- [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator): consente di gestire l'accesso degli utenti alle risorse di Azure.

Gli altri ruoli predefiniti consentono la gestione di risorse di Azure specifiche. Ad esempio, il ruolo [Collaboratore Macchina virtuale](built-in-roles.md#virtual-machine-contributor) consente a un utente di creare e gestire macchine virtuali. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](custom-roles.md).

Azure dispone di operazioni sui dati che consentono di concedere l'accesso ai dati all'interno di un oggetto. Ad esempio, se un utente dispone dell'accesso in lettura ai dati per un account di archiviazione, può leggere i BLOB o i messaggi all'interno di tale account. Per altre informazioni, vedere [Informazioni sulle definizioni dei ruoli di Azure](role-definitions.md).

### <a name="scope"></a>Scope

*Ambito* è il set di risorse a cui si applica l'accesso. Quando si assegna un ruolo, è possibile limitare ulteriormente le azioni consentite definendo un ambito. Ciò è utile se si intende creare un [collaboratore di siti Web](built-in-roles.md#website-contributor), ma solo per un gruppo di risorse.

In Azure, è possibile specificare un ambito su più livelli: [gruppo di gestione](../governance/management-groups/overview.md), sottoscrizione, gruppo di risorse o risorsa. Gli ambiti sono strutturati in una relazione padre-figlio.

![Ambito per un'assegnazione di ruolo](./media/overview/rbac-scope.png)

Quando si concede l'accesso in un ambito padre, tali autorizzazioni vengono ereditate negli ambiti figlio. Ad esempio:

- Se si assegna il ruolo [Proprietario](built-in-roles.md#owner) a un utente nell'ambito del gruppo di gestione, tale utente può gestire tutto in tutte le sottoscrizioni nel gruppo di gestione.
- Se si assegna il ruolo [Lettore](built-in-roles.md#reader) a un gruppo nell'ambito di sottoscrizione, i membri di tale gruppo possono visualizzare ogni gruppo di risorse e risorsa nella sottoscrizione.
- Se si assegna il ruolo [Collaboratore](built-in-roles.md#contributor) a un'applicazione nell'ambito del gruppo di risorse, è possibile gestire risorse di tutti i tipi in quel gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### <a name="role-assignments"></a>Assegnazioni di ruoli

Un'*assegnazione di ruolo* è il processo di associazione di una definizione di ruolo a un utente, un gruppo, un'entità servizio o un'identità gestita in un determinato ambito allo scopo di concedere l'accesso. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo.

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio, al gruppo marketing è stato assegnato il ruolo di [collaboratore](built-in-roles.md#contributor) per il gruppo di risorse pharma-sales. Ciò significa che gli utenti del gruppo marketing possono creare o gestire le risorse di Azure nel gruppo di risorse pharma-sales. Gli utenti marketing non hanno accesso alle risorse all'esterno del gruppo di risorse pharma-sales, a meno che non facciano parte di un'altra assegnazione di ruolo.

![Assegnazione di ruolo per controllare l'accesso](./media/overview/rbac-overview.png)

È possibile creare assegnazioni di ruolo nel portale di Azure, nell'interfaccia della riga di comando di Azure, in Azure PowerShell, tramite gli SDK Azure o le API REST. È possibile avere fino a **2000** assegnazioni di ruolo in ogni sottoscrizione. Questo limite include le assegnazioni di ruolo negli ambiti di sottoscrizione, gruppo di risorse e risorsa. È possibile avere fino a **500** assegnazioni di ruolo in ogni sottoscrizione. Per creare e rimuovere assegnazioni di ruoli, l'utente deve disporre dell'autorizzazione `Microsoft.Authorization/roleAssignments/*`. Questa autorizzazione viene concessa tramite il ruolo [Proprietario](built-in-roles.md#owner) o [Amministratore accessi utente](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Più assegnazioni di ruolo

Cosa succede se si hanno più assegnazioni di ruolo sovrapposte? Il controllo degli accessi in base al ruolo di Azure è un modello additivo, per cui le autorizzazioni effettive corrispondono alla somma delle assegnazioni di ruolo. Nell'esempio seguente a un utente viene concesso il ruolo Collaboratore nell'ambito della sottoscrizione e il ruolo Lettore in un gruppo di risorse. La somma delle autorizzazioni di Collaboratore e di quelle di Lettore rende effettivo il ruolo di Collaboratore per il gruppo di risorse. Quindi, in questo caso, l'assegnazione del ruolo Lettore non ha alcun impatto.

![Più assegnazioni di ruolo](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Assegnazioni di rifiuto

In precedenza, il controllo degli accessi in base al ruolo di Azure era un modello per il solo consenso, ma ora supporta in modo limitato le assegnazioni di rifiuto. Analogamente a un'assegnazione di ruolo, un'*assegnazione di rifiuto* associa un set di azioni di rifiuto a un utente, un gruppo, un'entità servizio o un'identità gestita in un determinato ambito allo scopo di rifiutare l'accesso. Un'assegnazione di ruolo definisce un set di azioni *consentite*, mentre un'assegnazione di rifiuto definisce un set di azioni *non consentite*. In altre parole, le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specificate, anche se un'assegnazione di ruolo concede loro l'accesso. Le assegnazioni di rifiuto hanno la precedenza sulle assegnazioni di ruolo. Per altre informazioni, vedere [Informazioni sulle assegnazioni di rifiuto di Azure](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Come il controllo degli accessi in base al ruolo di Azure determina se un utente può accedere a una risorsa

Di seguito è indicata la procedura generale usata dal controllo degli accessi in base al ruolo per determinare se l'utente ha accesso a una risorsa nel piano di gestione. Può essere utile se si sta tentando di risolvere un problema di accesso.

1. Un utente (o entità servizio) acquisisce un token per Azure Resource Manager.

    Il token include le appartenenze a gruppi dell'utente (incluse le appartenenze a gruppi transitive).

1. L'utente effettua una chiamata API REST ad Azure Resource Manager con il token associato.

1. Azure Resource Manager recupera tutte le assegnazioni di ruolo e le assegnazioni di rifiuto che si applicano alla risorsa su cui viene eseguita l'azione.

1. Azure Resource Manager restringe le assegnazioni di ruolo a quelle che si applicano all'utente o al gruppo corrispondente e determina i ruoli di cui l'utente dispone per questa risorsa.

1. Azure Resource Manager determina se l'azione nella chiamata API è inclusa nei ruoli di cui l'utente dispone per questa risorsa.

1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, Azure Resource Manager controlla se si applica un'assegnazione di rifiuto.

1. Se si applica un'assegnazione di rifiuto, l'accesso viene bloccato. In caso contrario, l'accesso viene concesso.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Visualizzare l'accesso di un utente alle risorse di Azure](check-access.md)
- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
- [Informazioni sui diversi ruoli](rbac-and-directory-admin-roles.md)
- [Cloud Adoption Framework: gestione dell'accesso alle risorse in Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
