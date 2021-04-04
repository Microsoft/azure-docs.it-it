---
title: Come gestire le password per le app - Azure Active Directory | Microsoft Docs
description: Informazioni sulle password per le app e sul loro utilizzo nell'ambito della verifica in due passaggi.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179423"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gestire le password per le app per la verifica in due passaggi

> [!Important]
>L'amministratore potrebbe non consentire di usare le password delle app. Se l'opzione **Password dell'app** non è visibile, non è disponibile nell'organizzazione.

Quando si usano password per le app, è importante tenere presente quanto segue:

- Le password dell'app vengono generate automaticamente e devono essere create e immesse una volta per ogni app.

- Il limite è di 40 password per utente. Se si prova a crearne un'altra oltre questo limite, verrà chiesto di eliminare una password esistente prima di poter creare quella nuova.

    >[!Note]
    >I client di Office 2013, tra cui Outlook, supportano i nuovi protocolli di autenticazione e possono essere usati con la verifica in due passaggi. Questo supporto significa che dopo l'attivazione della verifica in due passaggi non sono più necessarie le password per le app per i client Office 2013. Per altre informazioni, vedere l'articolo [Funzionamento dell'autenticazione moderna per le applicazioni client di Office 2013 e Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Creare nuove password per l'app

Durante il processo iniziale di registrazione della verifica a due fattori, viene fornita una sola password per l'app. Se ne serve più di una, sarà necessario crearle autonomamente. È possibile creare password per le app da più aree, in base alla configurazione della verifica a due fattori nell'organizzazione. Per altre informazioni sulla registrazione per l'uso della verifica a due fattori con l'account aziendale o dell'istituto di istruzione, vedere la pagina di [panoramica sulla verifica a due fattori e sull'account aziendale o dell'istituto di istruzione](multi-factor-authentication-end-user-first-time.md) e gli articoli correlati.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Dove creare ed eliminare password per le app

È possibile creare ed eliminare le password per le app in base all'uso della verifica a due fattori:

- **L'organizzazione usa la verifica a due fattori e la pagina Verifica di sicurezza aggiuntiva**. Se si usa l'account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, con la verifica a due fattori all'interno dell'organizzazione, è possibile gestire le password per le app dalla pagina [Verifica di sicurezza aggiuntiva](https://account.activedirectory.windowsazure.com/Proofup.aspx). Per istruzioni dettagliate, vedere [Creare ed eliminare password per le app usando la pagina Verifica di sicurezza aggiuntiva](#create-and-delete-app-passwords-from-the-additional-security-verification-page) in questo articolo.

- **L'organizzazione usa la verifica a due fattori e il portale di Office 365**. Se si usa l'account aziendale o dell'Istituto di istruzione (ad esempio, alain@contoso.com ), la verifica a due fattori e Microsoft 365 app nell'organizzazione, è possibile gestire le password dell'app dalla [pagina del portale di Office 365](https://www.office.com). Per istruzioni dettagliate, vedere [Creare ed eliminare password per le app usando il portale di Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) in questo articolo.

- **Si usa la verifica a due fattori con un account Microsoft personale**. Se si usa un account Microsoft personale, ad esempio alain@outlook.com, con la verifica a due fattori, è possibile gestire le password per le app dalla pagina [Informazioni di base sulla sicurezza](https://account.microsoft.com/security/). Per istruzioni dettagliate, vedere [Utilizzo delle password con le app che non supportano la verifica in due passaggi](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Creare ed eliminare le password per le app nella pagina Verifica di sicurezza aggiuntiva

È possibile creare ed eliminare le password per le app dalla pagina **Verifica di sicurezza aggiuntiva** per l'account aziendale o dell'istituto di istruzione.

1. Accedere alla pagina [Verifica di sicurezza aggiuntiva](https://account.activedirectory.windowsazure.com/Proofup.aspx), quindi selezionare **Password dell'app**.

    ![Pagina Password dell'app, con la scheda Password dell'app evidenziata](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selezionare **Crea**, digitare il nome dell'app che richiede la password e quindi selezionare **Avanti**.

    ![Pagina Crea password dell'app, con il nome dell'app che richiede la password](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copiare la password dalla pagina **Password dell'app** e quindi selezionare **Chiudi**.

    ![Pagina Password dell'app con la password per l'app specificata](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Nella pagina **password app** verificare che l'app sia elencata.

    ![Pagina Password dell'app, con la nuova app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Aprire l'app per cui è stata creata la password, ad esempio Outlook 2010, quindi incollare la password per l'app quando viene richiesta. Questa operazione deve essere eseguita una sola volta per ogni app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Per eliminare una password dell'app tramite la pagina Password dell'app

1. Nella pagina **password app** selezionare **Elimina** accanto alla password dell'app che si vuole eliminare.

   ![Screenshot che mostra l'eliminazione di una password dell'app nella pagina password dell'app](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selezionare **Sì** per confermare l'eliminazione della password e quindi **Chiudi**.

    La password viene eliminata.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Creare ed eliminare password per le app tramite il portale di Office 365

Se si usa la verifica in due passaggi con l'account aziendale o dell'Istituto di istruzione e le app Microsoft 365, è possibile creare ed eliminare le password dell'app tramite il portale di Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Per creare password per le app tramite il portale di Office 365

1. Accedere all'account aziendale o dell'Istituto di istruzione, passare alla [pagina account personale](https://myaccount.microsoft.com)e selezionare **info di sicurezza**.

    ![Portale di Office che mostra le informazioni di sicurezza scheda](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. Selezionare **Aggiungi metodo**, scegliere **password app** dall'elenco a discesa, quindi fare clic su **Aggiungi**.

    ![Pagina delle informazioni di sicurezza con l'elenco Aggiungi un metodo drowpdown](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. Immettere un nome per la password dell'app e quindi fare clic su **Avanti**.

    ![Pagina creare password dell'app, con il nome della password dell'app](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. Copiare la password dalla pagina **password dell'app** e quindi fare clic su **fine**.

    ![Pagina password app con la nuova password dell'app creata](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. Nella pagina **informazioni di sicurezza** verificare che la password dell'app sia elencata.

    ![Pagina delle informazioni di sicurezza con la nuova password dell'app visualizzata nell'elenco](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. Aprire l'app per cui è stata creata la password dell'app, ad esempio Outlook 2016, quindi incollare la password dell'app quando viene richiesta. Questa operazione deve essere eseguita una sola volta per ogni app.

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>Per eliminare le password dell'app usando la pagina info di sicurezza

1. Nella pagina **informazioni di sicurezza** selezionare **Elimina** accanto alla password dell'app che si vuole eliminare.

   ![Screenshot che mostra l'eliminazione di una password dell'app nella pagina delle informazioni di sicurezza](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. Fare clic su **OK** nella casella di conferma.

    La password viene eliminata.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se le app per le password non funzionano correttamente

Assicurarsi di aver digitato correttamente la password. Se si è certi di aver immesso la password correttamente, è possibile provare ad accedere di nuovo e creare una nuova password. Se nessuna di queste opzioni risolve il problema, contattare l'help desk dell'organizzazione per richiedere l'eliminazione delle password per le app esistenti, in modo da poterne creare di nuove.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)

- Provare l'[app Microsoft Authenticator](user-help-auth-app-download-install.md) per verificare gli accessi con le notifiche delle app, invece di ricevere messaggi o chiamate.
