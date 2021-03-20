---
title: Configurare un dispositivo mobile come metodo di verifica a due fattori - Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare un dispositivo mobile come metodo di verifica a due fattori.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530256"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Configurare un dispositivo mobile come metodo di verifica a due fattori

È possibile configurare un dispositivo mobile per usarlo come metodo di verifica a due fattori. Il telefono cellulare può ricevere un SMS con un codice di verifica oppure una telefonata.

>[!Note]
> Se l'opzione Telefono per l'autenticazione è disattivata, è possibile che l'organizzazione non consenta di usare un numero di telefono o un SMS per la verifica. In questo caso, sarà necessario selezionare un altro metodo oppure contattare l'amministratore per ottenere supporto.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Configurare il dispositivo mobile per usare un SMS come metodo di verifica

1. Nella pagina **verifica aggiuntiva di sicurezza** selezionare **telefono per l'autenticazione** dall'area **passaggio 1: modalità di contatto dell'utente** , selezionare il paese o la regione dall'elenco a discesa, quindi digitare il numero di telefono del dispositivo mobile.

2. Selezionare **Invia un codice tramite messaggio di testo** nell'area **Metodo** e quindi **Avanti**.

    ![Screenshot che mostra la pagina "verifica aggiuntiva di sicurezza", con l'opzione "telefono per l'autenticazione" e "Invia un codice per SMS" selezionato.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Digitare il codice di verifica del messaggio di testo inviato da Microsoft nel **passaggio 2: è stato inviato un messaggio di testo all'** area del telefono e quindi selezionare **Verifica**.

    ![Pagina Verifica di sicurezza aggiuntiva, con Telefono per l'autenticazione e SMS](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. Dall'area **passaggio 3: continua a usare le applicazioni esistenti** , copiare la password dell'app specificata e incollarla in un luogo sicuro.

    ![Area delle password per le app nella pagina Verifica di sicurezza aggiuntiva](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Per informazioni su come usare la password per le app meno recenti, vedere [Gestire le password delle app](multi-factor-authentication-end-user-app-passwords.md). È necessario usare le password per le app solo se si continuano a usare app meno recenti che non supportano la verifica a due fattori.

5. Selezionare **Operazione completata**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Configurare il dispositivo mobile per ricevere una telefonata

1. Nella pagina **verifica aggiuntiva di sicurezza** selezionare **telefono per l'autenticazione** dall'area **passaggio 1: modalità di contatto dell'utente** , selezionare il paese o la regione dall'elenco a discesa, quindi digitare il numero di telefono del dispositivo mobile.

2. Selezionare **Chiama** nell'area **Metodo** e quindi **Avanti**.

    ![Pagina Verifica di sicurezza aggiuntiva, con Telefono per l'autenticazione e telefonata](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Si riceverà una telefonata da Microsoft, in cui verrà richiesto di premere cancelletto (#) sul dispositivo mobile per la verifica della propria identità.

    ![Test del numero di telefono specificato](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. Dall'area **passaggio 3: continua a usare le applicazioni esistenti** , copiare la password dell'app specificata e incollarla in un luogo sicuro.

    ![Area delle password per le app nella pagina Verifica di sicurezza aggiuntiva](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Per informazioni su come usare la password per le app meno recenti, vedere [Gestire le password delle app](multi-factor-authentication-end-user-app-passwords.md). È necessario usare le password per le app solo se si continuano a usare app meno recenti che non supportano la verifica a due fattori.

5. Selezionare **Operazione completata**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il metodo di verifica a due fattori, è possibile aggiungere altri metodi, gestire le impostazioni e le password per le app, effettuare l'accesso oppure ottenere informazioni su alcuni problemi comuni relativi alla verifica a due fattori.

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Gestire le password delle app](multi-factor-authentication-end-user-app-passwords.md)

- [Accedere usando la verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risolvere i problemi relativi alla verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md)
