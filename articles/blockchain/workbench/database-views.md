---
title: Viste di database di Azure blockchain Workbench
description: Panoramica delle viste del database SQL di Azure blockchain Workbench di anteprima disponibili.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74325980"
---
# <a name="azure-blockchain-workbench-database-views"></a>Viste di database di Azure blockchain Workbench

Azure blockchain Workbench Preview recapita i dati da Ledger distribuiti a un database di database SQL non *concatenato* . Il database fuori catena consente di usare SQL e gli strumenti esistenti, ad esempio [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), per interagire con i dati blockchain.

Azure Blockchain Workbench offre un set di viste di database che forniscono accesso a dati utili quando si eseguono le query. Queste viste sono notevolmente denormalizzate per permettere di iniziare rapidamente a creare report e analisi e utilizzare in altri modi i dati delle blockchain con gli strumenti esistenti, senza dover formare di nuovo il personale che si occupa dei database.

Questa sezione include una panoramica delle viste di database e dei dati che contengono.

> [!NOTE]
> Anche se possibile, oltre a queste viste non è supportato alcun utilizzo diretto delle tabelle di database presenti nel database.
>

## <a name="vwapplication"></a>vwApplication

Questa vista fornisce informazioni dettagliate sulle **applicazioni** che sono state caricate in Azure Blockchain Workbench.

| Nome                             | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                  | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDescription           | nvarchar(255) | Sì         | Descrizione dell'applicazione |
| ApplicationDisplayName           | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled               | bit           | No          | Specifica se l'applicazione è attualmente abilitata<br /> **Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database. |
| UploadedDtTm                     | datetime2(7)  | No          | Data e ora in cui un contratto è stato caricato |
| UploadedByUserId                 | int           | No          | ID dell'utente che ha caricato l'applicazione |
| UploadedByUserExternalId         | nvarchar(255) | No          | Identificatore esterno dell'utente che ha caricato l'applicazione. Per impostazione predefinita, questo ID è l'utente della Azure Active Directory per il Consorzio.                                                                                                |
| UploadedByUserProvisioningStatus | int           | No          | Identifica lo stato corrente del processo di provisioning per l'utente. I valori possibili sono: <br />0: l'utente è stato creato dall'API<br />1: una chiave è stata associata all'utente nel database<br />2: è stato effettuato il provisioning completo per l'utente                         |
| UploadedByUserFirstName          | nvarchar(50)  | Sì         | Nome dell'utente che ha caricato il contratto |
| UploadedByUserLastName           | nvarchar(50)  | Sì         | Cognome dell'utente che ha caricato il contratto |
| UploadedByUserEmailAddress       | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente che ha caricato il contratto |

## <a name="vwapplicationrole"></a>vwApplicationRole

Questa vista fornisce informazioni dettagliate sui ruoli definiti nelle applicazioni Azure Blockchain Workbench.

In un'applicazione di *trasferimento di asset*, ad esempio, è possibile definire ruoli come *Acquirente* e *Venditore*.

| Nome                   | Type             | Può essere Null | Descrizione                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | No          | Identificatore univoco dell'applicazione           |
| ApplicationName        | nvarchar(50)     | No          | Nome dell'applicazione                       |
| ApplicationDescription | nvarchar(255)    | Sì         | Descrizione dell'applicazione                  |
| ApplicationDisplayName | nvarchar(255)    | No          | Nome da visualizzare in un'interfaccia utente      |
| RoleId                 | int              | No          | Identificatore univoco di un ruolo nell'applicazione |
| RoleName               | nvarchar(50)      | No          | Nome del ruolo                              |
| RoleDescription        | description(255) | Sì         | Descrizione del ruolo                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Questa vista fornisce informazioni dettagliate sui ruoli definiti nelle applicazioni Azure Blockchain Workbench e sui rispettivi utenti associati.

In un'applicazione di *trasferimento degli asset*, ad esempio, *John Smith* può essere associato al ruolo *Acquirente*.

| Nome                       | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | No          | Identificatore univoco dell'applicazione                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | No          | Nome dell'applicazione                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Sì         | Descrizione dell'applicazione                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente                                                                                                                                                                                          |
| ApplicationRoleId          | int           | No          | Identificatore univoco di un ruolo nell'applicazione                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar(50)   | No          | Nome del ruolo                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Sì         | Descrizione del ruolo                                                                                                                                                                                                             |
| UserId                     | int           | No          | ID dell'utente associato al ruolo |
| UserExternalId             | nvarchar(255) | No          | Identificatore esterno dell'utente associato al ruolo. Per impostazione predefinita, questo ID è l'utente della Azure Active Directory per il Consorzio.                                                                     |
| UserProvisioningStatus     | int           | No          | Identifica lo stato corrente del processo di provisioning per l'utente. I valori possibili sono: <br />0: l'utente è stato creato dall'API<br />1: una chiave è stata associata all'utente nel database<br />2: è stato effettuato il provisioning completo per l'utente |
| UserFirstName              | nvarchar(50)  | Sì         | Nome dell'utente associato al ruolo |
| UserLastName               | nvarchar(255) | Sì         | Cognome dell'utente associato al ruolo |
| UserEmailAddress           | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente associato al ruolo |

## <a name="vwconnectionuser"></a>vwConnectionUser

Questa vista fornisce informazioni dettagliate sulle connessioni definite in Azure Blockchain Workbench e sui rispettivi utenti associati. Per ogni connessione, questa vista contiene i dati seguenti:

-   Informazioni dettagliate sui libri mastri associati
-   Informazioni sugli utenti associati

| Nome                     | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | No          | Identificatore univoco di una connessione in Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | No          | URL dell'endpoint per una connessione |
| ConnectionFundingAccount | nvarchar(255) | Sì         | Conto finanziario associato a una connessione, se applicabile |
| LedgerId                 | int           | No          | Identificatore univoco di un libro mastro |
| LedgerName               | nvarchar(50)  | No          | Nome del libro mastro |
| LedgerDisplayName        | nvarchar(255) | No          | Nome del libro mastro da visualizzare nell'interfaccia utente |
| UserId                   | int           | No          | ID dell'utente associato alla connessione |
| UserExternalId           | nvarchar(255) | No          | Identificatore esterno dell'utente associato alla connessione. Per impostazione predefinita, questo ID è l'utente della Azure Active Directory per il Consorzio. |
| UserProvisioningStatus   | int           | No          |Identifica lo stato corrente del processo di provisioning per l'utente. I valori possibili sono: <br />0: l'utente è stato creato dall'API<br />1: una chiave è stata associata all'utente nel database<br />2: è stato effettuato il provisioning completo per l'utente |
| UserFirstName            | nvarchar(50)  | Sì         | Nome dell'utente associato alla connessione |
| UserLastName             | nvarchar(255) | Sì         | Cognome dell'utente associato alla connessione |
| UserEmailAddress         | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente associato alla connessione |

## <a name="vwcontract"></a>vwContract

Questa vista fornisce informazioni dettagliate sui contratti distribuiti. Per ogni contratto, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Implementazione del libro mastro associato per la funzione
-   Informazioni dettagliate sull'utente che ha avviato l'azione
-   Informazioni dettagliate correlate al blocco e alla transazione della blockchain

| Nome                                     | Type           | Può essere Null | Descrizione                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | No          | Identificatore univoco di una connessione in Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | No          | URL dell'endpoint per una connessione |
| ConnectionFundingAccount                 | nvarchar(255)  | Sì         | Conto finanziario associato a una connessione, se applicabile |
| LedgerId                                 | int            | No          | Identificatore univoco di un libro mastro |
| LedgerName                               | nvarchar(50)   | No          | Nome del libro mastro |
| LedgerDisplayName                        | nvarchar(255)  | No          | Nome del libro mastro da visualizzare nell'interfaccia utente |
| ApplicationId                            | int            | No          | Identificatore univoco dell'applicazione |
| ApplicationName                          | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName                   | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                       | bit            | No          | Specifica se l'applicazione è attualmente abilitata.<br /> **Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database.  |
| WorkflowId                               | int            | No          | Identificatore univoco del flusso di lavoro associato a un contratto |
| WorkflowName                             | nvarchar(50)   | No          | Nome del flusso di lavoro associato a un contratto |
| WorkflowDisplayName                      | nvarchar(255)  | No          | Nome del flusso di lavoro associato al contratto da visualizzare nell'interfaccia utente |
| WorkflowDescription                      | nvarchar(255)  | Sì         | Descrizione del flusso di lavoro associato a un contratto |
| ContractCodeId                           | int            | No          | Identificatore univoco del codice del contratto associato al contratto |
| ContractFileName                         | int            | No          | Nome del file contenente il codice del contratto intelligente per questo flusso di lavoro. |
| ContractUploadedDtTm                     | int            | No          | Data e ora in cui il codice del contratto è stato caricato |
| ContractId                               | int            | No          | Identificatore univoco del contratto |
| ContractProvisioningStatus               | int            | No          | Identifica lo stato corrente del processo di provisioning per il contratto. I valori possibili sono: <br />0: il contratto è stato creato dall'API nel database<br />1: il contratto è stato inviato al libro mastro<br />2: il contratto è stato distribuito correttamente nel libro mastro<br />3 o 4: il contratto non è stato distribuito correttamente nel libro mastro<br />5: il contratto è stato distribuito correttamente nel libro mastro <br /><br />A partire dalla versione 1.5, sono supportati i valori da 0 a 5. Per la compatibilità con le versioni precedenti della versione corrente, è disponibile la visualizzazione **vwContractV0** che supporta solo valori da 0 a 2. |
| ContractLedgerIdentifier                 | nvarchar(255) |             | Indirizzo di posta elettronica dell'utente che ha distribuito il contratto |
| ContractDeployedByUserId                 | int            | No          | Identificatore esterno dell'utente che ha distribuito il contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta l'ID Azure Active Directory per l'utente.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | No          | Identificatore esterno dell'utente che ha distribuito il contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta l'ID Azure Active Directory per l'utente.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | No          | Identifica lo stato corrente del processo di provisioning per l'utente. I valori possibili sono: <br />0: l'utente è stato creato dall'API<br />1: una chiave è stata associata all'utente nel database <br />2: è stato effettuato il provisioning completo per l'utente                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Sì         | Nome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserLastName           | nvarchar(255)  | Sì         | Cognome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Sì         | Indirizzo di posta elettronica dell'utente che ha distribuito il contratto |

## <a name="vwcontractaction"></a>vwContractAction

Questa vista rappresenta la maggior parte delle informazioni correlate ad azioni eseguite sui contratti ed è progettata per semplificare notevolmente scenari comuni di creazione di report. Per ogni azione eseguita, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Definizione di funzioni e parametri del contratto intelligente associato
-   Implementazione del libro mastro associato per la funzione
-   Valori di istanza specifici forniti per i parametri
-   Informazioni dettagliate sull'utente che ha avviato l'azione
-   Informazioni dettagliate correlate al blocco e alla transazione della blockchain

| Nome                                     | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                          | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName                   | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                       | bit           | No          | Questo campo specifica se l'applicazione è attualmente abilitata. Nota: anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati sui questi contratti restano nel database.                                                  |
| WorkflowId                               | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                             | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName                      | nvarchar(255) | No          | Nome del flusso di lavoro da visualizzare in un'interfaccia utente |
| WorkflowDescription                      | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| ContractId                               | int           | No          | Identificatore univoco del contratto |
| ContractProvisioningStatus               | int           | No          | Identifica lo stato corrente del processo di provisioning per il contratto. I valori possibili sono: <br />0: il contratto è stato creato dall'API nel database<br />1: il contratto è stato inviato al libro mastro<br />2: il contratto è stato distribuito correttamente nel libro mastro<br />3 o 4: il contratto non è stato distribuito correttamente nel libro mastro<br />5: il contratto è stato distribuito correttamente nel libro mastro <br /><br />A partire dalla versione 1.5, sono supportati i valori da 0 a 5. Per la compatibilità con le versioni precedenti della versione corrente, è disponibile la visualizzazione **vwContractActionV0** che supporta solo valori da 0 a 2. |
| ContractCodeId                           | int           | No          | Identificatore univoco per l'implementazione del codice del contratto |
| ContractLedgerIdentifier                 | nvarchar(255) | Sì         | Identificatore univoco associato alla versione distribuita di un contratto intelligente per un libro mastro distribuito specifico. Ad esempio, Ethereum. |
| ContractDeployedByUserId                 | int           | No          | Identificatore univoco dell'utente che ha distribuito il contratto |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Sì         | Nome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserLastName           | nvarchar(255) | Sì         | Cognome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserExternalId         | nvarchar(255) | No          | Identificatore esterno dell'utente che ha distribuito il contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta la propria identità nel Consorzio Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente che ha distribuito il contratto |
| WorkflowFunctionId                       | int           | No          | Identificatore univoco di una funzione del flusso di lavoro |
| WorkflowFunctionName                     | nvarchar(50)  | No          | Nome della funzione |
| WorkflowFunctionDisplayName              | nvarchar(255) | No          | Nome di una funzione da visualizzare nell'interfaccia utente |
| WorkflowFunctionDescription              | nvarchar(255) | No          | Descrizione della funzione |
| ContractActionId                         | int           | No          | Identificatore univoco di un'azione del contratto |
| ContractActionProvisioningStatus         | int           | No          | Identifica lo stato corrente del processo di provisioning per l'azione del contratto. I valori possibili sono: <br />0: l'azione del contratto è stata creata dall'API nel database<br />1: l'azione del contratto è stata inviata al libro mastro<br />2: l'azione del contratto è stata distribuita correttamente nel libro mastro<br />3 o 4: il contratto non è stato distribuito correttamente nel libro mastro<br />5: il contratto è stato distribuito correttamente nel libro mastro <br /><br />A partire dalla versione 1.5, sono supportati i valori da 0 a 5. Per la compatibilità con le versioni precedenti della versione corrente, è disponibile la visualizzazione **vwContractActionV0** che supporta solo valori da 0 a 2. |
| ContractActionTimestamp                  | datetime(2,7) | No          | Timestamp dell'azione del contratto |
| ContractActionExecutedByUserId           | int           | No          | Identificatore univoco dell'utente che ha eseguito l'azione del contratto |
| ContractActionExecutedByUserFirstName    | INT           | Sì         | Nome dell'utente che ha eseguito l'azione del contratto |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Sì         | Cognome dell'utente che ha eseguito l'azione del contratto |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Sì         | Identificatore esterno dell'utente che ha eseguito l'azione del contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta la propria identità nel Consorzio Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente che ha eseguito l'azione del contratto |
| WorkflowFunctionParameterId              | int           | No          | Identificatore univoco di un parametro della funzione |
| WorkflowFunctionParameterName            | nvarchar(50)  | No          | Nome di un parametro della funzione |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | No          | Nome di un parametro della funzione da visualizzare nell'interfaccia utente |
| WorkflowFunctionParameterDataTypeId      | int           | No          | Identificatore univoco del tipo di dati associato a un parametro della funzione del flusso di lavoro |
| WorkflowParameterDataTypeName            | nvarchar(50)  | No          | Nome del tipo di dati associato a un parametro della funzione del flusso di lavoro |
| ContractActionParameterValue             | nvarchar(255) | No          | Valore del parametro archiviato nel contratto intelligente |
| BlockHash                                | nvarchar(255) | Sì         | Hash del blocco |
| BlockNumber                              | INT           | Sì         | Numero del blocco nel libro mastro |
| BlockTimestamp                           | datetime(2,7) | Sì         | Timestamp del blocco |
| TransactionId                            | int           | No          | Identificatore univoco della transazione |
| TransactionFrom                          | nvarchar(255) | Sì         | Parte da cui ha avuto origine la transazione |
| TransactionTo                            | nvarchar(255) | Sì         | Parte con cui è stata effettuata la transazione |
| TransactionHash                          | nvarchar(255) | Sì         | Hash di una transazione |
| TransactionIsWorkbenchTransaction        | bit           | Sì         | Bit che identifica se la transazione è una transazione di Azure blockchain Workbench |
| TransactionProvisioningStatus            | INT           | Sì         | Identifica lo stato corrente del processo di provisioning per la transazione. I valori possibili sono: <br />0: la transazione è stata creata dall'API nel database<br />1: la transazione è stata inviata al libro mastro<br />2: la transazione è stata distribuita correttamente nel libro mastro                 |
| TransactionValue                         | decimal(32,2) | Sì         | Valore della transazione |

## <a name="vwcontractproperty"></a>vwContractProperty

Questa vista rappresenta la maggior parte delle informazioni correlate alle proprietà associate a un contratto ed è progettata per semplificare notevolmente scenari comuni di creazione di report. Per ogni proprietà acquisita, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Informazioni dettagliate sull'utente che ha distribuito il flusso di lavoro
-   Definizione della proprietà del contratto intelligente associata
-   Valori di istanza specifici per le proprietà
-   Informazioni dettagliate per la proprietà di stato del contratto

| Nome                               | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                    | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName             | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                 | bit           | No          | Specifica se l'applicazione è attualmente abilitata.<br />**Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database.                      |
| WorkflowId                         | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                       | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName                | nvarchar(255) | No          | Nome del flusso di lavoro da visualizzare nell'interfaccia utente |
| WorkflowDescription                | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| ContractId                         | int           | No          | Identificatore univoco del contratto |
| ContractProvisioningStatus         | int           | No          | Identifica lo stato corrente del processo di provisioning per il contratto. I valori possibili sono: <br />0: il contratto è stato creato dall'API nel database<br />1: il contratto è stato inviato al libro mastro<br />2: il contratto è stato distribuito correttamente nel libro mastro<br />3 o 4: il contratto non è stato distribuito correttamente nel libro mastro<br />5: il contratto è stato distribuito correttamente nel libro mastro <br /><br />A partire dalla versione 1.5, sono supportati i valori da 0 a 5. Per la compatibilità con le versioni precedenti della versione corrente, è disponibile la visualizzazione **vwContractPropertyV0** che supporta solo valori da 0 a 2. |
| ContractCodeId                     | int           | No          | Identificatore univoco per l'implementazione del codice del contratto |
| ContractLedgerIdentifier           | nvarchar(255) | Sì         | Identificatore univoco associato alla versione distribuita di un contratto intelligente per un libro mastro distribuito specifico. Ad esempio, Ethereum. |
| ContractDeployedByUserId           | int           | No          | Identificatore univoco dell'utente che ha distribuito il contratto |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sì         | Nome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserLastName     | nvarchar(255) | Sì         | Cognome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserExternalId   | nvarchar(255) | No          | Identificatore esterno dell'utente che ha distribuito il contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta la propria identità nel Consorzio Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente che ha distribuito il contratto |
| WorkflowPropertyId                 | INT           |             | Identificatore univoco di una proprietà di un flusso di lavoro |
| WorkflowPropertyDataTypeId         | int           | No          | ID del tipo di dati della proprietà |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | No          | Nome del tipo di dati della proprietà |
| WorkflowPropertyName               | nvarchar(50)  | No          | Nome della proprietà del flusso di lavoro |
| WorkflowPropertyDisplayName        | nvarchar(255) | No          | Nome visualizzato della proprietà del flusso di lavoro |
| WorkflowPropertyDescription        | nvarchar(255) | Sì         | Descrizione della proprietà |
| ContractPropertyValue              | nvarchar(255) | No          | Valore di una proprietà nel contratto |
| StateName                          | nvarchar(50)  | Sì         | Se questa proprietà contiene lo stato del contratto, si tratta del nome visualizzato per lo stato. Se non è associata allo stato, il valore sarà null. |
| StateDisplayName                   | nvarchar(255) | No          | Se questa proprietà contiene lo stato, si tratta del nome visualizzato per lo stato. Se non è associata allo stato, il valore sarà null. |
| StateValue                         | nvarchar(255) | Sì         | Se questa proprietà contiene lo stato, si tratta del valore di stato. Se non è associata allo stato, il valore sarà null. |

## <a name="vwcontractstate"></a>vwContractState

Questa vista rappresenta la maggior parte delle informazioni correlate allo stato di un contratto specifico ed è progettata per semplificare notevolmente scenari comuni di creazione di report. Ogni record in questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Informazioni dettagliate sull'utente che ha distribuito il flusso di lavoro
-   Definizione della proprietà del contratto intelligente associata
-   Informazioni dettagliate per la proprietà di stato del contratto

| Nome                               | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                    | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName             | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                 | bit           | No          | Specifica se l'applicazione è attualmente abilitata.<br />**Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database. |
| WorkflowId                         | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                       | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName                | nvarchar(255) | No          | Nome visualizzato nell'interfaccia utente |
| WorkflowDescription                | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| ContractLedgerImplementationId     | nvarchar(255) | Sì         | Identificatore univoco associato alla versione distribuita di un contratto intelligente per un libro mastro distribuito specifico. Ad esempio, Ethereum. |
| ContractId                         | int           | No          | Identificatore univoco del contratto |
| ContractProvisioningStatus         | int           | No          |Identifica lo stato corrente del processo di provisioning per il contratto. I valori possibili sono: <br />0: il contratto è stato creato dall'API nel database<br />1: il contratto è stato inviato al libro mastro<br />2: il contratto è stato distribuito correttamente nel libro mastro<br />3 o 4: il contratto non è stato distribuito correttamente nel libro mastro<br />5: il contratto è stato distribuito correttamente nel libro mastro <br /><br />A partire dalla versione 1.5, sono supportati i valori da 0 a 5. Per la compatibilità con le versioni precedenti della versione corrente, è disponibile la visualizzazione **vwContractStateV0** che supporta solo valori da 0 a 2. |
| ConnectionId                       | int           | No          | Identificatore univoco dell'istanza di blockchain in cui viene distribuito il flusso di lavoro |
| ContractCodeId                     | int           | No          | Identificatore univoco per l'implementazione del codice del contratto |
| ContractDeployedByUserId           | int           | No          | Identificatore univoco dell'utente che ha distribuito il contratto |
| ContractDeployedByUserExternalId   | nvarchar(255) | No          | Identificatore esterno dell'utente che ha distribuito il contratto. Per impostazione predefinita, questo ID è il GUID che rappresenta la propria identità nel Consorzio Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Sì         | Nome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserLastName     | nvarchar(255) | Sì         | Cognome dell'utente che ha distribuito il contratto |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Sì         | Indirizzo di posta elettronica dell'utente che ha distribuito il contratto |
| WorkflowPropertyId                 | int           | No          | Identificatore univoco di una proprietà del flusso di lavoro |
| WorkflowPropertyDataTypeId         | int           | No          | ID del tipo di dati della proprietà del flusso di lavoro |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | No          | Nome del tipo di dati della proprietà del flusso di lavoro |
| WorkflowPropertyName               | nvarchar(50)  | No          | Nome della proprietà del flusso di lavoro |
| WorkflowPropertyDisplayName        | nvarchar(255) | No          | Nome della proprietà da visualizzare in un'interfaccia utente |
| WorkflowPropertyDescription        | nvarchar(255) | Sì         | Descrizione della proprietà |
| ContractPropertyValue              | nvarchar(255) | No          | Valore di una proprietà archiviata nel contratto |
| StateName                          | nvarchar(50)  | Sì         | Se questa proprietà contiene lo stato, il nome visualizzato per lo stato. Se non è associata allo stato, il valore sarà null. |
| StateDisplayName                   | nvarchar(255) | No          | Se questa proprietà contiene lo stato, si tratta del nome visualizzato per lo stato. Se non è associata allo stato, il valore sarà null. |
| StateValue                         | nvarchar(255) | Sì         | Se questa proprietà contiene lo stato, si tratta del valore di stato. Se non è associata allo stato, il valore sarà null. |

## <a name="vwuser"></a>vwUser

Questa visualizzazione fornisce informazioni dettagliate sui membri del consorzio di cui viene effettuato il provisioning per l'uso di Azure Blockchain Workbench. Per impostazione predefinita, i dati vengono popolati tramite il provisioning iniziale dell'utente.

| Nome               | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | No          | Identificatore univoco di un utente |
| ExternalID         | nvarchar(255) | No          | Identificatore esterno di un utente. Per impostazione predefinita, questo ID è il GUID che rappresenta l'ID Azure Active Directory per l'utente. |
| ProvisioningStatus | int           | No          |Identifica lo stato corrente del processo di provisioning per l'utente. I valori possibili sono: <br />0: l'utente è stato creato dall'API<br />1: una chiave è stata associata all'utente nel database<br />2: è stato effettuato il provisioning completo per l'utente |
| FirstName          | nvarchar(50)  | Sì         | Nome dell'utente |
| LastName           | nvarchar(50)  | Sì         | Cognome dell'utente |
| EmailAddress       | nvarchar(255) | Sì         | L'indirizzo e-mail dell'utente |

## <a name="vwworkflow"></a>vwWorkflow

Questa vista rappresenta informazioni dettagliate sui metadati del flusso di lavoro di base, insieme alle funzioni e ai parametri del flusso di lavoro. Progettato per la creazione di report, contiene inoltre i metadati relativi all'applicazione associata al flusso di lavoro. Questa vista contiene dati di più tabelle sottostanti per semplificare la creazione di report sui flussi di lavoro. Per ogni flusso di lavoro, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Informazioni sullo stato iniziale del flusso di lavoro associato

| Nome                              | Type          | Può essere Null | Descrizione                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                   | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName            | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                | bit           | No          | Specifica se l'applicazione è abilitata |
| WorkflowId                        | INT           | Sì         | Identificatore univoco del flusso di lavoro |
| WorkflowName                      | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName               | nvarchar(255) | No          | Nome visualizzato nell'interfaccia utente |
| WorkflowDescription               | nvarchar(255) | Sì         | Descrizione del flusso di lavoro. |
| WorkflowConstructorFunctionId     | int           | No          | Identificatore della funzione del flusso di lavoro che funge da costruttore per il flusso di lavoro |
| WorkflowStartStateId              | int           | No          | Identificatore univoco dello stato |
| WorkflowStartStateName            | nvarchar(50)  | No          | Nome dello stato |
| WorkflowStartStateDisplayName     | nvarchar(255) | No          | Nome dello stato da visualizzare nell'interfaccia utente |
| WorkflowStartStateDescription     | nvarchar(255) | Sì         | Descrizione dello stato del flusso di lavoro |
| WorkflowStartStateStyle           | nvarchar(50)  | Sì         | Questo valore specifica la percentuale di completamento del flusso di lavoro quando è in questo stato |
| WorkflowStartStateValue           | int           | No          | Valore dello stato |
| WorkflowStartStatePercentComplete | int           | No          | Descrizione di testo che fornisce un'indicazione ai client su come eseguire il rendering di questo stato nell'interfaccia utente. Gli stati supportati includono *Operazione riuscita* e *Errore* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Questa vista rappresenta informazioni dettagliate sui metadati del flusso di lavoro di base, insieme alle funzioni e ai parametri del flusso di lavoro. Progettato per la creazione di report, contiene inoltre i metadati relativi all'applicazione associata al flusso di lavoro. Questa vista contiene dati di più tabelle sottostanti per semplificare la creazione di report sui flussi di lavoro. Per ogni funzione del flusso di lavoro, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Dettagli della funzione del flusso di lavoro

| Nome                                 | Type          | Può essere Null | Descrizione                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName                      | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName               | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled                   | bit           | No          | Specifica se l'applicazione è abilitata |
| WorkflowId                           | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                         | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName                  | nvarchar(255) | No          | Nome del flusso di lavoro da visualizzare nell'interfaccia utente |
| WorkflowDescription                  | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| WorkflowFunctionId                   | int           | No          | Identificatore univoco di una funzione |
| WorkflowFunctionName                 | nvarchar(50)  | Sì         | Nome della funzione |
| WorkflowFunctionDisplayName          | nvarchar(255) | No          | Nome di una funzione da visualizzare nell'interfaccia utente |
| WorkflowFunctionDescription          | nvarchar(255) | Sì         | Descrizione della funzione del flusso di lavoro |
| WorkflowFunctionIsConstructor        | bit           | No          | Identifica se la funzione del flusso di lavoro è il costruttore del flusso di lavoro. |
| WorkflowFunctionParameterId          | int           | No          | Identificatore univoco di un parametro di una funzione |
| WorkflowFunctionParameterName        | nvarchar(50)  | No          | Nome di un parametro della funzione |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | No          | Nome di un parametro della funzione da visualizzare nell'interfaccia utente |
| WorkflowFunctionParameterDataTypeId  | int           | No          | Identificatore univoco del tipo di dati associato a un parametro della funzione del flusso di lavoro |
| WorkflowParameterDataTypeName        | nvarchar(50)  | No          | Nome del tipo di dati associato a un parametro della funzione del flusso di lavoro |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Questa vista rappresenta le proprietà definite per un flusso di lavoro. Per ogni proprietà, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Dettagli della proprietà del flusso di lavoro

| Nome                         | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName              | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName       | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| ApplicationEnabled           | bit           | No          | Specifica se l'applicazione è attualmente abilitata.<br />**Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database. |
| WorkflowId                   | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                 | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName          | nvarchar(255) | No          | Nome del flusso di lavoro da visualizzare in un'interfaccia utente |
| WorkflowDescription          | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| WorkflowPropertyID           | int           | No          | Identificatore univoco di una proprietà di un flusso di lavoro |
| WorkflowPropertyName         | nvarchar(50)  | No          | Nome della proprietà. |
| WorkflowPropertyDescription  | nvarchar(255) | Sì         | Descrizione della proprietà |
| WorkflowPropertyDisplayName  | nvarchar(255) | No          | Nome da visualizzare in un'interfaccia utente |
| WorkflowPropertyWorkflowId   | int           | No          | ID del flusso di lavoro cui è associata questa proprietà |
| WorkflowPropertyDataTypeId   | int           | No          | ID del tipo di dati definito per la proprietà |
| WorkflowPropertyDataTypeName | nvarchar(50)  | No          | Nome del tipo di dati definito per la proprietà |
| WorkflowPropertyIsState      | bit           | No          | Questo campo specifica se la proprietà del flusso di lavoro contiene lo stato del flusso di lavoro |

## <a name="vwworkflowstate"></a>vwWorkflowState

Questa vista rappresenta le proprietà associate a un flusso di lavoro. Per ogni contratto, questa vista contiene i dati seguenti:

-   Definizione dell'applicazione associata
-   Definizione del flusso di lavoro associato
-   Informazioni sullo stato del flusso di lavoro

| Nome                         | Type          | Può essere Null | Descrizione                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | No          | Identificatore univoco dell'applicazione |
| ApplicationName              | nvarchar(50)  | No          | Nome dell'applicazione |
| ApplicationDisplayName       | nvarchar(255) | No          | Descrizione dell'applicazione |
| ApplicationEnabled           | bit           | No          | Specifica se l'applicazione è attualmente abilitata.<br />**Nota:** anche se un'applicazione può essere indicata come disabilitata nel database, i contratti associati restano nella blockchain e i dati su questi contratti restano nel database. |
| WorkflowId                   | int           | No          | Identificatore univoco del flusso di lavoro |
| WorkflowName                 | nvarchar(50)  | No          | Nome del flusso di lavoro |
| WorkflowDisplayName          | nvarchar(255) | No          | Nome del flusso di lavoro da visualizzare nell'interfaccia utente |
| WorkflowDescription          | nvarchar(255) | Sì         | Descrizione del flusso di lavoro |
| WorkflowStateID              | int           | No          | Identificatore univoco dello stato |
| WorkflowStateName            | nvarchar(50)  | No          | Nome dello stato |
| WorkflowStateDisplayName     | nvarchar(255) | No          | Nome dello stato da visualizzare nell'interfaccia utente |
| WorkflowStateDescription     | nvarchar(255) | Sì         | Descrizione dello stato del flusso di lavoro |
| WorkflowStatePercentComplete | int           | No          | Questo valore specifica la percentuale di completamento del flusso di lavoro quando è in questo stato |
| WorkflowStateValue           | nvarchar(50)  | No          | Valore dello stato |
| WorkflowStateStyle           | nvarchar(50)  | No          | Descrizione di testo che fornisce un'indicazione ai client su come eseguire il rendering di questo stato nell'interfaccia utente. Gli stati supportati includono *Operazione riuscita* e *Errore* |
