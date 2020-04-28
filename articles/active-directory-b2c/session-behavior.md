---
title: Configurare il comportamento della sessione-Azure Active Directory B2C | Microsoft Docs
description: Configurare il comportamento della sessione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186812"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurare il comportamento della sessione in Azure Active Directory B2C

Questa funzionalità offre un controllo dettagliato, in base ai [singoli flussi utente](user-flow-overview.md), per gli elementi seguenti:

- Durate delle sessioni delle applicazioni Web gestite da Azure AD B2C.
- Comportamento dell'accesso Single Sign-On (SSO) in più app e flussi utente nel tenant di Azure AD B2C.

Queste impostazioni non sono disponibili per i flussi utente di reimpostazione della password.

Azure AD B2C supporta il [protocollo di autenticazione OpenID Connect](openid-connect.md) per abilitare l'accesso sicuro alle applicazioni Web. È possibile usare le proprietà seguenti per gestire le sessioni delle applicazioni Web:

## <a name="session-behavior-properties"></a>Proprietà del comportamento della sessione

- **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
    - Impostazione predefinita: 1440 minuti.
    - Valore minimo (inclusivo): 15 minuti.
    - Valore massimo (inclusivo): 1440 minuti.
- **Timeout della sessione dell'app Web**: se questa opzione è impostata su **Assoluto**, l'utente dovrà ripetere l'autenticazione dopo la scadenza del periodo di tempo specificato da **Durata della sessione dell'app Web (minuti)**. Se l'opzione è impostata su **Continuo** (impostazione predefinita), l'utente rimane connesso, purché sia continuamente attivo nell'applicazione Web.
- **Configurazione di Single Sign-on** Se si dispone di più applicazioni e flussi utente nel tenant B2C, è possibile gestire le interazioni degli utenti tra di essi usando la proprietà di **configurazione Single Sign-on** . È possibile impostare la proprietà su uno dei valori seguenti:
    - **Tenant**: questa è l'impostazione predefinita. L'uso di questa impostazione consente a più applicazioni e flussi utente del tenant di B2C di condividere la stessa sessione utente. Ad esempio, quando un utente accede a un'applicazione, può accedere contemporaneamente anche a un'altra applicazione, Contoso Pharmacy, senza alcun problema.
    - **Applicazione**: questa impostazione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendentemente dalle altre applicazioni. Ad esempio, un utente può accedere a Contoso Pharmacy, usando le stesse credenziali, anche se ha già eseguito l'accesso a Contoso Shopping, un'altra applicazione nello stesso tenant di B2C.
    - **Criterio**: questa impostazione consente di mantenere una sessione utente esclusivamente per un flusso utente, indipendentemente dalle applicazioni che lo usano. Ad esempio, se un utente ha già effettuato l'accesso e completato un passaggio di autenticazione a più fattori (MFA), otterrà l'accesso a parti a sicurezza più elevata di più applicazioni, purché la sessione associata al flusso utente non scada.
    - **Disabilitato**: questa impostazione impone all'utente di eseguire il percorso utente completo a ogni esecuzione del flusso utente.

I seguenti casi d'uso sono abilitati usando queste proprietà:

- Soddisfare i requisiti di sicurezza e conformità del settore, impostando durate appropriate per la sessione dell'applicazione Web.
- Imporre l'autenticazione dopo un periodo di tempo specifico durante l'interazione di un utente con una parte a sicurezza elevata dell'applicazione Web.

## <a name="configure-the-properties"></a>Configurare le proprietà

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **flussi utente (criteri)**.
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. Configurare la **durata della sessione dell'app Web (minuti)**, il **timeout della sessione dell'app Web**, la **configurazione dell'accesso Single Sign-on**e **richiedere il token ID nelle richieste di disconnessione** , se necessario.

    ![Impostazioni delle proprietà di comportamento della sessione nell'portale di Azure](./media/session-behavior/session-behavior.png)

8. Fare clic su **Salva**.
