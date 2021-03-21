---
title: Introduzione al server di autenticazione a più fattori di Azure-Azure Active Directory
description: Guida introduttiva dettagliata al server Azure MFA locale
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742341"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introduzione al server Azure Multi-Factor Authentication

<center>

![Introduzione al server multi-factor authentication locale](./media/howto-mfaserver-deploy/server2.png)</center>

Questa pagina include una nuova installazione del server che include l'impostazione di Active Directory locale. Se il server MFA è già installato e lo si vuole aggiornare, vedere [Upgrade to the latest Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-upgrade.md) (Eseguire l'aggiornamento al server Azure Multi-Factor Authentication più recente). Per informazioni sull'installazione solo del servizio Web, vedere [Distribuzione del servizio Web App Mobile di Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-mobileapp.md).

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offre più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori durante gli eventi di accesso devono usare Multi-Factor Authentication di Azure AD basati sul cloud.
>
> Per iniziare a usare l'autenticazione a più fattori basata sul cloud, vedere [esercitazione: proteggere gli eventi di accesso utente con Azure AD multi-factor authentication](tutorial-enable-azure-mfa.md).
>
> I clienti esistenti che hanno attivato il server multi-factor authentication prima del 1 ° luglio 2019 possono scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="plan-your-deployment"></a>Pianificare la distribuzione

Prima di scaricare il server Azure Multi-Factor Authentication, valutare i propri requisiti in termini di carico e disponibilità elevata. Usare queste informazioni per decidere come e dove eseguire la distribuzione.

Un'indicazione valida per la quantità di memoria necessaria è data dal numero di utenti che in base alle previsioni eseguirà regolarmente l'autenticazione.

| Utenti | RAM |
| ----- | --- |
| 1-10.000 | 4 GB |
| 10.001-50.000 | 8 GB |
| 50.001-100.000 | 12 GB |
| 100.000-200.001 | 16 GB |
| Oltre 200.001 | 32 GB |

Se è necessario configurare più server per la disponibilità elevata o il bilanciamento del carico, sono disponibili diversi modi per definire questa configurazione con il server Azure MFA. Il primo server Azure MFA installato diventa il master. Tutti i server aggiuntivi diventano subordinati ed eseguono automaticamente la sincronizzazione degli utenti e della configurazione con il master. È quindi possibile configurare un server primario e usare il resto come backup oppure configurare il bilanciamento del carico tra tutti i server.

Quando un server Azure MFA master passa in modalità offline, le richieste di verifica in due passaggi vengono comunque elaborate dai server subordinati. Finché il master non viene riportato online o non viene alzato di livello un subordinato, tuttavia, non è possibile aggiungere nuovi utenti e gli utenti esistenti non possono aggiornare le proprie impostazioni.

### <a name="prepare-your-environment"></a>Preparare l'ambiente

Verificare che il server usato per Azure Multi-Factor Authentication soddisfi i requisiti seguenti:

| Requisiti del server Azure Multi-Factor Authentication | Descrizione |
|:--- |:--- |
| Hardware |<li>200 MB di spazio su disco rigido</li><li>processore idoneo per x32 o x64</li><li>1 GB o più di RAM</li> |
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 [(solo con](/lifecycle/faq/extended-security-updates) la sola unità di stato)</li><li>Windows 10</li><li>Windows 8.1, tutte le edizioni</li><li>Windows 8, tutte le edizioni</li><li>Windows 7, [tutte le edizioni (solo con](/lifecycle/faq/extended-security-updates) la sola unità di stato)</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 o versione successiva se si installa il portale utenti o l'SDK servizi Web</li> |
| Autorizzazioni | Account di amministratore del dominio o dell'organizzazione per la registrazione con Active Directory |

### <a name="azure-mfa-server-components"></a>Componenti del server Azure MFA

Il server Azure MFA è costituito da tre componenti Web:

* SDK servizio Web: consente la comunicazione con gli altri componenti ed è installato nel server Azure MFA.
* Portale utenti: sito Web IIS che consente agli utenti di registrarsi in Azure Multi-Factor Authentication (MFA) e gestire i relativi account.
* Servizio Web per app per dispositivi mobili: consente di usare un'app per dispositivi mobili come Microsoft Authenticator per la verifica in due passaggi.

Tutti e tre i componenti possono essere installati nello stesso server se questo è connesso a Internet. Se si separano i componenti, l'SDK servizio Web viene installato nel server Azure MFA, mentre il portale utenti e il servizio Web per app per dispositivi mobili vengono installati in un server con connessione Internet.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisiti del firewall del server Azure Multi-Factor Authentication

Ogni server Multi-Factor Authentication deve essere in grado di comunicare sulla porta 443 in uscita negli indirizzi seguenti:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Se i firewall in uscita sono limitati sulla porta 443, sarà necessario aprire gli intervalli di indirizzi IP seguenti:

| Subnet IP | Netmask | Intervallo IP |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Se non si usa la funzionalità di conferma dell'evento e gli utenti non usano app per dispositivi mobili per la verifica da dispositivi nella rete aziendale, sono necessari solo gli intervalli seguenti:

| Subnet IP | Netmask | Intervallo IP |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Scaricare il server MFA

Seguire questi passaggi per scaricare il server Azure Multi-Factor Authentication dal portale di Azure:

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offre più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori (multi-factor authentication) dagli utenti devono usare Multi-Factor Authentication di Azure AD basati sul cloud.
>
> Per iniziare a usare l'autenticazione a più fattori basata sul cloud, vedere [esercitazione: proteggere gli eventi di accesso utente con Azure AD multi-factor authentication](tutorial-enable-azure-mfa.md).
>
> I clienti esistenti che hanno attivato il server multi-factor authentication prima del 1 ° luglio 2019 possono scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto. I passaggi seguenti funzionano solo se si è un cliente del server multi-factor authentication esistente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Cercare e selezionare *Azure Active Directory*. Selezionare autenticazione a più fattori di **sicurezza**  >  .
3. In **Manager Server** di autenticazione a più fattori selezionare **Impostazioni server**.
4. Selezionare **Scarica** e seguire le istruzioni nella pagina di download per salvare il programma di installazione. 

   ![Scaricare il server di autenticazione a più fattori dal portale di Azure](./media/howto-mfaserver-deploy/downloadportal.png)

5. Tenere aperta questa pagina, perché sarà necessaria dopo l'esecuzione del programma di installazione.

## <a name="install-and-configure-the-mfa-server"></a>Installare e configurare il server MFA

Dopo averlo scaricato, è possibile installare e configurare il server. Assicurarsi che il server in cui viene installato soddisfi i requisiti elencati nella sezione relativa alla pianificazione.

1. Fare doppio clic sul file eseguibile.
2. Nella schermata di selezione della cartella di installazione, assicurarsi che la cartella sia corretta e fare clic su **Avanti**.
3. Al termine dell'installazione, fare clic su **Fine**. Viene avviata la configurazione guidata.
4. Nella schermata iniziale della configurazione guidata selezionare **Non utilizzare la Configurazione guidata autenticazione** e fare clic su **Avanti**. La procedura guidata viene chiusa e viene avviato il server.

   ![Ignora utilizzo della configurazione guidata autenticazione](./media/howto-mfaserver-deploy/skip2.png)

5. Tornare alla pagina da cui è stato scaricato il server e fare clic sul pulsante **Genera credenziali di attivazione**. Copiare queste informazioni nel server Azure MFA nelle apposite caselle e fare clic su **Attiva**.

> [!NOTE]
> Solo gli amministratori globali sono in grado di generare le credenziali di attivazione nella portale di Azure.

## <a name="send-users-an-email"></a>Inviare agli utenti un messaggio di posta elettronica

Per semplificare l'implementazione, consentire al server MFA di comunicare con gli utenti. Il server MFA potrà inviare un messaggio di posta elettronica per informare gli utenti che sono stati registrati per la verifica in due passaggi.

Il messaggio di posta elettronica viene determinato dalla configurazione degli utenti per la verifica in due passaggi. Ad esempio, se è possibile importare i numeri di telefono dalla directory aziendale, il messaggio di posta elettronica deve includere i numeri di telefono predefiniti in modo che gli utenti sappiano cosa aspettarsi. Se i numeri di telefono non vengono importati o è previsto che gli utenti usino l'app per dispositivi mobili, inviare un messaggio di posta elettronica per invitarli a completare la registrazione dell'account. Includere nel messaggio un collegamento ipertestuale al portale utenti di Azure Multi-Factor Authentication.

Il contenuto del messaggio di posta elettronica varia a seconda del metodo di verifica impostato per l'utente (telefonata, SMS o app per dispositivi mobili). Se, ad esempio, l'utente deve usare un PIN quando esegue l'autenticazione, il messaggio di posta elettronica indicherà quale PIN iniziale è stato impostato. Agli utenti viene richiesto di modificare il PIN nel corso della prima verifica.

### <a name="configure-email-and-email-templates"></a>Configurare l'indirizzo di posta elettronica e i modelli di messaggio di posta elettronica

Fare clic sull'icona del messaggio di posta elettronica a sinistra per configurare le impostazioni per l'invio dei messaggi di posta elettronica. In questa pagina è possibile immettere le informazioni SMTP del server di posta elettronica e inviare un messaggio selezionando la casella di controllo **Invia messaggi agli utenti**.

![Configurazione della posta elettronica per il server MFA](./media/howto-mfaserver-deploy/email1.png)

Nella scheda Contenuto messaggio è possibile visualizzare i modelli di messaggio di posta elettronica disponibili per la selezione. A seconda della modalità di configurazione scelta per l'esecuzione della verifica in due passaggi, è possibile scegliere il modello che meglio si adatta alle proprie esigenze.

![Modelli di posta elettronica del server multi-factor authentication nella console](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Importare gli utenti da Active Directory

Ora che il server è installato, è possibile aggiungere gli utenti. È possibile scegliere di crearli manualmente, importare gli utenti da Active Directory o configurare la sincronizzazione automatica con Active Directory.

### <a name="manual-import-from-active-directory"></a>Importazione manuale da Active Directory

1. Nel server Azure MFA, a sinistra, selezionare **Utenti**.
2. Nella parte inferiore, selezionare **Importa da Active Directory**.
3. A questo punto è possibile eseguire la ricerca di singoli utenti o effettuare una ricerca delle unità organizzative con utenti all'interno di Active Directory. In questo caso, viene specificata l'unità organizzativa utenti.
4. Selezionare tutti gli utenti a destra e fare clic su **Importa**. Verrà visualizzata una finestra popup che informa che tutte le operazioni sono state eseguite correttamente. Chiudere la finestra di importazione.

   ![Importazione utente server multi-factor authentication da Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Sincronizzazione automatica con Active Directory

1. Nel server Azure MFA selezionare **Integrazione directory** sulla sinistra.
2. Passare alla scheda **Sincronizzazione**.
3. Nella parte inferiore scegliere **Aggiungi**.
4. Nella casella **Aggiungi elemento di sincronizzazione** visualizzata scegliere il dominio, OU **oppure** gruppo di sicurezza, le impostazioni, le impostazioni predefinite del metodo e le impostazioni predefinite della lingua per questa attività di sincronizzazione. Quindi fare clic su **Aggiungi**.
5. Selezionare la casella **Abilita sincronizzazione con Active Directory** e scegliere un **intervallo di sincronizzazione** compreso tra un minuto e 24 ore.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Come gestire i dati utente tramite il server Multi-Factor Authentication

Quando si usa il server di Multi-Factor Authentication (multi-factor authentication) in locale, i dati di un utente vengono archiviati nei server locali. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue una verifica in due passaggi, Azure MFA Server invia i dati al servizio cloud Azure MFA per eseguire la verifica. Quando queste richieste di autenticazione vengono inviate al servizio cloud, i campi seguenti vengono inviati nella richiesta e dei log, in modo che siano disponibili nei report di autenticazione/utilizzo del cliente. Alcuni campi sono facoltativi e possono essere abilitati o disabilitati nel server Multi-Factor Authentication. La comunicazione dal server MFA al servizio cloud MFA usa SSL/TLS sulla porta 443 in uscita. Questi campi sono:

* ID univoco: nome utente o ID interno del MFA
* Nome e cognome (facoltativo)
* Indirizzo di posta elettronica (facoltativo)
* Numero di telefono: quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* Token del dispositivo: quando si esegue l'autenticazione con l'app per dispositivi mobili
* Modalità di autenticazione
* Risultato dell'autenticazione
* Nome del server MFA
* IP del server MFA
* IP client: se disponibile

Oltre a questi campi, il risultato della verifica (esito positivo/rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme ai dati di autenticazione e sono disponibili nei report di autenticazione/utilizzo.

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni per le chiamate telefoniche non saranno disponibili per gli utenti del server MFA nei tenant di Azure AD gratuiti/di prova. Questa modifica non ha alcun effetto sui messaggi SMS. La chiamata telefonica continuerà a essere disponibile per gli utenti nei tenant di Azure AD a pagamento. Questa modifica ha effetto solo sui tenant di Azure AD gratuiti/di prova.

## <a name="back-up-and-restore-azure-mfa-server"></a>Eseguire il backup e il ripristino del server Azure MFA

In qualsiasi sistema è importante assicurarsi di avere a disposizione un backup valido.

Per eseguire il backup del server Azure MFA, occorre avere una copia della cartella **C:\Program Files\Multi-Factor Authentication Server\Data** e del file **PhoneFactor.pfdata**. 

In caso di ripristino, seguire questa procedura:

1. Reinstallare il server Azure MFA in un nuovo server.
2. Attivare il nuovo server Azure MFA.
3. Arrestare il servizio **MultiFactorAuth**.
4. Sovrascrivere il file **PhoneFactor.pfdata** con la copia di backup.
5. Avviare il servizio **MultiFactorAuth**.

Il nuovo server è ora operativo con i dati utente e la configurazione di backup originali.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gestione dei protocolli TLS/SSL e dei pacchetti di crittografia

Dopo avere eseguito l'aggiornamento o avere installato il server MFA 8.x o versione successiva, è consigliabile disabilitare o rimuovere i pacchetti di crittografia meno recenti e meno sicuri, a meno che non siano richiesti dall'organizzazione. Altre informazioni sul completamento di questa attività sono disponibili nell'articolo [Gestione dei protocolli SSL/TLS e dei pacchetti di crittografia per AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

## <a name="next-steps"></a>Passaggi successivi

- Installare e configurare il [portale utenti](howto-mfaserver-deploy-userportal.md) per la modalità self-service per gli utenti finali.
- Installare e configurare il server Azure MFA con [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), l'[autenticazione RADIUS](howto-mfaserver-dir-radius.md) o l'[autenticazione LDAP](howto-mfaserver-dir-ldap.md).
- Installare e configurare [Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](howto-mfaserver-nps-rdg.md).
- [Distribuire il servizio Web App per dispositivi mobili server multi-factor authentication di Azure](howto-mfaserver-deploy-mobileapp.md).
- [Scenari avanzati con Azure Multi-Factor Authentication e soluzioni VPN di terze parti](howto-mfaserver-nps-vpn.md).