---
title: Accedere tramite l'app Microsoft Authenticator - Azure AD
description: Usare l'app Microsoft Authenticator per accedere all'account aziendale o dell'istituto di istruzione oppure agli account personali Microsoft e non Microsoft, con la verifica a due fattori o l'accesso tramite telefono.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: abf5d0d4aeeb18a11f50220e982448d41a9ac1b4
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773203"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Accedere agli account con l'app Microsoft Authenticator

L'app Microsoft Authenticator consente di accedere ai propri account se si usa la verifica a due fattori. La verifica a due fattori consente di accedere agli account in modo più sicuro, in particolare durante la visualizzazione di informazioni riservate. Poiché le password possono essere dimenticate, rubate o compromesse, la verifica a due fattori è un'ulteriore misura di sicurezza che consente di proteggere l'account rendendo più difficile l'intromissione di altri utenti.

È possibile usare l'app Microsoft Authenticator in diversi modi, tra cui:

- Richiedendo un secondo metodo di verifica dopo l'accesso con il nome utente e la password.

- Fornendo l'accesso senza richiedere una password, usando il nome utente e il dispositivo mobile con l'impronta digitale, il viso o il PIN.

  >[!Important]
  >Questo metodo di accesso tramite telefono funziona solo con gli account Microsoft personali, aziendali o dell'istituto. Con gli account non Microsoft è necessario usare il processo di verifica a due fattori standard.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare l'app Microsoft Authenticator, è necessario:

 1. Scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, vedere [Scaricare e installare l'app](user-help-auth-app-download-install.md).

 2. Aggiungere gli account aziendali o dell'istituto di istruzione, personali e di terze parti all'app Microsoft Authenticator. Per i passaggi dettagliati, consultare [Aggiungere l'account aziendale o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md), [Aggiungere gli account personali](user-help-auth-app-add-personal-ms-account.md) e [Add your non-Microsoft accounts](user-help-auth-app-add-non-ms-account.md) (Aggiungere gli account non Microsoft).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Attivare e usare l'accesso tramite telefono per l'account aziendale o dell'istituto di istruzione

L'accesso tramite telefono è un tipo di verifica in due passaggi. È comunque necessario verificare l'identità, fornendo un elemento noto e un elemento posseduto, ma l'accesso tramite telefono evita di dover immettere la password dell'account ed esegue tutta la verifica dell'identità sul dispositivo mobile.

Prima di poter attivare l'accesso tramite telefono, è necessario attivare la verifica a due fattori. Per altre informazioni su come attivare la verifica a due fattori per un account, vedere [Aggiungere l'account aziendale o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md) e [Aggiungere gli account personali](user-help-auth-app-add-personal-ms-account.md).

L'accesso tramite telefono è disponibile solo su dispositivi iOS e Android 6.0 e versioni successive.

### <a name="turn-on-phone-sign-in"></a>Attivare l'accesso tramite telefono

Aprire l'app Microsoft Authenticator, passare all'account aziendale o dell'istituto di istruzione e attivare l'accesso tramite telefono.

Su un dispositivo Android:

- **Se sul telefono è visualizzata questa icona ![icona che indica che l'accesso è già configurato](media/user-help-auth-app-sign-in/icon.png).** Se questa icona viene visualizzata accanto al nome dell'account aziendale o dell'istituto di istruzione, significa che l'accesso tramite telefono per l'account è già stato configurato. Potrebbe essere necessario aggiungere le notifiche push per l'account per poter ricevere notifiche relative alle richieste di autenticazione esternamente all'app.
- **Se l'app per la verifica a due fattori è già stata usata.** Se l'app e la verifica a due fattori sono già state usate, è possibile selezionare la freccia accanto al nome dell'account e quindi selezionare **Abilita l'accesso tramite telefono**.
- **Se non è possibile trovare l'account aziendale o dell'istituto di istruzione.** Se non è possibile trovare l'account aziendale o dell'istituto di istruzione nella schermata **Account** dell'app, significa che non è ancora stato aggiunto all'app. Aggiungere l'account aziendale o dell'istituto di istruzione eseguendo i passaggi illustrati nell'articolo [Aggiungere l'account aziendale o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md).

Su un dispositivo iOS:

- **Quando si tocca il riquadro account**, l'account viene aperto in una visualizzazione a schermo intero dell'account. Se viene visualizzato **L'accesso tramite telefono è abilitato** significa che è stata completata la configurazione per l'accesso senza password. Se viene visualizzato **Abilita l'accesso tramite telefono**, toccarlo per attivare l'accesso tramite telefono.
- **Se l'app per la verifica a due fattori è già stata usata**, è possibile toccare il riquadro account che si espande in una visualizzazione a schermo intero dell'account. Quindi toccare **Abilita l'accesso tramite telefono** per attivare l'accesso tramite telefono.
- **Se non è possibile trovare l'account aziendale o dell'istituto di istruzione** nella schermata **Account** dell'app, significa che non è ancora stato aggiunto all'app. Aggiungere l'account aziendale o dell'istituto di istruzione eseguendo i passaggi illustrati nell'articolo Aggiungere l'account aziendale o dell'istituto di istruzione.

> [!NOTE]
> Non è supportata una combinazione di registrazione del dispositivo e autenticazione basata su certificati in Authenticator in iOS. L'utente deve usare una soluzione alternativa registrando il dispositivo manualmente tramite le impostazioni di Authenticator prima dell'accesso.

Dopo avere attivato l'accesso tramite telefono, è possibile accedere usando solo l'app Microsoft Authenticator.

1. Accedere all'account aziendale o dell'istituto di istruzione.

    Dopo aver digitato il nome utente, viene visualizzata una schermata **Approva l'accesso** con un numero a due cifre, in cui viene richiesto di accedere tramite l'app Microsoft Authenticator. Se non si vuole usare questo metodo di accesso, è possibile selezionare **Usa la tua password** e quindi accedere usando la password.

    ![Finestra di dialogo Approva l'accesso sul computer](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Aprire la notifica o l'app Microsoft Authenticator sul dispositivo e quindi toccare il numero corrispondente a quello visualizzato nella schermata **Approva l'accesso** del computer.

    ![Finestra di dialogo Approva l'accesso sul dispositivo](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Scegliere **Approva** se si riconosce il tentativo di accesso. In caso contrario, scegliere **Nega**.

4. Usare il PIN del telefono o la chiave biometrica per completare l'autenticazione.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Attivare e usare l'accesso tramite telefono per gli account Microsoft personali

È possibile attivare l'accesso tramite telefono per l'account Microsoft personale, ad esempio l'account usato per accedere a Outlook.com, Xbox o Skype.

>[!NOTE]
>Per proteggere l'account, l'app Microsoft Authenticator richiede un PIN o un blocco biometrico sul dispositivo. Se il telefono resta sbloccato, l'app chiede di configurare un blocco di sicurezza prima di attivare l'accesso tramite telefono.

### <a name="turn-on-phone-sign-in"></a>Attivare l'accesso tramite telefono 

Aprire l'app Microsoft Authenticator, passare all'account aziendale o dell'istituto di istruzione e attivare l'accesso tramite telefono.

Su un dispositivo Android:

- **Se sul telefono è visualizzata questa icona ![icona che indica che l'accesso è già configurato](media/user-help-auth-app-sign-in/icon.png).** Se questa icona viene visualizzata accanto al nome dell'account Microsoft personale, significa che l'accesso tramite telefono per l'account è già stato configurato. Potrebbe essere necessario aggiungere le notifiche push per l'account per poter ricevere notifiche relative alle richieste di autenticazione esternamente all'app.
- **Se è stata usata l'app per la verifica a due fattori.** Se l'app e la verifica a due fattori sono già state usate, è possibile selezionare la freccia accanto al nome dell'account e quindi selezionare **Abilita l'accesso tramite telefono**.
- **Se non è possibile trovare l'account aziendale o dell'istituto di istruzione.** Se non è possibile trovare l'account nella schermata **Account** dell'app, significa che non è ancora stato aggiunto all'app. Aggiungere l'account eseguendo i passaggi illustrati nell'articolo [Aggiungere account Microsoft personali](user-help-auth-app-add-personal-ms-account.md).

Su un dispositivo iOS:

- **Quando si tocca il riquadro account**, l'account viene aperto in una visualizzazione a schermo intero dell'account. Se viene visualizzato **L'accesso tramite telefono è abilitato** significa che è stata completata la configurazione per l'accesso senza password. Se viene visualizzato **Abilita l'accesso tramite telefono**, toccarlo per attivare l'accesso tramite telefono.
- **Se l'app per la verifica a due fattori è già stata usata**, è possibile toccare il riquadro account che si espande in una visualizzazione a schermo intero dell'account. Quindi toccare **Abilita l'accesso tramite telefono** per attivare l'accesso tramite telefono.
- **Se non è possibile trovare l'account** nella schermata **Account** dell'app, significa che non è ancora stato aggiunto all'app. Aggiungere l'account Microsoft personale eseguendo i passaggi illustrati nell'articolo [Aggiungere account Microsoft personali](user-help-auth-app-add-personal-ms-account.md).

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Accedere all'account con l'accesso tramite telefono

1. Passare alla pagina di accesso dell'account Microsoft personale e quindi selezionare il collegamento **Usare l'app Microsoft Authenticator** invece di digitare la password.

    Microsoft invia una notifica al telefono cellulare dell'utente.

2. Approvare la notifica.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Accedere usando la verifica a due fattori per l'account

Il metodo standard di verifica a due fattori richiede l'immissione del nome utente e della password nel dispositivo a cui si sta effettuando l'accesso. Quindi, occorre aprire l'app Microsoft Authenticator e digitare il codice generato in modo casuale per l'account nella casella **Immettere il codice**. In un dispositivo Android, questi codici di verifica sono disponibili nella schermata **Account**. In un dispositivo iOS, questi codici di verifica sono disponibili nella schermata **Account** o nella visualizzazione a schermo intero di un account, a seconda del tipo di account. La verifica a due fattori per l'account viene attivata durante l'aggiunta dell'account all'app Microsoft Authenticator.

>[!Note]
>Se l'account aziendale o dell'istituto di istruzione o l'account personale non è visibile nella schermata **Account** dell'app Microsoft Authenticator, significa che l'account non è stato aggiunto all'app Microsoft Authenticator. Per aggiungere l'account, vedere [Aggiungere l'account aziendale o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md) o [Aggiungere gli account personali](user-help-auth-app-add-personal-ms-account.md).

Per i passaggi necessari per accedere all'account aziendale o dell'istituto di istruzione oppure a quello personale usando i diversi metodi di verifica a due fattori, vedere [Eseguire l'accesso usando la verifica in due passaggi o le informazioni di sicurezza](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

| Domanda | Soluzione |
| -------- | -------- |
| In che modo l'accesso tramite telefono è più sicuro dell'accesso tramite password? | Oggi la maggior parte delle persone accede a siti Web o alle app usando un nome utente e una password. Sfortunatamente, le password possono essere smarrite, rubate o individuate da pirati informatici.<br><br>Una volta installata, l'app Microsoft Authenticator crea una chiave nel telefono per sbloccare l'account protetto dal PIN o dal blocco biometrico del telefono. Questa chiave viene quindi usata per dimostrare l'identità dell'utente durante l'accesso.<br><br>**Importante**<br>I dati vengono usati solo per proteggere la chiave in locale. Non vengono mai inviati né archiviati nel cloud. |
| L'accesso tramite telefono sostituisce la verifica in due passaggi? È consigliabile disattivarla? | L'accesso tramite telefono è un tipo di verifica in due passaggi in cui entrambi i passaggi vengono eseguiti sul dispositivo mobile. È consigliabile mantenere attivata la verifica in due passaggi per fornire protezione aggiuntiva per l'account. |
| Se la verifica in due passaggi resta attiva per l'account, è necessario approvare due notifiche? | No. L'accesso all'account Microsoft tramite il telefono viene considerato una verifica in due passaggi e di conseguenza non è necessaria una seconda approvazione. |
| Che cosa succede se si perde il telefono o se il telefono non è a portata di mano? Come si accede all'account in questi casi? | È sempre possibile selezionare il collegamento Usa la tua password nella pagina di accesso per tornare a usare la password. Tuttavia, se si usa la verifica in due passaggi, sarà comunque necessario usare un secondo metodo per verificare l'identità.<br><br>**Importante**<br>È consigliabile assicurarsi di usare più di un metodo di verifica aggiornato associato all'account.<br><br>È possibile gestire i metodi di verifica per gli account personali dalla pagina [Impostazioni di sicurezza](https://account.live.com/proofs/manage). Per gli account aziendali o dell'istituto di istruzione, è possibile passare alla pagina [Verifica aggiuntiva di sicurezza](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) dell'organizzazione o alla pagina **Proteggi l'account** se l'amministratore ha attivato le informazioni di sicurezza. Per altri dettagli sulle informazioni di sicurezza, vedere [Panoramica (anteprima) delle informazioni di sicurezza](user-help-security-info-overview.md).<br><br>Se non si è in grado di gestire i metodi di verifica, è necessario contattare l'amministratore. |
| Come si smette di usare questa funzionalità per tornare a usare la password? | Per gli account personali, selezionare il collegamento **Usa la tua password** durante l'accesso. La scelta più recente viene memorizzata dal sistema e proposta per impostazione predefinita al successivo accesso. Se si vuole tornare all'accesso tramite telefono, selezionare il collegamento **Usa un'app** durante l'accesso.<br><br>Per gli account aziendali o dell'istituto di istruzione, è necessario annullare la registrazione del dispositivo dalla pagina **Impostazioni** dell'app Microsoft Authenticator o disabilitare il dispositivo dall'area **Dispositivi e attività** del profilo. Per altre informazioni sulla disabilitazione del dispositivo dal profilo, vedere [Aggiornare il profilo e le informazioni sull'account dal portale App personali](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Perché non è possibile usare più di un account aziendale o dell'istituto di istruzione per l'accesso tramite telefono? | Un telefono deve essere registrato in un solo account aziendale o dell'istituto di istruzione. Se si vuole attivare l'accesso tramite telefono per un account aziendale o dell'istituto di istruzione diverso, è necessario annullare la registrazione dell'account da questo dispositivo tramite la pagina **Impostazioni**. |
| È possibile accedere al computer tramite il telefono? | Per il computer, è consigliabile accedere con Windows Hello in Windows 10. Windows Hello permette di usare il viso, le impronte digitali o un PIN per accedere. |

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi durante il recupero del codice di verifica per l'account Microsoft personale, vedere la sezione **Risoluzione dei problemi del codice di verifica** dell'articolo [Informazioni di sicurezza e codici di verifica dell'account Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- In caso di domande generali sull'app, vedere le [domande frequenti su Microsoft Authenticator](user-help-auth-app-faq.md)

- Per altre informazioni sulla verifica in due passaggi, vedere [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- Per altri dettagli sulle informazioni di sicurezza, vedere [Panoramica (anteprima) delle informazioni di sicurezza](user-help-security-info-overview.md)
