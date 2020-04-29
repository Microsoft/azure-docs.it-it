---
title: Domande frequenti sulla reimpostazione della password self-service - Azure Active Directory
description: Domande frequenti su sulla reimpostazione della password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1246b5b980f60c2f3e65aa5b32a7d79dd6efc7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407168"
---
# <a name="password-management-frequently-asked-questions"></a>Domande frequenti sulla gestione delle password

Di seguito sono riportate alcune domande frequenti su tutti gli aspetti riguardanti la reimpostazione della password.

Per domande generali su Azure Active Directory (Azure AD) e sulla reimpostazione password self-service per cui non è disponibile una risposta in questo articolo, è possibile richiedere assistenza alla community nel [forum di Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). La community è composta da ingegneri, responsabili di prodotto, MVP e informatici.

Questo articolo di domande frequenti è suddiviso nelle sezioni seguenti:

* [Domande sulla registrazione per la reimpostazione della password](#password-reset-registration)
* [Domande sulla reimpostazione della password](#password-reset)
* [Domande sulla modifica della password](#password-change)
* [Domande sui report di gestione delle password](#password-management-reports)
* [Domande sul writeback delle password](#password-writeback)

## <a name="password-reset-registration"></a>Registrazione per la reimpostazione della password

* **D: Gli utenti possono registrare i propri dati per la reimpostazione della password?**

  > **R:** Sì. Se la reimpostazione della password è abilitata, gli utenti con licenza possono accedere al portale di registrazione per la reimpostazione della password (https://aka.ms/ssprsetup) per registrare le proprie informazioni di autenticazione. La registrazione può essere eseguita anche tramite il pannello di accesso (https://myapps.microsoft.com). Per la registrazione tramite il pannello di accesso, gli utenti devono selezionare la propria immagine del profilo, quindi **Profilo** e infine l'opzione **Registrazione per reimpostazione password**.
  >
  >
* **D: Se si abilita la reimpostazione della password per un gruppo e quindi si decide di abilitare questa funzionalità per tutti gli utenti, è necessario che gli utenti ripetano la registrazione?**

  > **R:** No. Gli utenti che hanno inserito i dati di autenticazione non devono ripetere la registrazione.
  >
  >
* **D: è possibile definire i dati di reimpostazione della password per conto degli utenti?**

  > **R:** Sì, è possibile farlo con Azure AD Connect, PowerShell, il [portale di Azure](https://portal.azure.com)o l'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com). Per altre informazioni, vedere l'articolo relativo ai [dati usati per la reimpostazione password self-service di Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **D: È possibile sincronizzare i dati per le domande di sicurezza dall'ambiente locale?**

  > **R:** No. Attualmente non è possibile.
  >
  >
* **D: Gli utenti possono registrare i dati in modo che non siano visibili ad altri utenti?**

  > **R:** Sì. I dati che l'utente registra usando il portale di registrazione per la reimpostazione della password vengono salvati in campi di autenticazione privati visibili solo agli amministratori globali e all'utente.
  >
  >
* **D: gli utenti devono essere registrati prima di poter usare la reimpostazione della password?**

  > **R:** No. Se le informazioni di autenticazione definite per conto degli utenti sono sufficienti, gli utenti non dovranno eseguire la registrazione. La reimpostazione della password funziona a condizione che i dati archiviati nei campi appropriati nella directory siano nel formato corretto.
  >
  >
* **D: è possibile sincronizzare o impostare i campi telefono per l'autenticazione, indirizzo di posta elettronica per l'autenticazione o telefono per l'autenticazione alternativo per conto degli utenti?**

  > **R:** i campi che possono essere impostati da un amministratore globale vengono definiti nell'articolo [Requisiti relativi ai dati di SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **D: In che modo il portale di registrazione determina le opzioni da visualizzare agli utenti?**

  > **R:** Il portale di registrazione per la reimpostazione della password mostra solo le opzioni che sono state abilitate per gli utenti. Queste opzioni si trovano nella sezione **criteri di reimpostazione password utente** della scheda **Configura** della directory. Se ad esempio non si abilitano le domande di sicurezza, gli utenti non saranno in grado di eseguire la registrazione per tale opzione.
  >
  >
* **D: quando un utente viene considerato registrato?**

  > **R:** Un utente viene considerato registrato per la reimpostazione password self-service quando ha registrato almeno il **numero di metodi necessari per la reimpostazione** che è stato impostato nel [portale di Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reimpostazione della password

* **D: Agli utenti viene impedito di effettuare più tentativi di reimpostazione di una password in un breve periodo di tempo?**

  > **R:** Sì. Per evitarne un uso improprio, il processo di reimpostazione password include funzionalità di sicurezza. 
  >
  > Gli utenti possono effettuare solo cinque tentativi di reimpostazione password nell'arco di 24 ore, dopo i quali verranno bloccati per 24 ore. 
  >
  > Gli utenti possono provare a convalidare un numero di telefono, inviare un SMS o convalidare domande e risposte di sicurezza solo cinque volte nell'arco di un'ora, dopodiché verranno bloccati per 24 ore. 
  >
  > Gli utenti possono inviare un messaggio di posta elettronica massimo 10 volte nell'arco di 10 minuti, trascorsi i quali verranno bloccati per 24 ore.
  >
  > I contatori vengono reimpostati dopo che un utente ha reimpostato la password.
  >
  >
* **D: quanto tempo è necessario attendere per ricevere un messaggio di posta elettronica, un SMS o una telefonata dalla reimpostazione della password?**

  > **R:** Messaggi di posta elettronica, SMS e telefonate dovrebbero essere ricevuti in meno di un minuto. L'intervallo normale è da 5 a 20 secondi.
  > Se non si riceve la notifica entro questo periodo di tempo:
  > * Controllare la cartella della posta indesiderata.
  > * Controllare che il numero o l'indirizzo di posta elettronica contattato sia quello previsto.
  > * Verificare che i dati di autenticazione nella directory siano formattati correttamente, ad esempio + 1 4255551234 o *utente\@contoso.com*. 
* **D: quali lingue sono supportate per la reimpostazione della password?**

  > **R:** L'interfaccia utente, gli SMS e le chiamate vocali per la reimpostazione della password sono localizzati nelle stesse lingue supportate in Office 365.
  >
  >
* **D: quali parti dell'esperienza di reimpostazione della password vengono personalizzate quando si impostano gli elementi di personalizzazione dell'organizzazione nella scheda Configura della directory?**

  > **R:** Il portale per la reimpostazione della password mostra il logo dell'organizzazione e consente di configurare il collegamento "Contattare l'amministratore" in modo da puntare a un indirizzo di posta elettronica o un URL personalizzato. Tutti i messaggi di posta elettronica inviati tramite la reimpostazione della password includono il logo, i colori e il nome dell'organizzazione nel corpo del messaggio di posta elettronica e vengono personalizzati in base alle impostazioni per il nome specifico.
  >
  >
* **D: In che modo è possibile spiegare agli utenti come fare per reimpostare le proprie password?**

  > **R:** Provare alcuni dei suggerimenti disponibili nell'articolo sulla [distribuzione di SSPR](howto-sspr-deployment.md#plan-communications) .
  >
  >
* **D: Questa pagina può essere usata da un dispositivo mobile?**

  > **R:** Sì, la pagina funziona anche sui dispositivi mobili.
  >
  >
* **D: è supportato lo sblocco di account Active Directory locali quando gli utenti reimpostano le password?**

  > **R:** Sì. Quando un utente reimposta la password, se il writeback delle password è stato distribuito tramite Azure AD Connect, l'account dell'utente viene sbloccato automaticamente quando viene reimpostata la password.
  >
  >
* **D: come è possibile integrare la reimpostazione della password direttamente nell'esperienza di accesso desktop dell'utente?**

  > **R:** I clienti di Azure AD Premium possono installare gratuitamente Microsoft Identity Manager e distribuire la soluzione di reimpostazione password locale.
  >
  >
* **D: È possibile impostare domande di sicurezza diverse per impostazioni locali diverse?**

  > **R:** No. Attualmente non è possibile.
  >
  >
* **D: Quante domande si possono configurare per l'opzione di autenticazione delle domande di sicurezza?**

  > **R:** Nel [portale di Azure](https://portal.azure.com) è possibile configurare un massimo di 20 domande di sicurezza personalizzate.
  >
  >
* **D: Qual è la lunghezza consentita per le domande di sicurezza?**

  > **R:** Le domande di sicurezza possono contenere da 3 a 200 caratteri.
  >
  >
* **D: Qual è la lunghezza consentita per le risposte alle domande di sicurezza?**

  > **R:** Le risposte possono contenere da 3 a 40 caratteri.
  >
  >
* **D: le risposte duplicate alle domande di sicurezza sono state rifiutate?**

  > **R:** Sì, vengono rifiutate.
  >
  >
* **D: Un utente può registrare più volte la stessa domanda di sicurezza?**

  > **R:** No. Dopo che aver registrato una determinata domanda, l'utente non potrà eseguire la registrazione per quella domanda una seconda volta.
  >
  >
* **D: è possibile impostare un limite minimo di domande di sicurezza per la registrazione e la reimpostazione?**

  > **R:** Sì, è possibile impostare un limite per la registrazione e un altro per la reimpostazione. Possono essere richieste da tre a cinque domande di sicurezza per la registrazione e da tre a cinque domande per la reimpostazione.
  >
  >
* **D: Se si sono configurati criteri per richiedere agli utenti di usare domande di sicurezza per la reimpostazione, perché la configurazione risulta diversa per gli amministratori di Azure?**

  > **R:** Si tratta del comportamento previsto. Per qualsiasi ruolo di amministratore di Azure, Microsoft applica criteri avanzati predefiniti di reimpostazione password con doppio controllo che impediscono agli amministratori di usare domande di sicurezza. Per altre informazioni su tali criteri, vedere l'articolo [Restrizioni e criteri password in Azure Active Directory](concept-sspr-policy.md).
  >
  >
* **D: Se un utente ha registrato un numero di domande superiore al numero massimo delle domande necessarie per la reimpostazione, come vengono selezionate le domande di sicurezza durante la reimpostazione?**

  > **R: Dal numero totale di domande di sicurezza per cui un utente ha eseguito la registrazione viene selezionato in modo casuale un numero ** *N* di domande, dove *N* è il valore impostato per l'opzione **Numero di domande necessarie per la reimpostazione**. Se un utente ha registrato cinque domande di sicurezza ma per reimpostare una password ne sono necessarie solo tre, ad esempio, al momento della reimpostazione vengono selezionate in modo casuale e presentate solo tre delle cinque domande. Per evitare attacchi hammering sulle domande, se l'utente sbaglia le risposte alle domande, il processo di selezione ricomincia.
  >
  >
* **D: Per quanto tempo sono validi i passcode monouso inviati per posta elettronica o SMS?**

  > **R:** La durata della sessione per la reimpostazione della password è di 15 minuti. Dall'inizio dell'operazione di reimpostazione password, l'utente ha 15 minuti per completare la reimpostazione. Il codice di posta elettronica e SMS monouso sono validi per 5 minuti durante la sessione di reimpostazione della password.
  >
  >
* **D: È possibile impedire agli utenti di reimpostare la password?**

  > **R:** Sì. Se si usa un gruppo per abilitare la reimpostazione password self-service, è possibile rimuovere un singolo utente dal gruppo che consente agli utenti di reimpostare la propria password. Se l'utente è un amministratore globale, manterrà la possibilità di reimpostare la password; questa impostazione non può essere disabilitata.
  >
  >

## <a name="password-change"></a>Modifica della password

* **D: Dove devono andare gli utenti per modificare la password?**

  > **R:** Gli utenti possono modificare le password ovunque venga visualizzata l'immagine o l'icona del profilo, ad esempio nell'angolo in alto a destra nell'esperienza del portale di [Office 365](https://portal.office.com) o del [pannello di accesso](https://myapps.microsoft.com). Gli utenti possono modificare le password dalla [pagina del profilo del pannello di accesso](https://account.activedirectory.windowsazure.com/r#/profile). Agli utenti potrebbe essere anche richiesto automaticamente di modificare la password nella pagina di accesso di Azure AD in caso di scadenza della password. Infine, se vogliono modificare la password, gli utenti possono accedere direttamente al [portale di modifica della password di Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx).
  >
  >
* **D: gli utenti possono ricevere una notifica nel portale di Office quando la password locale scade?**

  > **R:** Sì. Questo è attualmente possibile se si usa Active Directory Federation Services (AD FS). Se si usa AD FS, seguire le istruzioni riportate nell'articolo relativo all'[invio di attestazioni per i criteri password con AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se si usa la sincronizzazione dell'hash delle password, al momento non è possibile. I criteri password delle directory locali non vengono sincronizzati e non è quindi possibile pubblicare notifiche di scadenza nelle esperienze cloud. In entrambi i casi, è anche possibile [inviare notifiche agli utenti con password in scadenza tramite PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **D: È possibile impedire agli utenti di modificare la password?**

  > **R:** Per gli utenti solo cloud, le modifiche delle password non possono essere bloccate. Per gli utenti locali, è possibile selezionare l'opzione **Cambiamento password non consentito**. Gli utenti selezionati non potranno modificare la password.
  >
  >

## <a name="password-management-reports"></a>Report di gestione delle password

* **D: quanto tempo occorre affinché i dati vengano visualizzati nei report di gestione delle password?**

  > **R:** I dati dovrebbero essere visualizzati in tali report in 5-10 minuti. In alcuni casi, la visualizzazione potrebbe richiedere fino a un'ora.
  >
  >
* **D: come è possibile filtrare i report di gestione delle password?**

  > **R:** Per filtrare i report di gestione delle password, selezionare la piccola lente di ingrandimento all'estrema destra delle etichette di colonna, nella parte superiore del report. Per applicare filtri più avanzati, è possibile scaricare il report in Excel e creare una tabella pivot.
  >
  >
* **D: Qual è il numero massimo di eventi archiviato nei report di gestione delle password?**

  > **R:** Nei report di gestione delle password vengono archiviati fino a 75.000 eventi di reimpostazione password o di registrazione per la reimpostazione della password, risalendo fino a 30 giorni prima. Stiamo lavorando per espandere questo numero al fine di includere un maggior numero di eventi.
  >
  >
* **D: per quanto riguarda il backup dei report di gestione delle password?**

  > **R:** Questi report mostrano le operazioni eseguite negli ultimi 30 giorni. Per il momento, se occorre archiviare i dati, è possibile scaricare i report periodicamente e salvarli in un percorso separato.
  >
  >
* **D: Esiste un limite al numero massimo di righe visualizzabili nei report di gestione delle password?**

  > **R:** Sì. I report di gestione delle password, sia visualizzati nell'interfaccia utente che scaricati, possono contenere un massimo di 75.000 righe.
  >
  >
* **D: esiste un'API per accedere ai dati di reimpostazione della password o di report di registrazione?**

  > **R:** Sì. Per informazioni su come accedere ai dati dei report di reimpostazione della password, vedere le informazioni di [riferimento sull'API REST di Azure log Analytics](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>writeback delle password

* **D: Come funziona il writeback delle password in background?**

  > **R:** Per una spiegazione di ciò che accade quando si abilita il writeback delle password e del flusso dei dati dal sistema all'ambiente locale, vedere [Funzionamento del writeback delle password](howto-sspr-writeback.md).
  >
  >
* **D: quanto tempo è necessario per il funzionamento del writeback delle password? Si verifica un ritardo di sincronizzazione, ad esempio con la sincronizzazione dell'hash delle password?**

  > **R:** Il writeback delle password è immediato. Si tratta di una pipeline sincrona che funziona fondamentalmente in modo diverso rispetto alla sincronizzazione di hash della password. Il writeback delle password consente agli utenti di avere un feedback in tempo reale sulla riuscita dell'operazione di reimpostazione o modifica della propria password. L'intervallo medio per un writeback della password riuscito è inferiore a 500 ms.
  >
  >
* **D: Se l'account locale è disabilitato, qual è l'impatto sull'account e l'accesso cloud?**

  > **R:** Se l'ID locale è disabilitato, anche l'ID e l'accesso cloud verranno disabilitati al successivo intervallo di sincronizzazione tramite Azure AD Connect. Per impostazione predefinita, la sincronizzazione viene eseguita ogni 30 minuti.
  >
  >
* **D: Se l'account locale è vincolato da criteri password di Active Directory locali, la reimpostazione password self-service rispetta questi criteri quando si modifica la password?**

  > **R:** Sì. La reimpostazione password self-service applica e rispetta i criteri password di Active Directory locali, che includono i criteri password tipici per i domini di Active Directory nonché tutti i criteri password specifici per un utente.
  >
  >
* **D: Per quali tipi di account funziona il writeback delle password?**

  > **R:** il writeback delle password funziona per gli account utente sincronizzati da Active Directory locale ad Azure AD, inclusi gli utenti federati, con hash delle password sincronizzati e con autenticazione pass-through.
  >
  >
* **D: il writeback delle password applica i criteri password del dominio?**

  > **R:** Sì. Il writeback delle password applica validità, cronologia, complessità, filtri e qualsiasi altra restrizione eventualmente applicata alle password nel dominio locale.
  >
  >
* **D: il writeback delle password è sicuro?  Come posso assicurarmi che non venga violato?**

  > **R:** Sì, il writeback delle password è sicuro. Per altre informazioni sui molti livelli di sicurezza implementati dal servizio di writeback delle password, vedere la sezione [Sicurezza del writeback delle password](concept-sspr-writeback.md#password-writeback-security) dell'articolo [Panoramica del writeback delle password](howto-sspr-writeback.md).
  >
  >

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Eseguire la registrazione per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Hai una domanda per le licenze?](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Credo che qualcosa sia rotto. Ricerca per categorie risolvere i problemi di SSPR?](active-directory-passwords-troubleshoot.md)
