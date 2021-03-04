---
title: Domande frequenti sull'appliance Azure Migrate
description: Risposte alle domande più comuni sull'appliance Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 5a050d9aab9e8665c6048391488e57c9b4af10a5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043066"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni sull'appliance Azure Migrate. Per altre domande, vedere le risorse seguenti:

- [Domande generali](resources-faq.md) su Azure migrate
- Domande su [individuazione, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)
- Domande sulla [migrazione del server](common-questions-server-migration.md)
- Risposte alle domande nel [forum Azure migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Che cos'è la Azure Migrate Appliance?

Il dispositivo Azure Migrate è un dispositivo leggero che lo strumento Azure Migrate: server Assessment USA per individuare e valutare i server fisici o virtuali da locale o da qualsiasi cloud. Lo strumento di migrazione Azure Migrate: Server usa anche l'appliance per la migrazione senza agenti di macchine virtuali VMware locali.

Di seguito sono riportate altre informazioni sull'appliance Azure Migrate:

- Il dispositivo viene distribuito in locale come macchina virtuale o computer fisico.
- L'appliance individua i computer locali e invia continuamente i dati sulle prestazioni e i metadati del computer a Azure Migrate.
- L'individuazione dell'appliance è senza agente. Non viene installato nulla nei computer individuati.

[Altre](migrate-appliance.md) informazioni sull'appliance.

## <a name="how-can-i-deploy-the-appliance"></a>Come si distribuisce l'appliance?

L'appliance può essere distribuita usando due metodi:

- Il dispositivo può essere distribuito usando un modello per i server in esecuzione nell'ambiente VMware o Hyper-V ([modello OVA per VMware](how-to-set-up-appliance-vmware.md) o [VHD per Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se non si vuole usare un modello, è possibile distribuire l'appliance per l'ambiente VMware o Hyper-V usando uno [script del programma di installazione di PowerShell](deploy-appliance-script.md).
- In Azure per enti pubblici è necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](deploy-appliance-script-government.md).
- Per i server fisici o virtualizzati in locale o in qualsiasi altro cloud, è sempre necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>In che modo l'appliance si connette ad Azure?

L'appliance può connettersi tramite Internet o usando Azure ExpressRoute. 

- Assicurarsi che l'appliance possa connettersi a questi [URL di Azure](./migrate-appliance.md#url-access). 
- È possibile usare ExpressRoute con il peering Microsoft. Il peering pubblico è deprecato e non è disponibile per i nuovi circuiti ExpressRoute.
- Il peering privato non è supportato.


## <a name="does-appliance-analysis-affect-performance"></a>L'analisi degli appliance influisce sulle prestazioni?

L'appliance Azure Migrate profilare i computer locali in modo continuo per misurare i dati sulle prestazioni. Questa profilatura non ha alcun effetto sulle prestazioni delle macchine profilate.

## <a name="can-i-harden-the-appliance-vm"></a>È possibile rafforzare la macchina virtuale dell'appliance?

Quando si usa il modello scaricato per creare la macchina virtuale dell'appliance, è possibile aggiungere componenti (ad esempio, antivirus) al modello se si lasciano invariate le regole di comunicazione e del firewall necessarie per l'appliance Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Quale connettività di rete è necessaria?

Il dispositivo deve accedere agli URL di Azure. [Esaminare](migrate-appliance.md#url-access) l'elenco di URL.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Vedere gli articoli seguenti per informazioni sui dati raccolti dal Azure Migrate Appliance sulle macchine virtuali:

- **VM VMware**: [esaminare](migrate-appliance.md#collected-data---vmware) i dati raccolti.
- **VM Hyper-V**: [esaminare](migrate-appliance.md#collected-data---hyper-v) i dati raccolti.
- **Server fisici o virtuali**:[esaminare](migrate-appliance.md#collected-data---physical) i dati raccolti.

## <a name="how-is-data-stored"></a>Come vengono archiviati i dati?

I dati raccolti dal dispositivo Azure Migrate vengono archiviati nella località di Azure in cui è stato creato il progetto Azure Migrate.

Di seguito sono riportate altre informazioni sulla modalità di archiviazione dei dati:

- I dati raccolti vengono archiviati in modo sicuro in CosmosDB in una sottoscrizione Microsoft. I dati vengono eliminati quando si elimina il progetto Azure Migrate. Lo spazio di archiviazione viene gestito da Azure Migrate. Non è possibile scegliere in modo specifico un account di archiviazione per i dati raccolti.
- Se si usa la [visualizzazione delle dipendenze](concepts-dependency-visualization.md), i dati raccolti vengono archiviati in un'area di lavoro di Azure log Analytics creata nella sottoscrizione di Azure. I dati vengono eliminati quando si elimina l'area di lavoro Log Analytics nella sottoscrizione. 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quanti dati vengono caricati durante la profilatura continua?

Il volume dei dati inviati a Azure Migrate dipende da più parametri. Ad esempio, un progetto di Azure Migrate con 10 computer (ognuno con un disco e una NIC) Invia circa 50 MB di dati al giorno. Questo valore è approssimativo. il valore effettivo varia a seconda del numero di punti dati per i dischi e le schede di rete. Se il numero di computer, dischi o schede di rete aumenta, l'aumento dei dati inviati è non lineare.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>I dati sono crittografati inattivi e in transito?

Sì, per entrambi:

- I metadati vengono inviati in modo sicuro al servizio Azure Migrate tramite Internet tramite HTTPS.
- I metadati vengono archiviati in un database di [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e nell' [archiviazione BLOB di Azure](../storage/common/storage-service-encryption.md) in una sottoscrizione Microsoft. I metadati vengono crittografati a riposo per l'archiviazione.
- I dati per l'analisi delle dipendenze sono inoltre crittografati in transito (tramite Secure HTTPS). Viene archiviato in un'area di lavoro Log Analytics nella sottoscrizione. I dati sono crittografati per l'analisi delle dipendenze.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>In che modo l'appliance si connette a server vCenter?

Questi passaggi descrivono il modo in cui l'appliance si connette a server VMware vCenter:

1. L'appliance si connette a server vCenter (porta 443) usando le credenziali specificate durante la configurazione dell'appliance.
2. L'appliance usa VMware PowerCLI per eseguire query server vCenter per raccogliere i metadati sulle macchine virtuali gestite da server vCenter.
3. Il dispositivo raccoglie i dati di configurazione sulle VM (core, memoria, dischi, schede di rete) e la cronologia delle prestazioni di ogni macchina virtuale per l'ultimo mese.
4. I metadati raccolti vengono inviati al Azure Migrate: strumento di valutazione del server (tramite Internet tramite HTTPS) per la valutazione.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Il dispositivo Azure Migrate può connettersi a più server vCenter?

No. Esiste un mapping uno-a-uno tra un [Azure migrate Appliance](migrate-appliance.md) e server vCenter. Per individuare le macchine virtuali in più istanze di server vCenter, è necessario distribuire più appliance. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Un progetto di Azure Migrate può avere più appliance?

Un progetto può avere più appliance registrate. Tuttavia, un dispositivo può essere registrato solo con un progetto.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Il dispositivo di Azure Migrate/appliance di replica può connettersi allo stesso vCenter?

Sì. È possibile aggiungere il dispositivo Azure Migrate (usato per la valutazione e la migrazione VMware senza agente) e l'appliance di replica (usato per la migrazione basata su agenti delle VM VMware) allo stesso server vCenter. Assicurarsi però di non configurare entrambi i dispositivi nella stessa macchina virtuale e che non sono attualmente supportati.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quante VM o server è possibile individuare con un'appliance?

È possibile individuare fino a 10.000 VM VMware, fino a 5.000 macchine virtuali Hyper-V e fino a 1000 server fisici con un'unica appliance. Se si dispone di più computer nell'ambiente locale, vedere la pagina relativa alla [scalabilità di una valutazione di Hyper-V](scale-hyper-v-assessment.md), alla [scalabilità di una valutazione VMware](scale-vmware-assessment.md)e alla [scalabilità di una valutazione del server fisico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>È possibile eliminare un appliance?

Attualmente, l'eliminazione di un appliance dal progetto non è supportata.

L'unico modo per eliminare l'appliance consiste nell'eliminare il gruppo di risorse che contiene il progetto di Azure Migrate associato all'appliance.

Tuttavia, l'eliminazione del gruppo di risorse Elimina anche altre appliance registrate, l'inventario individuato, le valutazioni e tutti gli altri componenti di Azure nel gruppo di risorse associato al progetto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>È possibile usare l'appliance con una sottoscrizione o un progetto diverso?

Per usare il dispositivo con una sottoscrizione o un progetto diverso, è necessario riconfigurare l'appliance esistente eseguendo lo script del programma di installazione di PowerShell per lo scenario specifico (VMware/Hyper-V/fisico) nel computer dell'appliance. Tramite lo script vengono puliti i componenti e le impostazioni dell'appliance esistente per distribuire una nuova appliance. Assicurarsi di cancellare la cache del browser prima di iniziare a usare la gestione configurazione Appliance appena distribuita.

Non è inoltre possibile riutilizzare una chiave di progetto Azure Migrate esistente in un'appliance riconfigurata. Assicurarsi di generare una nuova chiave dal progetto o dalla sottoscrizione desiderata per completare la registrazione dell'appliance.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>È possibile configurare l'appliance in una macchina virtuale di Azure?

No. Attualmente questa opzione non è supportata.

## <a name="can-i-discover-on-an-esxi-host"></a>È possibile individuare in un host ESXi?

No. Per individuare le macchine virtuali VMware, è necessario avere server vCenter.

## <a name="how-do-i-update-the-appliance"></a>Ricerca per categorie aggiornare l'appliance?

Per impostazione predefinita, l'appliance e gli agenti installati vengono aggiornati automaticamente. Il dispositivo verifica la disponibilità di aggiornamenti ogni 24 ore. Gli aggiornamenti che non riescono vengono ripetuti. 

Solo gli agenti Appliance e appliance vengono aggiornati da questi aggiornamenti automatici. Il sistema operativo non viene aggiornato da Azure Migrate aggiornamenti automatici. Utilizzare gli aggiornamenti di Windows per rendere aggiornato il sistema operativo.

## <a name="can-i-check-agent-health"></a>È possibile controllare l'integrità dell'agente?

Sì. Nel portale passare alla pagina **integrità agente** per lo strumento di migrazione Azure migrate: server Assessment o Azure migrate: Server. Qui è possibile controllare lo stato della connessione tra Azure e gli agenti di individuazione e valutazione nell'appliance.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>È possibile aggiungere più credenziali del server nell'appliance VMware?

Sì, sono ora supportate più credenziali server per eseguire l'inventario software (individuazione di applicazioni installate), l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database. [Altre informazioni](tutorial-discover-vmware.md#provide-server-credentials) su come fornire le credenziali in Gestione configurazione Appliance.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Quali tipi di credenziali del server è possibile aggiungere nell'appliance VMware?
È possibile specificare le credenziali di autenticazione Domain/Windows (non di dominio)/Linux (non di dominio)/SQL Server in Gestione configurazione Appliance. [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Quali tipi di proprietà di connessione SQL Server sono supportati da Azure Migrate per l'individuazione SQL?
Azure Migrate crittografa la comunicazione tra le istanze Azure Migrate Appliance e SQL Server di origine (con la proprietà Crittografa connessione impostata su TRUE). Queste connessioni vengono crittografate con [TrustServerCertificate](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (impostato su true); il livello trasporto userà SSL per crittografare il canale e ignorare la catena di certificati per convalidare l'attendibilità. Il server appliance deve essere configurato in modo da [considerare attendibile l'autorità radice del certificato](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Se al momento dell'avvio non è stato eseguito il provisioning di alcun certificato sul server, SQL Server genera un certificato autofirmato che viene utilizzato per crittografare i pacchetti di accesso. [Altre informazioni](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)


## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).