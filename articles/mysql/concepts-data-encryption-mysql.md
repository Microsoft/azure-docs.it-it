---
title: Crittografia dei dati con una chiave gestita dal cliente - Database di Azure per MySQL
description: La crittografia dei dati di Database di Azure per MySQL con una chiave gestita dal cliente consente di attuare uno scenario Bring Your Own Key (BYOK) per la protezione dei dati inattivi. Consente anche alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 809827fc61cde3c8077484dbe2a5be5037609cd8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748549"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Crittografia dei dati di Database di Azure per MySQL con una chiave gestita dal cliente

La crittografia dei dati con chiavi gestite dal cliente per Database di Azure per MySQL consente di usare Bring Your Own Key (BYOK) per la protezione dei dati inattivi. Consente anche alle organizzazioni di implementare la separazione dei compiti nella gestione delle chiavi e dei dati. Con la crittografia gestita dal cliente, l'utente è responsabile e, in pieno controllo, del ciclo di vita di una chiave, delle autorizzazioni di utilizzo delle chiavi e del controllo delle operazioni sulle chiavi.

La crittografia dei dati con chiavi gestite dal cliente per il servizio Database di Azure per MySQL viene impostata a livello di server. Per un determinato server, una chiave gestita dal cliente, denominata chiave di crittografia della chiave (chiave KEK), viene usata per crittografare la chiave di crittografia dei dati (chiave DEK) usata dal servizio. La chiave KEK è una chiave asimmetrica archiviata in un'istanza di [Azure Key Vault](../key-vault/general/security-overview.md) di proprietà del cliente e gestita dal cliente. Le chiavi KEK e DEK vengono descritte in maggior dettaglio più avanti in questo articolo.

Key Vault è un sistema esterno di gestione delle chiavi basato sul cloud. Offre disponibilità elevata e fornisce una risorsa di archiviazione scalabile e sicura per le chiavi di crittografia RSA, supportata facoltativamente da moduli di protezione hardware (HSM) con convalida di tipo FIPS 140-2 Livello 2. Non consente l'accesso diretto a una chiave archiviata, ma offre servizi di crittografia e decrittografia per le entità autorizzate. Key Vault può generare la chiave, importarla o fare in modo che venga [trasferita da un dispositivo HSM locale](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per MySQL supporta i piani tariffari per utilizzo generico e ottimizzati per la memoria. Per altre limitazioni, vedere la sezione [relativa alle](concepts-data-encryption-mysql.md#limitations) limitazioni.

## <a name="benefits"></a>Vantaggi

La crittografia dei dati con chiavi gestite dal cliente per Database di Azure per MySQL offre i vantaggi seguenti:

* Accesso ai dati completamente controllato dall'utente, grazie alla possibilità di rimuovere la chiave e rendere inaccessibile il database 
* Controllo completo del ciclo di vita della chiave, inclusa la rotazione della chiave per soddisfare i criteri aziendali
* Gestione centralizzata e organizzazione delle chiavi in Azure Key Vault
* Possibilità di implementare la separazione dei compiti tra i responsabili della sicurezza, gli amministratori di database e gli amministratore di sistema


## <a name="terminology-and-description"></a>Terminologia e descrizione

**Chiave DEK (Data Encryption Key)** : una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave DEK viene sostituita con una nuova chiave, è necessario ripetere la crittografia con la nuova chiave solo per i dati nel blocco associato.

**Chiave KEK (Key Encryption Key)** : una chiave di crittografia usata per crittografare le chiavi DEK. Una chiave KEK che non lascia mai Key Vault consente la crittografia e il controllo delle chiavi DEK. L'entità che ha accesso alla chiave KEK può essere diversa da quella che richiede la chiave DEK. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi DEK, crittografate con chiavi KEK, vengono archiviate separatamente. Solo un'entità con accesso alla chiave KEK può decrittografare queste chiavi DEK. Per altre informazioni, vedere [Sicurezza della crittografia di dati inattivi](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Funzionamento della crittografia dei dati con una chiave gestita dal cliente

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagramma che mostra una panoramica dello scenario Bring Your Own Key":::

Affinché un server MySQL usi chiavi gestite dal cliente archiviate in Key Vault per la crittografia della chiave DEK, un amministratore di Key Vault concede al server i diritti di accesso seguenti:

* **get**: per recuperare la parte pubblica e le proprietà della chiave nell'insieme di credenziali delle chiavi.
* **wrapKey**: per poter crittografare la chiave DEK. La chiave DEK crittografata viene archiviata in Database di Azure per MySQL.
* **unwrapKey**: per poter decrittografare la chiave DEK. Database di Azure per MySQL richiede la chiave DEK decrittografata per crittografare/decrittografare i dati

L'amministratore dell'insieme di credenziali delle chiavi può anche [abilitare la registrazione degli eventi di controllo di Key Vault](../azure-monitor/insights/key-vault-insights-overview.md), in modo che possano essere controllati in un secondo momento.

Se il server è configurato per l'uso della chiave gestita dal cliente archiviata nell'insieme di credenziali delle chiavi, il server invia la chiave DEK all'insieme di credenziali delle chiavi per la crittografia. Key Vault restituisce la chiave DEK crittografata, che viene archiviata nel database utente. Analogamente, se necessario, il server invia la chiave DEK protetta all'insieme di credenziali delle chiavi per la decrittografia. I revisori possono usare Monitoraggio di Azure per esaminare i log degli eventi di controllo di Key Vault, se la registrazione è abilitata.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Requisiti per la configurazione della crittografia dei dati per Database di Azure per MySQL

Di seguito sono illustrati i requisiti per la configurazione di Key Vault:

* Key Vault e Database di Azure per MySQL devono appartenere allo stesso tenant di Azure Active Directory (Azure AD). Le interazioni di server e Key Vault tra più tenant non sono supportate. Per spostare Key Vault risorsa in un secondo momento, è necessario riconfigurare la crittografia dei dati.
* Abilitare la [funzionalità di eliminazione](../key-vault/general/soft-delete-overview.md) automatica nell'insieme di credenziali delle chiavi con periodo di conservazione impostato su **90** giorni, per evitare la perdita di dati in caso di eliminazione accidentale della chiave (o Key Vault). Le risorse eliminate automaticamente vengono conservate per 90 giorni per impostazione predefinita, a meno che il periodo di conservazione non sia impostato in modo esplicito su <=90 giorni. Alle azioni di recupero e rimozione definitiva sono associate autorizzazioni specifiche nei criteri di accesso di Key Vault. La funzionalità di eliminazione temporanea è disattivata per impostazione predefinita, ma è possibile abilitarla tramite PowerShell o l'interfaccia della riga di comando di Azure (si noti che non è possibile abilitarla tramite il portale di Azure).
* Abilitare la [funzionalità Protezione dall'eliminazione](../key-vault/general/soft-delete-overview.md#purge-protection) nell'insieme di credenziali delle chiavi con periodo di conservazione impostato **su 90 giorni.** La protezione dall'eliminazione può essere abilitata solo dopo aver abilitato l'eliminazione soft. Può essere attivato tramite l'interfaccia della riga di comando di Azure o PowerShell. Quando la protezione dall'eliminazione è impostata su On, un insieme di credenziali o un oggetto nello stato eliminato non può essere ripulito fino al termine del periodo di conservazione. Gli insiemi di credenziali e gli oggetti eliminati automaticamente possono comunque essere ripristinati, assicurandosi che i criteri di conservazione siano seguiti. 
* Concedere a Database di Azure per MySQL l'accesso all'insieme di credenziali delle chiavi con le autorizzazioni get, wrapKey e unwrapKey usando l'identità gestita univoca. Nell'portale di Azure, l'identità "Servizio" univoca viene creata automaticamente quando è abilitata la crittografia dei dati in MySQL. Per istruzioni dettagliate nel caso in cui si usi il portale di Azure, vedere [Configurare la crittografia dei dati per MySQL](howto-data-encryption-portal.md).

Di seguito sono illustrati i requisiti per la configurazione della chiave gestita dal cliente:

* La chiave gestita dal cliente da usare per la crittografia della chiave DEK può essere solo di tipo RSA 2048 e asimmetrica.
* La data di attivazione della chiave (se impostata) deve essere una data/ora nel passato. Data di scadenza non impostata.
* La chiave deve avere lo stato *Abilitato*.
* Per la chiave deve essere [impostata l'eliminazione](../key-vault/general/soft-delete-overview.md) soft con periodo di conservazione **impostato su 90 giorni.** In questo modo viene impostato in modo implicito l'attributo chiave obbligatorio recoveryLevel: "Recoverable". Se la conservazione è impostata su < 90 giorni, recoveryLevel: "CustomizedRecoverable", che non è il requisito, assicurarsi di impostare il periodo di conservazione su **90 giorni.**
* Per la chiave deve essere [abilitata la protezione dall'eliminazione.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Se si importa una [chiave esistente](/rest/api/keyvault/ImportKey/ImportKey) nell'insieme di credenziali delle chiavi, assicurarsi di specificarla nei formati di file supportati ( , `.pfx` , `.byok` `.backup` ).

## <a name="recommendations"></a>Consigli

Se si usa la crittografia dei dati con una chiave gestita dal cliente, ecco le raccomandazioni per la configurazione di Key Vault:

* Impostare un blocco della risorsa in Key Vault per controllare chi può eliminare questa risorsa critica e impedire l'eliminazione accidentale o non autorizzata.
* Abilitare il controllo e la creazione di report per tutte le chiavi di crittografia. Key Vault include log che possono essere facilmente inseriti in altri strumenti di informazioni di sicurezza e gestione degli eventi (SIEM, Security Information and Event Management). Log Analytics di Monitoraggio di Azure è un esempio di servizio già integrato.
* Assicurarsi che Key Vault e Database di Azure per MySQL si trovino nella stessa area, per garantire un accesso più rapido per le operazioni di wrapping e annullamento del wrapping della chiave DEK.
* Bloccare Azure KeyVault per limitarne l'accesso solo **all'endpoint privato e alle reti selezionate** e consentire solo ai servizi *Microsoft attendibili* di proteggere le risorse.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Servizio attendibile con Azure Key Vault":::

Ecco le raccomandazioni per la configurazione di una chiave gestita dal cliente:

* Conservare una copia della chiave gestita dal cliente in un luogo sicuro o inserirla nel servizio di deposito.

* Se Key Vault genera la chiave, crearne un backup prima di usarla per la prima volta. Il backup può essere ripristinato solo in Key Vault. Per altre informazioni sul comando di backup, vedere [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condizione inaccessibile della chiave gestita dal cliente

Quando si configura la crittografia dei dati con una chiave gestita dal cliente in Key Vault, è necessario l'accesso continuo a questa chiave affinché il server resti online. Se perde l'accesso alla chiave gestita dal cliente in Key Vault, il server inizia a negare tutte le connessioni entro 10 minuti. Il server genera un messaggio di errore corrispondente e cambia il proprio stato in *Inaccessibile*. Ecco alcuni motivi per cui il server può raggiungere questo stato:

* Se si crea un server di ripristino temporizzato per Database di Azure per MySQL con la crittografia dei dati abilitata, il nuovo server creato avrà lo stato *Inaccessibile*. È possibile correggere questo problema tramite il [portale di Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) o l'[interfaccia della riga di comando](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se si crea una replica in lettura per Database di Azure per MySQL con la crittografia dei dati abilitata, il server di replica avrà lo stato *Inaccessibile*. È possibile correggere questo problema tramite il [portale di Azure](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) o l'[interfaccia della riga di comando](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Se si elimina l'istanza di KeyVault, Database di Azure per MySQL non può accedere alla chiave e passa allo stato *Inaccessibile*. Recuperare [Key Vault](../key-vault/general/key-vault-recovery.md) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.
* Se si elimina la chiave da KeyVault, Database di Azure per MySQL non può accedere alla chiave e passa allo stato *Inaccessibile*. Recuperare la [chiave](../key-vault/general/key-vault-recovery.md) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.
* Se la chiave archiviata in Azure KeyVault scade, diventa non valida e Database di Azure per MySQL passa allo stato *Inaccessibile*. Estendere la data di scadenza della chiave usando l'[interfaccia della riga di comando](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) e ripetere la convalida della crittografia dei dati per rendere il server *Disponibile*.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revoca accidentale dell'accesso alla chiave da Key Vault

È possibile che un utente con diritti di accesso sufficienti per Key Vault disabiliti accidentalmente l'accesso del server alla chiave eseguendo le operazioni seguenti:

* Revoca delle autorizzazioni get, wrapKey e unwrapKey dell'insieme di credenziali delle chiavi dal server.
* Eliminazione della chiave.
* Eliminazione dell'insieme di credenziali delle chiavi.
* Modifica delle regole del firewall dell'insieme di credenziali delle chiavi.
* Eliminazione dell'identità gestita del server in Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorare la chiave gestita dal cliente in Key Vault

Per monitorare lo stato del database e abilitare gli avvisi per la perdita dell'accesso alla protezione di Transparent Data Encryption, configurare le funzionalità di Azure seguenti:

* [Integrità risorse di Azure](../service-health/resource-health-overview.md): un database inaccessibile che ha perso l'accesso alla chiave del cliente viene indicato come "Inaccessibile" dopo che viene negata la prima connessione al database.
* [Log attività](../service-health/alerts-activity-log-service-notifications-portal.md): quando l'accesso alla chiave del cliente nell'istanza di Key Vault gestita dal cliente non riesce, nel log attività vengono aggiunte voci. Se si creano avvisi per questi eventi, è possibile ripristinare l'accesso tempestivamente.

* [Gruppi di azioni](../azure-monitor/alerts/action-groups.md): definire questi gruppi per l'invio di notifiche e avvisi in base alle preferenze.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Eseguire il ripristino e la replica con una chiave gestita del cliente in Key Vault

Una volta eseguita la crittografia di Database di Azure per MySQL con una chiave gestita dal cliente archiviata in Key Vault, viene crittografata anche qualsiasi nuova copia creata del server. Questa nuova copia può essere creata tramite un'operazione di ripristino locale o geografico oppure tramite repliche in lettura. Tuttavia, la copia può essere modificata in modo da riflettere una nuova chiave gestita dal cliente per la crittografia. Quando la chiave gestita dal cliente cambia, i backup precedenti del server iniziano a usare la chiave più recente.

Per evitare problemi durante la configurazione della crittografia dei dati gestita dal cliente durante il ripristino o la creazione della replica di lettura, è importante seguire questa procedura nei server di origine e ripristinati/di replica:

* Avviare il processo di ripristino o lettura della replica dal database di Azure di origine per MySQL.
* Mantenere il server appena creato (ripristinato/di replica) in uno stato inaccessibile, perché la relativa identità univoca non ha ancora ricevuto le autorizzazioni per Key Vault.
* Nel server ripristinato o di replica ripetere la convalida della chiave gestita dal cliente nelle impostazioni di crittografia dei dati per assicurarsi che il server appena creato disponga di autorizzazioni di wrapping e annullamento del wrapping per la chiave archiviata in Key Vault.

## <a name="limitations"></a>Limitazioni

Per Database di Azure per MySQL, il supporto per la crittografia dei dati in stato di inquieto usando la chiave gestita dei clienti (CMK) presenta alcune limitazioni:

* Il supporto per questa funzionalità è limitato **per utilizzo generico** **piani** tariffari ottimizzati per la memoria.
* Questa funzionalità è supportata solo nelle aree e sui server che supportano l'archiviazione fino a 16 TB. Per l'elenco delle aree di Azure che supportano l'archiviazione fino a 16 TB, vedere la sezione relativa all'archiviazione nella documentazione [qui](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Tutti i nuovi server MySQL creati nelle aree elencate in precedenza supportano la crittografia con le chiavi **del** customer manager. Il server point-in-time ripristinato (PITR) o la replica di lettura non saranno idonei anche se in teoria sono "nuovi".
    > - Per verificare se il server di cui è stato effettuato il provisioning supporta fino a 16 TB, è possibile passare al pannello piano tariffario nel portale e visualizzare le dimensioni massime di archiviazione supportate dal server di cui è stato effettuato il provisioning. Se è possibile spostare il dispositivo di scorrimento fino a 4 TB, il server potrebbe non supportare la crittografia con le chiavi gestite dal cliente. Tuttavia, i dati vengono crittografati con chiavi gestite dal servizio in qualsiasi momento. In caso di domande, rivolgersi AskAzureDBforMySQL@service.microsoft.com a .

* La crittografia è supportata solo con la chiave crittografica RSA 2048.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare la crittografia dei dati con una chiave gestita dal cliente per il database di Azure per MySQL usando il portale di Azure e l'interfaccia della [riga](howto-data-encryption-portal.md) di comando [di Azure.](howto-data-encryption-cli.md)
