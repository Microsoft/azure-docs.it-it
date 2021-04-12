---
title: Migrare i dati da un server Netezza locale ad Azure
description: Usare Azure Data Factory per eseguire la migrazione dei dati da un server Netezza locale ad Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367776"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Usare Azure Data Factory per migrare i dati da un server Netezza locale ad Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory offre un meccanismo efficiente, affidabile ed economico per eseguire la migrazione dei dati su larga scala da un server Netezza locale all'account di archiviazione di Azure o al database di analisi delle sinapsi di Azure. 

In questo articolo vengono fornite le seguenti informazioni per i data engineer e gli sviluppatori:

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura di alto livello della soluzione 
> * Procedure consigliate dell'implementazione  

## <a name="performance"></a>Prestazioni

Azure Data Factory offre un'architettura senza server che consente il parallelismo a vari livelli. Se si è uno sviluppatore, questo significa che è possibile compilare pipeline per usare completamente la larghezza di banda di rete e di database per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.

![Diagramma prestazioni](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Il diagramma precedente può essere interpretato nel modo seguente:

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili. Quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUs](./copy-activity-performance.md#data-integration-units) per ogni attività di copia in modo senza server. Con un runtime di integrazione self-hosted (runtime di integrazione self-hosted), è possibile scalare manualmente il computer o scalare orizzontalmente in più computer ([fino a quattro nodi](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e una singola attività di copia distribuisce la partizione tra tutti i nodi. 

- Una singola attività di copia legge e scrive nell'archivio dati usando più thread. 

- Azure Data Factory flusso di controllo può avviare più attività di copia in parallelo. Ad esempio, può avviarle usando un [ciclo for each](./control-flow-for-each-activity.md). 

Per ulteriori informazioni, vedere [Guida alle prestazioni e alla scalabilità dell'attività di copia](./copy-activity-performance.md).

## <a name="resilience"></a>Resilienza

All'interno di una singola esecuzione dell'attività di copia, Azure Data Factory dispone di un meccanismo di ripetizione dei tentativi incorporato, che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante.

Con Azure Data Factory attività di copia, quando si copiano dati tra gli archivi dati di origine e sink, sono disponibili due modi per gestire le righe incompatibili. È possibile interrompere l'attività di copia o continuare a copiare il resto dei dati ignorando le righe di dati incompatibili. Per apprendere la causa dell'errore, è inoltre possibile registrare le righe incompatibili nell'archivio BLOB di Azure o in Azure Data Lake Store, correggere i dati nell'origine dati e ripetere l'attività di copia.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, Azure Data Factory trasferisce i dati dal server Netezza locale a un account di archiviazione di Azure o a un database di analisi delle sinapsi di Azure tramite una connessione crittografata tramite Hypertext Transfer Protocol Secure (HTTPS). Il protocollo HTTPS offre la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-middle.

In alternativa, se non si vuole trasferire i dati attraverso la rete Internet pubblica, è possibile ottenere una maggiore sicurezza trasferendo i dati tramite un collegamento di peering privato tramite Azure Express route. 

Nella sezione successiva viene illustrato come ottenere una maggiore sicurezza.

## <a name="solution-architecture"></a>Architettura della soluzione

In questa sezione vengono illustrati due modi per eseguire la migrazione dei dati.

### <a name="migrate-data-over-the-public-internet"></a>Migrare i dati attraverso la rete Internet pubblica

![Migrare i dati attraverso la rete Internet pubblica](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Il diagramma precedente può essere interpretato nel modo seguente:

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS tramite la rete Internet pubblica.

- Per realizzare questa architettura, è necessario installare Azure Data Factory Integration Runtime (self-hosted) in un computer Windows che si trova dietro un firewall aziendale. Verificare che il runtime di integrazione possa accedere direttamente al server Netezza. Per usare completamente la rete e la larghezza di banda di archiviazione dei dati per copiare i dati, è possibile scalare manualmente il computer o aumentare le prestazioni in più computer.

- Utilizzando questa architettura, è possibile migrare sia i dati di snapshot iniziali che i dati Delta.

### <a name="migrate-data-over-a-private-network"></a>Migrare i dati in una rete privata 

![Migrare i dati in una rete privata](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Il diagramma precedente può essere interpretato nel modo seguente:

- In questa architettura, si esegue la migrazione dei dati tramite un collegamento di peering privato tramite Azure Express Route e i dati non passano mai attraverso la rete Internet pubblica. 

- Per realizzare questa architettura, è necessario installare Azure Data Factory Integration Runtime (self-hosted) in una macchina virtuale (VM) Windows nella rete virtuale di Azure. Per usare completamente la rete e la larghezza di banda di archiviazione dei dati per copiare i dati, è possibile scalare manualmente la macchina virtuale o aumentare le prestazioni in più macchine virtuali.

- Utilizzando questa architettura, è possibile migrare sia i dati di snapshot iniziali che i dati Delta.

## <a name="implement-best-practices"></a>Implementare le procedure consigliate 

### <a name="manage-authentication-and-credentials"></a>Gestire l'autenticazione e le credenziali 

- Per eseguire l'autenticazione a Netezza, è possibile usare [l'autenticazione ODBC tramite la stringa di connessione](./connector-netezza.md#linked-service-properties). 

- Per eseguire l'autenticazione nell'archivio BLOB di Azure: 

   - Si consiglia vivamente [di usare identità gestite per le risorse di Azure](./connector-azure-blob-storage.md#managed-identity). Basato su un'identità Azure Data Factory gestita automaticamente in Azure Active Directory (Azure AD), le identità gestite consentono di configurare le pipeline senza dover fornire le credenziali nella definizione del servizio collegato.  

   - In alternativa, è possibile eseguire l'autenticazione nell'archiviazione BLOB di Azure usando un' [entità servizio](./connector-azure-blob-storage.md#service-principal-authentication), una [firma di accesso condiviso](./connector-azure-blob-storage.md#shared-access-signature-authentication)o una [chiave dell'account di archiviazione](./connector-azure-blob-storage.md#account-key-authentication). 

- Per eseguire l'autenticazione a Azure Data Lake Storage Gen2: 

   - Si consiglia vivamente [di usare identità gestite per le risorse di Azure](./connector-azure-data-lake-storage.md#managed-identity).
   
   - È anche possibile usare un' [entità servizio](./connector-azure-data-lake-storage.md#service-principal-authentication) o una [chiave dell'account di archiviazione](./connector-azure-data-lake-storage.md#account-key-authentication). 

- Per eseguire l'autenticazione in Azure sinapsi Analytics:

   - Si consiglia vivamente [di usare identità gestite per le risorse di Azure](./connector-azure-sql-data-warehouse.md#managed-identity).
   
   - È anche possibile usare l' [entità servizio](./connector-azure-sql-data-warehouse.md#service-principal-authentication) o [l'autenticazione SQL](./connector-azure-sql-data-warehouse.md#sql-authentication).

- Quando non si usano identità gestite per le risorse di Azure, è consigliabile [archiviare le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md) per semplificare la gestione e la rotazione centralizzate delle chiavi senza dover modificare Azure Data Factory servizi collegati. Questa è anche una delle [procedure consigliate per CI/CD](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Eseguire la migrazione dei dati dello snapshot iniziale 

Per le tabelle di piccole dimensioni (ovvero le tabelle con un volume inferiore a 100 GB o di cui è possibile eseguire la migrazione in Azure entro due ore), è possibile fare in modo che ogni processo di copia carichi i dati per ogni tabella. Per una maggiore velocità effettiva, è possibile eseguire più processi di copia Azure Data Factory per caricare tabelle separate simultaneamente. 

All'interno di ogni processo di copia, per eseguire query parallele e copiare dati in base alle partizioni, è anche possibile raggiungere un certo livello di parallelismo usando l' [ `parallelCopies` impostazione della proprietà](./copy-activity-performance.md#parallel-copy) con una delle seguenti opzioni di partizione dei dati:

- Per ottenere una maggiore efficienza, si consiglia di iniziare da una sezione di dati.  Verificare che il valore nell' `parallelCopies` impostazione sia inferiore al numero totale di partizioni di sezioni di dati nella tabella nel server Netezza.  

- Se il volume di ogni partizione di sezione dati è ancora grande (ad esempio, 10 GB o superiore), si consiglia di passare a una partizione a intervalli dinamici. Questa opzione offre una maggiore flessibilità per definire il numero di partizioni e il volume di ogni partizione in base alla colonna di partizione, al limite superiore e al limite inferiore.

Per le tabelle di dimensioni maggiori (ovvero le tabelle con un volume di 100 GB o superiore o che *non possono* essere migrate in Azure entro due ore), è consigliabile partizionare i dati in base a una query personalizzata e quindi fare in modo che ogni copia-processo copi una partizione alla volta. Per una migliore velocità effettiva, è possibile eseguire più processi di copia Azure Data Factory simultaneamente. Per ogni destinazione del processo di copia del caricamento di una partizione tramite query personalizzata, è possibile aumentare la velocità effettiva abilitando il parallelismo tramite una sezione di dati o un intervallo dinamico. 

Se un processo di copia ha esito negativo a causa di un problema temporaneo di rete o archivio dati, è possibile rieseguire il processo di copia non riuscito per ricaricare la partizione specifica dalla tabella. Non sono interessati altri processi di copia che caricano altre partizioni.

Quando si caricano i dati in un database di analisi delle sinapsi di Azure, si consiglia di abilitare la polibase nel processo di copia con archiviazione BLOB di Azure come gestione temporanea.

### <a name="migrate-delta-data"></a>Eseguire la migrazione dei dati Delta 

Per identificare le righe nuove o aggiornate della tabella, utilizzare una colonna timestamp o una chiave di incremento nello schema. È quindi possibile archiviare il valore più recente come limite massimo in una tabella esterna e quindi usarlo per filtrare i dati Delta al successivo caricamento dei dati. 

Ogni tabella può utilizzare una colonna di filigrana diversa per identificare le righe nuove o aggiornate. Si consiglia di creare una tabella di controllo esterna. Nella tabella ogni riga rappresenta una tabella nel server Netezza con il nome della colonna filigrana e il valore limite massimo specifici. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configurare un runtime di integrazione self-hosted

Se si esegue la migrazione dei dati dal server Netezza in Azure, indipendentemente dal fatto che il server sia locale dietro il firewall aziendale o all'interno di un ambiente di rete virtuale, è necessario installare un runtime di integrazione self-hosted in un computer Windows o una macchina virtuale, ovvero il motore usato per spostare i dati. Quando si installa il runtime di integrazione self-hosted, si consiglia l'approccio seguente:

- Per ogni computer o macchina virtuale Windows, iniziare con una configurazione di 32 vCPU e 128 GB di memoria. È possibile continuare a monitorare l'utilizzo della CPU e della memoria del computer IR durante la migrazione dei dati per verificare se è necessario aumentare ulteriormente il computer per ottenere prestazioni migliori o ridurre il computer per ridurre i costi.

- È anche possibile aumentare la scalabilità orizzontale associando fino a quattro nodi con un singolo runtime di integrazione self-hosted. Un processo di copia singolo eseguito su un runtime di integrazione self-hosted applica automaticamente tutti i nodi della macchina virtuale per copiare i dati in parallelo. Per la disponibilità elevata, iniziare con quattro nodi VM per evitare un singolo punto di errore durante la migrazione dei dati.

### <a name="limit-your-partitions"></a>Limitare le partizioni

Come procedura consigliata, eseguire un modello di verifica delle prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare le dimensioni della partizione appropriate per ogni attività di copia. Si consiglia di caricare ogni partizione in Azure entro due ore.  

Per copiare una tabella, iniziare con una singola attività di copia con un singolo computer IR indipendente. Aumentare gradualmente l' `parallelCopies` impostazione in base al numero di partizioni di sezioni di dati nella tabella. Verificare se l'intera tabella può essere caricata in Azure entro due ore, in base alla velocità effettiva risultante dal processo di copia. 

Se non può essere caricato in Azure entro due ore e la capacità del nodo IR indipendente e l'archivio dati non sono completamente usati, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere il limite della rete o il limite di larghezza di banda degli archivi dati. 

È possibile monitorare l'utilizzo della CPU e della memoria nel computer IR indipendente ed essere pronti per la scalabilità verticale della macchina o per la scalabilità orizzontale in più computer quando si nota che la CPU e la memoria sono completamente utilizzate. 

Quando si verificano errori di limitazione, come indicato dall'attività di copia Azure Data Factory, ridurre la concorrenza o l' `parallelCopies` impostazione in Azure Data Factory oppure provare ad aumentare la larghezza di banda o le operazioni di i/o al secondo (IOPS) per la rete e gli archivi dati. 


### <a name="estimate-your-pricing"></a>Stima dei prezzi 

Si consideri la pipeline seguente, costruita per la migrazione dei dati dal server Netezza locale a un database di analisi delle sinapsi di Azure:

![Pipeline dei prezzi](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Si supponga che le istruzioni seguenti siano vere: 

- Il volume totale dei dati è 50 terabyte (TB). 

- Stiamo migrando i dati usando l'architettura della prima soluzione (il server Netezza è in locale, dietro il firewall).

- Il volume da 50 TB è diviso in 500 partizioni e ogni attività di copia sposta una partizione.

- Ogni attività di copia è configurata con un runtime di integrazione self-hosted su quattro computer e ottiene una velocità effettiva di 20 megabyte al secondo (MBps). (All'interno dell'attività `parallelCopies` di copia, è impostato su 4 e ogni thread per caricare i dati dalla tabella ottiene una velocità effettiva di 5 Mbps).

- La concorrenza ForEach è impostata su 3 e la velocità effettiva aggregata è 60 MBps.

- Per completare la migrazione, in totale sono necessarie 243 ore.

In base ai presupposti precedenti, il prezzo stimato è il seguente: 

![Tabella dei prezzi](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> I prezzi indicati nella tabella precedente sono ipotetici. I prezzi effettivi variano in base alla velocità effettiva dell'ambiente. Il prezzo per il computer Windows (con il runtime di integrazione self-hosted installato) non è incluso. 

### <a name="additional-references"></a>Altri riferimenti

Per ulteriori informazioni, vedere gli articoli e le guide seguenti:

- [Migrare i dati da un database relazionale data warehouse locale ad Azure usando Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Connettore Netezza](./connector-netezza.md)
- [Connettore ODBC](./connector-odbc.md)
- [Connettore di archiviazione BLOB di Azure](./connector-azure-blob-storage.md)
- [Connettore di Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md)
- [Connettore Azure sinapsi Analytics](./connector-azure-sql-data-warehouse.md)
- [Guida alle prestazioni delle attività di copia e all'ottimizzazione](./copy-activity-performance.md)
- [Creare e configurare un runtime di integrazione self-hosted](./create-self-hosted-integration-runtime.md)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hosted](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Considerazioni relative alla sicurezza per lo spostamento dei dati](./data-movement-security-considerations.md)
- [Archiviare le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md)
- [Copiare i dati in modo incrementale da una tabella](./tutorial-incremental-copy-portal.md)
- [Copiare i dati in modo incrementale da più tabelle](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Pagina dei prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passaggi successivi

- [Copiare i file da più contenitori usando Azure Data Factory](solution-template-copy-files-multiple-containers.md)