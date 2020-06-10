---
title: Controlli dell'esempio di progetto CIS Microsoft Azure Foundations Benchmark
description: Mapping di raccomandazioni del progetto di esempio CIS Microsoft Azure Foundations Benchmark in Criteri di Azure.
ms.date: 05/12/2020
ms.topic: sample
ms.openlocfilehash: b6029e147af49cfb91078c6228615c32ad2db5fe
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167231"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapping di raccomandazioni del progetto di esempio CIS Microsoft Azure Foundations Benchmark

L'articolo seguente illustra nel dettaglio come l'esempio di progetto Azure Blueprints CIS Microsoft Azure Foundations Benchmark esegue il mapping alle raccomandazioni specifiche di CIS Microsoft Azure Foundations Benchmark. Per altre informazioni sulle raccomandazioni, vedere [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Di seguito sono riportati i mapping alle raccomandazioni di **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping di raccomandazioni.
Molte raccomandazioni mappate vengono implementate con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica raccomandazioni CIS Microsoft Azure Foundations Benchmark v1.1.1.0 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti con privilegi

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che permettono di monitorare quando l'autenticazione a più fattori non è abilitata negli account Azure Active Directory con privilegi.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti senza privilegi

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare quando l'autenticazione a più fattori non è abilitata negli account Azure Active Directory senza privilegi.

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Assicurarsi che non siano presenti utenti Guest

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare gli account guest che devono essere rimossi.

- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Assicurarsi che non siano stati creati ruoli di proprietario della sottoscrizione personalizzati

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare i ruoli di proprietario della sottoscrizione personalizzati che devono essere rimossi.

- Non devono esistere ruoli di proprietario della sottoscrizione personalizzati

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Assicurarsi che sia selezionato il piano tariffario Standard

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le reti e le macchine virtuali in cui non è abilitato il livello Standard del Centro sicurezza.

- È consigliabile selezionare il piano tariffario Standard del Centro sicurezza

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Assicurarsi che il 'Provisioning automatico dell'agente di monitoraggio' sia impostato su 'Sì'

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che il provisioning automatico dell'agente di Log Analytics sia abilitato.

- È consigliabile abilitare il provisioning automatico dell'agente di monitoraggio di Log Analytics nella sottoscrizione

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora aggiornamenti del sistema" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che gli aggiornamenti del sistema siano installati nelle macchine virtuali.

- Gli aggiornamenti di sistema devono essere installati nelle macchine

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora vulnerabilità del sistema operativo" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di monitorare le vulnerabilità della macchina virtuale non risolte.

- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora protezione endpoint" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Endpoint Protection sia abilitata nelle macchine virtuali.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia disco" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che i dischi delle macchine virtuali siano crittografati.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora gruppi di sicurezza di rete" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di proteggere le macchine virtuali con connessione Internet.

- Le raccomandazioni di Protezione avanzata adattiva per la rete devono essere applicate alle macchine virtuali con connessione Internet

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Abilita monitoraggio firewall di nuova generazione" non sia disabilitata

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che consentono di proteggere le subnet e le macchine virtuali dalle minacce limitando l'accesso. Il criterio del Centro sicurezza a cui fa riferimento questa raccomandazione di CIS Microsoft Azure Foundations Benchmark è stato sostituito da due nuove raccomandazioni. I criteri indicati sotto fanno riferimento alle nuove raccomandazioni.

- Le subnet devono essere associate a un gruppo di sicurezza di rete
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora la valutazione della vulnerabilità" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le vulnerabilità vengano rilevate e risolte.

- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora la crittografia BLOB di archiviazione" non sia disabilitata

La crittografia di Archiviazione di Azure è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata. Questa è una funzionalità predefinita di Azure. Non vi è assegnazione dei criteri.

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora accesso JIT alla rete" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di controllare l'accesso alle macchine virtuali.

- Alle macchine virtuali deve essere applicato il controllo di accesso alla rete JIT

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora l'inserimento delle applicazioni adattive nell'elenco elementi consentiti" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che i controlli applicazioni adattivi siano abilitati nelle macchine virtuali.

- I controlli applicazioni adattivi devono essere abilitati nelle macchine virtuali

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora controllo SQL" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che il controllo del server SQL sia abilitato.

- È consigliabile abilitare il controllo in SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Assicurarsi che l'impostazione dei criteri predefinita del Centro sicurezza di Azure "Monitora crittografia SQL" non sia disabilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Transparent Data Encryption sia abilitata nei database SQL.

- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Assicurarsi che il criterio 'Indirizzi di posta elettronica dei contatti di sicurezza' sia configurato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di sicurezza siano abilitate correttamente.

- È consigliabile fornire un indirizzo di posta elettronica dei contatti di sicurezza per la sottoscrizione

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Assicurarsi che il criterio 'Numero di telefono' del contatto di sicurezza sia configurato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di sicurezza siano abilitate correttamente.

- È consigliabile fornire il numero di telefono dei contatti di sicurezza per la sottoscrizione

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Assicurarsi che il criterio 'Invia una notifica tramite posta elettronica per avvisi con gravità elevata' sia impostato su 'Sì'

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di sicurezza siano abilitate correttamente.

- È consigliabile abilitare le notifiche di posta elettronica per gli avvisi con gravità alta

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Assicurarsi che 'Invia un messaggio di posta elettronica anche ai proprietari della sottoscrizione' sia impostata su 'Sì'

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di sicurezza siano abilitate correttamente.

- È consigliabile abilitare le notifiche di posta elettronica al proprietario della sottoscrizione per gli avvisi con gravità alta

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Assicurarsi che l'opzione "Trasferimento sicuro obbligatorio" sia impostata su "Abilitato".

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare gli account di archiviazione che consentono connessioni non sicure.

- Il trasferimento sicuro negli account di archiviazione deve essere abilitato

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Assicurarsi che la regola di accesso alla rete predefinita per gli account di archiviazione sia impostata su Nega

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare gli account di archiviazione che consentono l'accesso illimitato.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Assicurarsi che il criterio 'Servizi Microsoft attendibili' sia abilitato per l'accesso all'account di archiviazione

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che permette di monitorare gli account di archiviazione che non consentono l'accesso da servizi Microsoft attendibili.

- Gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Assicurarsi che "Controllo" sia impostato su "Attivato"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che il controllo del server SQL sia abilitato. 

- È consigliabile abilitare il controllo in SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Assicurarsi che "AuditActionGroups" nel criterio "controllo" per un server SQL sia impostato correttamente

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che il controllo del server SQL sia configurato correttamente.

- Le impostazioni di controllo SQL devono avere gruppi di azione configurati per acquisire attività critiche

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Assicurarsi che il periodo di conservazione dei dati di controllo sia "superiore a 90 giorni"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i log del server SQL siano conservati per almeno 90 giorni.

- È consigliabile che i server SQL devono siano configurati con un periodo di conservazione dei dati di controllo superiore a 90 giorni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Assicurarsi che "Sicurezza dei dati avanzata" in un server SQL sia impostata su "Attivata"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Sicurezza dei dati avanzata sia abilitata nei server di database SQL e dell'istanza gestita di SQL.

- Sicurezza dei dati avanzata deve essere abilitata nei server SQL
- La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 assicurarsi che "Tipi di rilevamento minacce" sia impostato su "Tutti"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Advanced Threat Protection sia configurata correttamente nei server di database SQL e dell'istanza gestita di SQL.

- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- È consigliabile che i Tipi di Advanced Threat Protection siano impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata nell'istanza gestita di SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 assicurarsi che "Invia avvisi a" sia impostato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di Sicurezza dei dati avanzata siano abilitate correttamente.

- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Assicurarsi che "Invio di un messaggio di posta elettronica al servizio e ai coamministratori" sia "Abilitato"

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le notifiche di Sicurezza dei dati avanzata siano abilitate correttamente.

- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata del server SQL
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Assicurarsi che l'amministratore di Azure Active Directory sia configurato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che venga effettuato il provisioning di un amministratore di Azure Active Directory per i server SQL.

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Assicurarsi che "Crittografia dati" sia impostato su "Attivato" in un database SQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Transparent Data Encryption sia abilitata nei database SQL.

- Transparent Data Encryption deve essere abilitata nei database SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Assicurarsi che la protezione TDE di SQL Server sia crittografata con BYOK (Bring Your Own Key)

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) che consentono di garantire che la protezione TDE per i server di database SQL e dell'istanza gestita di SQL sia crittografata con una chiave personalizzata.

- È consigliabile che la protezione TDE di SQL Server sia crittografata con una chiave personalizzata
- È consigliabile che la protezione TDE dell'istanza gestita di SQL sia crittografata con una chiave personalizzata

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Assicurarsi che il criterio 'Imponi connessione SSL' sia abilitato per il server di database MySQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database MySQL impongano le connessioni TLS/SSL.

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Assicurarsi che il parametro del server 'log_checkpoints' sia impostato su 'Sì' per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database PostgreSQL registrino i punti di controllo.

- L'impostazione di registrazione dei punti di controllo deve essere abilitata per i server di database PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Assicurarsi che il criterio 'Imponi connessione SSL' sia abilitato per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database PostgreSQL impongano le connessioni TLS/SSL.

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Assicurarsi che il parametro del server 'log_connections' sia impostato su 'Sì' per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database PostgreSQL registrino le connessioni.

- L'impostazione di registrazione delle connessioni deve essere abilitata per i server di database PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Assicurarsi che il parametro del server 'log_disconnections' sia impostato su 'Sì' per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database PostgreSQL registrino le disconnessioni.

- L'impostazione di registrazione delle disconnessioni deve essere abilitata per i server di database PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Assicurarsi che il parametro del server 'log_duration' sia impostato su 'Sì' per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i server di database PostgreSQL registrino la durata delle istruzioni completate.

- L'impostazione di registrazione della durata deve essere abilitata per i server di database PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Assicurarsi che il parametro del server 'connection_throttling' sia impostato su 'Sì' per il server di database PostgreSQL

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di attenuare gli attacchi di forza bruta nei server di database PostgreSQL.

- La limitazione delle connessioni per i server di database PostgreSQL deve essere abilitata

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Assicurarsi che l'amministratore di Azure Active Directory sia configurato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che venga effettuato il provisioning di un amministratore di Azure Active Directory per i server SQL. Il progetto CIS Microsoft Azure Foundations Benchmark include questa raccomandazione, tuttavia, si tratta di un duplicato della [raccomandazione 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Assicurarsi che esista un profilo di log

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che esista un profilo di log per tutte le sottoscrizioni di Azure. 

- Le sottoscrizioni di Azure devono avere un profilo di log per il log attività

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Assicurarsi che la conservazione del log attività sia impostata su un valore di 365 giorni o superiore

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i log attività siano conservati per almeno un anno.

- Il log attività deve essere conservato per almeno un anno

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Assicurarsi che il profilo di controllo acquisisca tutte le attività

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che il profilo di log sia configurato correttamente.

- Il profilo di log di Monitoraggio di Azure deve raccogliere i log per le categorie 'scrittura', 'eliminazione' e 'azione'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Assicurarsi che il profilo di log acquisisca i log attività per tutte le aree, incluse quelle globali

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che il profilo di log sia configurato correttamente.

- Monitoraggio di Azure deve raccogliere i log attività da tutte le aree

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 Assicurarsi che l'account di archiviazione contenente il contenitore con i log attività sia crittografato con BYOK (Bring Your Own Key)

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che gli account di archiviazione contenenti i log attività siano crittografati con BYOK.

- L'account di archiviazione contenente il contenitore con i log attività deve essere crittografato con BYOK

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Assicurarsi che la registrazione per l'insieme di credenziali delle credenziali di Azure sia abilitata

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che i log di diagnostica siano abilitati per gli insieme di credenziali delle chiavi.

- I log di diagnostica in Key Vault devono essere abilitati

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 Assicurarsi che esista un avviso del log attività per l'assegnazione di criteri di creazione

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni dei criteri specifiche deve esistere un avviso del log attività

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento del gruppo di sicurezza di rete

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 Assicurarsi che esista un avviso del log attività per l'eliminazione del gruppo di sicurezza di rete

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una regola del gruppo di sicurezza di rete

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 Assicurarsi che esista un avviso del log attività per l'eliminazione di una regola del gruppo di sicurezza di rete

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una soluzione di sicurezza

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni di sicurezza specifiche deve esistere un avviso del log attività

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 Assicurarsi che esista un avviso del log attività per l'eliminazione di una soluzione di sicurezza

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni di sicurezza specifiche deve esistere un avviso del log attività

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 Assicurarsi che esista un avviso del log attività per la creazione, l'aggiornamento o l'eliminazione di una regola del firewall di SQL Server

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni amministrative specifiche deve esistere un avviso del log attività

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 Assicurarsi che esista un avviso del log attività per l'aggiornamento dei criteri di sicurezza

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che specifici avvisi del log attività esistano.

- Per operazioni di sicurezza specifiche deve esistere un avviso del log attività

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6.1 Assicurarsi che l'accesso RDP sia limitato da Internet

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che l'accesso RDP sia con restrizioni.

- L'accesso RDP da Internet deve essere bloccato

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6.2 Assicurarsi che l'accesso SSH sia limitato da Internet

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che l'accesso SSH sia con restrizioni.

- L'accesso SSH da Internet deve essere bloccato

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Assicurarsi che Network Watcher sia abilitato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che Network Watcher sia abilitato per tutte le aree in cui sono distribuite le risorse. Questo criterio richiede una matrice di parametri che specifichi tutte le aree applicabili. Il valore predefinito in questa definizione di iniziativa dei criteri è 'eastus'.

- È consigliabile abilitare Network Watcher

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Assicurarsi che "disco del sistema operativo" sia crittografato

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la crittografia dei dischi sia abilitata nelle macchine virtuali.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Assicurarsi che i "dischi dati" siano crittografati

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la crittografia dei dischi sia abilitata nelle macchine virtuali.

- La crittografia del disco deve essere applicata nelle macchine virtuali

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Assicurarsi che i 'dischi non collegati' siano crittografati

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che i dischi non collegati siano crittografati.

- È consigliabile crittografare i dischi non collegati

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Assicurarsi che siano installate solo le estensioni approvate

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi siano installate solo le estensione macchina virtuale approvate. Questo criterio richiede una matrice di parametri che specifichi tutte le estensioni macchina virtuale approvate. Questa definizione di iniziativa dei criteri contiene valori predefiniti suggeriti che i clienti devono convalidare. 

- Devono essere installate solo le estensioni macchina virtuale approvate

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Assicurarsi che vengano applicate le patch più recenti del sistema operativo per tutte le macchine virtuali

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che gli aggiornamenti del sistema siano installati nelle macchine virtuali.

- Gli aggiornamenti di sistema devono essere installati nelle macchine

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Assicurarsi che venga installata la protezione endpoint per tutte le Macchine virtuali di Microsoft Azure

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che la soluzione Endpoint Protection sia abilitata nelle macchine virtuali.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Assicurarsi che l'insieme di credenziali delle chiavi sia recuperabile

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che gli oggetti insieme di credenziali delle chiavi siano recuperabili in caso di eliminazione accidentale.

- Gli oggetti Key Vault devono essere recuperabili

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Abilitare il controllo degli accessi in base al ruolo con il servizio Azure Kubernetes

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di garantire che venga usato il controllo degli accessi in base al ruolo per gestire le autorizzazioni nei cluster del servizio Kubernetes

- il controllo degli accessi in base al ruolo deve essere usato con il servizio Azure Kubernetes

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9.1 Assicurarsi che l'autenticazione del servizio app sia impostata nel servizio app di Azure

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di assicurarsi che le richieste alle app del servizio app siano autenticate.

- L'autenticazione deve essere abilitata nell'app per le API
- L'autenticazione deve essere abilitata nell'app per le funzioni
- L'autenticazione deve essere abilitata nell'app Web

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Assicurarsi che l'app Web reindirizzi tutto il traffico HTTP a HTTPS nel servizio app Azure

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) che consente di garantire che le applicazioni Web siano accessibili solo tramite connessioni sicure.

- L'applicazione Web deve essere accessibile solo tramite HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Assicurarsi che l'app Web usi la versione più recente della crittografia TLS

Questo progetto assegna una definizione di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di TLS.

- Nell'app per le API è necessario usare la versione più recente di TLS
- Nell'app per le funzioni è necessario usare la versione più recente di TLS
- Nell'app Web è necessario usare la versione più recente di TLS

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che solo i client con certificati validi possano raggiungere un'app Web.

- Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app per le API sia impostata su 'Sì'
- Assicurarsi che l'opzione 'Certificati client (certificati client in ingresso)' dell'app per le funzioni sia impostata su 'Sì'
- Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nel Servizio app

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino un'identità gestita.

- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le API
- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le funzioni
- Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nell'app per le Web

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app Web

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di .NET Framework.

- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la versione di '.NET Framework' sia la più recente, se usata come parte dell'app Web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Assicurarsi che la 'versione di PHP' sia la più recente, se usata per eseguire l'app Web

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di PHP.

- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Assicurarsi che la 'versione di Python' sia la più recente, se usata per eseguire l'app Web

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di Python.

- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Assicurarsi che la 'versione di Java' sia la più recente, se usata per eseguire l'app Web

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di Java.

- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le API
- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni
- Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web

Questo progetto assegna le definizioni di [Criteri di Azure](../../../policy/overview.md) per assicurarsi che le app Web usino la versione più recente di HTTP.

- Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le API
- Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni
- Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli del progetto CIS Microsoft Azure Foundations Benchmark, leggere gli articoli seguenti per informazioni sul progetto oppure visitare Criteri di Azure nel portale di Azure per assegnare l'iniziativa:

> [!div class="nextstepaction"]
> [Panoramica del progetto CIS Microsoft Azure Foundations Benchmark](./index.md)
> [Procedura per la distribuzione del progetto CIS Microsoft Azure Foundations Benchmark](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).