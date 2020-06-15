---
title: Configurare le domande di sicurezza come metodo di verifica - Azure AD
description: Come configurare la pagina Informazione di sicurezza (anteprima) per usare le domande di sicurezza predefinite come metodo di verifica dell'identità.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744441"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Configurare le domande di sicurezza come metodo di verifica

È possibile seguire questi passaggi per aggiungere il metodo di reimpostazione della password. Dopo averle configurate per la prima volta, è possibile tornare alla pagina **Informazioni di sicurezza** per aggiungere, aggiornare o eliminare le informazioni di sicurezza.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se non viene visualizzata un'opzione per le domande di sicurezza, è possibile che l'organizzazione non consenta di usarla come metodo di reimpostazione della password. In questo caso, è necessario scegliere un altro metodo oppure contattare l'help desk dell'organizzazione per ricevere assistenza.

## <a name="security-verification-versus-password-reset-authentication"></a>Confronto tra verifica di sicurezza e autenticazione per la reimpostazione della password

I metodi di gestione delle informazioni di sicurezza vengono usati sia per la verifica di sicurezza a due fattori sia per la reimpostazione della password. Non tutti i metodi possono tuttavia essere usati per entrambe.

| Metodo | Utilizzo |
| ------ | -------- |
| App di autenticazione | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| SMS | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Telefonate | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Chiave di sicurezza | Verifica a due fattori e autenticazione per la reimpostazione della password. |
| Account di posta elettronica | Solo autenticazione per la reimpostazione della password. Per la verifica a due fattori sarà necessario scegliere un altro metodo. |
| Domande di sicurezza | Solo autenticazione per la reimpostazione della password. Per la verifica a due fattori sarà necessario scegliere un altro metodo. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurare le domande di sicurezza dalla pagina Info di sicurezza

A seconda delle impostazioni dell'organizzazione, potrebbe essere possibile scegliere alcune domande a cui rispondere, come metodo per le informazioni di sicurezza. L'amministratore configura il numero di domande di sicurezza che è necessario scegliere e a cui rispondere.

Se vengono usate le domande di sicurezza, è consigliabile combinare questo metodo con un altro. Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande.

> [!Note]
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.
>
> Se l'opzione relativa alle domande di sicurezza non viene visualizzata, è possibile che l'organizzazione non consenta di usare le domande di sicurezza per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.
>
> Gli account amministratore non sono autorizzati a utilizzare le domande di sicurezza come metodo di reimpostazione della password. Se si è connessi come account a livello di amministratore, queste opzioni non verranno visualizzate.

### <a name="to-set-up-your-security-questions"></a>Per configurare le domande di sicurezza

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myaccount.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Informazioni di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **Domande di sicurezza** dall'elenco a discesa e quindi selezionare **Aggiungi**.

    ![Finestra di dialogo Aggiungi un metodo, con l'opzione Domande di sicurezza selezionata](media/security-info/securityinfo-myprofile-addquestions.png)

4. Nella pagina **Domande di sicurezza** scegliere le domande di sicurezza, rispondere e quindi selezionare **Salva**.

    ![Aggiungere il numero di telefono e scegliere chiamate telefoniche](media/security-info/securityinfo-myprofile-securityquestions.png)

    Le informazioni di sicurezza vengono aggiornate ed è possibile usare le domande di sicurezza per verificare l'identità quando si usa la reimpostazione della password.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Eliminare le domande di sicurezza dai metodi di gestione delle informazioni di sicurezza

Per non usare più le domande di sicurezza come metodo di gestione delle informazioni di sicurezza, è possibile rimuoverle dalla pagina **Info di sicurezza**.

>[!Important]
>Se si eliminano per errore le domande di sicurezza, non è possibile annullare l'azione. Sarà necessario aggiungere nuovamente il metodo, seguendo la procedura illustrata nella sezione [Configurare le domande di sicurezza](#set-up-your-security-questions-from-the-security-info-page) di questo articolo.

### <a name="to-delete-your-security-questions"></a>Per eliminare le domande di sicurezza

1. Nella pagina **Info di sicurezza** selezionare il collegamento **Elimina** accanto all'opzione **Domande di sicurezza**.

    ![Collegamento per eliminare il metodo basato sul telefono dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selezionare **Sì** nella finestra di dialogo di conferma per eliminare le **domande di sicurezza**. Dopo l'eliminazione delle domande di sicurezza, il metodo viene rimosso dalle informazioni di sicurezza e non viene più visualizzato nella pagina **Info di sicurezza**.

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, Queste opzioni includono:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Mobile device text.** (SMS su dispositivo mobile) Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Chiave di sicurezza.** Registrare la chiave di sicurezza compatibile con Microsoft e usarla insieme a un PIN per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con una chiave di sicurezza, vedere [Configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza](security-info-setup-security-key.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
