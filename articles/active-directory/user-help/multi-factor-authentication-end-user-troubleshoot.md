---
title: "Problemi comuni relativi all'autenticazione a due fattori dell'account: Azure AD"
description: Soluzioni per alcuni dei più comuni problemi di verifica a due fattori e l'account aziendale o dell'Istituto di istruzione.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 555ba9a55a135bd38dcc46e1ff7a3d751083d47e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196424"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemi comuni con la verifica a due fattori e l'account aziendale o dell'Istituto di istruzione

Quando l'organizzazione Azure Active Directory (Azure AD) attiva la verifica a due fattori, l'accesso dell'account aziendale o dell'Istituto di istruzione richiede una combinazione del nome utente, della password e di un dispositivo mobile o telefono. Si tratta di una soluzione più sicura rispetto a una password, che si basa su due tipi di autenticazione: un elemento noto e qualcosa di cui si dispone. La verifica a due fattori può aiutare a impedire a pirati informatici di fingere di essere l'utente, perché anche se hanno una password, la probabilità è che non dispongano anche del dispositivo.

<center>

![Immagine concettuale relativa ai metodi di autenticazione](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Ci sono alcuni problemi comuni di verifica a due fattori che sembrano verificarsi con maggiore frequenza rispetto a quanto previsto da Microsoft. Questo articolo è stato raccolto per risolvere i problemi più comuni e alcune correzioni possibili.

>[!Important]
>Se si è un amministratore, è possibile trovare altre informazioni su come configurare e gestire l'ambiente di Azure AD nella documentazione di [Azure ad](https://docs.microsoft.com/azure/active-directory).
>
>Questo contenuto è destinato esclusivamente all'uso con l'account aziendale o dell'Istituto di istruzione, che corrisponde all'account fornito dall'organizzazione, ad esempio alain@contoso.com . Se si verificano problemi con la verifica a due fattori e la account Microsoft personale, ovvero un account configurato autonomamente (ad esempio, danielle@outlook.com ), vedere [attivazione o disattivazione della verifica a due fattori per l'account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Non ho il mio dispositivo mobile con me

Succede. Il dispositivo mobile è stato lasciato a casa e ora non è possibile usare il telefono per verificare chi è. Se in precedenza è stato aggiunto un altro metodo per accedere al proprio account, ad esempio il telefono dell'ufficio, dovrebbe essere possibile usare il metodo adesso. Se non è mai stato aggiunto un metodo di verifica aggiuntivo, sarà necessario contattare il supporto tecnico dell'organizzazione per poter accedere all'account.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Per accedere all'account aziendale o dell'Istituto di istruzione utilizzando un altro metodo di verifica

1. Accedere al proprio account, ma selezionare il collegamento **Accedi a un altro modo** nella pagina di **verifica a due fattori** .

    ![Modificare il metodo di verifica dell'accesso](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se il collegamento **Sign in another way** (Accedi in altro modo) non viene visualizzato, non è stato impostato alcun altro metodo di verifica. È necessario contattare l'amministratore per ottenere assistenza sull'accesso all'account.

2. Scegliere il metodo di verifica alternativo e continuare con il processo di verifica a due fattori.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Il dispositivo mobile è stato smarrito o rubato

In caso di smarrimento o di furto del dispositivo mobile, è possibile accedere utilizzando un metodo diverso oppure è possibile chiedere al supporto tecnico dell'organizzazione di cancellare le impostazioni. È consigliabile consentire al supporto tecnico dell'organizzazione di rilevare se il telefono è stato smarrito o rubato, in modo da poter effettuare gli aggiornamenti appropriati per l'account. Una volta cancellate le impostazioni, verrà richiesto di effettuare la [registrazione per la verifica a due fattori](multi-factor-authentication-end-user-first-time.md) al successivo accesso.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Non ricevo il codice di verifica inviato al mio dispositivo mobile

Il mancato recupero del codice di verifica è un problema comune ed è in genere correlato al dispositivo mobile e alle relative impostazioni. Ecco alcune operazioni possibili da provare:

mancanti | Informazioni aggiuntive
--------- | ------------
Riavviare il dispositivo mobile | A volte il dispositivo necessita solo di un aggiornamento. Il riavvio del dispositivo termina eventuali processi in background o servizi attualmente in esecuzione e potrebbe causare problemi, oltre ad aggiornare i componenti principali del dispositivo, riavviarli in caso di arresto anomalo in un determinato momento.
Verificare la correttezza delle informazioni di sicurezza | Assicurarsi che le informazioni sul metodo di verifica della sicurezza siano accurate, in particolare i numeri di telefono. Se si inserisce il numero di telefono errato, tutti gli avvisi passeranno a tale numero errato. Fortunatamente, l'utente non sarà in grado di eseguire alcuna operazione con gli avvisi, ma anche di accedere al proprio account. Per assicurarsi che le informazioni siano corrette, vedere le istruzioni riportate nell'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md) .
Verificare che le notifiche siano attivate | Verificare che nel dispositivo mobile siano attivate le notifiche e che sia stato selezionato un metodo di notifica che consente le chiamate telefoniche, l'app di autenticazione e l'app di messaggistica (per i messaggi di testo) per inviare notifiche di avviso visibili al dispositivo mobile.
Assicurarsi di disporre di un segnale del dispositivo e di una connessione Internet | Assicurarsi che le chiamate al telefono e i messaggi di testo vengano visualizzati nel dispositivo mobile. Chiedere a un amico di contattare l'utente e inviare un SMS per assicurarsi di ricevere entrambi. In caso contrario, verificare prima di tutto che il dispositivo mobile sia attivato. Se il dispositivo è acceso, ma non si sta ancora ricevendo la chiamata o il testo, probabilmente si tratta di un problema con la rete e sarà necessario comunicare con il provider. Se spesso si verificano problemi relativi ai segnali, è consigliabile installare e usare l' [app Microsoft Authenticator](user-help-auth-app-download-install.md) nel dispositivo mobile. L'app Authenticator può generare codici di sicurezza casuali per l'accesso, senza la necessità di un segnale di cella o di una connessione Internet.
Disattiva non disturbare | Assicurarsi che non sia stata attivata la funzionalità non **disturbare** per il dispositivo mobile. Quando questa funzionalità è attivata, le notifiche non sono autorizzate a ricevere avvisi nel dispositivo mobile. Per istruzioni su come disattivare questa funzionalità, vedere il manuale del dispositivo mobile.
Sbloccare i numeri di telefono | Nel Stati Uniti, le chiamate vocali da Microsoft provengono dai numeri seguenti: + 1 (866) 539 4191, + 1 (855) 330 8653 e + 1 (877) 668 6536.
Controllare le impostazioni relative alla batteria | Questo aspetto è leggermente strano, ma se è stato configurato l'ottimizzazione della batteria per arrestare le app meno utilizzate rimanenti attive in background, è probabile che il sistema di notifica sia interessato. Per tentare di risolvere il problema, disattivare l'ottimizzazione della batteria per l'app di autenticazione e l'app di messaggistica, quindi provare di nuovo ad accedere all'account.
Disabilitare le app per la sicurezza di terze parti | Se si dispone di un'app che protegge i messaggi di testo o le chiamate telefoniche per ridurre al minimo i chiamanti sconosciuti, potrebbe impedire la ricezione del codice di verifica. Provare a disabilitare eventuali app per la sicurezza di terze parti sul telefono, quindi richiedere l'invio di un altro codice di verifica.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Non vengono richieste le informazioni di verifica secondo

Se è stato effettuato l'accesso all'account aziendale o dell'Istituto di istruzione usando il nome utente e la password, ma non sono state richieste informazioni aggiuntive sulla verifica di sicurezza, forse non è stato ancora configurato il dispositivo. Il dispositivo mobile deve essere configurato per l'utilizzo con il metodo di verifica aggiuntiva di sicurezza specifico. Per assicurarsi di aver attivato il dispositivo mobile e che sia disponibile per l'uso con il metodo di verifica, vedere l'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md) . Se si è certi che il dispositivo o l'account non è stato configurato, è possibile eseguire questa operazione attenendosi alla procedura descritta nell'articolo [configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Ho un nuovo numero di telefono che desidero aggiungere

Se è stato ottenuto un nuovo numero di telefono, è necessario aggiornare i dettagli del metodo di verifica della sicurezza in modo che le richieste di verifica vadano alla posizione corretta. Per aggiornare il metodo di verifica, seguire la procedura descritta nella sezione **aggiungere o modificare il numero di telefono** dell'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Ho un nuovo dispositivo mobile che desidero aggiungere

Se è stato acquistato un nuovo dispositivo mobile, è necessario configurarlo per l'uso con la verifica a due fattori. Si tratta di una soluzione in più passaggi:

1. Configurare il dispositivo per l'uso con l'account aziendale o dell'Istituto di istruzione attenendosi alla procedura descritta nell'articolo [configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md) .

1. Aggiornare le informazioni relative all'account e al dispositivo nella pagina **verifica aggiuntiva di sicurezza** , eliminando il dispositivo precedente e aggiungendone uno nuovo. Per altre informazioni, vedere l'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md) .

Passaggi facoltativi:

- Scaricare, installare e configurare l'app Microsoft Authenticator nel dispositivo mobile attenendosi alla procedura descritta nell'articolo [scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md) .

- Attivare la verifica a due fattori per i dispositivi attendibili attenendosi alla procedura illustrata nella sezione **attivare le richieste di verifica a due fattori in un dispositivo attendibile** dell'articolo [gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Si verificano problemi durante l'accesso al dispositivo mobile mentre si è in viaggio

Potrebbe risultare più difficile usare un metodo di verifica correlato ai dispositivi mobili, ad esempio un messaggio di testo, mentre si è in una posizione internazionale. È anche possibile che il dispositivo mobile causi addebiti per il roaming. Per questa situazione, è consigliabile usare l'app Microsoft Authenticator, con l'opzione per connettersi a un hotspot Wi-Fi. Per altre informazioni su come scaricare, installare e configurare l'app Microsoft Authenticator nel dispositivo mobile, vedere l'articolo [scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Non è possibile fare in modo che le password dell'app funzionino

Le password dell'app sostituiscono la password normale per le applicazioni desktop meno recenti che non supportano la verifica a due fattori. Verificare prima che la password sia stata digitata correttamente. Se il problema persiste, provare a creare una nuova password dell'app per l'app seguendo la procedura descritta nella sezione **creare ed eliminare password di app usando il portale** delle app personali dell'articolo gestire le password delle app [per la verifica in due passaggi](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-two-factor-verification-off"></a>Non è possibile disattivare la verifica a due fattori

Se si usa la verifica a due fattori con l'account aziendale o dell'Istituto di istruzione (ad esempio, alain@contoso.com ), è molto probabile che l'organizzazione abbia deciso che è necessario usare questa funzionalità di sicurezza aggiuntiva. Poiché l'organizzazione ha deciso che è necessario usare questa funzionalità, non è possibile disattivarla individualmente. Se tuttavia si usa la verifica a due fattori con un account personale, alain@outlook.com ad esempio, è possibile attivare e disattivare la funzionalità. Per istruzioni su come controllare la verifica a due fattori per gli account personali, vedere [attivazione o disattivazione della verifica a due fattori per la account Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se non è possibile disattivare la verifica a due fattori, è possibile che si verifichino anche le impostazioni predefinite di sicurezza applicate a livello di organizzazione. Per ulteriori informazioni sulle impostazioni predefinite di sicurezza, vedere [che cosa sono le impostazioni predefinite della sicurezza?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nessuna risposta al problema riscontrato

Se si sono verificati questi passaggi, ma si verificano ancora problemi, contattare il supporto tecnico dell'organizzazione per assistenza.

## <a name="related-articles"></a>Articoli correlati

- [Gestire le impostazioni del metodo di verifica a due fattori](multi-factor-authentication-end-user-manage-settings.md)

- [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md)
