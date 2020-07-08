---
title: Configurare i requisiti di complessità delle password
titleSuffix: Azure AD B2C
description: Come configurare i requisiti di complessità delle password specificate dagli utenti in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 165529697949bba0233567b89c5a8a077d48a28b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384023"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurare i requisiti di complessità delle password in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) supporta la modifica dei requisiti di complessità delle password specificate da un utente finale durante la creazione di un account. Per impostazione predefinita, Azure AD B2C usa password di tipo `Strong`. Azure AD B2C supporta anche opzioni di configurazione per controllare la complessità delle password che i clienti possono usare.

## <a name="password-rule-enforcement"></a>Applicazione delle regole delle password

Durante la registrazione o la reimpostazione di una password, l'utente finale deve specificare una password che soddisfi le regole di complessità, che vengono applicate in base al flusso utente di riferimento. È possibile che un flusso utente richieda un pin a quattro cifre durante l'iscrizione mentre un altro flusso utente richiede una stringa di otto caratteri durante l'iscrizione. È possibile, ad esempio, usare un flusso utente con una complessità delle password diversa per gli adulti e per i bambini.

La complessità delle password non viene mai applicata durante l'accesso. Durante la registrazione, infatti, agli utenti non viene mai chiesto di modificare la password perché non soddisfa i requisiti di complessità correnti.

La complessità delle password può essere configurata nei tipi di flussi utente seguenti:

- Flusso utente di iscrizione o accesso
- Flusso utente di reimpostazione delle password

Se si usano criteri personalizzati, è possibile [configurare la complessità delle password nei criteri personalizzati](custom-policy-password-complexity.md).

## <a name="configure-password-complexity"></a>Configurare la complessità delle password

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
3. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente (criteri)** .
2. Selezionare un flusso utente e fare clic su **Proprietà**.
3. In **Complessità password** impostare la complessità delle password per questo flusso utente su **Semplice**, **Alta** o **Personalizzata**.

### <a name="comparison-chart"></a>Grafico di confronto

| Complessità | Descrizione |
| --- | --- |
| Semplice | Una password con un numero di caratteri compreso tra 8 e 64. |
| Assoluta | Una password con un numero di caratteri compreso tra 8 e 64. Richiede almeno tre dei quattro tipi di carattere seguenti: lettere minuscole, lettere maiuscole, numeri e simboli. |
| Personalizzato | Questa opzione offre il massimo controllo sulle regole di complessità delle password.  Consente infatti di configurare una lunghezza personalizzata  o di accettare password solo numeriche (PIN). |

## <a name="custom-options"></a>Opzioni personalizzate

### <a name="character-set"></a>Set di caratteri

Consente di accettare solo cifre (PIN) o l'intero set di caratteri.

- **Solo numeri** consente di immettere solo cifre (0-9) durante la configurazione di una password.
- **Tutti** consente qualsiasi lettera, numero o simbolo.

### <a name="length"></a>Length

Consente di controllare i requisiti di lunghezza della password.

- Il valore **Lunghezza minima** deve essere almeno 4.
- Il valore **Lunghezza massima** deve essere maggiore o uguale alla lunghezza minima e può essere al massimo di 64 caratteri.

### <a name="character-classes"></a>Classi di caratteri

Consente di controllare i diversi tipi di carattere usati nella password.

- **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (2 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga almeno due tipi di carattere, ad esempio un numero e un carattere minuscolo.
- **3 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), il simbolo** garantisce che la password contenga almeno tre tipi di caratteri. ad esempio un numero, un carattere minuscolo e un carattere maiuscolo.
- **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (4 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga tutti i quattro tipi di carattere.

    > [!NOTE]
    > L'opzione **4 of 4** (4 di 4) può determinare frustrazione nell'utente finale. Alcuni studi hanno dimostrato inoltre che questo requisito non migliora l'entropia delle password. Vedere [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) (Linee guida sulle password NIST)
