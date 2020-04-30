---
title: Risolvere gli errori di backup SAP HANA database
description: Viene descritto come risolvere gli errori comuni che possono verificarsi quando si usa backup di Azure per eseguire il backup di SAP HANA database.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79459311"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Risolvere i problemi di backup di database SAP HANA in Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per il backup di database SAP HANA in macchine virtuali di Azure. Per ulteriori informazioni sugli scenari di backup SAP HANA attualmente supportati, vedere [supporto dello scenario](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Prerequisiti e autorizzazioni

Per informazioni sui [prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [sullo script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , vedere le sezioni precedenti alla configurazione dei backup.

## <a name="common-user-errors"></a>Errori utente comuni

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Messaggio di errore**      | <span style="font-weight:normal">Backup di Azure non dispone dei privilegi di ruolo necessari per eseguire il backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possibili cause**    | È possibile che il ruolo sia stato sovrascritto.                          |
| **Azione consigliata** | Per risolvere il problema, eseguire lo script dal riquadro **Discover DB** o scaricarlo [qui](https://aka.ms/scriptforpermsonhana). In alternativa, aggiungere il ruolo "SAP_INTERNAL_HANA_SUPPORT" all'utente di backup del carico di lavoro (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Messaggio di errore      | <span style="font-weight:normal">Non è stato possibile connettersi al sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | L'istanza di SAP HANA potrebbe essere inattiva.<br/>Le autorizzazioni necessarie per il backup di Azure per interagire con il database HANA non sono impostate. |
| **Azione consigliata** | Verificare che il database di SAP HANA sia attivo. Se il database è attivo e in esecuzione, verificare che tutte le autorizzazioni necessarie siano impostate. Se non è presente alcuna autorizzazione, eseguire lo [script di preregistrazione](https://aka.ms/scriptforpermsonhana) per aggiungere le autorizzazioni mancanti. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Messaggio di errore      | <span style="font-weight:normal">L'istanza di SAP HANA specificata non è valida o non è stata trovata</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Non è possibile eseguire il backup di più istanze di SAP HANA in una singola macchina virtuale di Azure. |
| **Azione consigliata** | Eseguire lo [script di preregistrazione](https://aka.ms/scriptforpermsonhana) nell'istanza di SAP Hana di cui si vuole eseguire il backup. Se il problema persiste, contattare il supporto tecnico Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Messaggio di errore      | <span style="font-weight:normal">L'operazione di SAP HANA specificata non è supportata</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Backup di Azure per SAP HANA non supporta il backup incrementale e le azioni eseguite sui client SAP HANA nativi (studio/pozzetto/DBA) |
| **Azione consigliata** | Per ulteriori informazioni, vedere [qui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Messaggio di errore      | <span style="font-weight:normal">Il database SAP HANA non supporta il tipo di backup richiesto</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Backup di Azure non supporta il backup incrementale e il backup tramite snapshot |
| **Azione consigliata** | Per ulteriori informazioni, vedere [qui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Messaggio di errore      | <span style="font-weight:normal">Catena di log di backup interruppe</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | È possibile che la destinazione di backup del log sia stata aggiornata da backint a file system oppure che sia stato modificato il file eseguibile backint |
| **Azione consigliata** | Attivare un backup completo per risolvere il problema                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Messaggio di errore      | <span style="font-weight:normal">I sistemi di origine e di destinazione per il ripristino sono incompatibili</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Il sistema di destinazione per il ripristino non è compatibile con l'origine |
| **Azione consigliata** | Per informazioni sui tipi di ripristino supportati oggi, vedere la nota SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Messaggio di errore      | <span style="font-weight:normal">Rilevato aggiornamento da DSC a MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | L'istanza SAP HANA è stata aggiornata da DSC a MDC. I backup avranno esito negativo dopo l'aggiornamento. |
| **Azione consigliata** | Per risolvere il problema, seguire i passaggi elencati nella [sezione aggiornamento da SAP HANA 1,0 a 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore      | <span style="font-weight:normal">La configurazione backint rilevata non è valida</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | I parametri di supporto non sono specificati correttamente per backup di Azure |
| **Azione consigliata** | Verificare che siano impostati i seguenti parametri (backint):<br/>\*[catalog_backup_using_backint: true]<br/>\*[enable_accumulated_catalog_backup: false]<br/>\*[parallel_data_backup_backint_channels: 1]<br/>\*[log_backup_timeout_s: 900)]<br/>\*[backint_response_timeout: 7200]<br/>Se i parametri basati su backint sono presenti nell'HOST, rimuoverli. Se i parametri non sono presenti a livello di HOST ma sono stati modificati manualmente a livello di database, ripristinare i valori appropriati come descritto in precedenza. In alternativa, eseguire [Stop Protection e mantenere i dati di backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) dal portale di Azure, quindi selezionare **Riprendi backup**. |

## <a name="restore-checks"></a>Controlli di ripristino

### <a name="single-container-database-sdc-restore"></a>Ripristino di un database contenitore singolo (DSC)

Prestare attenzione agli input durante il ripristino di un singolo database contenitore (DSC) per HANA in un altro computer DSC. Il nome del database deve essere specificato con lettere minuscole e con "DSC" accodato tra parentesi quadre. L'istanza HANA verrà visualizzata in lettere maiuscole.

Si supponga che venga eseguito il backup di un'istanza DSC HANA "H21". Nella pagina elementi di backup viene visualizzato il nome dell'elemento di backup come **"H21 (DSC)"**. Se si tenta di ripristinare il database in un'altra DSC di destinazione, ad esempio H11, è necessario fornire gli input seguenti.

![Nome del database DSC ripristinato](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenere presente quanto segue:

- Per impostazione predefinita, il nome del database ripristinato verrà popolato con il nome dell'elemento di backup. In questo caso, H21 (DSC).
- Se si seleziona la destinazione come H11, il nome del database ripristinato non verrà modificato automaticamente. **Deve essere modificato in H11 (DSC)**. Per quanto concerne DSC, il nome del database ripristinato sarà l'ID dell'istanza di destinazione con lettere minuscole e "DSC" accodato tra parentesi quadre.
- Poiché la DSC può avere un solo database singolo, è anche necessario fare clic sulla casella di controllo per consentire l'override dei dati del database esistenti con i dati del punto di ripristino.
- Linux distingue tra maiuscole e minuscole. Prestare attenzione a mantenere il caso.

### <a name="multiple-container-database-mdc-restore"></a>Ripristino di più database contenitore (MDC)

In più database contenitore per HANA la configurazione standard è SYSTEMDB + 1 o più DB tenant. Il ripristino di un'intera istanza di SAP HANA significa ripristinare sia i database SYSTEMDB che i database tenant. Uno Ripristina prima SYSTEMDB, quindi procede per il database tenant. Il database di sistema indica essenzialmente di sostituire le informazioni di sistema nella destinazione selezionata. Questa operazione di ripristino sostituisce inoltre le informazioni correlate a BackInt nell'istanza di di destinazione. Quindi, dopo il ripristino del database di sistema in un'istanza di destinazione, eseguire di nuovo lo script di pre-registrazione. Solo i ripristini di database tenant successivi riusciranno.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Aggiornamento da SAP HANA 1,0 a 2,0

Se si sta proteggendo SAP HANA database 1,0 e si vuole eseguire l'aggiornamento a 2,0, seguire questa procedura:

- [Arrestare la protezione](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) con Mantieni i dati per il database DSC precedente.
- Eseguire l'aggiornamento. Al termine, HANA è ora MDC con un database di sistema e i database tenant
- Eseguire di nuovo [lo script di pre-registrazione](https://aka.ms/scriptforpermsonhana) con i dettagli corretti di (SID e MDC).
- Ripetere la registrazione dell'estensione per lo stesso computer in portale di Azure (backup-> visualizzare i dettagli-> selezionare la VM di Azure pertinente-> Re-Register).
- Fare clic su riindividuare i database per la stessa macchina virtuale. Questa azione dovrebbe mostrare i nuovi database nel passaggio 2 con i dettagli corretti (SYSTEMDB e tenant DB, non DSC).
- Configurare il backup per questi nuovi database.

## <a name="upgrading-without-an-sid-change"></a>Aggiornamento senza una modifica del SID

Gli aggiornamenti al sistema operativo o SAP HANA che non provocano una modifica del SID possono essere gestiti come indicato di seguito:

- [Arrestare la protezione](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) con Mantieni dati per il database
- Eseguire l'aggiornamento.
- Eseguire di nuovo lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana). In genere, è stato rilevato che il processo di aggiornamento rimuove i ruoli necessari. L'esecuzione dello script di pre-registrazione consentirà di verificare tutti i ruoli necessari.
- [Riattivare la protezione](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) per il database

## <a name="re-registration-failures"></a>Errori di ripetizione della registrazione

Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più dei seguenti sintomi:

- Tutte le operazioni, ad esempio backup, ripristino e configurazione del backup, non riescono nella macchina virtuale con uno dei codici di errore seguenti: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Se l'area **stato backup** per l'elemento di backup **non è raggiungibile**, escludere tutte le altre cause che possono generare lo stesso stato:

  - Mancanza di autorizzazioni per eseguire operazioni relative al backup nella macchina virtuale
  - La macchina virtuale è stata arrestata, quindi non è possibile eseguire i backup
  - Problemi di rete

Questi sintomi possono verificarsi per uno o più dei seguenti motivi:

- Un'estensione è stata eliminata o disinstallata dal portale.
- La macchina virtuale è stata ripristinata di nuovo nel tempo tramite il ripristino del disco sul posto.
- La macchina virtuale è stata arrestata per un periodo prolungato, quindi la configurazione dell'estensione è scaduta.
- La macchina virtuale è stata eliminata e un'altra VM è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminata.

Negli scenari precedenti è consigliabile attivare un'operazione di ripetizione della registrazione nella macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le [domande frequenti](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) sul backup dei database SAP Hana nelle VM di Azure.
