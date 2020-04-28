---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184351"
---
#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Nella pagina Panoramica dell' **app registrata** selezionare **Impostazioni**.
1. In **accesso all'API**selezionare **autorizzazioni necessarie**.
1. Selezionare **Microsoft Graph**.
1. In **Autorizzazioni applicazione**selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Leggi tutti i dati del registro di controllo**: selezionare questa autorizzazione per la lettura dei log di controllo della directory.
    * **Lettura e scrittura dei dati della directory**: selezionare questa autorizzazione per la migrazione degli utenti o gli scenari di gestione degli utenti.
    * **Leggere e scrivere i criteri del Framework di attendibilità dell'organizzazione**: selezionare questa autorizzazione per gli scenari di integrazione continua/recapito continuo (ci/CD). Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Salva**.
1. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **API Microsoft** , quindi selezionare **Microsoft Graph**.
1. Selezionare **Autorizzazioni applicazione**.
1. Espandere il gruppo di autorizzazioni appropriato e selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **AuditLog** > **auditlog. Read. All**: per la lettura dei log di controllo della directory.
    * **Directory**directory **. ReadWrite. All**: per gli scenari di migrazione utenti o gestione utenti. > 
    * **Policy**Policy policy **. ReadWrite. TrustFramework**: per gli scenari di integrazione continua/recapito continuo (ci/CD). >  Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna**, quindi verificare che "concesso per..." viene visualizzato in **stato**. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.
