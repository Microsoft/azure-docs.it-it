---
title: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory | Microsoft Docs
description: Domande frequenti sulla gestione dei dispositivi di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7637a4280d725aa8cd3482641645dbe19cb56210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84689045"
---
# <a name="azure-active-directory-device-management-faq"></a>Domande frequenti sulla gestione dei dispositivi di Azure Active Directory

## <a name="general-faq"></a>Domande frequenti di carattere generale

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>D: Di recente è stato registrato un dispositivo. Perché non è possibile visualizzare il dispositivo sotto le informazioni utente nel portale di Azure? Oppure perché il proprietario del dispositivo è contrassegnato come N/D per i dispositivi ibridi aggiunti ad Azure Active Directory (Azure AD)?

**R:** I dispositivi Windows 10 ibridi Azure AD aggiunti non vengono visualizzati in **dispositivi utente**.
Usare la visualizzazione **Tutti i dispositivi** nel portale di Azure. In alternativa, è possibile usare un cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) di PowerShell.

Sotto **Dispositivi utente** vengono elencati solo i dispositivi seguenti:

- Tutti i dispositivi personali non aggiunti ad Azure AD ibrido. 
- Tutti i dispositivi non Windows 10 o Windows Server 2016.
- Tutti i dispositivi non Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>D: Come è possibile conoscere lo stato di registrazione del dispositivo client?

**R:** Nel portale di Azure passare a **tutti i dispositivi**. Cercare il dispositivo usando l'ID dispositivo. Controllare il valore nella colonna Tipo di join. In alcuni casi, è possibile che il dispositivo sia stato reimpostato o che ne sia stata ricreata l'immagine. È quindi importante controllare anche lo stato di registrazione del dispositivo all'interno del dispositivo:

- Per i dispositivi Windows 10 e Windows Server 2016 o versioni successive, eseguire `dsregcmd.exe /status`.
- Per le versioni del sistema operativo di livello inferiore, eseguire `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**R:** Per informazioni sulla risoluzione dei problemi, vedere gli articoli seguenti:
- [Risoluzione dei problemi dei dispositivi con il comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>D: nel portale di Azure viene visualizzato il record del dispositivo sotto le informazioni utente. E lo stato viene visualizzato come registrato nel dispositivo. Sono configurati correttamente per l'uso dell'accesso condizionale?

**R:** Lo stato di join del dispositivo, illustrato da **DeviceID**, deve corrispondere allo stato in Azure ad e soddisfare i criteri di valutazione per l'accesso condizionale. Per altre informazioni, vedere [Richiedi i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>D: perché gli utenti visualizzano un messaggio di errore che informa che l'organizzazione ha eliminato il dispositivo o che l'organizzazione ha disabilitato il dispositivo nei dispositivi Windows 10?

**R:** Nei dispositivi Windows 10 aggiunti o registrati con Azure AD, viene emesso un [token di aggiornamento primario (PRT)](concept-primary-refresh-token.md) che abilita Single Sign-on. La validità del PRT è basata sulla validità del dispositivo stesso. Questo messaggio viene visualizzato dagli utenti se il dispositivo viene eliminato o disabilitato in Azure AD senza avviare l'azione dal dispositivo stesso. Un dispositivo può essere eliminato o disabilitato in Azure AD uno degli scenari seguenti: 

- L'utente disabilita il dispositivo dal portale app personali. 
- Un amministratore (o utente) Elimina o Disabilita il dispositivo nella portale di Azure o tramite PowerShell
- Azure AD ibrido solo join: un amministratore rimuove l'ambito di sincronizzazione dei dispositivi dall'ambito di sincronizzazione risultante dall'eliminazione dei dispositivi da Azure AD
- Aggiornamento Azure AD connettersi alla versione 1.4. XX. x. [Informazioni Azure ad Connect 1.4. XX. x e la scomparsa del dispositivo](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Vedere di seguito il modo in cui è possibile rettificare queste azioni.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>D: il dispositivo è stato disabilitato o eliminato nel portale di Azure o tramite Windows PowerShell. Ma lo stato locale nel dispositivo risulta ancora registrato. Cosa devo fare?

**R:** Questa operazione viene eseguita in base alla progettazione. In questo caso, il dispositivo non ha accesso alle risorse nel cloud. Gli amministratori possono eseguire questa operazione per dispositivi obsoleti, smarriti o rubati per impedire accessi non autorizzati. Se questa azione è stata eseguita involontariamente, sarà necessario riabilitare o registrare nuovamente il dispositivo come descritto di seguito.

- Se il dispositivo è stato disabilitato in Azure AD, un amministratore con privilegi sufficienti può abilitarlo dal portale di Azure AD  
  > [!NOTE]
  > Se si sincronizzano i dispositivi con Azure AD Connect, i dispositivi ibridi Azure AD aggiunti verranno riabilitati automaticamente durante il ciclo di sincronizzazione successivo. Quindi, se è necessario disabilitare un dispositivo ibrido Azure AD aggiunto, è necessario disabilitarlo dall'istanza locale di AD

 - Se il dispositivo viene eliminato in Azure AD, è necessario registrare di nuovo il dispositivo. Per ripetere la registrazione, è necessario eseguire un'azione manuale sul dispositivo. Per istruzioni sulla ripetizione della registrazione in base allo stato del dispositivo, vedere di seguito. 

      Per ripetere la registrazione dei dispositivi Windows 10 e Windows Server 2016/2019 aggiunti a Azure AD ibrido, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `dsregcmd.exe /debug /leave`.
      1. Disconnettersi e accedere per attivare l'attività pianificata che registra nuovamente il dispositivo con Azure AD. 

      Per le versioni di sistemi operativi Windows di livello inferiore ibride Azure AD Unite, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Immettere `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Per i dispositivi Azure AD aggiunti ai dispositivi Windows 10, seguire questa procedura:

      1. Aprire il prompt dei comandi come amministratore
      1. Immettere `dsregcmd /forcerecovery` (Nota: per eseguire questa azione, è necessario essere un amministratore).
      1. Fare clic su "Accedi" nella finestra di dialogo visualizzata e continuare con il processo di accesso.
      1. Disconnettersi e accedere di nuovo al dispositivo per completare il ripristino.

      Per Azure AD dispositivi Windows 10 registrati, seguire questa procedura:

      1. Passare a **Impostazioni**  >  **account**  >  **Accedi all'ufficio o all'Istituto di istruzione**. 
      1. Selezionare l'account e selezionare **Disconnetti**.
      1. Fare clic su "+ Connetti" e registrare di nuovo il dispositivo eseguendo il processo di accesso.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>D: perché vengono visualizzate voci di dispositivo duplicate nel portale di Azure?

**Un**

- Per i dispositivi Windows 10 e Windows Server 2016, i tentativi ripetuti di rimozione o aggiunta dello stesso dispositivo potrebbero dare luogo a elementi duplicati. 
- Ciascun utente Windows che usa l'opzione **Aggiungi account aziendale o dell'istituto di istruzione** creerà un nuovo record di dispositivo con lo stesso nome.
- Per le versioni di sistemi operativi Windows di livello inferiore aggiunte a un dominio Azure Directory locale, la registrazione automatica crea un nuovo record di dispositivo con lo stesso nome per ogni utente del dominio che accede al dispositivo. 
- Un computer aggiunto ad Azure AD cancellato, reinstallato e aggiunto nuovamente con lo stesso nome viene visualizzato con un record diverso ma con lo stesso nome dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>D: la registrazione dei dispositivi Windows 10 in Azure AD supporta TPMs in modalità FIPS?

**R:** La registrazione dei dispositivi Windows 10 è supportata solo per il TPM 2,0 conforme a FIPS e non è supportata per TPM 1,2. Se i dispositivi hanno un TPM compatibile con FIPS 1,2, è necessario disabilitarli prima di procedere con Azure AD join o Azure AD ibrido join. Microsoft non fornisce strumenti per disabilitare la modalità FIPS per TPMs poiché dipende dal produttore del TPM. Contattare l'OEM hardware per assistenza. 

---

**D: perché un utente può comunque accedere alle risorse da un dispositivo disabilitato nella portale di Azure?**

**R:** È necessaria un'ora per applicare una revoca dal momento in cui il dispositivo Azure AD è contrassegnato come disabilitato.

>[!NOTE] 
>Per quanto riguarda i dispositivi registrati, è consigliabile cancellarli per assicurare che gli utenti non possano accedere alle relative risorse. Per ulteriori informazioni, vedere [Informazioni sulla gestione dei dispositivi](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>D: perché sono presenti dispositivi contrassegnati come "in sospeso" nella colonna registrata della portale di Azure?

**R**: in sospeso indica che il dispositivo non è registrato. Questo stato indica che un dispositivo è stato sincronizzato con Azure AD connettersi da un'istanza di AD locale ed è pronto per la registrazione del dispositivo. Per questi dispositivi il tipo di JOIN è impostato su "Azure AD ibrido aggiunto". Scopri di più su [come pianificare l'implementazione del join di Azure Active Directory ibrido](hybrid-azuread-join-plan.md).

>[!NOTE]
>Un dispositivo può anche passare da uno stato registrato a "in sospeso"
>* Se un dispositivo viene eliminato da Azure AD prima e nuovamente sincronizzato da AD locale.
>* Se un dispositivo viene rimosso da un ambito di sincronizzazione su Azure AD Connect e aggiunto di nuovo.
>
>In entrambi i casi, è necessario registrare nuovamente il dispositivo manualmente in ognuno di questi dispositivi. Per verificare se il dispositivo è stato registrato in precedenza, è possibile [risolvere i problemi relativi ai dispositivi usando il comando dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>D: Ricerca per categorie separare un dispositivo Azure AD aggiunto localmente nel dispositivo?

**R:** Per i dispositivi puri Azure AD aggiunti, assicurarsi di disporre di un account amministratore locale offline o crearne uno. Non è possibile accedere con le credenziali di un utente Azure AD. Passare quindi a **Impostazioni**  >  **account**  >  **Accedi all'ufficio o all'Istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. Seguire le istruzioni e, quando richiesto, fornire le credenziali di amministratore locale. Riavviare il dispositivo per completare il processo di separazione.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>D: gli utenti possono accedere ai dispositivi Azure AD aggiunti che vengono eliminati o disabilitati in Azure AD?

**R:** Sì. Windows dispone di un nome utente memorizzato nella cache e della funzionalità di password che consente agli utenti che hanno effettuato l'accesso in precedenza di accedere al desktop rapidamente anche senza la connettività di rete. 

Quando un dispositivo viene eliminato o disabilitato in Azure AD, ciò non è noto al dispositivo Windows. Pertanto, gli utenti registrati in precedenza continuano ad accedere al desktop con il nome utente memorizzato nella cache e la password. Tuttavia, quando il dispositivo viene eliminato o disabilitato, gli utenti non possono accedere alle risorse protette dall'accesso condizionale basato su dispositivo. 

Gli utenti che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>D: è possibile disabilitare o eliminare l'accesso di un utente a un Azure AD dispositivi aggiunti

**R:** Sì, ma solo per un periodo di tempo limitato. Quando un utente viene eliminato o disabilitato in Azure AD, ciò non è noto immediatamente al dispositivo Windows. Pertanto, gli utenti registrati in precedenza possono accedere al desktop con il nome utente memorizzato nella cache e la password. 

In genere, il dispositivo è a conoscenza dello stato utente in meno di quattro ore. Quindi Windows blocca l'accesso degli utenti sul desktop. Quando l'utente viene eliminato o disabilitato in Azure AD, tutti i relativi token vengono revocati. Pertanto, non può accedere alle risorse. 

Gli utenti eliminati o disabilitati che non hanno effettuato l'accesso in precedenza non possono accedere al dispositivo. Non c'è alcun nome utente memorizzato nella cache né password abilitate per tale utente. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>D: perché gli utenti hanno problemi nei dispositivi Azure AD aggiunti dopo la modifica dell'UPN?

**R:** Attualmente, le modifiche UPN non sono completamente supportate nei dispositivi Azure AD aggiunti. Dopo la modifica dell'UPN, quindi, l'autenticazione degli utenti ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. Per risolvere il problema, gli utenti devono accedere a Windows tramite il riquadro "Altro utente" specificando il nuovo UPN. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

Le modifiche UPN sono supportate con l'aggiornamento di Windows 10 2004. Gli utenti dei dispositivi con questo aggiornamento non avranno alcun problema dopo aver modificato il UPN

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>D: gli utenti non possono cercare stampanti da Azure AD dispositivi aggiunti. In che modo è possibile abilitare la stampa da tali dispositivi?

**R:** Per distribuire le stampanti per i dispositivi Azure AD aggiunti, vedere [distribuire la stampa cloud ibrida di Windows Server con pre-autenticazione](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). È necessario un Server Windows locale per la distribuzione della stampa di cloud ibrido. Il servizio di stampa basato sul cloud non è attualmente disponibile. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>D: Ricerca per categorie connettersi a un dispositivo remoto Azure AD aggiunto?

**R:** Vedere [connettersi a un computer Azure Active Directory aggiunto in remoto](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>D: perché gli utenti non *riescono a trovarsi da qui*?

**R:** Sono state configurate alcune regole di accesso condizionale per richiedere uno specifico stato del dispositivo? Se il dispositivo non soddisfa i criteri, gli utenti vengono bloccati e viene visualizzato tale messaggio. Valutare le regole dei criteri di accesso condizionale. Assicurarsi che il dispositivo soddisfi i criteri in modo da evitare il messaggio.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>D: perché alcuni utenti non ricevono richieste di Multi-Factor Authentication di Azure per i dispositivi aggiunti Azure AD?

**R:** Un utente potrebbe partecipare o registrare un dispositivo con Azure AD usando Multi-Factor Authentication. Il dispositivo stesso diventa quindi un secondo fattore attendibile per tale utente. Ogni volta che lo stesso utente esegue l'accesso al dispositivo e accede a un'applicazione, Azure AD considera tale dispositivo come secondo fattore. Ciò consente di accedere facilmente alle applicazioni senza altri messaggi prompt da parte di Multi-Factor Authentication. 

Questo comportamento:

- È applicabile a tutti i dispositivi registrati in Azure AD e ai dispositivi aggiunti ad Azure AD, ma non ai dispositivi aggiunti ad Azure AD ibrido.
- Non è applicabile ad altri utenti che accedono al dispositivo. Pertanto, tutti gli altri utenti che accedono a tale dispositivo ricevono una richiesta di verifica di Multi-Factor Authentication. A questo punto è possibile accedere le applicazioni che richiedono l'autenticazione a più fattori.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>D: perché viene ricevuto un *nome utente o una password non corretta* per un dispositivo appena aggiunto al Azure ad?

**R:** I motivi più comuni per questo scenario sono i seguenti:

- Le credenziali dell'utente non sono più valide.
- Il computer non riesce a comunicare con Azure Active Directory. Verificare la presenza di eventuali problemi di connettività di rete.
- Gli accessi federati richiedono un server federativo con supporto per endpoint WS-Trust abilitati e accessibili. 
- Autenticazione pass-through abilitata. Pertanto, la password temporanea deve essere modificata quando si accede.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>D: perché vengono visualizzati gli *Oops... si è verificato un errore.* Quando si tenta di Azure AD aggiungere il PC?

**R:** Questo errore si verifica quando si configura Azure Active Directory registrazione con Intune. Verificare che all'utente che cerca di aggiungere il PC ad Azure AD sia assegnata la licenza di Intune corretta. Per altre informazioni, vedere [Set up enrollment for Windows devices (Configurare la registrazione in blocco per i dispositivi Windows)](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>D: perché il tentativo di Azure AD aggiunta a un PC ha esito negativo, anche se non sono state apportate informazioni sugli errori?

**R:** Una causa probabile è che è stato effettuato l'accesso al dispositivo utilizzando l'account Administrator predefinito locale. Creare un account locale diverso prima di usare l'aggiunta ad Azure Active Directory per completare la configurazione. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>D: quali sono i certificati MS-Organization-P2P-Access presenti sui dispositivi Windows 10?

**R:** I certificati MS-Organization-P2P-Access vengono rilasciati da Azure AD a entrambi i dispositivi aggiunti Azure AD e ibridi Azure AD aggiunti. Tali certificati vengono usati per abilitare l'attendibilità tra i dispositivi nello stesso tenant per scenari di desktop remoti. Un certificato viene rilasciato al dispositivo e un altro viene rilasciato all'utente. Il certificato del dispositivo è presente in `Local Computer\Personal\Certificates` ed è valido per un giorno. Tale certificato viene rinnovato (generando un nuovo certificato) se il dispositivo è ancora attivo in Azure AD. Il certificato utente è presente in `Current User\Personal\Certificates` e anch'esso è valido per un giorno, ma viene rilasciato su richiesta quando un utente tenta di eseguire una sessione di accesso da remoto a un altro dispositivo aggiunto ad Azure AD. Non viene rinnovato alla scadenza. Entrambi questi certificati vengono emessi usando il certificato MS-Organization-P2P-Access presente in `Local Computer\AAD Token Issuer\Certificates`. Tale certificato è emesso da Azure AD durante la registrazione del dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>D: Perché vengono visualizzati più certificati scaduti emessi da MS-Organization-P2P-Access nei dispositivi Windows 10? Come è possibile eliminarli?

**R:** Si è verificato un problema identificato in Windows 10 versione 1709 e nelle versioni precedenti in cui i certificati MS-Organization-P2P-Access scaduti continuano a esistere nell'archivio del computer a causa di problemi di crittografia. Gli utenti potrebbero affrontare problemi con la connettività di rete, se si usano qualsiasi client VPN (ad esempio, Cisco AnyConnect) che non è in grado di gestire il numero elevato di certificati scaduti. Questo problema è stato risolto nella versione 1803 di Windows 10 nella quale vengono eliminati automaticamente i certificati MS-Organization-P2P-Access scaduti. È possibile risolvere questo problema aggiornando i dispositivi alla versione 1803 di Windows 10. Se non si è in grado di aggiornare, è possibile eliminare questi certificati senza effetti negativi.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Domande frequenti sull'aggiunta ad Azure AD ibrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>D: Ricerca per categorie separare un dispositivo Azure AD ibrido aggiunto localmente nel dispositivo?

**R:** Per i dispositivi ibridi Azure AD aggiunti, assicurarsi di disattivare la registrazione automatica. In questo modo l'attività pianificata non registra nuovamente il dispositivo. A questo punto, aprire il prompt dei comandi come amministratore e immettere `dsregcmd.exe /debug /leave`. O eseguire questo comando come uno script in più dispositivi per separare in blocco.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>D: dove è possibile trovare informazioni sulla risoluzione dei problemi per diagnosticare gli errori di join Azure AD ibrido?

**R:** Per informazioni sulla risoluzione dei problemi, vedere gli articoli seguenti:

- [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>D: perché viene visualizzato un record registrato Azure AD duplicato per il dispositivo Windows 10 Hybrid Azure AD aggiunto nell'elenco Azure AD Devices?

**R:** Quando gli utenti aggiungono gli account alle app in un dispositivo aggiunto a un dominio, è possibile che venga richiesto di **aggiungere un account a Windows?** Se si immette **Sì** quando viene richiesto, si registra il dispositivo in Azure AD. Il tipo di attendibilità è contrassegnato come registrato in Azure AD. Quando si abilita l'aggiunta ad Azure AD ibrido nell'organizzazione, il dispositivo viene aggiunto anche ad Azure AD ibrido. Vengono visualizzati due stati dei dispositivi per lo stesso dispositivo. 

Lo stato di aggiunto ad Azure AD ibrido ha la precedenza rispetto allo stato di registrato in Azure AD. Quindi, il dispositivo viene considerato ibrido Azure AD Unito per la valutazione dell'autenticazione e dell'accesso condizionale. È possibile eliminare in modo sicuro il record di dispositivo registrato in Azure AD dal portale di Azure AD. Informazioni su come [evitare o eliminare il doppio stato nel computer Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>D: perché gli utenti hanno problemi nei dispositivi Windows 10 Hybrid Azure AD aggiunti dopo la modifica dell'UPN?

**R:** Attualmente le modifiche UPN non sono completamente supportate con i dispositivi ibridi Azure AD aggiunti. Dopo aver modificato il proprio UPN, gli utenti potranno accedere al dispositivo e alle applicazioni locali, ma l'autenticazione ad Azure AD avrà esito negativo. Di conseguenza, gli utenti riscontreranno problemi a livello di accesso SSO e accesso condizionale sui propri dispositivi. A questo punto, è necessario separare il dispositivo dal Azure AD (eseguire "dsregcmd/Leave" con privilegi elevati) e riunirlo (si verifica automaticamente) per risolvere il problema. Microsoft sta lavorando per risolvere questo problema, che non riguarda tuttavia gli utenti che accedono con Windows Hello for Business. 

Le modifiche UPN sono supportate con l'aggiornamento di Windows 10 2004. Gli utenti dei dispositivi con questo aggiornamento non avranno alcun problema dopo aver modificato il UPN

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>D: i dispositivi Windows 10 Hybrid Azure AD aggiunti richiedono la visione del controller di dominio per ottenere l'accesso alle risorse cloud?

**R:** No, tranne quando viene modificata la password dell'utente. Dopo che Windows 10 Hybrid Azure AD join è stato completato e l'utente ha eseguito l'accesso almeno una volta, il dispositivo non necessita di una linea di visibilità per il controller di dominio per accedere alle risorse cloud. Windows 10 può ottenere Single Sign-On Azure AD le applicazioni da qualsiasi posizione con una connessione Internet, tranne quando viene modificata una password. Gli utenti che effettuano l'accesso con Windows Hello for business continuano a ottenere Single Sign-On Azure AD le applicazioni anche dopo una modifica della password, anche se non hanno una linea di visibilità per il controller di dominio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>D: cosa accade se un utente modifica la password e tenta di accedere al dispositivo Windows 10 Hybrid Azure AD aggiunto all'esterno della rete aziendale?

**R:** Se una password viene modificata all'esterno della rete aziendale, ad esempio usando Azure AD SSPR, l'accesso dell'utente con la nuova password avrà esito negativo. Per i dispositivi ibridi Azure AD aggiunti, l'Active Directory locale è l'autorità primaria. Quando un dispositivo non dispone di una linea di visibilità per il controller di dominio, non è in grado di convalidare la nuova password. Quindi, l'utente deve stabilire una connessione con il controller di dominio (tramite VPN o nella rete aziendale) prima di poter accedere al dispositivo con la nuova password. In caso contrario, possono accedere solo con la vecchia password a causa della funzionalità di accesso memorizzato nella cache di Windows. Tuttavia, la vecchia password viene invalidata da Azure AD durante le richieste di token e, di conseguenza, impedisce Single Sign-On e non riesce a tutti i criteri di accesso condizionale basati su dispositivo. Questo problema non si verifica se si usa Windows Hello for business. 

---

## <a name="azure-ad-register-faq"></a>Domande frequenti sulla registrazione in Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>D: Ricerca per categorie rimuovere uno stato Azure AD registrato per un dispositivo localmente?

**Un** 
- Per i dispositivi registrati Azure ad Windows 10, passare a **Impostazioni**  >  **account**  >  **Accedi all'ufficio o all'Istituto di istruzione**. Selezionare l'account e quindi **Disconnetti**. La registrazione del dispositivo è per profilo utente in Windows 10.
- Per iOS e Android, è possibile usare le **Impostazioni**dell'applicazione Microsoft Authenticator  >  **registrazione del dispositivo** e selezionare **Annulla registrazione del dispositivo**.
- Per macOS, è possibile usare l'applicazione Portale aziendale Microsoft Intune per annullare la registrazione del dispositivo dalla gestione e rimuovere eventuali registrazioni. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>D: come è possibile impedire agli utenti di aggiungere account di lavoro aggiuntivi (Azure AD registrati) sui dispositivi Windows 10 aziendali?

**R:** Abilitare il registro di sistema seguente per impedire agli utenti di aggiungere altri account di lavoro al dominio aziendale aggiunto, Azure AD aggiunto o ibrido Azure AD aggiunto ai dispositivi Windows 10. Questo criterio può essere usato anche per bloccare la registrazione accidentale dei computer aggiunti al dominio Azure AD con lo stesso account utente. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>D: È possibile registrare dispositivi Android o iOS in modalità BYOD?

**R:** Sì, ma solo con il servizio registrazione dispositivo di Azure e per i clienti ibridi. Non è supportato con il servizio di registrazione dei dispositivi locali in Active Directory Federation Services (ADFS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>D: Come si registra un dispositivo macOS?

**R:** Seguire questa procedura:

1.    [Creare criteri di conformità](/intune/compliance-policy-create-mac-os)
1.    [Definire un criterio di accesso condizionale per i dispositivi macOS](../active-directory-conditional-access-azure-portal.md) 

**Osservazioni**

- Per accedere alle risorse, gli utenti inclusi nei criteri di accesso condizionale necessitano [di una versione supportata di Office per MacOS](../conditional-access/concept-conditional-access-conditions.md) . 
- Durante il primo tentativo di accesso, agli utenti viene richiesto di registrare il dispositivo tramite il portale aziendale.

---
## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sui [dispositivi registrati in Azure AD](concept-azure-ad-register.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD](concept-azure-ad-join.md)
- Vedere altre informazioni sui [dispositivi aggiunti ad Azure AD in modalità ibrida](concept-azure-ad-join-hybrid.md)
