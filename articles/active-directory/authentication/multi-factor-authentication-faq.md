---
title: Azure Multi-Factor Authentication FAQ - Azure Active Directory
description: Domande frequenti e risposte su Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e5a533bac5d7adac8b0423eff6c05f797c56f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652139"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Domande frequenti su Azure Multi-Factor Authentication

Queste domande frequenti offrono risposte su Azure Multi-Factor Authentication e sull'uso del servizio Multi-Factor Authentication. Le domande sono suddivise fra servizio in generale, modelli di fatturazione, esperienze utente e risoluzione dei problemi.

## <a name="general"></a>Generale

> [!IMPORTANT]
> A partire dal 1 luglio 2019, Microsoft non offrirà più MFA Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dai propri utenti devono usare L'autenticazione a più fattori di Azure basata su cloud. I clienti esistenti che hanno attivato MFA Server prima del 1 luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.
> 
> Le licenze basate sul consumo non sono più disponibili per i nuovi clienti a partire dal 1 settembre 2018.
> A partire dal 1 settembre 2018 non è più possibile creare nuovi provider di autenticazione. I provider di autenticazione esistenti potranno continuare a essere usati e aggiornato. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

> [!NOTE]
> Le informazioni condivise di seguito relative al server Azure Multi-Factor Authentication sono applicabili solo agli utenti che hanno già il server MFA in esecuzione.

**D: In che modo il server Azure Multi-Factor Authentication gestisce i dati utente?**

Con il server Multi-Factor Authentication, i dati utente vengono archiviati solo nei server locali. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue l'autenticazione a due fattori, il server Multi-Factor Authentication invia dati al servizio cloud Azure Multi-Factor Authentication per l'autenticazione. La comunicazione tra il server Multi-Factor Authentication e il servizio cloud Multi-Factor Authentication avviene tramite Secure Sockets Layer (SSL) o Transport Layer Security (TLS) sulla porta 443 in uscita.

Quando le richieste di autenticazione vengono inviate al servizio cloud, vengono raccolti dati per i report di autenticazione e uso. Di seguito sono riportati i campi di dati inclusi nei log di verifica in due passaggi:

* **ID univoco:** nome utente o ID del server Multi-Factor Authentication locale
* **Nome e Cognome** (facoltativo)
* **Indirizzo e-mail** (opzionale)
* **Numero di telefono:** quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* **Token del dispositivo:** quando si esegue l'autenticazione con l'app per dispositivi mobili
* **Modalità di autenticazione**
* **Risultato dell'autenticazione**
* **Nome del server Multi-Factor Authentication**
* **IP del server Multi-Factor Authentication**
* **IP client:** se disponibile

I campi facoltativi possono essere configurati nel server Multi-Factor Authentication.

Il risultato della verifica (esito positivo o rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme con i dati di autenticazione. Questi dati sono disponibili nei report di autenticazione e uso.

**D: Quali codici brevi SMS vengono usati per inviare i messaggi SMS agli utenti?**

Negli Stati Uniti Microsoft usa i seguenti codici brevi SMS:

   * 97671
   * 69829
   * 51789
   * 99399

In Canada Microsoft usa i seguenti codici brevi SMS:

   * 759731 
   * 673801

Microsoft non garantisce l'invio coerente di prompt Multi-Factor Authentication via SMS o vocali allo stesso numero. Nell'interesse degli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento per eseguire modifiche di route per migliorare il recapito degli SMS. Microsoft non supporta i codici brevi per i paesi oltre gli Stati Uniti e il Canada.

## <a name="billing"></a>Fatturazione

È possibile rispondere alla maggior parte delle domande sulla fatturazione facendo riferimento alla pagina [Multi-Factor Authentication Prezzi](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) o alla documentazione relativa a [Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**D: Alla mia organizzazione vengono addebitati i costi delle telefonate e degli SMS utilizzati per l'autenticazione?**

No, alle aziende non vengono addebitati i costi delle singole telefonate o SMS inviati agli utenti tramite Azure Multi-Factor Authentication. Se si usa un provider di autenticazione MFA con un accordo basato sul numero di autenticazioni, verrà fatturata ciascuna autenticazione ma non il metodo utilizzato.

Potrebbero essere addebitate agli utenti le chiamate telefoniche o gli SMS che ricevono, in base al loro servizio telefonico personale.

**D: Con il modello di fatturazione per utente mi vengono addebitati tutti gli utenti o solo quelli che hanno eseguito la verifica in due passaggi?**

La fatturazione si basa sul numero di utenti configurati per usare Multi-Factor Authentication, indipendentemente dal fatto che abbiano eseguito la verifica in due passaggi in quel mese.

**D: Come funziona la fatturazione per Multi-Factor Authentication?**

Quando si crea un provider di MFA per utente o per autenticazione, la sottoscrizione di Azure dell'organizzazione viene fatturata mensilmente in base all'utilizzo. Questo modello di fatturazione è simile a quello applicato da Azure per l'uso delle macchine virtuali e dei siti Web.

Quando si acquista una sottoscrizione per Azure Multi-Factor Authentication, l'organizzazione paga solo il diritto di licenza annuale per ogni utente. La fatturazione avviene in questo modo per le licenze MFA e per i bundle Office 365, Azure AD Premium o Enterprise Mobility + Security. 

Per altre informazioni sulle opzioni, vedere [Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**D: È disponibile una versione gratuita di Azure Multi-Factor Authentication?**

In alcuni casi, sì.

Multi-Factor Authentication for Azure Administrators offre un sottoinsieme di funzionalità di Azure MFA senza alcun costo per l'accesso ai servizi online Microsoft, tra cui il portale di [Azure](https://portal.azure.com) e l'interfaccia di amministrazione di [Microsoft 365.](https://admin.microsoft.com) Questa offerta è valida solo per gli amministratori globali nelle istanze di Azure Active Directory che non dispongono della versione completa di Azure MFA tramite una licenza MFA, un bundle o un provider autonomo in base al consumo. Se gli amministratori usano la versione gratuita e quindi si acquista una versione completa di Azure MFA, tutti gli amministratori globali vengono passati automaticamente alla versione a pagamento.

Multi-Factor Authentication per utenti di Office 365 offre un sottoinsieme delle funzionalità di Azure MFA senza costi aggiuntivi per l'accesso ai servizi Office 365, inclusi Exchange Online e SharePoint Online. Questa offerta è valida per gli utenti con una licenza Office 365 assegnata quando l'istanza corrispondente di Azure Active Directory non dispone della versione completa di Azure MFA tramite una licenza MFA, un bundle o un provider autonomo in base al consumo.

**D: L'organizzazione può passare dai modelli di fatturazione in base all'utilizzo per utente a quelli per autenticazione in qualsiasi momento?**

Se l'organizzazione acquista MFA come servizio autonomo con fatturazione in base al consumo, il modello di fatturazione viene scelto quando si crea un provider di MFA. Dopo la creazione del provider di MFA non è più possibile cambiare il modello di fatturazione. 

Se il provider di autenticazione a più fattori *non* è collegato a un tenant di Azure AD o si collega il nuovo provider di autenticazione a più fattori a un tenant di Azure AD diverso, le impostazioni utente e le opzioni di configurazione non vengono trasferite. Inoltre, i server Azure MFA esistenti devono essere riattivati usando le credenziali di attivazione generate tramite il nuovo provider di MFA. La riattivazione dei server MFA per collegarli al nuovo provider di MFA non inciderà sull'autenticazione con chiamata telefonica e SMS, ma le notifiche dell'app mobile smetteranno di funzionare per tutti gli utenti fino a quando riattiveranno l'app mobile.

Per altre informazioni sui provider di MFA, vedere [Introduzione all'uso di un provider di Azure Multi-Factor Authentication](concept-mfa-authprovider.md).

**D: L'organizzazione può passare dal modello di fatturazione in base al consumo e la sottoscrizione (modello basato su licenza) in qualsiasi momento?**

In alcuni casi, sì.

Se la directory include un provider di Azure Multi-Factor Authentication *per utente*, è possibile aggiungere licenze MFA. Gli utenti con licenza non vengono conteggiati nella fatturazione in base al consumo per utente. Gli utenti senza licenza possono comunque essere abilitati per MFA tramite il provider di MFA. Se si acquistano e assegnano licenze per tutti gli utenti configurati per l'uso di Multi-Factor Authentication, è possibile eliminare il provider di Azure Multi-Factor Authentication. Se in futuro si avranno più utenti che licenze, sarà sempre possibile creare un altro provider di MFA per utente.

Se la directory include un provider di Multi-Factor Authentication *per autenticazione*, viene sempre fatturata ogni autenticazione, fintanto che il provider di MFA è collegato alla sottoscrizione. È possibile assegnare le licenze MFA agli utenti, ma verrà comunque fatturata ogni richiesta di verifica in due passaggi, che provenga o meno da un utente con assegnata una licenza di autenticazione a più fattori.

**D: È necessario che l'organizzazione usi e sincronizzi le identità per usare Azure Multi-Factor Authentication?**

Se l'organizzazione usa un modello di fatturazione in base al consumo, è possibile usare Azure Active Directory ma non è obbligatorio. Se il provider di MFA non è collegato a un tenant di Azure Active Directory, è possibile distribuire il server di Azure Multi-Factor Authentication in locale.

Quando si usa il modello di licenza, è necessario usare Azure Active Directory in quanto le licenze vengono aggiunte al tenant di Azure AD quando vengono acquistate e assegnate agli utenti nella directory.

## <a name="manage-and-support-user-accounts"></a>Gestire e supportare gli account utente

**D: Cosa devo dire agli utenti di fare se non ricevono una risposta sul proprio telefono?**

Chiedere agli utenti di provare fino a 5 volte in 5 minuti per ottenere una telefonata o un SMS per l'autenticazione. Microsoft usa più provider per la distribuzione di chiamate e messaggi SMS. Se il problema persiste, aprire un caso di supporto con Microsoft per altre informazioni sulla risoluzione dei problemi.

Se i passaggi precedenti non restituiscono i risultati sperati, è auspicabile che tutti gli utenti abbiano configurato più metodi di verifica. Dire loro di tentare nuovamente l'accesso, ma selezionando un altro metodo di verifica nella pagina di accesso.

È possibile invitare gli utenti a leggere l'articolo [Problemi con la verifica in due passaggi](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**D: Cosa fare se uno degli utenti non riesce ad accedere al proprio account?**

È possibile reimpostare l'account utente chiedendo all'utente stesso di ripetere il processo di registrazione. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](howto-mfa-userdevicesettings.md).

**D: Cosa fare se uno degli utenti perde un telefono che usa le password di app?**

L'amministratore può eliminare tutte le password di app dell'utente per impedire eventuali accessi non autorizzati. Dopo aver sostituito il dispositivo, l'utente potrà ricrearle. Altre informazioni sulla [gestione delle impostazioni utente e dispositivo con Azure Multi-Factor Authentication nel cloud](howto-mfa-userdevicesettings.md).

**D: Cosa accade se un utente non riesce ad accedere alle applicazioni non basate su browser?**

Se l'organizzazione usa ancora client legacy ed è stato [consentito l'uso delle password di app](howto-mfa-mfasettings.md#app-passwords), gli utenti non possono eseguire l'accesso a questi client legacy con il loro nome utente e password. Devono invece [impostare le password di app](../user-help/multi-factor-authentication-end-user-app-passwords.md). Gli utenti devono cancellare (eliminare) le informazioni di accesso, riavviare l'app e quindi accedere con il proprio nome utente e *password di app* anziché le password normali.

Se l'organizzazione non ha client legacy, non è consigliabile consentire agli utenti di creare password di app.

> [!NOTE]
> Autenticazione moderna per i client di Office 2013
>
> Le password di app sono necessarie solo per le app che non supportano l'autenticazione moderna. I client Office 2013 supportano i protocolli dell'autenticazione moderna, ma devono essere configurati. Ora l'autenticazione moderna è disponibile per qualsiasi cliente che esegue l'aggiornamento di marzo 2015 o versione successiva per Office 2013. Per ulteriori informazioni, vedere il post di blog [Aggiornato Office 365 autenticazione moderna](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**D: Gli utenti dicono che a volte non ricevono il messaggio di testo o il timeout della verifica.**

Il recapito dei messaggi SMS non è garantito perché esistono fattori incontrollabili che potrebbero influire sull'affidabilità del servizio. Questi fattori includono il paese/regione di destinazione, l'operatore di telefonia mobile e la potenza del segnale.

Se agli utenti capita spesso di non ricevere in modo affidabile gli SMS, suggerire loro di usare il metodo dell'app per dispositivi mobili o della chiamata telefonica. L'app per dispositivi mobili può ricevere notifiche sia su rete cellulare che Wi-Fi. L'app per dispositivi mobili può generare codici di verifica anche in caso di totale assenza di segnale. L'app Microsoft Authenticator è disponibile per [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

**D: È possibile cambiare il tempo che gli utenti hanno a disposizione per immettere il codice di verifica dall'SMS prima del timeout?**

In alcuni casi sì. 

Per SMS unidirezionali con Azure MFA Server 7.0 o versione successiva, è possibile configurare il timeout impostando una chiave del Registro di sistema. Dopo l'invio dell'SMS dal servizio cloud MFA, a MFA Server viene restituito il codice di verifica (o passcode monouso). MFA Server archivia il codice in memoria per 300 secondi per impostazione predefinita. Se l'utente non immette il codice dopo 300 secondi, l'autenticazione viene negata. Usare questa procedura per modificare l'impostazione di timeout predefinita:

1. Passare ad HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Creare una chiave DWORD del Registro di sistema denominata **pfsvc_pendingSmsTimeoutSeconds** e impostare il tempo, in secondi, per cui il server Azure MFA dovrà archiviare i passcode monouso.

>[!TIP] 
>Se sono disponibili più MFA Server, il codice di verifica inviato all'utente e noto solo per il server che ha elaborato la richiesta di autenticazione originale. Quando l'utente immette il codice, la richiesta di autenticazione da convalidare deve essere inviata allo stesso server. Se la convalida del codice viene inviata a un server diverso, l'autenticazione viene negata. 

Se gli utenti non rispondono all'SMS entro il periodo di timeout definito, l'autenticazione viene negata. 

Non è possibile configurare l'impostazione di timeout per SMS unidirezionali con Azure MFA nel cloud, inclusi l'adattatore AD FS o l'estensione Server dei criteri di rete. Azure AD archivia il codice di verifica per 180 secondi. 

**D: È possibile usare i token hardware con il server Azure multi-Factor Authentication?**

Se si usa il server Azure Multi-Factor Authentication, è possibile importare token OATH TOPT di terze parti e quindi usarli per la verifica in due passaggi.

È possibile usare token ActiveIdentity che siano token OATH TOTP se si inserisce la chiave privata in un file CSV e lo si importa nel server Azure Multi-Factor Authentication. I token OATH possono essere usati con Active Directory Federation Services (AD FS), con l'autenticazione basata su moduli di Internet Information Server (IIS) e con il servizio RADIUS (Remote Authentication Dial-In User Service), a condizione che il sistema client possa accettare l'input dell'utente.

È possibile importare token OATH TOTP di terze parti con i formati seguenti:  

- Portable Symmetric Key Container (PSKC)  
- CSV se il file contiene un numero di serie, una chiave privata in formato Base 32 e un intervallo di tempo  

**D: È possibile usare il server di Azure Multi-Factor Authentication per proteggere Servizi Terminal?**

Sì, ma se si usa Windows Server 2012 R2 o versione successiva è possibile proteggere i Servizi Terminal solo usando Gateway Desktop remoto.

Le modifiche della sicurezza in Windows Server 2012 R2 hanno variato le modalità di connessione del server di Azure Multi-Factor Authentication al pacchetto di sicurezza dell'autorità di protezione locale in Windows Server 2012 e versioni precedenti. Per le versioni di Servizi terminal in Windows Server 2012 o versioni precedenti è possibile [proteggere un'applicazione con l'autenticazione di Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si usa Windows Server 2012 R2, è necessario un Gateway Desktop remoto.

**D: È stato configurato l'ID chiamante in MFA Server ma gli utenti continuano a ricevere le chiamate di Multi-Factor Authentication da un chiamante anonimo.**

Quando vengono effettuate chiamate MFA tramite la rete telefonica pubblica, queste vengono a volte indirizzate su un gestore che non supporta l'ID chiamante. Per questo motivo, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori lo invia sempre.

**D: Perché agli utenti viene chiesto di registrare le informazioni di sicurezza?**
Esistono diversi motivi per cui agli utenti potrebbe essere chiesto di registrare le informazioni di sicurezza:

- L'utente è stato abilitato per MFA dall'amministratore in Azure AD, ma non ha informazioni di sicurezza registrate per il proprio account.
- L'utente è stato abilitato per la reimpostazione password self-service in Azure AD. Le informazioni di sicurezza lo aiuteranno a reimpostare la password in futuro, se dovesse dimenticarla.
- L'utente ha eseguito l'accesso a un'applicazione che dispone di un criterio di accesso condizionale per richiedere l'autenticazione a più fattori e non è stato registrato in precedenza per l'autenticazione a più fattori.
- L'utente sta registrando un dispositivo con Azure AD (inclusa l'aggiunta ad Azure AD) e l'organizzazione richiede MFA per la registrazione del dispositivo, ma l'utente non è stato registrato in precedenza per MFA.
- L'utente sta generando Windows Hello for Business in Windows 10 (che richiede l'autenticazione a più fattori) e non è stato registrato in precedenza per l'autenticazione a più fattori.
- L'organizzazione ha creato e attivato un criterio di registrazione MFA che è stato applicato all'utente.
- L'utente è stato registrato in precedenza per MFA, ma ha scelto un metodo di verifica che un amministratore ha poi disabilitato. Pertanto l'utente deve eseguire di nuovo la registrazione MFA per scegliere un nuovo metodo di verifica predefinito.

## <a name="errors"></a>Errors

**D: Cosa devono fare gli utenti se viene visualizzato un messaggio di errore "Richiesta di autenticazione non per un account attivato" quando si utilizzano le notifiche delle app per dispositivi mobili?**

Seguire questa procedura per rimuovere il proprio account dall'app per dispositivi mobili e quindi aggiungerlo di nuovo:

1. Passare al [profilo nel portale di Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account dell'organizzazione.
2. Selezionare **Verifica aggiuntiva di sicurezza**.
3. Rimuovere l'account esistente dall'app per dispositivi mobili.
4. Fare clic su **Configura**e seguire le istruzioni per riconfigurare l'app per dispositivi mobili.

**D: Cosa è necessario fare se viene visualizzato un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non basata su browser?**

L'errore 0x800434D4L si verifica quando si tenta di accedere a un'applicazione diversa da un browser, installata in un computer locale, che non funziona con account che richiedono la verifica in due passaggi.

Una soluzione alternativa per questo errore consiste nel disporre di account utente separati per le operazioni correlate all'amministrazione e per quelle non amministrative. In un secondo momento è possibile collegare le cassette postali tra l'account amministratore e l'account non amministratore in modo da poter accedere a Outlook usando l'account non amministratore. Per altri dettagli su questa soluzione, vedere come [consentire a un amministratore di aprire e visualizzare il contenuto della cassetta postale di un utente](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passaggi successivi

Se la domanda non ha ricevuto risposta qui, riproporla nei commenti in fondo alla pagina. In alternativa, di seguito vengono elencate alcune opzioni aggiuntive per ottenere assistenza:

* Cercare nella [Knowledge Base del supporto tecnico Microsoft](https://support.microsoft.com) le soluzioni ai problemi tecnici comuni.
* È possibile cercare e sfogliare le domande e risposte tecniche dalla community o porre le proprie domande nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* I clienti legacy di PhoneFactor che hanno domande o necessitano di assistenza per reimpostare una password possono usare il collegamento per la [reimpostazione della password](mailto:phonefactorsupport@microsoft.com) per aprire una richiesta di assistenza.
* Contattare un tecnico del supporto tramite [Azure Multi-Factor Authentication - PhoneFactor](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Microsoft, è utile includere il maggior numero possibile di informazioni relative al problema. Tali informazioni includono la pagina in cui viene visualizzato l'errore, il codice dell'errore specifico, l'ID della sessione specifico e l'ID dell'utente che visualizza l'errore.
