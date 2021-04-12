---
title: Eseguire il backup di più macchine virtuali di SQL Server dall'insieme di credenziali
description: Questo articolo illustra come eseguire il backup di database di SQL Server in macchine virtuali di Azure con backup di Azure dall'insieme di credenziali di servizi di ripristino
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c03b833be6c5e4c352125f31ad8c5ed072674b49
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258470"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Eseguire il backup di più macchine virtuali SQL Server dall'insieme di credenziali di servizi di ripristino

I database di SQL Server sono carichi di lavoro critici che richiedono un obiettivo del punto di ripristino (RPO) basso e la conservazione a lungo termine. È possibile eseguire il backup di database SQL Server in esecuzione nelle macchine virtuali (VM) di Azure usando [Backup di Azure](backup-overview.md).

Questo articolo illustra come eseguire il backup di un database SQL Server in esecuzione in una macchina virtuale di Azure in un insieme di credenziali dei Servizi di ripristino di Backup di Azure.

In questo articolo si apprenderà come:

> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali.
> * Individuare i database e configurare i backup.
> * Configurare la protezione automatica per i database.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire il backup di un database di SQL Server, verificare i criteri seguenti:

1. Identificare o creare un [insieme di credenziali di Servizi di ripristino](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) nella stessa area e nella stessa sottoscrizione della macchina virtuale che ospita l'istanza di SQL Server.
1. Verificare che la macchina virtuale abbia [connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
1. Assicurarsi che i database di SQL Server seguano le [linee guida per la denominazione dei database per Backup di Azure](#database-naming-guidelines-for-azure-backup).
1. Verificare che la lunghezza combinata del nome della macchina virtuale SQL Server e il nome del gruppo di risorse non superi 84 caratteri per le VM Azure Resource Manager (o i caratteri 77 per le macchine virtuali classiche). Questa limitazione è dovuta al fatto che alcuni caratteri sono riservati dal servizio.
1. Verificare che non siano abilitate altre soluzioni di backup per il database. Disabilitare tutti gli altri backup di SQL Server prima di effettuare il backup del database.
1. Quando si usa SQL Server 2008 R2 o SQL Server 2012, è possibile che si sia verificata la presenza di un problema di fuso orario per il backup, come descritto [qui](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of). Assicurarsi di disporre degli aggiornamenti cumulativi più recenti per evitare il problema relativo al fuso orario descritto in precedenza. Se l'applicazione degli aggiornamenti all'istanza di SQL Server nella macchina virtuale di Azure non è fattibile, disabilitare l'ora legale (DST) per il fuso orario della macchina virtuale.

> [!NOTE]
> È possibile abilitare Backup di Azure per una VM di Azure e per un database SQL Server in esecuzione nella VM senza conflitto.

### <a name="establish-network-connectivity"></a>Stabilire la connettività di rete

Per tutte le operazioni, una macchina virtuale SQL Server richiede la connettività al servizio Backup di Azure, ad Archiviazione di Azure e ad Azure Active Directory. Questa operazione può essere eseguita usando endpoint privati oppure consentendo l'accesso agli indirizzi IP pubblici o FQDN richiesti. Non consentire una connettività corretta ai servizi di Azure necessari può causare errori nelle operazioni quali l'individuazione del database, la configurazione del backup, l'esecuzione di backup e il ripristino dei dati.

La tabella seguente elenca le varie alternative che è possibile usare per stabilire la connettività:

| **Opzione**                        | **Vantaggi**                                               | **Svantaggi**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Endpoint privati                 | Backup su indirizzi IP privati all'interno della rete virtuale  <br><br>   Offerta di un controllo granulare lato rete e lato insieme di credenziali | [Costi](https://azure.microsoft.com/pricing/details/private-link/) standard per endpoint privati |
| Tag del servizio NSG                  | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente   <br><br>   Nessun costo aggiuntivo | Utilizzabile solo con i gruppi di sicurezza di rete  <br><br>    Accesso consentito all'intero servizio |
| Tag FQDN di Firewall di Azure          | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure                         |
| Accesso consentito a FQDN/IP del servizio | Nessun costo aggiuntivo   <br><br>  Funziona con tutti i firewall e le appliance di sicurezza di rete | È possibile che sia necessario accedere a un ampio set di indirizzi IP o FQDN   |
| Usare un proxy HTTP                 | Singolo punto di accesso Internet alle VM                       | Costi aggiuntivi per l'esecuzione di una VM con il software proxy         |

Altre informazioni sull'uso di queste opzioni sono condivise di seguito:

#### <a name="private-endpoints"></a>Endpoint privati

Gli endpoint privati consentono di connettersi in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](./private-endpoints.md).

#### <a name="nsg-tags"></a>Tag NSG

Se si usano gruppi di sicurezza di rete (NSG), usare il tag del servizio *AzureBackup* per consentire l'accesso in uscita a Backup di Azure. Oltre al tag di Backup di Azure, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati creando [regole NSG](../virtual-network/network-security-groups-overview.md#service-tags) simili per Azure AD (*AzureActiveDirectory*) e Archiviazione di Azure (*Storage*).  I passaggi seguenti descrivono il processo di creazione di una regola per il tag di Backup di Azure:

1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.

1. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.

1. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](../virtual-network/manage-network-security-group.md#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su *Tag del servizio* e che l'opzione **Tag del servizio di destinazione** sia impostata su *AzureBackup*.

1. Selezionare **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

È possibile creare in modo analogo le regole di sicurezza NSG in uscita NSG per Archiviazione di Azure e Azure AD.

#### <a name="azure-firewall-tags"></a>Tag del Firewall di Azure

Se si usa Firewall di Azure, creare una regola dell'applicazione usando il tag [FQDN di Firewall di Azure](../firewall/fqdn-tags.md) *AzureBackup*. In questo modo si consente l'accesso in uscita a Backup di Azure.

#### <a name="allow-access-to-service-ip-ranges"></a>Consentire l'accesso agli intervalli IP del servizio

Se si decide di consentire l'accesso agli IP del servizio, fare riferimento agli intervalli IP nel file JSON disponibile [qui](https://www.microsoft.com/download/confirmation.aspx?id=56519). È necessario consentire l'accesso agli indirizzi IP corrispondenti a Backup di Azure, Archiviazione di Azure e Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Accesso consentito a FQDN del servizio

È anche possibile usare gli FQDN seguenti per consentire l'accesso ai servizi richiesti dai server:

| Service    | Nomi di dominio a cui accedere                             | Porte
| -------------- | ------------------------------------------------------------ | ---
| Backup di Azure  | `*.backup.windowsazure.com`                             | 443
| Archiviazione di Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | Consentire l'accesso ai nomi di dominio completo (FQDN) nelle sezioni 56 e 59 come indicato in [questo articolo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) | Come applicabile

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usare un server proxy HTTP per instradare il traffico

Quando si esegue il backup di un database SQL Server in una macchina virtuale di Azure, l'estensione di backup nella macchina virtuale usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Usare l'elenco di indirizzi IP e FQDN indicati in precedenza per consentire l'accesso ai servizi necessari. I server proxy autenticati non sono supportati.

### <a name="database-naming-guidelines-for-azure-backup"></a>Linee guida per la denominazione di database per Backup di Azure

Evitare di usare gli elementi seguenti nei nomi di database:

* Spazi finali e iniziali
* Punti esclamativi finali (!)
* Parentesi quadre (])
* Punto e virgola (;)
* Barra (/)

L'alias per i caratteri non supportati è disponibile, ma è consigliabile evitare tali caratteri. Per altre informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/understanding-the-table-service-data-model).

>[!NOTE]
>L'operazione di **configurazione della protezione** per i database con caratteri speciali come "+" o "&" nel nome non è supportata. È possibile modificare il nome del database o abilitare la **protezione automatica** che può proteggere correttamente questi database.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Individuare i database SQL Server

Come individuare i database in esecuzione in una macchina virtuale:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'insieme di credenziali di Servizi di ripristino usato per il backup del database.

2. Nel dashboard di **Insiemi di credenziali dei Servizi di ripristino** selezionare **Backup**.

   ![Selezionare Backup per aprire il menu Obiettivo di backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Obiettivo di backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure**.

4. In **Elementi di cui eseguire il backup** selezionare **SQL Server in una macchina virtuale di Azure**.

    ![Selezionare SQL Server in macchine virtuali di Azure per il backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Obiettivo di backup** > **Individuare i database nelle macchine virtuali** selezionare **Avvia individuazione** per cercare le macchine virtuali non protette nella sottoscrizione. Questa ricerca può richiedere del tempo, a seconda del numero di macchine virtuali non protette nella sottoscrizione.

   * Le VM non protette verranno visualizzate nell'elenco dopo l'individuazione, elencate per nome e gruppo di risorse.
   * Se una VM non è elencata come previsto, verificare se ne è già stato eseguito il backup in un insieme di credenziali.
   * Più macchine virtuali possono avere lo stesso nome, ma in questo caso appartengono a gruppi di risorse differenti.

     ![Il backup è in sospeso durante la ricerca dei database nelle macchine virtuali](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Nell'elenco delle macchine virtuali selezionare la VM nella quale è in esecuzione il database SQL Server e fare clic su **Individua database**.

7. Tenere traccia dell'individuazione dei database in **Notifiche**. Il tempo necessario per questa azione dipende dal numero di database della macchina virtuale. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

    ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Backup di Azure individua tutti i database SQL Server presenti nella macchina virtuale. Durante l'individuazione vengono eseguiti gli elementi seguenti in background:

    * Backup di Azure registra la VM con l'insieme di credenziali per il backup del carico di lavoro. Tutti i database presenti nella VM registrata possono essere sottoposti a backup solo in questo insieme di credenziali.
    * Backup di Azure installa l'estensione AzureBackupWindowsWorkload nella macchina virtuale. Nel database SQL non viene installato alcun agente.
    * Backup di Azure crea l'account del servizio NT Service\AzureWLBackupPluginSvc nella macchina virtuale.
      * Tutte le operazioni di backup e ripristino usano l'account del servizio.
      * NT Service\AzureWLBackupPluginSvc richiede le autorizzazioni sysadmin SQL. Tutte le macchine virtuali SQL Server create nel Marketplace vengono fornite con SqlIaaSExtension installato. L'estensione AzureBackupWindowsWorkload usa l'estensione SQLIaaSExtension per ottenere automaticamente le autorizzazioni richieste.
    * Se la VM non è stata creata dal Marketplace o se si sta usando SQL 2008 e 2008 R2, la VM può non aver installato SqlIaaSExtension e l'operazione di individuazione ha esito negativo con il messaggio di errore UserErrorSQLNoSysAdminMembership. Per risolvere questo problema, seguire le istruzioni riportate in [Impostare le autorizzazioni VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selezionare la macchina virtuale e il database](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurare il backup  

1. In **Obiettivo del backup** > **Passaggio 2: Configurare Backup**, selezionare **Configura backup**.

   ![Selezionare Configurare il backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Selezionare **Aggiungi risorse** per visualizzare tutti i gruppi di disponibilità registrati e le istanze di SQL Server autonome.

    ![Selezionare Aggiungi risorse](./media/backup-azure-sql-database/add-resources.png)

1. Nella schermata **Seleziona elementi a cui eseguire il backup** selezionare la freccia a sinistra di una riga per espandere l'elenco di tutti i database non protetti in tale istanza o Always on gruppo di disponibilità.

    ![Selezionare gli elementi di cui eseguire il backup](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Scegliere tutti i database da proteggere, quindi selezionare **OK**.

   ![Protezione del database](./media/backup-azure-sql-database/select-database-to-protect.png)

   Per ottimizzare i carichi di backup, Backup di Azure imposta il numero massimo di database in un processo di backup su 50.

     * Per proteggere più di 50 database, configurare più backup.
     * Per [abilitare](#enable-auto-protection) l'intera istanza o il gruppo di disponibilità Always On, nell'elenco a discesa **PROTEZIONE AUTOMATICA** selezionare **ON** (ATTIVA), quindi selezionare **OK**.

         > [!NOTE]
         > La funzionalità di [protezione automatica](#enable-auto-protection) non solo abilita la protezione per tutti i database esistenti in un'unica operazione, ma protegge anche automaticamente i nuovi database aggiunti a tale istanza o gruppo di disponibilità.  

1. Definire i **criteri di backup**. È possibile eseguire una delle operazioni seguenti:

   * Selezionare i criteri predefiniti come *HourlyLogBackup*.
   * Scegliere un criterio di backup creato in precedenza per SQL.
   * Definire un nuovo criterio basato sull'obiettivo del punto di ripristino (RPO) e sull'intervallo di conservazione.

     ![Selezionare il criterio di backup](./media/backup-azure-sql-database/select-backup-policy.png)

1. Selezionare **Abilita backup** per inviare l'operazione di configurazione della **protezione** e tenere traccia dello stato di avanzamento della configurazione nell'area **notifiche** del portale.

   ![Verificare lo stato della configurazione](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Creare un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.
* Quando si crea un criterio di backup, il backup completo giornaliero è l'impostazione predefinita.
* È possibile aggiungere un backup differenziale, ma solo se si configura l'esecuzione settimanale del backup completo.
* Informazioni sui [vari tipi criteri di backup](backup-architecture.md#sql-server-backup-types).

Per creare un criterio di backup:

1. Nell'insieme di credenziali selezionare **Criterio di backup** > **Aggiungi**.
1. In **Aggiungi** selezionare **SQL Server nella macchina virtuale di Azure** per definire il tipo di criterio.

   ![Scegliere un tipo per il nuovo criterio di backup](./media/backup-azure-sql-database/policy-type-details.png)

1. In **Nome criterio** immettere un nome per il nuovo criterio.

    ![Immettere il nome dei criteri](./media/backup-azure-sql-database/policy-name.png)

1. Selezionare il collegamento **modifica** corrispondente a **backup completo** per modificare le impostazioni predefinite.

   * Selezionare una **frequenza di backup**. Scegliere **Giornaliero** o **Settimanale**.
   * Per **Giornaliero**, scegliere l'ora e il fuso orario per l'inizio del processo di backup. Se si sceglie di eseguire backup completi giornalieri, non è possibile creare backup differenziali.

     ![Nuovi campi di criteri di backup](./media/backup-azure-sql-database/full-backup-policy.png)  

1. In **INTERVALLO DI CONSERVAZIONE** per impostazione predefinita sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti di intervallo di conservazione indesiderati e impostare gli intervalli da usare.

    * Il periodo di conservazione minimo di qualsiasi tipo di backup (completo, differenziale e del log) è di sette giorni.
    * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
    * Il backup del giorno specifico viene contrassegnato e conservato in base all'intervallo di conservazione settimanale e alla conservazione settimanale.
    * Gli intervalli di conservazione mensile e annuale si comportano allo stesso modo.

       ![Impostazione dell'intervallo di conservazione](./media/backup-azure-sql-database/retention-range-interval.png)

1. Selezionare **OK** per accettare l'impostazione per i backup completi.
1. Selezionare il collegamento **modifica** corrispondente a **backup differenziale** per modificare le impostazioni predefinite.

    * Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione.
    * È possibile attivare solo un backup differenziale al giorno. Un backup differenziale non può essere attivato nello stesso giorno di un backup completo.
    * I backup differenziali possono essere conservati al massimo per 180 giorni.
    * Il backup differenziale non è supportato per il database master.

      ![Criteri di backup differenziale](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Selezionare il collegamento **modifica** corrispondente a **backup del log** per modificare le impostazioni predefinite

    * In **Backup del log** selezionare **Abilita** e quindi impostare le opzioni di frequenza e conservazione.
    * I backup del log possono verificarsi ogni 15 minuti ed essere conservati per un massimo di 35 giorni.
    * Se il database è nel [modello di recupero con registrazione minima](/sql/relational-databases/backup-restore/recovery-models-sql-server), la pianificazione del backup del log per il database verrà sospesa, quindi non verrà attivato alcun backup del log.
    * Se il modello di recupero del database viene modificato da **completo** a **semplice**, i backup del log verranno sospesi entro 24 ore dalla modifica del modello di recupero. Analogamente, se il modello di recupero viene modificato da **Simple**, è ora possibile supportare i backup del log per il database. le pianificazioni dei backup del log verranno abilitate entro 24 ore dalla modifica del modello di recupero.

      ![Criteri di backup del log](./media/backup-azure-sql-database/log-backup-policy.png)

1. Nel menu **Criterio di backup**, scegliere se abilitare la **compressione dei backup SQL** o meno. L'opzione è disabilitata per impostazione predefinita. Se abilitata, SQL Server invierà un flusso di backup compresso al VDI. Backup di Azure esegue l'override dei valori predefiniti a livello di istanza con la clausola COMPRESSION/NO_COMPRESSION a seconda del valore di questo controllo.

1. Dopo aver completato le modifiche ai criteri di backup, selezionare **OK**.

> [!NOTE]
> Ogni backup del log viene concatenato al backup completo precedente per formare una catena di recupero. Questo backup completo verrà mantenuto fino alla scadenza della conservazione dell'ultimo backup del log. Questo potrebbe significare che il backup completo viene mantenuto per un periodo aggiuntivo per assicurarsi che tutti i log possano essere ripristinati. Si supponga di avere un backup completo settimanale, un backup giornaliero differenziale e un log di 2 ore. Vengono tutti conservati per 30 giorni. Tuttavia, la versione settimanale completa può essere effettivamente eliminata o eliminata solo dopo i successivi backup completi, ovvero dopo 30 + 7 giorni. Ad esempio, un backup completo settimanale si verifica il 16 novembre. In base ai criteri di conservazione, deve essere mantenuto fino al 16 dicembre. L'ultimo backup del log per questa versione completa si verifica prima del successivo completo in programma, il 22 novembre. Questo log è disponibile fino al 22 dicembre e fino a quel momento non sarà possibile eliminare il backup completo del 16 novembre. Il backup completo del 16 novembre, quindi, viene mantenuto fino al 22 dicembre.

## <a name="enable-auto-protection"></a>Abilitare la protezione automatica  

È possibile abilitare la protezione automatica per eseguire automaticamente il backup di tutti i database esistenti e futuri a un'istanza autonoma di SQL Server o a un gruppo di disponibilità Always On.

* Non esiste alcun limite al numero di database che è possibile selezionare per la protezione automatica alla volta. L'individuazione viene in genere eseguita ogni otto ore. Tuttavia, è possibile individuare e proteggere immediatamente i nuovi database se si esegue manualmente un'individuazione selezionando l'opzione **riindividua i DB** .
* Non è possibile proteggere o escludere in modo selettivo i database dalla protezione in un'istanza nel momento in cui si abilita la protezione automatica.
* Se l'istanza include già alcuni database protetti, questi rimarranno protetti in base ai rispettivi criteri anche dopo l'attivazione della protezione automatica. Tutti i database non protetti aggiunti in un secondo momento avranno un solo criterio definito al momento dell'abilitazione della protezione automatica, elencati in **Configura backup**. Tuttavia, è possibile modificare in un secondo momento il criterio associato a un database protetto automaticamente.  

Per abilitare la protezione automatica:

  1. In **Elementi per backup** selezionare l'istanza per la quale si vuole abilitare la protezione automatica.
  2. Selezionare l'elenco a discesa in **PROTEZIONE AUTOMATICA**, scegliere **ON** (ATTIVA), quindi selezionare **OK**.

      ![Abilitare la protezione automatica per il gruppo di disponibilità](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Il backup è configurato per tutti i database insieme e può essere monitorato in **Processi di backup**.

Se è necessario disabilitare la protezione automatica, selezionare il nome dell'istanza in **Configura backup**, quindi selezionare **Disabilita protezione automatica** per l'istanza. Tutti i database continueranno a essere sottoposti a backup, ma i database futuri non verranno protetti automaticamente.

![Disabilitare la protezione automatica per l'istanza](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

* [Ripristinare database SQL Server di cui è stato eseguito il backup](restore-sql-database-azure-vm.md)
* [Gestire database SQL Server di cui è stato eseguito il backup](manage-monitor-sql-database-backup.md)
