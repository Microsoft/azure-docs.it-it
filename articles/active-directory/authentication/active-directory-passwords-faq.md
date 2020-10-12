---
title: Domande frequenti sulla reimpostazione della password self-service - Azure Active Directory
description: Domande frequenti su sulla reimpostazione della password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1927cc103ba14b4b9b545eca82aee28cd32eeb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052384"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Domande frequenti sulla reimpostazione della password self-service

Di seguito sono riportate alcune domande frequenti per tutti gli aspetti correlati alla reimpostazione della password self-service.

Per domande generali su Azure Active Directory (Azure AD) e sulla reimpostazione della password self-service per cui non è disponibile una risposta in questo articolo, è possibile richiedere assistenza nella [pagina di domande e risposte Microsoft per Azure Active Directory](/answers/topics/azure-active-directory.html). La community è composta da ingegneri, responsabili di prodotto, MVP e informatici.

Questo articolo di domande frequenti è suddiviso nelle sezioni seguenti:

* [Domande sulla registrazione per la reimpostazione della password](#password-reset-registration)
* [Domande sulla reimpostazione della password](#password-reset)
* [Domande sulla modifica della password](#password-change)
* [Domande sui report di gestione delle password](#password-management-reports)
* [Domande sul writeback delle password](#password-writeback)

## <a name="password-reset-registration"></a>Registrazione per la reimpostazione della password

* **D:  Gli utenti possono registrare i propri dati per la reimpostazione della password?**

  > **R:** Sì. Se la reimpostazione della password è abilitata, gli utenti con licenza possono accedere al portale di registrazione per la reimpostazione della password (https://aka.ms/ssprsetup) per registrare le proprie informazioni di autenticazione. La registrazione può essere eseguita anche tramite il pannello di accesso (https://myapps.microsoft.com). Per la registrazione tramite il pannello di accesso, gli utenti devono selezionare la propria immagine del profilo, quindi **Profilo** e infine l'opzione **Registrazione per reimpostazione password**.
  >
  > Se si Abilita la [registrazione combinata](concept-registration-mfa-sspr-combined.md), gli utenti possono registrarsi sia per SSPR che per Azure multi-factor authentication nello stesso momento.
* **D:  Se si abilita la reimpostazione della password per un gruppo e quindi si decide di abilitare questa funzionalità per tutti gli utenti, è necessario che gli utenti ripetano la registrazione?**

  > **R:** No. Gli utenti che hanno inserito i dati di autenticazione non devono ripetere la registrazione.
  >
  >
* **D:  È possibile definire i dati di reimpostazione della password per conto degli utenti?**

  > **R:** Sì, è possibile eseguire questa operazione con Azure AD Connect, PowerShell, il [portale di Azure](https://portal.azure.com) o l'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com). Per altre informazioni, vedere l'articolo relativo ai [dati usati per la reimpostazione password self-service di Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **D:  È possibile sincronizzare i dati per le domande di sicurezza dall'ambiente locale?**

  > **R:** No, attualmente non è possibile.
  >
  >
* **D:  Gli utenti possono registrare i dati in modo che non siano visibili ad altri utenti?**

  > **R:** Sì. I dati che l'utente registra usando il portale di registrazione per la reimpostazione della password vengono salvati in campi di autenticazione privati visibili solo agli amministratori globali e all'utente.
  >
  >
* **D:  Gli utenti devono essere registrati prima di poter usare la funzionalità di reimpostazione della password?**

  > **R:** No. Se le informazioni di autenticazione definite per conto degli utenti sono sufficienti, gli utenti non dovranno eseguire la registrazione. La reimpostazione della password funziona a condizione che i dati archiviati nei campi appropriati nella directory siano nel formato corretto.
  >
  >
* **D:  È possibile sincronizzare o impostare i campi Telefono per l'autenticazione, Indirizzo di posta elettronica per l'autenticazione o Telefono per l'autenticazione alternativo per conto degli utenti?**

  > **R:** I campi che possono essere impostati da un amministratore globale vengono definiti nell'articolo [Requisiti relativi ai dati di SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **D:  In che modo il portale di registrazione determina le opzioni da visualizzare agli utenti?**

  > **R:** Il portale di registrazione per la reimpostazione della password mostra solo le opzioni che sono state abilitate per gli utenti. Queste opzioni si trovano nella sezione **Criteri di reimpostazione password utente** della scheda **Configura** della directory. Se non si abilitano le domande di sicurezza, ad esempio, gli utenti non potranno registrarsi per questa opzione.
  >
  >
* **D:  Quando viene considerato registrato un utente?**

  > **R:** Un utente viene considerato registrato per la reimpostazione della password self-service quando ha registrato almeno il **numero di metodi necessari per la reimpostazione** di una password che è stato impostato nel [portale di Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reimpostazione della password

* **D:  Agli utenti viene impedito di effettuare più tentativi di reimpostazione di una password in un breve periodo di tempo?**

  > **R:** Sì, la reimpostazione della password include funzionalità di sicurezza per evitare un uso improprio di questa procedura. 
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
* **D:  Quanto tempo deve passare prima di ricevere un messaggio di posta elettronica, un SMS o una telefonata dopo la reimpostazione della password?**

  > **R:** Messaggi di posta elettronica, SMS e telefonate dovrebbero arrivare dopo meno di un minuto. L'intervallo normale è da 5 a 20 secondi.
  > Se non si riceve la notifica entro questo periodo di tempo:
  > * Controllare la cartella della posta indesiderata.
  > * Controllare che il numero o l'indirizzo di posta elettronica contattato sia quello previsto.
  > * Controllare che i dati di autenticazione nella directory siano nel formato corretto, ad esempio +1 4255551234 o *utente\@contoso.com*. 
* **D:  Quali lingue sono supportate per la reimpostazione della password?**

  > **R:** L'interfaccia utente di reimpostazione della password, i messaggi SMS e le chiamate vocali sono localizzati nelle stesse lingue supportate in Microsoft 365.
  >
  >
* **D:  A quali parti dell'esperienza di reimpostazione della password vengono applicate le impostazioni di personalizzazione dell'organizzazione specificate nella scheda Configura della directory?**

  > **R:** Il portale per la reimpostazione della password mostra il logo dell'organizzazione e consente di configurare il collegamento "Contattare l'amministratore" in modo da puntare a un indirizzo di posta elettronica o un URL personalizzato. Tutti i messaggi di posta elettronica inviati dalla funzionalità di reimpostazione della password includono nel corpo il logo, i colori e il nome dell'organizzazione e sono personalizzati in base alle impostazioni associate al nome specifico.
  >
  >
* **D:  In che modo è possibile spiegare agli utenti come fare per reimpostare le proprie password?**

  > **R:** Provare alcuni dei suggerimenti contenuti nell'articolo relativo alla [distribuzione della reimpostazione della password self-service](howto-sspr-deployment.md#plan-communications).
  >
  >
* **D:  Questa pagina può essere usata da un dispositivo mobile?**

  > **R:** Sì, la pagina funziona anche sui dispositivi mobili.
  >
  >
* **D:  È supportato lo sblocco di account Active Directory locali quando gli utenti reimpostano le password?**

  > **R:** Sì. Quando un utente reimposta la password ed è stato distribuito il writeback delle password tramite Azure AD Connect, l'account utente viene sbloccato automaticamente alla reimpostazione della password.
  >
  >
* **D:  Come è possibile integrare la reimpostazione della password direttamente nell'esperienza di accesso desktop degli utenti?**

  > **R:** I clienti di Azure AD Premium possono installare gratuitamente Microsoft Identity Manager e distribuire la soluzione locale di reimpostazione della password.
  >
  >
* **D:  È possibile impostare domande di sicurezza diverse per impostazioni locali diverse?**

  > **R:** No, attualmente non è possibile.
  >
  >
* **D:  Quante domande si possono configurare per l'opzione di autenticazione delle domande di sicurezza?**

  > **R:** Nel [portale di Azure](https://portal.azure.com) è possibile configurare un massimo di 20 domande di sicurezza personalizzate.
  >
  >
* **D:  Qual è la lunghezza consentita per le domande di sicurezza?**

  > **R:** Le domande di sicurezza possono contenere da 3 a 200 caratteri.
  >
  >
* **D:  Qual è la lunghezza consentita per le risposte alle domande di sicurezza?**

  > **R:** Le risposte possono contenere da 3 a 40 caratteri.
  >
  >
* **D:  Le risposte duplicate alle domande di sicurezza vengono rifiutate?**

  > **R:** Sì, vengono rifiutate.
  >
  >
* **D:  Un utente può registrare più volte la stessa domanda di sicurezza?**

  > **R:** No. Dopo che aver registrato una determinata domanda, l'utente non potrà eseguire la registrazione per quella domanda una seconda volta.
  >
  >
* **D:  È possibile impostare un limite minimo di domande di sicurezza per la registrazione e la reimpostazione?**

  > **R:** Sì, è possibile impostare un limite per la registrazione e un altro per la reimpostazione. Possono essere richieste da tre a cinque domande di sicurezza per la registrazione e da tre a cinque domande per la reimpostazione.
  >
  >
* **D:  Se si sono configurati criteri per richiedere agli utenti di usare domande di sicurezza per la reimpostazione, perché la configurazione risulta diversa per gli amministratori di Azure?**

  > **R:** Si tratta del comportamento previsto. Per qualsiasi ruolo di amministratore di Azure, Microsoft applica criteri avanzati predefiniti di reimpostazione password con doppio controllo che impediscono agli amministratori di usare domande di sicurezza. Per altre informazioni su tali criteri, vedere l'articolo [Restrizioni e criteri password in Azure Active Directory](concept-sspr-policy.md).
  >
  >
* **D:  Se un utente ha registrato un numero di domande superiore al numero massimo delle domande necessarie per la reimpostazione, come vengono selezionate le domande di sicurezza durante la reimpostazione?**

  > **R:** Dal numero totale di domande di sicurezza per cui un utente ha eseguito la registrazione viene selezionato in modo casuale un numero *N* di domande, dove *N* è il valore impostato per l'opzione **Numero di domande necessarie per la reimpostazione**. Se un utente ha registrato cinque domande di sicurezza ma per reimpostare una password ne sono necessarie solo tre, ad esempio, al momento della reimpostazione vengono selezionate in modo casuale e presentate solo tre delle cinque domande. Per evitare attacchi hammering sulle domande, se l'utente sbaglia le risposte alle domande, il processo di selezione ricomincia.
  >
  >
* **D:  Per quanto tempo sono validi i passcode monouso inviati per posta elettronica o SMS?**

  > **R:** La durata della sessione per la reimpostazione della password è di 15 minuti. Dall'inizio dell'operazione di reimpostazione password, l'utente ha 15 minuti per completare la reimpostazione. Il passcode monouso ricevuto tramite posta elettronica o SMS è valido per 5 minuti durante la sessione di reimpostazione della password.
  >
  >
* **D:  È possibile impedire agli utenti di reimpostare la password?**

  > **R:** Sì, se si usa un gruppo per abilitare la reimpostazione della password self-service, è possibile rimuovere un singolo utente dal gruppo che consente agli utenti di reimpostare la propria password. Se l'utente è un amministratore globale, manterrà la possibilità di reimpostare la password; questa impostazione non può essere disabilitata.
  >
  >

## <a name="password-change"></a>Modifica della password

* **D:  Dove devono andare gli utenti per modificare la password?**

  > **R:** Gli utenti possono modificare le password ovunque venga visualizzata l'immagine o l'icona del profilo, ad esempio nell'angolo in alto a destra nell'esperienza del portale di [Office 365](https://portal.office.com) o del [pannello di accesso](https://myapps.microsoft.com). Gli utenti possono modificare le password dalla [pagina del profilo del pannello di accesso](https://account.activedirectory.windowsazure.com/r#/profile). Agli utenti potrebbe essere anche richiesto automaticamente di modificare la password nella pagina di accesso di Azure AD in caso di scadenza della password. Infine, se vogliono modificare la password, gli utenti possono accedere direttamente al [portale di modifica della password di Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx).
  >
  >
* **D:  Gli utenti possono ricevere una notifica nel portale di Office alla scadenza della password locale?**

  > **R:** Sì, questo è attualmente possibile se si usa Active Directory Federation Services (AD FS). Se si usa AD FS, seguire le istruzioni riportate nell'articolo relativo all'[invio di attestazioni per i criteri password con AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se si usa la sincronizzazione dell'hash delle password, al momento non è possibile. I criteri password delle directory locali non vengono sincronizzati e non è quindi possibile pubblicare notifiche di scadenza nelle esperienze cloud. In entrambi i casi, è anche possibile [inviare notifiche agli utenti con password in scadenza tramite PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **D:  È possibile impedire agli utenti di modificare la password?**

  > **R:** Per gli utenti solo cloud è impossibile bloccare le modifiche delle password. Per gli utenti locali, è possibile selezionare l'opzione **Cambiamento password non consentito**. Gli utenti selezionati non potranno modificare la password.
  >
  >

## <a name="password-management-reports"></a>Report di gestione delle password

* **D:  Quanto tempo è richiesto prima che i dati vengano visualizzati nei report di gestione delle password?**

  > **R:** I dati dovrebbero essere visualizzati in questi report in 5-10 minuti. In alcuni casi, la visualizzazione potrebbe richiedere fino a un'ora.
  >
  >
* **D:  Come si possono filtrare i report di gestione delle password?**

  > **R:** Per filtrare i report di gestione delle password, selezionare la piccola lente di ingrandimento all'estrema destra delle etichette di colonna, nella parte superiore del report. Per applicare filtri più avanzati, è possibile scaricare il report in Excel e creare una tabella pivot.
  >
  >
* **D: Qual è il numero massimo di eventi archiviato nei report di gestione delle password?**

  > **R:** Nei report di gestione delle password vengono archiviati fino a 75.000 eventi di reimpostazione della password o di registrazione per la reimpostazione della password, risalendo fino a 30 giorni prima. Stiamo lavorando per espandere questo numero al fine di includere un maggior numero di eventi.
  >
  >
* **D:  Fino a quando risalgono i report di gestione delle password?**

  > **R:** Questi report mostrano le operazioni eseguite negli ultimi 30 giorni. Per il momento, se occorre archiviare i dati, è possibile scaricare i report periodicamente e salvarli in un percorso separato.
  >
  >
* **D:  Esiste un limite al numero massimo di righe visualizzabili nei report di gestione delle password?**

  > **R:** Sì. I report di gestione delle password, sia visualizzati nell'interfaccia utente che scaricati, possono contenere un massimo di 75.000 righe.
  >
  >
* **D:  Esiste un'API per accedere ai dati di report delle registrazioni o di reimpostazione delle password?**

  > **R:** Sì. Per informazioni su come accedere ai dati dei report sulla reimpostazione della password, vedere le [informazioni di riferimento sull'API REST Azure Log Analytics](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>writeback delle password

* **D:  Come funziona il writeback delle password in background?**

  > **R:** Per una spiegazione di quello che accade quando si abilita il writeback delle password e del flusso dei dati dal sistema all'ambiente locale, vedere [Funzionamento del writeback delle password](./tutorial-enable-sspr-writeback.md).
  >
  >
* **D:  Entro quanto tempo si attiva il funzionamento del writeback delle password? Esiste un ritardo di sincronizzazione come nel caso della sincronizzazione dell'hash delle password?**

  > **R:** Il writeback delle password è immediato. Si tratta di una pipeline sincrona che funziona fondamentalmente in modo diverso rispetto alla sincronizzazione di hash della password. Il writeback delle password consente agli utenti di avere un feedback in tempo reale sulla riuscita dell'operazione di reimpostazione o modifica della propria password. L'intervallo medio per un writeback della password riuscito è inferiore a 500 ms.
  >
  >
* **D:  Se l'account locale è disabilitato, qual è l'impatto sull'account e sull'accesso cloud?**

  > **R:** Se l'ID locale è disabilitato, anche l'ID e l'accesso cloud verranno disabilitati al successivo intervallo di sincronizzazione tramite Azure AD Connect. Per impostazione predefinita, la sincronizzazione viene eseguita ogni 30 minuti.
  >
  >
* **D:  Se l'account locale è vincolato da criteri password di Active Directory locali, la reimpostazione della password self-service rispetta questi criteri quando si modifica la password?**

  > **R:** Sì, la reimpostazione della password self-service applica e rispetta i criteri password di Active Directory locali, che includono i criteri password tipici per i domini di Active Directory nonché tutti i criteri password specifici per un utente.
  >
  >
* **D:  Per quali tipi di account funziona il writeback delle password?**

  > **R:** Il writeback delle password funziona per gli account utente sincronizzati da Active Directory locale ad Azure AD, inclusi gli utenti federati, con hash delle password sincronizzati e con autenticazione pass-through.
  >
  >
* **D:  Il writeback delle password applica i criteri password del dominio?**

  > **R:** Sì. Il writeback delle password applica validità, cronologia, complessità, filtri e qualsiasi altra restrizione eventualmente applicata alle password nel dominio locale.
  >
  >
* **D:  Il writeback delle password è sicuro?  Come si può essere certi di non essere oggetto di un attacco?**

  > **R:** Sì, il writeback delle password è sicuro. Per altre informazioni sui molti livelli di sicurezza implementati dal servizio di writeback delle password, vedere la sezione [Sicurezza del writeback delle password](concept-sspr-writeback.md#password-writeback-security) dell'articolo [Panoramica del writeback delle password](./tutorial-enable-sspr-writeback.md).
  >
  >

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](./tutorial-enable-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)