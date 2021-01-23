---
title: Metodi di autenticazione telefono-Azure Active Directory
description: Per informazioni sull'uso dei metodi di autenticazione tramite telefono, vedere Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5e8b933f617d767f017f73fb6778a45b5a1ce3
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725590"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Metodi di autenticazione nelle opzioni del telefono Azure Active Directory

Per l'autenticazione diretta tramite SMS, è possibile [configurare e abilitare gli utenti per l'autenticazione basata su SMS](howto-authentication-sms-signin.md). L'accesso basato su SMS è ideale per i ruoli di lavoro Frontline. Con l'accesso basato su SMS, non è necessario che l'utente conosca nome utente e password per accedere alle applicazioni e ai servizi. Può immettere invece il numero di telefono cellulare registrato, ricevere un SMS con un codice di verifica e inserirlo nell'interfaccia di accesso.

Gli utenti possono inoltre verificare se stessi utilizzando un telefono cellulare o un telefono dell'ufficio come forma secondaria di autenticazione utilizzata durante Azure AD Multi-Factor Authentication o la reimpostazione della password self-service (SSPR).

Per garantirne il corretto funzionamento, i numeri di telefono devono essere nel formato *+PrefissoInternazionale NumeroTelefonico*, ad esempio *+1 4251234567*.

> [!NOTE]
> È necessario uno spazio tra il prefisso internazionale o della regione e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato *+1 4251234567X12345*, le estensioni vengono rimosse prima della chiamata.

## <a name="mobile-phone-verification"></a>Verifica tramite telefono cellulare

Per Azure AD Multi-Factor Authentication o SSPR, gli utenti possono scegliere di ricevere un SMS con un codice di verifica da immettere nell'interfaccia di accesso o ricevere una telefonata.

Se gli utenti non desiderano che il numero di telefono cellulare sia visibile nella directory, ma vogliono comunque usarlo per la reimpostazione della password, gli amministratori non devono inserirlo nella directory. Gli utenti devono invece popolare il proprio attributo **Telefono per l'autenticazione** tramite la registrazione delle informazioni di sicurezza combinate all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Screenshot del portale di Azure che mostra i metodi di autenticazione con un numero di telefono inserito":::

Microsoft non garantisce Azure AD coerenti di SMS o basate su Voice Multi-Factor Authentication il recapito con lo stesso numero. Nell'interesse degli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento per eseguire modifiche di route e migliorare il recapito degli SMS. Microsoft non supporta i codici brevi in paesi o aree geografiche diversi da Stati Uniti e Canada.

### <a name="text-message-verification"></a>Verifica tramite SMS

Con la verifica dei messaggi di testo durante SSPR o Azure AD Multi-Factor Authentication, viene inviato un SMS al numero di telefono cellulare contenente un codice di verifica. Per completare il processo di accesso, il codice di verifica inviato viene immesso nell'interfaccia di accesso.

### <a name="phone-call-verification"></a>Verifica tramite telefonata

Con la verifica tramite telefonata durante SSPR o Azure AD Multi-Factor Authentication viene effettuata una chiamata vocale automatizzata al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di premere # sulla tastiera.

## <a name="office-phone-verification"></a>Verifica tramite telefono dell'ufficio

Con la verifica tramite telefonata durante SSPR o Azure AD Multi-Factor Authentication viene effettuata una chiamata vocale automatizzata al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di premere # sulla tastiera.

## <a name="troubleshooting-phone-options"></a>Risoluzione dei problemi delle opzioni che si servono del telefono

In caso di problemi con l'autenticazione tramite telefono in Azure AD, esaminare la procedura per la risoluzione dei problemi seguente:

* "Hai raggiunto il limite per le chiamate di verifica" o "Hai raggiunto il limite per i codici di verifica del testo" durante l'accesso
   * Microsoft può limitare i tentativi di autenticazione ripetuti eseguiti dallo stesso utente in un breve periodo di tempo. Questa limitazione non si applica all'Microsoft Authenticator o al codice di verifica. Se sono stati raggiunti questi limiti, è possibile usare l'app Authenticator, il codice di verifica oppure provare ad accedere di nuovo in pochi minuti.
* "Si è verificato un errore durante la verifica dell'account" durante l'accesso
   * Microsoft può limitare o bloccare i tentativi di autenticazione vocale o SMS eseguiti dallo stesso utente, numero di telefono o organizzazione a causa di un numero elevato di tentativi di autenticazione vocale o SMS non riusciti. Se si verifica questo errore, è possibile provare un altro metodo, ad esempio l'app Authenticator o il codice di verifica, oppure rivolgersi all'amministratore per assistenza.
* ID del chiamante bloccato su un solo dispositivo.
   * Esaminare i numeri bloccati configurati sul dispositivo.
* Numero di telefono errato o codice internazionale o dell'area errato, oppure confusione tra il numero di telefono personale e il numero di telefono dell'ufficio.
   * Risolvere i problemi relativi all'oggetto utente e ai metodi di autenticazione configurati. Assicurarsi che siano registrati i numeri di telefono corretti.
* PIN immesso errato.
   * Verificare che l'utente abbia usato il PIN corretto come registrato per il proprio account (solo per gli utenti del server multi-factor authentication).
* Chiamata inoltrata alla segreteria telefonica.
   * Verificare che il telefono dell'utente sia acceso e che il servizio sia disponibile nella propria area oppure usare un altro metodo.
* L'utente è bloccato
   * Chiedere a un amministratore di Azure AD di sbloccare l'utente nel portale di Azure.
* Il metodo con SMS non è stato sottoscritto sul dispositivo.
   * Chiedere all'utente di modificare i metodi o di attivare il metodo con SMS sul dispositivo.
* Servizi di telecomunicazione non funzionanti correttamente, ad esempio input del telefono mancanti, problemi di toni DTMF mancanti, ID del chiamante bloccato oppure SMS bloccati in più dispositivi.
   * Microsoft usa più provider di telecomunicazioni per instradare telefonate e messaggi SMS per l'autenticazione. Se si nota uno dei problemi descritti in precedenza, chiedere all'utente di provare a usare il metodo almeno cinque volte in 5 minuti e recuperare le informazioni dell'utente prima di contattare il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti relativi a multi-factor authentication, vedere funzionamento di [Azure AD multi-factor authentication][concept-mfa].

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
