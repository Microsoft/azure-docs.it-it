---
title: Autorizzazioni in Azure Active Directory | Microsoft Docs
description: Informazioni sulle autorizzazioni in Azure Active Directory e su come usarle.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c600e1fddc0089a508ff0cfebbbb3476f3a90008
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117618"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Autorizzazioni e consenso nell'endpoint v1.0 di Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) fa ampio uso delle autorizzazioni per i flussi OAuth e OpenID Connect (OIDC). Quando l'app riceve un token di accesso da Azure AD, il token di accesso include le attestazioni che descrivono le autorizzazioni dell'app in relazione a una particolare risorsa.

Le *autorizzazioni*, note anche come *ambiti*, semplificano l'autorizzazione per la risorsa perché la risorsa deve solo controllare che il token contenga l'autorizzazione appropriata per qualsiasi API chiamata dall'app.

## <a name="types-of-permissions"></a>Tipi di autorizzazioni

Azure AD definisce due tipi di autorizzazioni:

* **Autorizzazioni delegate**: usati dalle app con un utente connesso. Per queste app, l'utente o un amministratore fornisce il consenso per le autorizzazioni richieste dall'app e all'app viene delegata l'autorizzazione per agire per conto dell'utente connesso quando vengono effettuate chiamate a un'API. A seconda dell'API, l'utente potrebbe non essere in grado di acconsentire direttamente all'API e [richiederebbe invece a un amministratore di fornire il consenso dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Autorizzazioni dell'applicazione**: usate dalle app che vengono eseguite senza un utente connesso, ad esempio le app eseguite come servizi in background o daemon. Le autorizzazioni dell'applicazione possono essere [consentite solo dagli amministratori](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) perché sono in genere potenti e consentono l'accesso ai dati attraverso i limiti utente o ai dati che altrimenti sarebbero limitati agli amministratori. Gli utenti definiti come proprietari dell'applicazione della risorsa (ad esempio, l'API che pubblica le autorizzazioni) possono concedere anche le autorizzazioni dell'applicazione per le API di loro proprietà.

Le autorizzazioni valide sono le autorizzazioni che l'app avrà quando effettuerà le richieste a un'API. 

* Per le autorizzazioni delegate, le autorizzazioni valide dell'app sono costituite dall'intersezione con meno privilegi tra le autorizzazioni delegate concesse all'app (tramite il consenso) e i privilegi dell'utente attualmente connesso. L'app non può mai avere più privilegi rispetto all'utente connesso. All'interno delle organizzazioni, i privilegi dell'utente connesso possono essere determinati da criteri o dall'appartenenza a uno o più ruoli di amministratore. Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
    Si supponga, ad esempio, che all'app sia stata concessa l'autorizzazione delegata `User.ReadWrite.All` in Microsoft Graph. Questa autorizzazione concede nominalmente all'app l'autorizzazione per leggere e aggiornare il profilo di ogni utente in un'organizzazione. Se l'utente connesso è un amministratore globale, l'app sarà in grado di aggiornare il profilo di tutti gli utenti dell'organizzazione. Se tuttavia l'utente connesso non ha un ruolo di amministratore, l'app sarà in grado di aggiornare solo il profilo dell'utente connesso. Non sarà in grado di aggiornare i profili di altri utenti nell'organizzazione, perché l'utente per conto del quale è autorizzata ad agire non dispone di tali privilegi.
* Per le autorizzazioni dell'applicazione, le autorizzazioni valide dell'app corrispondono al livello completo di privilegi impliciti nell'autorizzazione. Un'app che ha l'autorizzazione dell'applicazione `User.ReadWrite.All` può ad esempio aggiornare il profilo di tutti gli utenti dell'organizzazione.

## <a name="permission-attributes"></a>Attributi delle autorizzazioni
Le autorizzazioni in Azure AD hanno diverse proprietà che aiutano utenti, amministratori o sviluppatori di app a prendere decisioni informate sugli elementi a cui l'autorizzazione concede l'accesso.

> [!NOTE]
> È possibile visualizzare le autorizzazioni esposte da un'entità servizio o un'applicazione Azure AD usando il portale di Azure o PowerShell. Provare a usare questo script per visualizzare le autorizzazioni esposte da Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nome proprietà | Descrizione | Esempio |
| --- | --- | --- |
| `ID` | Valore GUID che identifica in modo univoco l'autorizzazione. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Indica se l'autorizzazione è disponibile per l'uso. | true |
| `Type` | Indica se l'autorizzazione richiede il consenso dell'utente o dell'amministratore. | Utente |
| `AdminConsentDescription` | Descrizione che viene visualizzata per gli amministratori durante l'esperienza di consenso dell'amministratore. | Consente all'app di leggere i messaggi di posta elettronica nelle cassette postali degli utenti. |
| `AdminConsentDisplayName` | Nome descrittivo visualizzato per gli amministratori durante l'esperienza di consenso dell'amministratore. | Leggi i messaggi di un altro utente |
| `UserConsentDescription` | Descrizione che viene visualizzata per gli utenti durante l'esperienza di consenso dell'utente. |  Consente all'app di leggere i messaggi di posta elettronica nella cassetta postale personale. |
| `UserConsentDisplayName` | Nome descrittivo visualizzato per gli utenti durante l'esperienza di consenso dell'utente. | Lettura dei messaggi |
| `Value` | Stringa usata per identificare l'autorizzazione durante i flussi di autorizzazione OAuth 2.0. `Value` può anche essere combinata con la stringa dell'URI ID app per formare un nome di autorizzazione completo. | `Mail.Read` |

## <a name="types-of-consent"></a>Tipi di consenso

Le applicazioni in Azure AD si basano sul consenso per ottenere l'accesso alle API o alle risorse necessarie. Ci sono diversi tipi di consenso che l'app deve riconoscere per funzionare correttamente. Se si definiscono le autorizzazioni, è anche necessario comprendere in che modo gli utenti potranno accedere all'app o all'API.

* **Consenso dell'utente statico**: si verifica automaticamente durante il [flusso di autorizzazione OAuth 2.0](v1-protocols-oauth-code.md#request-an-authorization-code) quando si specifica la risorsa con cui l'app vuole interagire. Nello scenario di consenso dell'utente statico, l'app deve avere già specificato tutte le autorizzazioni necessarie nella configurazione dell'app nel portale di Azure. Se l'utente (o l'amministratore, a seconda dei casi) non ha fornito il consenso per l'app, Azure AD chiederà all'utente di fornire il consenso in questo momento. 

    Leggere altre informazioni sulla registrazione di un'app Azure AD che richiede l'accesso a un set statico di API.
* **Consenso dell'utente dinamico**: si tratta di una funzionalità del modello di app Azure AD v2. In questo scenario l'app richiede un set di autorizzazioni necessarie nel [flusso di autorizzazione OAuth 2.0 per le app v2](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent). Se l'utente non ha già fornito il consenso, verrà chiesto di fornirlo in questo momento. [Altre informazioni sul consenso dinamico](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Il consenso dinamico può risultare comodo, ma presenta un'importante sfida per le autorizzazioni che richiedono il consenso dell'amministratore, perché nell'esperienza di consenso dell'amministratore tali autorizzazioni non sono note al momento del consenso. Se sono necessarie le autorizzazioni con privilegio di amministratore o se l'app usa il consenso dinamico, è necessario registrare tutte le autorizzazioni nel portale di Azure (non solo il sottoinsieme delle autorizzazioni che richiedono il consenso dell'amministratore). Ciò consente agli amministratori del tenant di fornire il consenso per conto di tutti gli utenti.
  
* **Consenso dell'amministratore**: è necessario quando l'app richiede l'accesso a determinate autorizzazioni con privilegi elevati. Il consenso dell'amministratore garantisce che gli amministratori dispongano di ulteriori controlli prima di autorizzare le app o gli utenti ad accedere a dati dell'organizzazione che richiedono privilegi elevati. [Altre informazioni su come fornire il consenso dell'amministratore](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Procedure consigliate

### <a name="client-best-practices"></a>Procedure consigliate per il client

- Richiedere solo le autorizzazioni necessarie all'app. Le app con un numero eccessivo di autorizzazioni potrebbero comportare l'esposizione dei dati utente, se vengono compromesse.
- Scegliere tra autorizzazioni delegate e autorizzazioni dell'applicazione in base allo scenario supportato dall'app.
    - Usare sempre le autorizzazioni delegate se la chiamata viene eseguita per conto di un utente.
    - Usare le autorizzazioni dell'applicazione solo se l'app è non interattiva e non effettua chiamate per conto di utenti specifici. Le autorizzazioni dell'applicazione hanno privilegi elevati e devono essere usate solo quando è strettamente necessario.
- Quando si usa un'app basata sull'endpoint v2.0, impostare sempre le autorizzazioni statiche (quelle specificate nella registrazione dell'applicazione) come superset delle autorizzazioni dinamiche richieste in fase di esecuzione (quelle specificate nel codice e inviate come parametri di query nella richiesta di autorizzazione) in modo che gli scenari come il consenso dell'amministratore funzionino correttamente.

### <a name="resourceapi-best-practices"></a>Procedure consigliate per risorse/API

- Le risorse che espongono le API devono definire autorizzazioni specifiche dei dati o delle azioni che stanno proteggendo. Seguire questa procedura consigliata aiuta a garantire che i client non ottengano autorizzazioni di accesso ai dati non necessarie e che gli utenti siano ben informati in merito ai dati per i quali forniscono il consenso.
- Le risorse devono definire esplicitamente le autorizzazioni `Read` e `ReadWrite` separatamente.
- Le risorse devono contrassegnare le autorizzazioni che consentono l'accesso ai dati di più utenti come autorizzazioni `Admin`.
- Le risorse devono seguire il criterio di denominazione `Subject.Permission[.Modifier]`, dove:
  - `Subject` corrisponde al tipo di dati disponibili
  - `Permission` corrisponde all'azione che un utente può eseguire su tali dati
  - `Modifier` viene usato facoltativamente per descrivere le specializzazioni di un'altra autorizzazione
    
    Ad esempio:
  - Mail.Read: consente agli utenti di leggere i messaggi di posta.
  - Mail.ReadWrite: consente agli utenti di leggere o scrivere messaggi di posta.
  - Mail.ReadWrite.All: consente a un amministratore o a un utente di accedere a tutti i messaggi di posta nell'organizzazione.