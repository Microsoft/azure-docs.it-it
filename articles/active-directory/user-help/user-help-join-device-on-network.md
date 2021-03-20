---
title: Aggiungere il dispositivo aziendale alla rete dell'organizzazione - AD
description: Informazioni su come aggiungere il dispositivo aziendale alla rete dell'organizzazione.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536971"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Aggiungere il dispositivo aziendale alla rete dell'organizzazione
Aggiungere il dispositivo Windows 10 di proprietà dell'azienda alla rete dell'organizzazione in modo da poter accedere a risorse potenzialmente limitate.

## <a name="what-happens-when-you-join-your-device"></a>Cosa accade quando si aggiunge il dispositivo
Mentre si aggiunge il dispositivo Windows 10 alla rete dell'organizzazione, si verificheranno le azioni seguenti:

- Windows registra il dispositivo nella rete dell'organizzazione, consentendo l'accesso alle risorse con l'account personale. Dopo aver registrato il dispositivo, Windows aggiunge il dispositivo alla rete, in modo da poter usare nome utente e password dell'organizzazione per eseguire l'accesso e accedere alle risorse limitate.

- Facoltativamente, in base alle scelte dell'organizzazione, potrebbe essere richiesto di configurare la verifica in due passaggi tramite [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) oppure [info di sicurezza](./security-info-setup-signin.md).

- Facoltativamente, in base alle scelte dell'organizzazione, il dispositivo potrebbe essere registrato automaticamente in una soluzione di gestione di dispositivi mobili, ad esempio Microsoft Intune. Per altre informazioni sulla registrazione in Microsoft Intune, vedere [Registrare il dispositivo in Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Verrà eseguito il processo di accesso, usando l'accesso automatico con l'account aziendale.

## <a name="to-join-a-brand-new-windows-10-device"></a>Per aggiungere un nuovo dispositivo Windows 10
Se il dispositivo è nuovo e non è ancora stato configurato, è possibile usare il processo Configurazione guidata di Windows per aggiungere il dispositivo alla rete.

1. Avviare il nuovo dispositivo e avviare la Configurazione guidata.

2. Nella schermata **Accedi con Microsoft** digitare l'indirizzo di posta elettronica dell'account aziendale o dell'istituto di istruzione.

    ![Schermata di accesso con indirizzo di posta elettronica](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Nella schermata **Immetti la password** digitare la password.

    ![Schermata Immetti la password](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Nel dispositivo mobile approvare il dispositivo in modo che possa accedere all'account. 

    ![Screenshot che mostra la schermata di notifica "immettere la password".](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Completare il processo di Configurazione guidata, incluse le impostazioni per la privacy e la configurazione di Windows Hello (se necessario).

    Il dispositivo risulta ora aggiunto alla rete dell'organizzazione.

## <a name="to-make-sure-youre-joined-new-device"></a>Per assicurarsi di essere Uniti (nuovo dispositivo)
È possibile verificare che il dispositivo sia stato aggiunto controllando le impostazioni.

1. Aprire **Impostazioni** e quindi selezionare **Account**.

    ![Account nella schermata Impostazioni](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selezionare **Accedi all'ufficio o all'Istituto di istruzione** e verificare che venga visualizzato un testo simile **a, connesso a *\<your_organization>* Azure ad**.

    ![Screenshot che mostra la finestra "accedi all'azienda o all'Istituto di istruzione" con l'account "connesso a (organizzazione) Azure AD" selezionato.](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Per aggiungere un dispositivo Windows 10 già configurato
Se si possiede il dispositivo da tempo ed è già stato configurato, è possibile seguire questa procedura per aggiungere il dispositivo alla rete.

1. Aprire **Impostazioni** e quindi selezionare **Account**.

2. Selezionare **Accedi all'azienda o all'istituto di istruzione** e quindi selezionare **Connetti**.

    ![Collegamenti Accedi all'azienda o all'istituto di istruzione e Connetti](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Nella schermata **Configura un account aziendale o dell'istituto di istruzione** fare clic su **Aggiungi il dispositivo ad Azure Active Directory**.

    ![Schermata Configura un account aziendale o dell'istituto di istruzione](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Nella schermata **Esegui l'accesso** digitare l'indirizzo di posta elettronica (ad esempio, alain@contoso.com) e quindi selezionare **Avanti**.

    ![Schermata Esegui l'accesso](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Nella schermata **Immetti la password** digitare la password e quindi fare clic su **Accedi**.

    ![Immettere la password](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Nel dispositivo mobile approvare il dispositivo in modo che possa accedere all'account. 

    ![Schermata di notifica sul dispositivo mobile](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Nella schermata **Verifica che questa sia la tua organizzazione** controllare le informazioni per assicurarsi che siano corrette e quindi selezionare **Aggiungi**.

    ![Schermata Verifica che questa sia la tua organizzazione](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Nella schermata **Configurazione completata** fare clic su **Fine**.

    ![Schermata Configurazione completata](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Per verificare che sia aggiunto
È possibile verificare che il dispositivo sia stato aggiunto controllando le impostazioni.

1. Aprire **Impostazioni** e quindi selezionare **Account**.

    ![Account nella schermata Impostazioni](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selezionare **Accedi all'ufficio o all'Istituto di istruzione** e verificare che venga visualizzato un testo simile **a, connesso a *\<your_organization>* Azure ad**.

    ![Schermata Accedi all'azienda o all'istituto di istruzione con l'account contoso connesso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere aggiunto il dispositivo alla rete dell'organizzazione, è necessario essere in grado di accedere a tutte le risorse usando l'account aziendale o dell'istituto di istruzione.

- Se l'organizzazione richiede la registrazione del dispositivo personale, ad esempio il telefono, vedere [Registrare il dispositivo personale nella rete dell'organizzazione](user-help-register-device-on-network.md).

- Se l'organizzazione viene gestita con Microsoft Intune e si hanno dubbi su iscrizione, accesso o si vuole segnalare qualsiasi altro problema correlato a Intune, vedere la [Guida per l'utente di Intune](/intune-user-help/use-managed-devices-to-get-work-done).