---
title: Considerazioni relative alla sicurezza
description: Descrive l'infrastruttura di sicurezza di base usata dai servizi di spostamento dei dati in Azure Data Factory per proteggere i dati.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416476"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considerazioni sulla sicurezza dello spostamento dei dati in Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
>
> * [Versione 1](v1/data-factory-data-movement-security-considerations.md)
> * [Versione corrente](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo descrive l'infrastruttura di sicurezza di base usata dai servizi di spostamento dei dati in Azure Data Factory per proteggere i dati. Le risorse di gestione di Data Factory si basano sull'infrastruttura di sicurezza di Azure e ricorrono a tutte le misure di sicurezza offerte da Azure.

In una soluzione Data Factory si creano una o più [pipeline](concepts-pipelines-activities.md)di dati. Una pipeline è un raggruppamento logico di attività che insieme eseguono un compito. Queste pipeline si trovano nell'area in cui è stata creata la data factory. 

Sebbene Data Factory sia disponibile solo in alcune regioni, il servizio di spostamento dati è [disponibile a livello globale](concepts-integration-runtime.md#integration-runtime-location) per garantire la conformità dei dati, l'efficienza e costi ridotti per il trasferimento di dati in uscita dalla rete. 

Azure Data Factory non archivia i dati a eccezione delle credenziali del servizio collegato per gli archivi di dati cloud, che vengono crittografate usando i certificati. Con Data Factory, è possibile creare flussi di lavoro basati sui dati per orchestrare lo spostamento di dati tra [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) e l'elaborazione di dati usando i [servizi di calcolo](compute-linked-services.md) in altre aree o in un ambiente locale. È anche possibile monitorare e gestire i flussi di lavoro usando SDK e Monitoraggio di Azure.

Data Factory è stato certificato per:

| **[Certificazione CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Se si è interessati alla conformità di Azure e alle modalità di protezione dell'infrastruttura da parte di Azure, visitare [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). Per un elenco aggiornato di tutte le offerte di conformità di Azure, consultare - https://aka.ms/AzureCompliance.

In questo articolo vengono prese in esame le considerazioni sulla sicurezza nei due scenari di spostamento di dati seguenti: 

- **Scenario cloud**: in questo scenario l'origine e la destinazione sono accessibili pubblicamente tramite Internet. Sono inclusi i servizi di archiviazione cloud gestiti come Archiviazione di Azure, Azure SQL Data Warehouse, Database SQL di Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, i servizi SaaS come Salesforce e i protocolli Web, ad esempio FTP e OData. Per un elenco completo delle origini dati supportate, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenario ibrido**: in questo scenario l'origine o la destinazione si trova dietro un firewall o in una rete aziendale locale oppure l'archivio dati si trova in una rete privata o in una rete virtuale (il più delle volte l'origine) e non è accessibile pubblicamente. Anche i server di database ospitati nelle macchine virtuali rientrano in questo scenario.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenari cloud

### <a name="securing-data-store-credentials"></a>Proteggere le credenziali dell'archivio dati

- **Archiviare credenziali crittografate i un archivio gestito di Azure Data Factory**. Data Factory consente di proteggere le credenziali dell'archivio dati crittografandole con i certificati gestiti da Microsoft. Questi certificati ruotano ogni due anni. In questo arco temporale è compreso il rinnovo del certificato e la migrazione delle credenziali. Per altre informazioni sulla sicurezza di Archiviazione di Azure, vedere [Panoramica sulla sicurezza di Archiviazione di Azure](../security/fundamentals/storage-overview.md).
- **Archiviare le credenziali in Archiviazione chiave**di Azure . È anche possibile archiviare la credenziale dell'archivio dati in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory recupera la credenziale durante l'esecuzione di un'attività. Per altre informazioni, vedere [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md) (Archiviare credenziali in Azure Key Vault).

### <a name="data-encryption-in-transit"></a>Crittografia di dati in transito
Se l'archivio dati cloud supporta HTTPS o TLS, tutti i trasferimenti di dati tra i servizi di spostamento dei dati in Data Factory e un archivio dati cloud avvengono tramite un canale TLS o HTTPS sicuro.

> [!NOTE]
> Tutte le connessioni al database SQL di Azure e ad Azure SQL Data Warehouse richiedono la crittografia (SSL/TLS) quando i dati sono in transito da e verso il database. Quando si crea una pipeline usando JSON, aggiungere la proprietà encryption e impostarla su **true** nella stringa di connessione. Per Archiviazione di Azure è possibile usare **HTTPS** nella stringa di connessione.

> [!NOTE]
> Per abilitare la crittografia in transito durante lo spostamento dei dati da Oracle seguire uno delle opzioni seguenti:
> 1. Nel server Oracle passare a Oracle Advanced Security (OAS) e configurare le impostazioni di crittografia, che supporta la crittografia Triple DES (3DES) e Advanced Encryption Standard (AES). Per informazioni dettagliate, vedere [qui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Azure Data Factory negozia automaticamente il metodo di crittografia per usare uno dei due configurati in OAS per stabilire la connessione a Oracle.
> 2. In Azure Data Factory è possibile aggiungere EncryptionMethod = 1 nella stringa di connessione (nel servizio collegato). Questo userà SSL/TLS come metodo di crittografia. Per usarlo, è necessario disabilitare le impostazioni di crittografia non SSL in OAS sul lato server Oracle per evitare conflitti di crittografia.

> [!NOTE]
> La versione TLS usata è 1.2.

### <a name="data-encryption-at-rest"></a>Crittografia di dati inattivi
Alcuni archivi di dati supportano la crittografia dei dati inattivi. È consigliabile abilitare il meccanismo di crittografia dei dati per gli archivi dati. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
La funzionalità Transparent Data Encryption (TDE) in Azure SQL Data Warehouse consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei dati inattivi. Questo comportamento è trasparente per il client. Per altre informazioni, vedere [Proteggere un database in SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>database SQL di Azure
Il database SQL di Azure supporta anche la funzionalità Transparent Data Encryption (TDE), che consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei dati, senza dover apportare modifiche all'applicazione. Questo comportamento è trasparente per il client. Per altre informazioni, vedere [Transparent Data Encryption per il database SQL e Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Archivio Azure Data Lake
Azure Data Lake Store offre anche la possibilità di crittografare i dati archiviati nell'account. Se abilitato, Data Lake Store crittografa automaticamente i dati prima di renderli persistenti e li decrittografa prima di recuperarli, rendendoli quindi trasparenti per il client che accede ai dati. Per altre informazioni, vedere [Sicurezza in Archivio Azure Data Lake](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Archiviazione BLOB di Azure e Archiviazione tabelle di Azure
Archiviazione BLOB di Azure e Archiviazione tabelle di Azure supportano la crittografia del servizio di archiviazione, che crittografa automaticamente i dati prima di renderli persistenti nella risorsa di archiviazione e li decrittografa prima di recuperarli. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 supporta la crittografia client e server dei dati inattivi. Per altre informazioni, vedere [Protezione dei dati mediante la crittografia](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift supporta la crittografia cluster per i dati inattivi. Per altre informazioni, vedere [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) (Crittografia database Amazon Redshift). 

#### <a name="salesforce"></a>Salesforce
Salesforce supporta il servizio Shield Platform Encryption, che consente la crittografia di tutti i file, gli allegati e i campi personalizzati. Per altre informazioni, vedere [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm) (Comprendere il flusso di autenticazione OAuth per il server Web).  

## <a name="hybrid-scenarios"></a>Scenari ibridi
Gli scenari ibridi richiedono l'installazione del runtime di integrazione self-hosted in una rete locale, in una rete virtuale (Azure) o in un cloud privato virtuale (Amazon). Il runtime di integrazione self-hosted deve essere in grado di accedere agli archivi dati locali. Per altre informazioni sul runtime di integrazione self-hosted, vedere [Come creare e configurare il runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![Canali di runtime di integrazione self-hosted](media/data-movement-security-considerations/data-management-gateway-channels.png)

Il canale di comando consente la comunicazione tra i servizi di spostamento dei dati in Data Factory e nel runtime di integrazione self-hosted. La comunicazione contiene informazioni relative all'attività. Il canale di dati viene usato per trasferire i dati tra gli archivi dati locali e quelli nel cloud.    

### <a name="on-premises-data-store-credentials"></a>Credenziali dell'archivio dati locale
Le credenziali possono essere archiviate all'interno della data factory o a cui la data factory può fare riferimento durante il runtime dall'insieme di credenziali delle chiavi di Azure.The credentials can be stored within data factory or [be referenced by data factory](store-credentials-in-key-vault.md) during the runtime from Azure Key Vault. Se si archiviano le credenziali all'interno della data factory, questa viene sempre archiviata crittografata nel runtime di integrazione self-hosted. 
 
- **Archiviare le credenziali in locale**. Se si utilizza direttamente il cmdlet **Set-AzDataFactoryV2LinkedService** con le stringhe di connessione e le credenziali inline in JSON, il servizio collegato viene crittografato e archiviato nel runtime di integrazione self-hosted.  In questo caso le credenziali passano attraverso il servizio back-end di Azure, che è estremamente sicuro, al computer di integrazione self-hosted in cui viene infine crittografato e archiviato. Il runtime di integrazione self-hosted usa Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) per crittografare i dati sensibili e le informazioni sulle credenziali.

- **Archiviare le credenziali in Archiviazione chiave**di Azure . È anche possibile archiviare la credenziale dell'archivio dati in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory recupera la credenziale durante l'esecuzione di un'attività. Per altre informazioni, vedere [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md) (Archiviare credenziali in Azure Key Vault).

- **Archiviare le credenziali in locale senza scorrere le credenziali tramite il back-end di Azure nel runtime di integrazione self-hosted.** Se si desidera crittografare e archiviare le credenziali in locale nel runtime di integrazione self-hosted senza dover passare il flusso delle credenziali tramite il back-end della data factory, seguire i passaggi descritti in [Crittografare le credenziali per gli archivi dati locali in Azure Data Factory.](encrypt-credentials-self-hosted-integration-runtime.md) Tutti i connettori supportano questa opzione. Il runtime di integrazione self-hosted usa Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) per crittografare i dati sensibili e le informazioni sulle credenziali. 

   Utilizzare il cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** per crittografare le credenziali del servizio collegato e i dettagli riservati nel servizio collegato. È quindi possibile utilizzare il codice JSON restituito (con l'elemento **EncryptedCredential** nella stringa di connessione) per creare un servizio collegato utilizzando il cmdlet **Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porte usate durante la crittografia del servizio collegato nel runtime di integrazione self-hosted
Per impostazione predefinita, PowerShell usa la porta 8060 nel computer con runtime di integrazione self-hosted per una comunicazione protetta. Se necessario, è possibile cambiare porta.  

![Porta HTTPS per il gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Crittografia in transito
Tutti i trasferimenti di dati avvengono attraverso un canale HTTPS e TLS sicuro su TCP per impedire attacchi di tipo "man-in-the-middle" durante la comunicazione con i servizi di Azure.

È anche possibile usare [VPN IPSec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) o [Azure ExpressRoute](../expressroute/expressroute-introduction.md) per proteggere ulteriormente il canale di comunicazione tra la rete locale e Azure.

Rete virtuale di Azure è una rappresentazione logica della propria rete nel cloud. È possibile connettere una rete locale alla propria rete virtuale configurando VPN IPSec (da sito a sito) o ExpressRoute (peering privato).    

La tabella seguente riassume i consigli di configurazione di rete e del runtime di integrazione self-hosted in base alle diverse combinazioni di percorsi di origine e destinazione per lo spostamento dei dati ibridi.

| Source (Sorgente)      | Destination                              | Configurazione di rete                    | Impostazione runtime di integrazione                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Locale | Servizi cloud e macchine virtuali distribuiti nelle reti virtuali | VPN IPSec (da punto a sito o da sito a sito) | Il runtime di integrazione self-hosted deve essere installato in una macchina virtuale di Azure nella rete virtuale.  |
| Locale | Servizi cloud e macchine virtuali distribuiti nelle reti virtuali | ExpressRoute (peering privato)           | Il runtime di integrazione self-hosted deve essere installato in una macchina virtuale di Azure nella rete virtuale.  |
| Locale | Servizi basati su Azure con un endpoint pubblico | ExpressRoute (peering Microsoft)            | Il runtime di integrazione self-hosted può essere installato in locale o in una macchina virtuale di Azure.The self-hosted integration runtime can be installed on-premises or on an Azure virtual machine. |

Le immagini seguenti mostrano come usare il runtime di integrazione self-hosted per spostare i dati tra un database locale e i servizi di Azure con ExpressRoute e la VPN IPSec (con Rete virtuale di Azure):

**ExpressRoute**

![Usare ExpressRoute con il gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![VPN IPSec con gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Configurazioni del firewall e impostazione dell'elenco Consenti per gli indirizzi IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisiti del firewall per la rete locale/privata    
In un'azienda il firewall aziendale viene eseguito nel router centrale dell'organizzazione. Windows Firewall viene eseguito come daemon nel computer locale in cui è stato installato il runtime di integrazione self-hosted. 

La tabella seguente indica la porta in uscita e i requisiti di dominio per i firewall aziendale:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Potrebbe essere necessario gestire le porte o configurare l'elenco Consenti per i domini a livello di firewall aziendale come richiesto dalle rispettive origini dati. Nella tabella sono riportati solo esempi di database SQL di Azure, Azure SQL Data Warehouse e Azure Data Lake Store.   

Nella tabella seguente vengono indicati i requisiti relativi alla porta in ingresso per Windows Firewall:

| Porte in ingresso | Descrizione                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Richiesta dal cmdlet di crittografia PowerShell, come descritto in [Crittografare le credenziali per gli archivi dati locali in Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), e dall'applicazione di gestione delle credenziali per impostare in modo sicuro le credenziali per gli archivi dati locali nel runtime di integrazione self-hosted. |

![Requisiti relativi alla porta del gateway](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>Configurazioni IP e impostazione dell'elenco Consenti negli archivi dati
Alcuni archivi dati nel cloud richiedono inoltre l'accesso all'indirizzo IP del computer che accede all'archivio. Assicurarsi che l'indirizzo IP del computer runtime di integrazione self-hosted sia consentito o configurato nel firewall in modo appropriato.

Gli archivi dati cloud seguenti richiedono l'autorizzazione dell'indirizzo IP del computer runtime di integrazione self-hosted. Alcuni di questi archivi dati, per impostazione predefinita, potrebbero non richiedere l'elenco Consenti. 

- [Database SQL di Azure](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Archivio di Azure Data Lake](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Domande frequenti

**Il runtime di integrazione self-hosted può essere condiviso tra diverse data factory?**

Sì. Ulteriori dettagli [qui](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Quali sono i requisiti delle porte per il corretto funzionamento del runtime di integrazione self-hosted?**

Il runtime di integrazione self-hosted stabilisce connessioni basate su HTTP per accedere a Internet. La porta in uscita 443 deve essere aperta per permettere al runtime di integrazione self-hosted di stabilire una connessione. Aprire la porta in ingresso 8060 solo a livello di computer (non a livello di firewall aziendale) per l'applicazione di gestione delle credenziali. Se si usa il database SQL di Azure o Azure SQL Data Warehouse come origine o destinazione, è necessario aprire anche la porta 1433. Per ulteriori informazioni, vedere la sezione Configurazione delle [configurazioni del firewall e dell'elenco Consenti per gli indirizzi IP.](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle prestazioni dell'attività di copia di Azure Data Factory, vedere [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](copy-activity-performance.md).

 
