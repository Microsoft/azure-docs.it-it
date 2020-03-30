---
title: Gestire gli account di amministratore per l'accesso di emergenza - Azure AD Documenti Microsoft
description: Questo articolo descrive come usare gli account di accesso di emergenza per evitare di essere inavvertitamente bloccati dall'organizzazione di Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028137"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gestire gli account di accesso di emergenza in Azure AD

È importante evitare di essere bloccati accidentalmente dall'organizzazione di Azure Active Directory (Azure AD) perché non è possibile accedere o attivare l'account di un altro utente come amministratore. È possibile ridurre l'impatto della mancanza accidentale di accesso amministrativo creando due o più account di accesso di *emergenza* nell'organizzazione.

Gli account di accesso di emergenza hanno privilegi elevati e non sono assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati agli scenari di emergenza o "break glass" in cui non è possibile utilizzare i normali account amministrativi. Ti consigliamo di mantenere l'obiettivo di limitare l'uso dell'account di emergenza solo i momenti in cui è assolutamente necessario.

Questo articolo fornisce indicazioni per la gestione degli account di accesso di emergenza in Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Perché utilizzare un account di accesso di emergenzaWhy use an emergency access account

Per un'organizzazione può essere necessario usare un account di accesso di emergenza nelle situazioni seguenti:

- Gli account utente sono federati e la federazione non è attualmente disponibile a causa di un'interruzione del funzionamento della rete cellulare o di un problema tecnico del provider di identità. Se ad esempio l'host del provider di identità dell'ambiente è stato disattivato, gli utenti potrebbero non essere in grado di effettuare l'accesso quando Azure AD li reindirizza al provider di identità.
- Gli amministratori vengono registrati tramite Azure Multi-Factor Authentication e tutti i loro dispositivi non sono disponibili oppure il servizio non è disponibile. Gli utenti potrebbero non essere in grado di completare l'autenticazione a più fattori per attivare un ruolo. Ad esempio, un'interruzione del funzionamento della rete cellulare può impedire di rispondere alle chiamate telefoniche o di ricevere SMS, gli unici due meccanismi di autenticazione registrati per il dispositivo.
- L'utente con l'accesso di amministratore globale più recente ha lasciato l'organizzazione. Azure Active Directory impedisce l'eliminazione dell'ultimo account di amministratore globale, ma non impedisce l'eliminazione o la disabilitazione dell'account a livello locale. Entrambi i casi potrebbero rendere impossibile per l'organizzazione ripristinare l'account.
- Circostanze impreviste come una calamità naturale durante la quale la rete cellulare o altre reti potrebbero non essere disponibili. 

## <a name="create-emergency-access-accounts"></a>Creare account di accesso di emergenza

Creare due o più account di accesso di emergenza. Devono essere account solo cloud che usano il dominio \*.onmicrosoft.com e che non sono federati o sincronizzati da un ambiente locale.

Durante la configurazione di questi account, devono essere soddisfatti i requisiti seguenti:

- Gli account di accesso di emergenza non devono essere associati a un utente specifico dell'organizzazione. Assicurarsi che gli account non siano connessi a cellulari forniti ai dipendenti, token hardware che viaggiano con i singoli dipendenti o altre credenziali specifiche del dipendente. Questa precauzione consente di affrontare le situazioni in cui un singolo dipendente è irraggiungibile quando sono necessarie le credenziali. È importante verificare che tutti i dispositivi registrati vengano conservati in una posizione nota e sicura con più mezzi di comunicazione con Azure AD.
- Il meccanismo di autenticazione usato per un account di accesso di emergenza deve essere diverso da quello usato dagli altri account amministrativi, inclusi altri account di accesso di emergenza.  Ad esempio, se l'amministratore usuale accede tramite l'autenticazione a più fattori locale, Azure MFA sarebbe un meccanismo diverso.  Tuttavia, se Azure MFA è la parte principale dell'autenticazione per gli account amministrativi, considerare un approccio diverso per questi, ad esempio l'utilizzo di accesso condizionale con un provider di autenticazione a più fattori di terze parti.
- Il dispositivo o le credenziali non devono scadere o trovarsi nell'ambito della pulizia automatica per mancanza di utilizzo.  
- È necessario rendere permanente l'assegnazione del ruolo di amministratore globale per gli account di accesso di emergenza. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Escludere almeno un account dall'autenticazione a più fattori basata su telefono

Per ridurre il rischio di attacchi derivanti da una password compromessa, in Azure AD è consigliabile richiedere l'autenticazione a più fattori per tutti gli utenti singoli. Questo gruppo include gli amministratori e tutti gli altri utenti (ad esempio dirigenti del reparto finanziario) per cui un account compromesso avrebbe un impatto significativo.

Tuttavia, almeno uno degli account di accesso di emergenza deve avere un meccanismo di autenticazione a più fattori diverso rispetto agli altri account non di emergenza. Questo include le soluzioni di autenticazione a più fattori di terze parti. Se si dispone di criteri di accesso condizionale che richiedono [l'autenticazione](../authentication/howto-mfa-userstates.md) a più fattori per ogni amministratore di Azure AD e altri software connessi come app SaaS (Service), è necessario escludere gli account di accesso di emergenza da questo requisito e configurare un meccanismo diverso. Inoltre, assicurarsi che gli account non abbiano criteri di autenticazione a più fattori per utente.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Escludere almeno un account dai criteri di accesso condizionale

Durante un'emergenza, non è desiderabile che un criterio possa potenzialmente bloccare l'accesso necessario per risolvere un problema. Almeno un account di accesso di emergenza deve essere escluso da tutti i criteri di accesso condizionale. Se è stato abilitato un [criterio di base](../conditional-access/baseline-protection.md), è necessario escludere gli account di accesso di emergenza.

## <a name="federation-guidance"></a>Linee guida per la federazione

Un'altra possibilità, per le organizzazioni che usano Azure AD Domain Services e AD FS o un provider di identità simile per eseguire la federazione con Azure AD, consiste nel configurare un account di accesso di emergenza la cui attestazione MFA può essere fornita dal provider.  Ad esempio, l'account di accesso di emergenza potrebbe essere supportato da una coppia di certificato e chiave archiviata su una smart card.  Quando l'utente viene autenticato in Active Directory, AD FS può fornire un'attestazione ad Azure AD per indicare che l'utente ha soddisfatto i requisiti MFA.  Anche con questo approccio, le organizzazioni devono comunque disporre di account di accesso di emergenza basati sul cloud nel caso in cui non sia possibile stabilire la federazione. 

## <a name="store-account-credentials-safely"></a>Archiviare le credenziali dell'account in modo sicuro

Le organizzazioni devono garantire che le credenziali per gli account di accesso di emergenza siano sempre conservate in modo sicuro e siano note solo a coloro che sono autorizzati a usarle. Alcuni clienti usano una smart card, mentre altri usano password. Una password per un account di accesso di emergenza in genere è suddivisa in due o tre parti, scritte su pezzi di carta separati e archiviate in casseforti a prova di fuoco in luoghi sicuri e separati.

Se si usano password, assicurarsi che gli account abbiano password complesse senza scadenza. Idealmente, le password devono contenere almeno 16 caratteri generati in modo casuale.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorare i log di accesso e di controllo

Le organizzazioni devono monitorare l'attività di accesso e log di controllo dagli account di emergenza e attivare le notifiche ad altri amministratori. Quando si monitora l'attività sugli account break glass, è possibile verificare che questi account vengano utilizzati solo per i test o le emergenze effettive. È possibile usare Analisi dei log di Azure per monitorare i log di accesso e attivare avvisi tramite posta elettronica e SMS per gli amministratori ogni volta che si rompono gli account di glass.

### <a name="prerequisites"></a>Prerequisiti

1. Inviare i log di accesso di Azure AD a Monitoraggio di [Azure.Send Azure AD sign-in logs](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) to Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Ottenere gli ID oggetto dei conti break glass

1. Accedere al [portale](https://portal.azure.com) di Azure con un account assegnato al ruolo Amministratore utente.
1. Selezionare**Utenti** **di Azure Active Directory** > .
1. Cercare l'account break-glass e selezionare il nome dell'utente.
1. Copiare e salvare l'attributo ID oggetto in modo da poterlo utilizzare in un secondo momento.
1. Ripetere i passaggi precedenti per il secondo account break-glass.

### <a name="create-an-alert-rule"></a>Creare una regola di avviso

1. Accedere al [portale](https://portal.azure.com) di Azure con un account assegnato al ruolo Collaboratore monitoraggio in Monitoraggio di Azure.Sign in to the Azure portal with an account assigned to the Monitoring Contributor role in Azure Monitor.
1. Selezionare **Tutti i servizi**", immettere "log analytics" in Ricerca e quindi selezionare Log Analytics **workspaces**.
1. Selezionare un'area di lavoro.
1. Nell'area di lavoro selezionare**Nuova regola di avviso** **avvisi** > .
    1. In **Risorsa**verificare che la sottoscrizione sia quella a cui si desidera associare la regola di avviso.
    1. In **Condizione**selezionare **Aggiungi**.
    1. Selezionare **Ricerca log personalizzata** in **Nome segnale**.
    1. In **Query di ricerca**immettere la query seguente, inserendo gli ID oggetto dei due conti break glass.
        > [!NOTE]
        > Per ogni account break glass aggiuntivo che si desidera includere, aggiungere un altro "o UserId " "ObjectGuid"" alla query.

        ![Aggiungere gli ID oggetto dei conti break glass a una regola di avviso](./media/directory-emergency-access/query-image1.png)

    1. In **Logica di avviso**immettere quanto segue:

        - In base a: Numero di risultati
        - Operatore: maggiore di
        - Valore soglia: 0

    1. In **Valutato in base**a , selezionare il Periodo **(in minuti)** per quanto tempo si desidera eseguire la query e **la frequenza (in minuti)** per la frequenza di esecuzione della query. La frequenza deve essere minore o uguale al periodo.

        ![logica di avviso](./media/directory-emergency-access/alert-image2.png)

    1. Selezionare **Fatto**. È ora possibile visualizzare il costo mensile stimato di questo avviso.
1. Selezionare un gruppo di azioni di utenti a cui ricevere la notifica tramite l'avviso. Se si desidera crearne uno, vedere [Creare un gruppo di azioni.](#create-an-action-group)
1. Per personalizzare la notifica tramite posta elettronica inviata ai membri del gruppo di azioni, selezionare le azioni in **Personalizza azioni**.
1. In **Dettagli avviso**specificare il nome della regola di avviso e aggiungere una descrizione facoltativa.
1. Impostare il livello di **gravità** dell'evento. Si consiglia di impostarlo su **Critical(Sev 0)**.
1. In **Abilita regola al momento della creazione**, lasciarla impostata su **sì**.
1. Per disattivare gli avvisi per un po', selezionare la casella di controllo **Sopprimi avvisi** e immettere nuovamente la durata di attesa prima dell'invio, quindi selezionare **Salva**.
1. Fare clic su **Crea regola di avviso**.

### <a name="create-an-action-group"></a>Creare un gruppo di azioni

1. Selezionare **Crea un gruppo di azioni**.

    ![creare un gruppo di azioni per le azioni di notifica](./media/directory-emergency-access/action-group-image3.png)

1. Immettere il nome del gruppo di azioni e un nome breve.
1. Verificare la sottoscrizione e il gruppo di risorse.
1. In Tipo di azione selezionare **E-mail/SMS/Push/Voice**.
1. Immettere un nome di azione, ad esempio **Notifica amministratore globale**.
1. Selezionare Il **tipo di azione** come **E-mail/SMS/Push/Voice**.
1. Selezionare **Modifica dettagli** per selezionare i metodi di notifica che si desidera configurare e immettere le informazioni di contatto necessarie, quindi scegliere **OK** per salvare i dettagli.
1. Aggiungere eventuali azioni aggiuntive che si desidera attivare.
1. Selezionare **OK**.

## <a name="validate-accounts-regularly"></a>Convalidare regolarmente gli account

Quando si addestrano i membri del personale all'utilizzo degli account di accesso di emergenza e si convalidano gli account di accesso di emergenza, eseguire almeno la procedura seguente a intervalli regolari:

- Assicurarsi che il personale che monitora la sicurezza sia informato che l'attività di verifica degli account è in corso.
- Assicurarsi che il processo per gli scenari di emergenza per usare questi account sia documentato e corrente.
- Assicurarsi che gli amministratori e i responsabili della sicurezza che potrebbero dover eseguire queste procedure durante un'emergenza siano formati sul processo.
- Aggiornare le credenziali dell'account, in particolare le password, per gli account di accesso di emergenza e quindi verificare che gli account di accesso di emergenza possano accedere ed eseguire attività amministrative.
- Verificare che gli utenti non abbiano l'autenticazione a più fattori o la reimpostazione della password self-service (SSPR) per qualsiasi dispositivo utente o per le informazioni personali. 
- Se gli account sono registrati per l'autenticazione a più fattori per un dispositivo da usare durante l'accesso o l'attivazione del ruolo, assicurarsi che il dispositivo sia accessibile a tutti gli amministratori che potrebbero aver bisogno di usarlo durante un'emergenza. Verificare inoltre che il dispositivo possa comunicare tramite almeno due percorsi di rete che non condividono una modalità di errore comune. Ad esempio, il dispositivo può comunicare con Internet tramite la rete wireless di una struttura e una rete cellulare.

Questi passaggi devono essere eseguiti a intervalli regolari e per le modifiche chiave:

- Almeno ogni 90 giorni
- In caso di modifica recente del personale IT, ad esempio per un cambio di mansione, una partenza o una nuova assunzione
- Quando le sottoscrizioni di Azure AD dell'organizzazione sono cambiate

## <a name="next-steps"></a>Passaggi successivi

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](directory-admin-roles-secure.md)
- [Aggiungere utenti tramite Azure AD](../fundamentals/add-users-azure-active-directory.md) e [assegnare il nuovo utente al ruolo di amministratore globale](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Iscriversi a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), se non è ancora stato fatto
- [Come richiedere la verifica in due passaggi per un utente](../authentication/howto-mfa-userstates.md)
- [Configurare protezioni aggiuntive per gli amministratori globali in Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se si usa Office 365
- [Avviare una verifica di accesso per gli amministratori globali](../privileged-identity-management/pim-how-to-start-security-review.md) e [spostare gli amministratori globali esistenti in ruoli di amministratore più specifici](directory-assign-admin-roles.md)
