---
title: Risolvere i problemi di gestione Integration Runtime SSIS
description: Questo articolo fornisce indicazioni per la risoluzione dei problemi di gestione di SSIS Integration Runtime (IR SSIS)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: b2c1d08656ce9ef6b76e34a943f133859b78345a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86172027"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Risolvere i problemi di gestione di SSIS Integration Runtime in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo fornisce indicazioni per la risoluzione dei problemi di gestione in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), noto anche come runtime di integrazione SSIS.

## <a name="overview"></a>Panoramica

Se si verificano problemi durante il provisioning o il deprovisioning del runtime di integrazione SSIS, verrà visualizzato un messaggio di errore nel portale di Microsoft Azure Data Factory o un errore restituito da un cmdlet di PowerShell. L'errore viene sempre visualizzato nel formato di un codice di errore con un messaggio di errore dettagliato.

Se il codice di errore è InternalServerError, il servizio presenta problemi temporanei ed è necessario ripetere l'operazione in un secondo momento. Se un nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

In caso contrario, tre dipendenze esterne principali possono causare errori: database SQL di Azure o Azure SQL Istanza gestita, uno script di installazione personalizzato e una configurazione di rete virtuale.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Problemi relativi al database SQL o a SQL Istanza gestita

Il database SQL o il Istanza gestita SQL è necessario se si esegue il provisioning del runtime di integrazione SSIS con un database del catalogo SSIS. Il runtime di integrazione SSIS deve essere in grado di accedere al database SQL o a SQL Istanza gestita. Inoltre, l'account di accesso per il database SQL o il Istanza gestita SQL deve disporre dell'autorizzazione per la creazione di un database del catalogo SSIS (SSISDB). Se si verifica un errore, nel portale di Data Factory verrà visualizzato un codice errore con un messaggio di eccezione SQL dettagliato. Usare le informazioni nell'elenco seguente per risolvere i problemi relativi ai codici di errore.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Questo problema può essere visualizzato quando si effettua il provisioning di un nuovo SSIS IR o quando il runtime di integrazione è in esecuzione. Se l'errore si verifica durante il provisioning del runtime di integrazione, è possibile ricevere un messaggio SqlException dettagliato nel messaggio di errore, a indicare uno dei problemi seguenti:

* Un problema di connessione di rete. Controllare se il nome host per il database SQL o il Istanza gestita SQL è accessibile. Verificare inoltre che nessun firewall o gruppo di sicurezza di rete (NSG) blocchi l'accesso di SSIS IR al server.
* Errore di accesso durante l'autenticazione a SQL. L'account specificato non può accedere al database di SQL Server. Assicurarsi di specificare l'account utente corretto.
* Errore di accesso durante l'autenticazione a Microsoft Azure Active Directory (Azure AD) (identità gestita). Aggiungere l'identità gestita della factory a un gruppo AAD e verificare che tale identità gestita disponga delle autorizzazioni di accesso al server di database del catalogo.
* Timeout della connessione. Questo errore è sempre causato da una configurazione relativa alla sicurezza. È consigliabile:
  1. Creare una nuova macchina virtuale.
  1. Aggiungere la macchina virtuale alla stessa Rete virtuale di Microsoft Azure di IR se il runtime di integrazione è in una rete virtuale.
  1. Installare SSMS e verificare lo stato del database SQL o di SQL Istanza gestita.

Per altri problemi, risolvere l'errore visualizzato nel messaggio di errore di eccezione SQL dettagliato. Se i problemi persistono, contattare il team di supporto di SQL Istanza gestita o del database SQL.

Se viene visualizzato l'errore durante l'esecuzione del runtime di integrazione, è probabile che il gruppo di sicurezza di rete o le modifiche del firewall impediscano l'accesso al database SQL o al Istanza gestita SQL da parte del nodo di lavoro IR SSIS Sbloccare il nodo del ruolo di lavoro IR SSIS in modo che possa accedere al database SQL o a SQL Istanza gestita.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Questo tipo di messaggio di errore potrebbe essere simile al seguente: "il database ' SSISDB ' ha raggiunto la quota di dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni ". 

Le possibili soluzioni sono:
* Aumentare le dimensioni della quota di SSISDB.
* Modificare la configurazione di SSISDB per ridurre le dimensioni:
   * Riduzione del periodo di memorizzazione e del numero di versioni del progetto.
   * Riduzione del periodo di memorizzazione del log.
   * Modifica del livello predefinito del log.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Questo errore indica che il database SQL o SQL Istanza gestita dispone già di un SSISDB e che è usato da un altro runtime di integrazione. È necessario specificare un database SQL diverso o un Istanza gestita SQL oppure eliminare il database SSISDB esistente e riavviare il nuovo runtime di integrazione.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Questo errore può verificarsi per uno dei motivi seguenti:

* L'account utente configurato per SSIS IR non dispone dell'autorizzazione per creare il database. È possibile concedere all'utente l'autorizzazione per creare il database.
* Si verifica un timeout durante la creazione del database, ad esempio un timeout nell'esecuzione o nel funzionamento del database. Ripetere l'operazione in un secondo momento. Se il nuovo tentativo non funziona, contattare il database SQL o il team di supporto di SQL Istanza gestita.

Per altri problemi, controllare il messaggio di errore di eccezione SQL e risolvere il problema indicato nei dettagli dell'errore. Se i problemi persistono, contattare il team di supporto di SQL Istanza gestita o del database SQL.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Questo tipo di messaggio di errore è simile al seguente: "nome di oggetto non valido ' catalog.catalog_properties '". In questa situazione, si dispone già di un database denominato SSISDB ma non è stato creato dal runtime di integrazione SSIS oppure il database è in uno stato non valido causato da errori nell'ultimo provisioning del runtime di integrazione SSIS. È possibile eliminare il database esistente con il nome SSISDB oppure è possibile configurare un nuovo database SQL o SQL Istanza gestita per il runtime di integrazione.

## <a name="custom-setup-issues"></a>Problemi di installazione personalizzati

L'installazione personalizzata offre un'interfaccia per l'aggiunta di passaggi di installazione durante il provisioning o la riconfigurazione di SSIS IR. Per altre informazioni, vedere [Personalizzare l'installazione per Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Assicurarsi che il contenitore includa solo i file di installazione personalizzata necessari; tutti gli altri file nel contenitore verranno scaricati nel nodo di lavoro di SSIS IR. È consigliabile testare lo script di installazione personalizzata in un computer locale per correggere eventuali errori di esecuzione prima di eseguire lo script in SSIS IR.

Il contenitore dello script di installazione personalizzata verrà controllato mentre è in esecuzione il runtime di integrazione, perché SSIS IR viene aggiornato regolarmente. Questo aggiornamento richiede l'accesso al contenitore per scaricare lo script di installazione personalizzata e installarlo di nuovo. Il processo controlla anche se il contenitore è accessibile e se il file main.cmd esiste.

Per eventuali errori che coinvolgono la configurazione personalizzata, verrà visualizzato un codice di errore CustomSetupScriptFailure con codice secondario come CustomSetupScriptBlobContainerInaccessible o CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Questo errore indica che SSIS IR non può accedere al contenitore BLOB di Azure per l'installazione personalizzata. Assicurarsi che l'URI SAS del contenitore sia raggiungibile e non sia scaduto.

Se è in esecuzione, arrestare il runtime di esecuzione, riconfigurarlo con il nuovo URI SAS del contenitore dell'installazione personalizzata, quindi riavviare il runtime di esecuzione.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Questo errore indica che SSIS IR non è in grado di trovare uno script di installazione personalizzata (main.cmd) nel contenitore BLOB. Verificare il punto fondamentale per l'installazione personalizzata, cioè che main.cmd esista nel contenitore.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Questo errore indica che l'esecuzione dello script di installazione personalizzata (main.cmd) ha avuto esito negativo. Provare a eseguire prima lo script nel computer locale oppure controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Questo errore indica un timeout di esecuzione dello script di installazione personalizzata. Assicurarsi che lo script possa essere eseguito in modo invisibile all'utente e che non siano necessari input interattivi e assicurarsi anche che il contenitore BLOB contenga solo i file di installazione personalizzati necessari. È consigliabile testare prima lo script sul computer locale. È inoltre necessario controllare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB. Il periodo massimo per l'installazione personalizzata è di 45 minuti prima del timeout, mentre il periodo massimo include il tempo per scaricare tutti i file dal contenitore e installarli in SSIS IR. Se è necessario un periodo più lungo, generare un ticket di supporto.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Questo errore indica che il tentativo di caricare i log di esecuzione dell'installazione personalizzata nel contenitore BLOB ha avuto esito negativo. Questo problema si verifica perché SSIS IR non dispone delle autorizzazioni di scrittura per il contenitore BLOB o a causa di problemi di archiviazione o di rete. Se l'installazione personalizzata ha esito positivo, l'errore non influirà su alcuna funzione di SSIS, ma i log saranno mancanti. Se l'installazione personalizzata ha esito negativo con un altro errore e il log non viene caricato, l'errore verrà segnalato per primo, in modo che il log possa essere caricato per l'analisi. Inoltre, dopo la risoluzione del problema, verranno segnalati eventuali problemi più specifici. Se il problema non viene risolto dopo un nuovo tentativo, contattare il team di supporto di Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configurazione della rete virtuale

Quando viene aggiunto alla rete virtuale di Azure, SSIS IR usa la rete virtuale prevista dalla sottoscrizione dell'utente. Per altre informazioni, vedere [Aggiungere Azure-SSIS Integration Runtime a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
Dopo l'avvio del runtime di integrazione SSIS, se si verificano problemi di connessione di rete, è possibile provare a usare [lo strumento di connettività di diagnostica](ssis-integration-runtime-diagnose-connectivity-faq.md) per diagnosticare il problema.
Quando si verifica un problema relativo alla rete virtuale, viene visualizzato uno degli errori seguenti.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Questo errore può verificarsi per vari motivi. Per risolverlo, vedere le sezioni [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Accesso negato

Questo tipo di errore potrebbe essere simile al seguente: "SubnetId non è abilitato per l'account corrente. Il provider di risorse Microsoft.Batch non è registrato nella stessa sottoscrizione di VNet. "

Questi dettagli indicano che Azure Batch non può accedere alla rete virtuale. Registrare il provider di risorse Microsoft.Batch nella stessa sottoscrizione della rete virtuale.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Questo tipo di errore potrebbe essere simile a uno dei seguenti: 

- "Il VNet specificato non esiste o il servizio batch non dispone dell'accesso."
- "La subnet specificata xxx non esiste".

Questi errori indicano che la rete virtuale non esiste, che il servizio Azure Batch non può accedervi o che la subnet specificata non esiste. Verificare che la rete virtuale e la subnet esistano e che Azure Batch possa accedervi.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Questo tipo di messaggio di errore può essere simile al seguente: "Impossibile eseguire il provisioning Integration Runtime in VNet. Se sono configurate le impostazioni server DNS o NSG, verificare che il server DNS sia accessibile e che NSG sia configurato correttamente.

In questa situazione è probabilmente presente una configurazione personalizzata delle impostazioni del server DNS o del gruppo di sicurezza di rete, che impedisce la risoluzione o l'accesso al nome del server di Azure richiesto da SSIS IR. Per altre informazioni, vedere [Configurazione della rete virtuale di SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR verrà aggiornato automaticamente a intervalli regolari. Durante l'aggiornamento viene creato un nuovo pool di Azure Batch e viene eliminato quello precedente. Inoltre, le risorse correlate alla rete virtuale per il pool precedente vengono eliminate e nella sottoscrizione vengono create nuove risorse correlate alla rete virtuale. Questo errore indica che l'eliminazione delle risorse correlate alla rete virtuale per il pool precedente ha avuto esito negativo a causa di un blocco di eliminazione a livello di sottoscrizione o di gruppo di risorse. Poiché il cliente controlla e imposta il blocco di eliminazione, in questo caso è necessario rimuoverlo.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se il provisioning di SSIS IR ha esito negativo, vengono eliminate tutte le risorse che sono state create. Tuttavia, se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse, le risorse di rete virtuale non vengono eliminate come previsto. Per correggere l'errore, rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando viene arrestato SSIS IR, vengono eliminate tutte le risorse correlate alla rete virtuale. L'eliminazione può tuttavia avere esito negativo se è presente un blocco di eliminazione delle risorse a livello di sottoscrizione o di gruppo di risorse. Anche in questo caso il cliente controlla e imposta il blocco di eliminazione. Di conseguenza, deve rimuoverlo e arrestare di nuovo SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

Questo errore si verifica quando il runtime di integrazione è in esecuzione; significa che il runtime di integrazione è diventato non integro. Questo errore è sempre causato da una modifica nella configurazione del server DNS o del gruppo di sicurezza di rete che impedisce a SSIS IR di connettersi al servizio necessario. Poiché la configurazione del server DNS e del gruppo di sicurezza di rete è controllata dal cliente, il cliente deve risolvere i problemi di blocco da parte sua. Per altre informazioni, vedere [Configurazione della rete virtuale di SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se i problemi persistono, contattare il team di supporto di Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configurazione degli indirizzi IP pubblici statici

Quando si aggiunge il Azure-SSIS IR alla rete virtuale di Azure, è anche possibile portare gli indirizzi IP pubblici statici per il runtime di integrazione in modo che il runtime di integrazione possa accedere alle origini dati che limitano l'accesso a indirizzi IP specifici. Per altre informazioni, vedere [Aggiungere Azure-SSIS Integration Runtime a una rete virtuale](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Oltre ai problemi relativi alla rete virtuale, è anche possibile che si verifichi un problema relativo agli indirizzi IP pubblici statici. Per informazioni, vedere gli errori seguenti.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Questo errore può verificarsi per diversi motivi quando si avvia la Azure-SSIS IR:

| Messaggio di errore | Soluzione|
|:--- |:--- |
| L'indirizzo IP pubblico statico specificato è già in uso. specificare due inutilizzati per la Azure-SSIS Integration Runtime. | È necessario selezionare due indirizzi IP pubblici statici non usati o rimuovere i riferimenti correnti all'indirizzo IP pubblico specificato, quindi riavviare il Azure-SSIS IR. |
| Per l'indirizzo IP pubblico statico specificato non esiste alcun nome DNS. specificare due nomi DNS per la Azure-SSIS Integration Runtime. | È possibile configurare il nome DNS dell'indirizzo IP pubblico in portale di Azure, come illustrato nella figura seguente. I passaggi specifici sono i seguenti: (1) Apri portale di Azure e vai alla pagina delle risorse di questo indirizzo IP pubblico; (2) selezionare la sezione di **configurazione** e impostare il nome DNS, quindi fare clic sul pulsante **Salva** . (3) riavviare il Azure-SSIS IR. |
| Gli indirizzi IP pubblici statici e VNet specificati per il Azure-SSIS Integration Runtime devono trovarsi nella stessa posizione. | In base ai requisiti della rete di Azure, l'indirizzo IP pubblico statico e la rete virtuale devono trovarsi nello stesso percorso e nella stessa sottoscrizione. Fornire due indirizzi IP pubblici statici validi e riavviare il Azure-SSIS IR. |
| L'indirizzo IP pubblico statico specificato è uno di base, fornire due standard per la Azure-SSIS Integration Runtime. | Per informazioni, vedere [SKU dell'indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) . |

![Runtime di integrazione Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se Azure-SSIS IR provisioning ha esito negativo, vengono eliminate tutte le risorse create. Tuttavia, se è presente un blocco di eliminazione delle risorse nella sottoscrizione o nel gruppo di risorse, che contiene il livello di indirizzo IP pubblico statico, le risorse di rete non vengono eliminate come previsto. Per correggere l'errore, rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Quando si arresta Azure-SSIS IR, verranno eliminate tutte le risorse di rete create nel gruppo di risorse che contiene l'indirizzo IP pubblico. L'eliminazione può tuttavia avere esito negativo se è presente un blocco di eliminazione delle risorse nella sottoscrizione o nel gruppo di risorse, che contiene il livello di indirizzo IP pubblico statico. Rimuovere il blocco di eliminazione e riavviare il runtime di integrazione.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR viene automaticamente aggiornato a intervalli regolari. I nuovi nodi IR vengono creati durante l'aggiornamento e i nodi precedenti verranno eliminati. Inoltre, le risorse di rete create (ad esempio, il servizio di bilanciamento del carico e il gruppo di sicurezza di rete) per i nodi precedenti vengono eliminate e le nuove risorse di rete vengono create nella sottoscrizione. Questo errore indica che l'eliminazione delle risorse di rete per i nodi precedenti non è riuscita a causa di un blocco di eliminazione nella sottoscrizione o nel gruppo di risorse, che contiene il livello di indirizzo IP pubblico statico. Rimuovere il blocco di eliminazione in modo da poter pulire i nodi precedenti e rilasciare l'indirizzo IP pubblico statico per i nodi precedenti. In caso contrario, non sarà possibile rilasciare l'indirizzo IP pubblico statico e non sarà più possibile aggiornare ulteriormente il runtime di integrazione.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Quando si vogliono importare indirizzi IP pubblici statici, è necessario fornire due indirizzi IP pubblici. Uno di essi verrà usato per creare i nodi IR immediatamente e ne verrà usato un altro durante l'aggiornamento del runtime di integrazione. Questo errore può verificarsi quando l'altro indirizzo IP pubblico non è utilizzabile durante l'aggiornamento. Per le possibili cause, fare riferimento a  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) .