---
title: Ruoli di amministratore personalizzato in Azure Active Directory | Microsoft Docs
description: Visualizzare in anteprima i ruoli di Azure AD personalizzati per delegare la gestione delle identità. Gestire i ruoli di Azure in portale di Azure, PowerShell o API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085111"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Ruoli di amministratore personalizzato in Azure Active Directory (anteprima)

Questo articolo descrive come comprendere Azure AD ruoli personalizzati in Azure Active Directory (Azure AD) con il controllo degli accessi in base al ruolo e gli ambiti delle risorse. I ruoli di Azure AD personalizzati ripartiscono le autorizzazioni sottostanti dei [ruoli predefiniti, in](directory-assign-admin-roles.md)modo che sia possibile creare e organizzare ruoli personalizzati. Questo approccio consente di concedere l'accesso in modo più granulare rispetto ai ruoli predefiniti, ogni volta che sono necessari. Questa prima versione di Azure AD ruoli personalizzati include la possibilità di creare un ruolo per assegnare le autorizzazioni per la gestione delle registrazioni delle app. Nel corso del tempo verranno aggiunte ulteriori autorizzazioni per le risorse dell'organizzazione, come applicazioni aziendali, utenti e dispositivi.  

Inoltre, Azure AD ruoli personalizzati supportano le assegnazioni in base alle singole risorse, oltre alle assegnazioni più tradizionali a livello di organizzazione. Questo approccio offre la possibilità di concedere l'accesso per gestire alcune risorse (ad esempio, una registrazione dell'app) senza concedere l'accesso a tutte le risorse (tutte le registrazioni dell'app).

Azure AD controllo degli accessi in base al ruolo è una funzionalità di anteprima pubblica di Azure AD ed è disponibile con qualsiasi piano di licenza Azure AD a pagamento. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Informazioni sul controllo degli accessi in base al ruolo Azure AD

La concessione di autorizzazioni tramite ruoli di Azure AD personalizzati è un processo in due passaggi che prevede la creazione di una definizione di ruolo personalizzata e la successiva assegnazione tramite un'assegnazione di ruolo. Una definizione di ruolo personalizzata è una raccolta di autorizzazioni aggiunte da un elenco di set di impostazioni. Queste autorizzazioni sono le stesse autorizzazioni utilizzate nei ruoli predefiniti.  

Una volta creata la definizione di ruolo, è possibile assegnarla a un utente creando un'assegnazione di ruolo. Un'assegnazione di ruolo concede all'utente le autorizzazioni in una definizione di ruolo in un ambito specificato. Questo processo in due passaggi consente di creare una singola definizione di ruolo e di assegnarla più volte in ambiti diversi. Un ambito definisce il set di risorse Azure AD cui il membro del ruolo ha accesso. L'ambito più comune è l'ambito a livello di organizzazione. Un ruolo personalizzato può essere assegnato a livello di organizzazione, vale a dire che il membro del ruolo dispone delle autorizzazioni di ruolo per tutte le risorse dell'organizzazione. È anche possibile assegnare un ruolo personalizzato a un ambito di oggetti. Un esempio di ambito di un oggetto è costituito da una singola applicazione. Lo stesso ruolo può essere assegnato a un utente su tutte le applicazioni dell'organizzazione e quindi a un altro utente con un ambito solo dell'app Contoso Expense Reports.  

Azure AD ruoli predefiniti e personalizzati operano su concetti simili al [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). La [differenza tra questi due sistemi di controllo degli accessi in base al ruolo](../../role-based-access-control/rbac-and-directory-admin-roles.md) è che il controllo degli accessi in base al ruolo di Azure controlla l'accesso alle risorse di Azure, ad esempio le macchine virtuali o l'archiviazione tramite Gestione risorse di azure e Azure ad i ruoli personalizzati controllano l'accesso Azure AD alle API Graph risorse Entrambi i sistemi sfruttano il concetto di definizioni di ruolo e assegnazioni di ruolo.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Come Azure AD determina se un utente ha accesso a una risorsa

Di seguito sono riportati i passaggi di alto livello che Azure AD utilizza per determinare se si dispone dell'accesso a una risorsa di gestione. Usare queste informazioni per risolvere i problemi di accesso.

1. Un utente (o un'entità servizio) acquisisce un token per l'endpoint Microsoft Graph o Azure AD Graph.

1. L'utente effettua una chiamata API per Azure Active Directory (Azure AD) tramite Microsoft Graph o Azure AD Graph usando il token emesso.

1. A seconda della circostanza, Azure AD esegue una delle operazioni seguenti:

    - Valuta le appartenenze ai ruoli dell'utente in base all' [attestazione WIDS](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) nel token di accesso dell'utente.
    - Recupera tutte le assegnazioni di ruolo valide per l'utente, direttamente o tramite l'appartenenza al gruppo, alla risorsa in cui viene eseguita l'azione.

1. Azure AD determina se l'azione nella chiamata API è inclusa nei ruoli dell'utente per questa risorsa.
1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, l'accesso viene concesso.

### <a name="role-assignments"></a>Assegnazioni di ruoli

Un'assegnazione di ruolo è l'oggetto che connette una definizione di ruolo a un utente in un determinato ambito per concedere Azure AD accesso alle risorse. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo. Al suo nucleo, un'assegnazione di ruolo è costituita da tre elementi:

- Utente (persona che ha un profilo utente in Azure Active Directory)
- Definizione di ruolo
- Ambito delle risorse

È possibile [creare assegnazioni di ruolo](roles-create-custom.md) usando il portale di Azure, Azure ad PowerShell o API Graph. È anche possibile [visualizzare le assegnazioni per un ruolo personalizzato](roles-view-assignments.md#view-the-assignments-of-a-role).

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio, a Chris Green è stato assegnato il ruolo personalizzato amministratore registrazione app nell'ambito della registrazione dell'app Contoso Widget Builder. L'assegnazione concede a Chris le autorizzazioni del ruolo di amministratore della registrazione dell'app solo per la registrazione di questa app specifica.

![L'assegnazione di ruolo è il modo in cui vengono applicate le autorizzazioni e include tre parti](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entità di sicurezza

Un'entità di sicurezza rappresenta l'utente a cui deve essere assegnato l'accesso alle risorse Azure AD. Un *utente* è una persona che ha un profilo utente in Azure Active Directory.

### <a name="role"></a>Ruolo

Una definizione di ruolo, o Role, è una raccolta di autorizzazioni. In una definizione di ruolo sono elencate le operazioni che possono essere eseguite sulle risorse Azure AD, ad esempio creazione, lettura, aggiornamento ed eliminazione. Esistono due tipi di ruoli in Azure AD:

- Ruoli predefiniti creati da Microsoft che non possono essere modificati.
- Ruoli personalizzati creati e gestiti dall'organizzazione.

### <a name="scope"></a>Scope

Un ambito è la restrizione delle azioni consentite a una determinata risorsa Azure AD come parte di un'assegnazione di ruolo. Quando si assegna un ruolo, è possibile specificare un ambito che limita l'accesso dell'amministratore a una risorsa specifica. Ad esempio, se si desidera concedere a uno sviluppatore un ruolo personalizzato, ma solo per gestire una registrazione di applicazione specifica, è possibile includere la registrazione dell'applicazione specifica come ambito nell'assegnazione di ruolo.

  > [!Note]
  > I ruoli personalizzati possono essere assegnati nell'ambito della directory e nell'ambito della risorsa. Non possono ancora essere assegnati in ambito di unità amministrative.
  > I ruoli predefiniti possono essere assegnati nell'ambito della directory e, in alcuni casi, nell'ambito dell'unità amministrativa. Non è ancora possibile assegnarli a Azure AD ambito di risorse.

## <a name="required-license-plan"></a>Piano di licenza obbligatorio

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare assegnazioni di ruolo personalizzate usando [il portale di Azure, Azure ad PowerShell e API Graph](roles-create-custom.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](roles-view-assignments.md#view-assignments-of-single-application-scope)
