---
title: Novità di Azure NetApp Files | Microsoft Docs
description: Viene fornito un riepilogo delle nuove funzionalità e dei miglioramenti più recenti di Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: f2167159b03cd0387acfccf4bbd0a2e840f739df
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955232"
---
# <a name="whats-new-in-azure-netapp-files"></a>Novità di Azure NetApp Files

Azure NetApp Files viene aggiornato regolarmente. Questo articolo fornisce un riepilogo delle nuove funzionalità e dei miglioramenti più recenti. 

## <a name="march-2021"></a>Marzo 2021

* [Condivisioni di disponibilità continua SMB (CA)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (anteprima)  

    Il failover trasparente SMB consente di eseguire operazioni di manutenzione nel servizio Azure NetApp Files senza interrompere la connettività alle applicazioni server che archiviano e accedono ai dati nei volumi SMB. Per supportare il failover trasparente SMB, Azure NetApp Files supporta ora l'opzione condivisioni di disponibilità continua SMB per l'uso con SQL Server applicazioni su SMB in esecuzione su macchine virtuali di Azure. Questa funzionalità è attualmente supportata in Windows SQL Server. Linux SQL Server attualmente non è supportato. L'abilitazione di questa funzionalità offre miglioramenti significativi in termini di prestazioni SQL Server e scalabilità e vantaggi in termini di costi per [istanza singola, Always-On istanza del cluster di failover e distribuzioni Always-On gruppo di disponibilità](azure-netapp-files-solution-architectures.md#sql-server) Vedere [i vantaggi dell'uso di Azure NetApp files per la distribuzione di SQL Server](solutions-benefits-azure-netapp-files-sql-server.md).

* [Ridimensionamento automatico di un volume di destinazione della replica tra aree](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    In una relazione di replica tra aree, un volume di destinazione viene ridimensionato automaticamente in base alle dimensioni del volume di origine. Di conseguenza, non è necessario ridimensionare il volume di destinazione separatamente. Questo comportamento di ridimensionamento automatico è applicabile quando i volumi si trovano in una relazione di replica attiva o quando il peering della replica è interruppe con l'operazione di risincronizzazione. Per il corretto funzionamento di questa funzionalità, è necessario garantire una capacità sufficiente nei pool di capacità per i volumi di origine e di destinazione.

## <a name="december-2020"></a>Dicembre 2020

* [Strumento di snapshot coerente applicazione Azure](azacsnap-introduction.md) (anteprima)    

    Azure Application Consistent Snapshot Tool (AzAcSnap) è uno strumento da riga di comando che semplifica la protezione dei dati dei database di terze parti (SAP HANA) negli ambienti Linux (ad esempio SUSE e RHEL).   

    AzAcSnap sfrutta le funzionalità di snapshot e replica del volume disponibili in Azure NetApp Files e in istanze Large di Azure. e in grado di offrire i vantaggi indicati di seguito.

    * Protezione dei dati coerente con l'applicazione 
    * Gestione del catalogo del database 
    * Protezione dei volumi *ad hoc* 
    * Clonazione dei volumi di archiviazione 
    * Supporto del ripristino di emergenza 

## <a name="november-2020"></a>Novembre 2020

* [Ripristino dello snapshot](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    La funzionalità di ripristino dello snapshot consente di ripristinare rapidamente un volume allo stato in cui si trovava quando è stato effettuato uno snapshot specifico. Nella maggior parte dei casi, il ripristino di un volume è molto più rapido rispetto al ripristino dei singoli file da uno snapshot al file system attivo. È anche più efficiente in termini di spazio rispetto al ripristino di uno snapshot in un nuovo volume.

## <a name="september-2020"></a>Settembre 2020

* [Azure NetApp files la replica tra aree](cross-region-replication-introduction.md) (anteprima)

  Azure NetApp Files supporta ora la replica tra più aree. Con questa nuova funzionalità di ripristino di emergenza, è possibile replicare i volumi di Azure NetApp Files da un'area di Azure a un'altra in modo rapido e conveniente, proteggendo i dati da errori imprevedibili a livello di area. La replica tra più aree di Azure NetApp Files sfrutta la tecnologia NetApp SnapMirror® e vengono inviati in rete solo i blocchi modificati, in un formato compresso ed efficiente. Questa tecnologia proprietaria riduce al minimo la quantità di dati che è necessario replicare tra le aree, consentendo così di risparmiare sui costi di trasferimento dei dati. Viene inoltre ridotto il tempo necessario per la replica, quindi è possibile ottenere un obiettivo del punto di ripristino (RPO) più piccolo.

* [Pool di capacità QoS manuale](manual-qos-capacity-pool-introduction.md) (anteprima)  

    In un pool di capacità QoS manuale è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. La velocità effettiva totale di tutti i volumi creati con un pool di capacità QoS manuale è limitata dalla velocità effettiva totale del pool. Il suo valore è determinato dalla combinazione della velocità effettiva del livello di servizio e delle dimensioni del pool. In alternativa, il [tipo QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) di un pool di capacità può essere automatico, che è l'impostazione predefinita. In un pool di capacità QoS automatico la velocità effettiva viene assegnata automaticamente ai volumi nel pool, proporzionalmente alla quota di dimensioni assegnata ai volumi.

* [Firma LDAP](azure-netapp-files-create-volumes-smb.md) (anteprima)   

    Azure NetApp Files supporta ora la firma LDAP per le ricerche LDAP sicure tra il servizio Azure NetApp Files e i controller di dominio di Active Directory Domain Services specificati dall'utente. Questa funzionalità è attualmente in anteprima.

* [Crittografia AES per l'autenticazione di AD](azure-netapp-files-create-volumes-smb.md) (anteprima)

    Azure NetApp Files supporta ora la crittografia AES sulla connessione LDAP al controller di dominio per abilitare la crittografia AES per un volume SMB. Questa funzionalità è attualmente in anteprima. 

* Nuove [metriche](azure-netapp-files-metrics.md):   

    * Nuove metriche per i volumi: 
        * *Dimensioni allocate del volume*: dimensioni di un volume di cui è stato effettuato il provisioning
    * Nuove metriche per i pool: 
        * *Dimensioni allocate del pool*: dimensioni del pool di cui è stato effettuato il provisioning 
        * *Dimensioni totali degli snapshot per il pool*: somma delle dimensioni degli snapshot di tutti i volumi nel pool

## <a name="july-2020"></a>Luglio 2020

* [Volume con doppio protocollo (NFSv3 e SMB)](create-volumes-dual-protocol.md)

    È ora possibile creare un volume Azure NetApp Files che consente l'accesso simultaneo a un doppio protocollo (NFS v3 e SMB) con supporto per il mapping utente LDAP. Questa funzionalità consente casi d'uso in cui è possibile avere un carico di lavoro basato su Linux che genera e archivia i dati in un volume Azure NetApp Files. Contemporaneamente, il personale deve usare client e software basati su Windows per analizzare i nuovi dati generati dallo stesso volume Azure NetApp Files. La funzionalità di accesso simultaneo al doppio protocollo elimina la necessità di copiare i dati generati dal carico di lavoro in un volume separato con un protocollo diverso per la post-analisi, consentendo di risparmiare sui costi di archiviazione e sul tempo operativo. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è disponibile a livello generale. Per altre informazioni, vedere la [documentazione sull'accesso simultaneo al doppio protocollo](create-volumes-dual-protocol.MD).

* [Crittografia Kerberos NFS v4.1 in transito](configure-kerberos-encryption.MD)

    Azure NetApp Files supporta ora la crittografia client NFS nelle modalità Kerberos (krb5, krb5i e krb5p) con la crittografia AES-256, per una maggiore sicurezza dei dati. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è disponibile a livello generale. Per altre informazioni, vedere la [documentazione sulla crittografia Kerberos NFS v4.1](configure-kerberos-encryption.MD).

* [Modifica del livello di servizio del volume dinamico](dynamic-change-volume-service-level.MD)

    Il cloud offre flessibilità nella spesa IT. È ora possibile modificare il livello di servizio di un volume Azure NetApp Files esistente spostando il volume in un altro pool di capacità che usa il livello di servizio desiderato. Questa modifica sul posto del livello di servizio per il volume non richiede la migrazione dei dati. Non ha inoltre alcun impatto sull'accesso del piano dati al volume. È possibile modificare un volume esistente per usare un livello di servizio superiore per ottenere prestazioni migliori oppure per usare un livello di servizio inferiore per l'ottimizzazione dei costi. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è attualmente disponibile in anteprima pubblica. È possibile eseguire la registrazione per l'anteprima della funzionalità seguendo quanto indicato nella [documentazione sulla modifica del livello di servizio del volume dinamico](dynamic-change-volume-service-level.md).

* [Criteri snapshot del volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (anteprima) 

    Azure NetApp Files consente di creare snapshot temporizzati dei volumi. È ora possibile creare criteri snapshot per fare in modo che Azure NetApp Files crei automaticamente snapshot del volume in base a una frequenza definita. È possibile pianificare l'esecuzione degli snapshot in cicli orari, giornalieri, settimanali o mensili. È anche possibile specificare il numero massimo di snapshot da conservare nell'ambito dei criteri snapshot. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è attualmente disponibile in anteprima. È possibile eseguire la registrazione per l'anteprima della funzionalità seguendo quanto indicato nella [documentazione sui criteri snapshot del volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Criteri di esportazione dell'accesso radice NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files consente ora di specificare se l'account radice può accedere al volume. 

* [Opzione Nascondi percorso snapshot](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files consente ora di specificare se un utente può visualizzare la directory `.snapshot` (client NFS) o la cartella `~snapshot` (client SMB) in un volume montato e accedervi.

## <a name="may-2020"></a>Maggio 2020

* [Utenti dei criteri di backup](create-active-directory-connections.md) (anteprima)

    Azure NetApp Files consente di includere account aggiuntivi che richiedono privilegi elevati per l'account computer creato per l'uso con Azure NetApp Files. Agli account specificati sarà consentito modificare le autorizzazioni NTFS a livello di file o di cartella. È ad esempio possibile specificare un account del servizio senza privilegi usato per la migrazione dei dati a una condivisione file SMB in Azure NetApp Files. La funzionalità Utenti dei criteri di backup è attualmente in anteprima.

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su Azure NetApp Files](azure-netapp-files-introduction.md)
* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
