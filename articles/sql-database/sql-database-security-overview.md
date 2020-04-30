---
title: Panoramica della sicurezza
description: In questo argomento vengono fornite informazioni sulla sicurezza del database SQL di Azure e di SQL Server, incluse le differenze tra il cloud e SQL Server locale.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 1d08770d81ade2d976142b2ce1fd94dae044cf32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461396"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Panoramica della funzionalità di sicurezza del database SQL di Azure

Questo articolo illustra le informazioni di base relative alla protezione del livello dati di un'applicazione in cui viene usato il database SQL di Azure. La strategia di sicurezza descritta segue l'approccio a più livelli di difesa avanzata come illustrato nell'immagine seguente e si sposta dall'esterno in:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Sicurezza di rete

Il database SQL di Microsoft Azure assicura un servizio di database relazionale per le applicazioni cloud e aziendali. Per contribuire a proteggere i dati dei clienti, i firewall impediscono l'accesso alla rete per il server di database finché non viene esplicitamente concesso l'accesso basato su indirizzo IP o origine del traffico di rete virtuale di Azure.

### <a name="ip-firewall-rules"></a>Regole del firewall IP

Le regole del firewall IP concedono l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta. Per altre informazioni, vedere [Panoramica sulle regole del firewall per il database SQL di Azure e SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

Gli [endpoint di servizio della rete](../virtual-network/virtual-network-service-endpoints-overview.md) virtuale estendono la connettività della rete virtuale tramite la backbone di Azure e consentono al database SQL di Azure di identificare la subnet della rete virtuale da cui ha origine il traffico. Per consentire al traffico di raggiungere il database SQL di Azure, usare i [tag di servizio](../virtual-network/security-overview.md) SQL per consentire il traffico in uscita tramite gruppi di sicurezza di rete.

Le [regole di rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) consentono al database SQL di Azure di accettare solo le comunicazioni inviate da subnet specifiche all'interno di una rete virtuale.

> [!NOTE]
> Il controllo dell'accesso con regole del firewall *non* si applica a **un'istanza gestita**. Per altre informazioni sulla configurazione di rete necessaria, vedere l'articolo sulla [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md).

## <a name="access-management"></a>Gestione degli accessi

> [!IMPORTANT]
> La gestione dei database e dei server di database in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticazione

L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. Il database SQL di Azure supporta due tipi di autenticazione:

- **Autenticazione SQL**:

    L'autenticazione del database SQL si riferisce all'autenticazione di un utente durante la connessione al [database SQL di Azure](sql-database-technical-overview.md) tramite nome utente e password. Durante la creazione di server di database per il database, è necessario specificare un accesso "Amministratore del server" con un nome utente e password. Tramite queste credenziali, un "amministratore del server" può essere autenticato in qualsiasi database di tale server di database in qualità di proprietario del database. In seguito, l'amministratore del server può creare altri utenti e accessi SQL, che consentono agli utenti di connettersi tramite nome utente e password.

- **Autenticazione Azure Active Directory**:

    Azure Active Directory autenticazione è un meccanismo di connessione al [database SQL di Azure](sql-database-technical-overview.md) e [SQL data warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando le identità in Azure Active Directory (Azure ad). L'autenticazione di Azure AD consente agli amministratori di gestire centralmente le identità e le autorizzazioni degli utenti del database oltre ad altri servizi Microsoft. Ciò include la riduzione dell'archiviazione di password e abilita i criteri centralizzati di rotazione delle password.

     Occorre creare un amministratore del server denominato **Amministratore di Active Directory Domain Services** per usare l'autenticazione di Azure AD con il database SQL. Per altre informazioni, vedere [Connessione al database SQL tramite l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md). L'autenticazione di Azure AD supporta sia gli account gestiti che quelli federati. Gli account federati supportano gli utenti di Windows e gruppi per un dominio utente federato con Azure AD.

    Altre opzioni di autenticazione di Azure AD disponibili sono le connessioni [Autenticazione universale di Active Directory per SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) che includono [Autenticazione a più fattori](../active-directory/authentication/concept-mfa-howitworks.md) e [ Accesso condizionale](sql-database-conditional-access.md).

> [!IMPORTANT]
> La gestione dei database e dei server in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md). Il controllo dell'accesso con regole del firewall *non* si applica a **un'istanza gestita**. Per ulteriori informazioni sulla configurazione di rete necessaria, vedere l'articolo seguente sulla [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md) .

## <a name="authorization"></a>Autorizzazione

L'autorizzazione fa riferimento alle autorizzazioni assegnate a un utente all'interno di un database SQL di Azure e determina le operazioni che l'utente è autorizzato a eseguire. Le autorizzazioni vengono controllate aggiungendo account utente ai [ruoli del database](/sql/relational-databases/security/authentication-access/database-level-roles) e assegnando autorizzazioni a livello di database a tali ruoli o concedendo all'utente determinate [autorizzazioni a livello di oggetto](/sql/relational-databases/security/permissions-database-engine). Per altre informazioni, vedere [Accessi e utenti](sql-database-manage-logins.md)

Come procedura consigliata, creare ruoli personalizzati quando necessario. Aggiungere utenti al ruolo con i privilegi minimi necessari per eseguire la funzione del processo. Non assegnare autorizzazioni direttamente agli utenti. L'account amministratore del server è un membro del ruolo db_owner incorporato, che dispone di autorizzazioni estese e deve essere concesso solo a pochi utenti con compiti amministrativi. Per le applicazioni del database SQL di Azure, usare [Execute As](/sql/t-sql/statements/execute-as-clause-transact-sql) per specificare il contesto di esecuzione del modulo chiamato o usare i [ruoli applicazione](/sql/relational-databases/security/authentication-access/application-roles) con autorizzazioni limitate. Questa procedura garantisce che l'applicazione che si connette al database disponga dei privilegi minimi necessari per l'applicazione. Seguendo queste procedure consigliate si favorisce anche la separazione dei compiti.

### <a name="row-level-security"></a>Sicurezza a livello di riga

La sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella del database in base alle caratteristiche dell'utente che esegue una query (ad esempio l'appartenenza al gruppo o il contesto di esecuzione). La sicurezza a livello di riga può essere usata anche per implementare concetti di sicurezza personalizzati basati su etichetta. Per altre informazioni, vedere [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Threat Protection

Il database SQL protegge i dati dei clienti fornendo funzionalità di controllo e di rilevamento delle minacce.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Controllo SQL nei log di monitoraggio di Azure e hub eventi

Il servizio di controllo del database SQL tiene traccia delle attività del database e consente di garantire la conformità agli standard di sicurezza tramite la registrazione degli eventi del database in un log di controllo in un account di archiviazione di Azure, di proprietà del cliente. Il servizio di controllo consente agli utenti di monitorare le attività del database in corso e di analizzare ed esaminare l'attività cronologica per identificare potenziali minacce o uso improprio sospetto e violazioni della sicurezza. Per ulteriori informazioni, vedere Introduzione al [controllo del database SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection sta analizzando i log di SQL Server per rilevare comportamenti insoliti e tentativi potenzialmente dannosi di accesso o exploit dei database. Gli avvisi vengono creati per attività sospette, ad esempio SQL injection, potenziali infiltrazione dei dati e attacchi di forza bruta o per le anomalie nei modelli di accesso per intercettare le escalation dei privilegi e l'uso delle credenziali violate. Gli avvisi vengono visualizzati dal [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), in cui vengono forniti i dettagli delle attività sospette e le raccomandazioni per un'ulteriore analisi fornita insieme alle azioni per attenuare la minaccia. Advanced Threat Protection può essere abilitato per ogni server per un costo aggiuntivo. Per altre informazioni, vedere [Introduzione al database SQL Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security (TLS - crittografia in transito)

Il database SQL protegge i dati del cliente mediante la crittografia dei dati in movimento avvalendosi di [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server impone sempre la crittografia (SSL/TLS) per tutte le connessioni. Ciò garantisce che tutti i dati siano crittografati in transito tra il client e il server indipendentemente dall'impostazione di **Encrypt** o **TrustServerCertificate** nella stringa di connessione.

Come procedura consigliata, nella stringa di connessione dell'applicazione è consigliabile specificare una connessione crittografata e _**non**_ considerare attendibile il certificato del server. In questo modo, l'applicazione deve verificare il certificato del server e quindi impedire che l'applicazione venga vulnerabile agli attacchi di tipo intermedio.

Ad esempio, quando si usa il driver ADO.NET, questa operazione viene eseguita tramite **Encrypt = True** e **TrustServerCertificate = false**. Se si ottiene la stringa di connessione dal portale di Azure, le impostazioni saranno corrette.

> [!IMPORTANT]
> Si noti che alcuni driver non Microsoft non possono usare TLS per impostazione predefinita o si basano su una versione precedente di TLS (<1,2) per funzionare. In questo caso SQL Server ancora possibile connettersi al database. Tuttavia, si consiglia di valutare i rischi per la sicurezza che consentono a tali driver e applicazioni di connettersi al database SQL, soprattutto se si archiviano dati sensibili.
>
> Per ulteriori informazioni su TLS e sulla connettività, vedere [considerazioni su TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (TDE - crittografia inattiva)

[Transparent Data Encryption (TDE) per il database SQL di Azure](transparent-data-encryption-azure-sql.md) aggiunge un livello di sicurezza per proteggere i dati inattivi da accessi non autorizzati o non in linea per i file non elaborati o i backup. Gli scenari comuni includono il furto in data center o eliminazioni non protette di hardware o supporti, ad esempio unità disco e nastri di backup.TDE crittografa l'intero database usando un algoritmo di crittografia AES, che non richiede agli sviluppatori di applicazioni di apportare modifiche alle applicazioni esistenti.

In Azure, tutti i nuovi database SQL vengono crittografati per impostazione predefinita e la chiave di crittografia del database è protetta da un certificato del server incorporato.  La manutenzione e la rotazione del certificato sono gestite dal servizio e non richiedono alcun intervento da parte dell'utente. I clienti che preferiscono assumere il controllo delle chiavi di crittografia possono gestire le chiavi presenti in [Azure Key Vault](../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestione delle chiavi con Azure Key Vault

Il supporto [Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) per  [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) consente ai clienti di appropriarsi della gestione e rotazione delle chiavi tramite  [Azure Key Vault](../key-vault/general/secure-your-key-vault.md), il sistema di gestione delle chiavi esterne di Azure basato sul cloud. Se viene revocato l'accesso del database per l'insieme di credenziali delle chiavi, il database non può essere decrittografato e letto in memoria. Azure Key Vault offre una piattaforma di gestione centralizzata delle chiavi, sfrutta i moduli di protezione hardware (HSM) accuratamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e i dati per contribuire a rispettare i requisiti di conformità alle norme di sicurezza.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (crittografia in uso)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) è una funzionalità progettata per proteggere da accessi i dati sensibili archiviati in colonne specifiche del database (ad esempio, i numeri delle carte di credito o i numeri di identificazione nazionale o dati sulla base della _necessità di conoscere_). Sono inclusi gli amministratori del database o altri utenti con privilegi che sono autorizzati ad accedere al database per eseguire attività di gestione, ma che non hanno esigenza di accedere a dati particolari nelle colonne crittografate. I dati vengono sempre crittografati, ossia i dati crittografati vengono decrittografati solo per l'elaborazione da parte di applicazioni client con accesso alla chiave di crittografia.  La chiave di crittografia non viene mai esposta a SQL e può essere archiviata sia nell'[Archivio certificati di Windows](sql-database-always-encrypted.md) sia in [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Maschera dati dinamica

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

La funzione Maschera dati dinamica del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica individua automaticamente dati potenzialmente sensibili nel database SQL di Azure e fornisce indicazioni pratiche per il mascheramento di questi campi, con un impatto minimo sul livello dell'applicazione. Questa funzionalità si basa sull'offuscamento dei dati sensibili nel set dei risultati di una query su campi designati del database, mentre i dati del database non subiscono modifiche. Per altre informazioni, vedere [Introduzione alla maschera dati dinamica del database SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Gestione della sicurezza

### <a name="vulnerability-assessment"></a>Valutazione della vulnerabilità

[Valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database, con l'obiettivo di migliorare in modo proattivo la sicurezza generale del database. Valutazione della vulnerabilità fa parte dell'offerta Sicurezza dei dati avanzata, che è un pacchetto unificato per le funzionalità di sicurezza avanzate SQL. È possibile accedere e gestire Valutazione della vulnerabilità tramite il portale centrale di Sicurezza dei dati avanzata SQL.

### <a name="data-discovery--classification"></a>Individuazione dati e classificazione

Individuazione dati e classificazione (attualmente in anteprima) offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione e la protezione dei dati sensibili presenti nei database, nonché per l'aggiunta di etichette a tali dati. L'individuazione e la classificazione dei dati più sensibili (dati commerciali e finanziari, dati relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Possono costituire l'infrastruttura per:

- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso ai database che contengono dati molto sensibili e rafforzarne la sicurezza.
- Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati.

Per altre informazioni, vedere [Introduzione a Individuazione dati e classificazione](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformità

Oltre alle caratteristiche e alle funzionalità sopra descritte, che consentono all'applicazione di soddisfare vari requisiti di sicurezza, il database SQL di Azure è inoltre sottoposto a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per ulteriori informazioni, vedere la [Microsoft Azure Centro protezione](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) in cui è possibile trovare l'elenco più aggiornato delle certificazioni di conformità del database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'utilizzo degli account di accesso, degli account utente, dei ruoli del database e delle autorizzazioni nel database SQL, vedere [gestire account utente e](sql-database-manage-logins.md)account di accesso.
- Per informazioni sul controllo del database, vedere [SQL Database auditing](sql-database-auditing.md) (Controllo del database SQL).
- Per informazioni sul rilevamento delle minacce, vedere [SQL Database threat detection](sql-database-threat-detection.md) (Rilevamento delle minacce nel database SQL).
