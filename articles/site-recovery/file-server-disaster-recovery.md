---
title: Proteggere un file server usando Azure Site Recovery
description: Questo articolo descrive come proteggere un file server usando Azure Site Recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 9cef163c1b53360222ca32a827552fa361e9dd40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874248"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Proteggere un file server usando Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e ripristino di vari carichi di lavoro.

Questo articolo descrive come proteggere un file server usando Site Recovery e include altre raccomandazioni specifiche per i vari ambienti. 

- [Replicare macchine virtuali file server IaaS di Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replicare un file server locale usando Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architettura del file server
L'obiettivo di un sistema di condivisione dei file distribuito aperto è realizzare un ambiente in cui un gruppo di utenti distribuiti geograficamente può collaborare per lavorare sui file in modo efficiente e garantire che vengano applicati i requisiti di integrità. Un ecosistema di file server locale tipico che supporta un numero elevato di utenti simultanei e un numero elevato di elementi di contenuto usa la replica DFS (file system distribuito) per pianificare le repliche e limitare la larghezza di banda. 

La replica DFS usa un algoritmo di compressione, noto come compressione RDC (Remote Differential Compression), che consente di aggiornare in modo efficiente i file in una rete con larghezza di banda limitata. Rileva gli inserimenti, le rimozioni e le ridisposizioni dei dati nei file. La replica DFS è abilitata per replicare solo i blocchi di file modificati quando vengono aggiornati i file. Esistono anche ambienti di file server, in cui i backup giornalieri vengono eseguiti in periodi non di punta, che soddisfano le esigenze di emergenza. La replica DFS non è implementata.

Il diagramma seguente illustra l'ambiente di file server in cui è implementata la replica DFS.
        
![Architettura della replica DFS](media/site-recovery-file-server/dfsr-architecture.JPG)

Nel diagramma precedente più file server, denominati membri, partecipano attivamente alla replica dei file in un gruppo di replica. Il contenuto nella cartella replicata è disponibile per tutti i client che inviano richieste a uno dei membri, anche se un membro viene portato offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Raccomandazione sul ripristino di emergenza per i file server

* **Replicare un file server usando Site Recovery**: i file server possono essere replicati in Azure usando Site Recovery. Quando uno o più file server in locale non sono accessibili, è possibile attivare le macchine virtuali di ripristino in Azure. Le macchine virtuali possono quindi elaborare le richieste dai client, in locale, purché sia disponibile la connettività VPN da sito a sito e Active Directory sia configurato in Azure. È possibile usare questo metodo nel caso di in un ambiente configurato con la replica DFS o in un ambiente di file server semplice senza la replica DFS. 

* **Estendere la replica DFS a una macchina virtuale IaaS di Azure**: in un ambiente di file server in cluster con la replica DFS implementata, è possibile estendere la replica DFS locale in Azure. Una macchina virtuale di Azure viene quindi abilitata per eseguire il ruolo di file server. 

    * Dopo che sono state gestite le dipendenze della connettività VPN da sito a sito e di Active Directory e che è stata applicata la replica DFS, quando non è possibile accedere a uno o più file server locali, i client possono connettersi alla macchina virtuale di Azure che soddisferà le richieste.

    * È possibile usare questo approccio se le macchine virtuali hanno configurazioni non supportate da Site Recovery. Un esempio è un disco del cluster condiviso, a volte usato comunemente negli ambienti dei file server. La replica DFS funziona correttamente anche negli ambienti con larghezza di banda ridotta con varianza media. È necessario prendere in considerazione il costo aggiuntivo della presenza di una macchina virtuale di Azure sempre in esecuzione. 

* **Usare sincronizzazione file di Azure per replicare i file**: se si prevede di usare il cloud o si usa già una macchina virtuale di Azure, è possibile usare sincronizzazione file di Azure. Sincronizzazione file di Azure offre la sincronizzazione di condivisioni file completamente gestite nel cloud accessibili tramite il protocollo SMB ( [Server Message Block](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ) standard del settore. Le condivisioni file di Azure possono essere montate simultaneamente da distribuzioni cloud o locali di Windows, Linux e macOS. 

Il diagramma seguente consente di determinare la strategia da usare per l'ambiente di file server.

![Albero delle decisioni](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Fattori da considerare per le decisioni riguardanti il ripristino di emergenza in Azure

|Ambiente  |Recommendation  |Elementi da considerare: |
|---------|---------|---------|
|Ambiente di file server con o senza replica DFS|   [Usare Site Recovery per la replica](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery non supporta cluster di dischi condivisi o NAS (Network Attached Storage). Se l'ambiente usa queste configurazioni, scegliere uno degli altri approcci in base alle esigenze. <br> Site Recovery non supporta SMB 3.0. La macchina virtuale replicata incorpora le modifiche solo quando le modifiche apportate ai file vengono aggiornate nella posizione originale dei file.<br>  Site Recovery offre un processo di replica dei dati quasi sincrono e, di conseguenza, nel caso di uno scenario di failover non pianificato, potrebbe verificarsi una potenziale perdita di dati e potrebbe creare problemi di mancata corrispondenza degli USN.
|Ambiente di file server con replica DFS     |  [Estendere la replica DFS a una macchina virtuale IaaS di Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    La replica DFS funziona correttamente in ambienti con larghezza di banda molto limitata. Questo approccio richiede una macchina virtuale di Azure sempre in esecuzione. È necessario tenere conto del costo della macchina virtuale nella pianificazione.         |
|Macchina virtuale IaaS di Azure     |     Sincronizzazione file    |     Se si usa Sincronizzazione file in uno scenario di ripristino di emergenza, durante il failover è necessario prevedere azioni manuali per assicurarsi che le condivisioni file siano accessibili per il computer client in modo trasparente. Sincronizzazione file richiede che la porta 445 sia aperta dal computer client.     |


### <a name="site-recovery-support"></a>Supporto di Site Recovery
Dato che la replica di Site Recovery è indipendente dall'applicazione, queste raccomandazioni saranno valide anche per gli scenari seguenti.

| Source (Sorgente)  |In un sito secondario  |In Azure
|---------|---------|---------|
|Azure|  -|Sì|
|Hyper-V|  Sì  |Sì
|VMware  |Sì|  Sì
|Server fisico|  Sì  |Sì
 

> [!IMPORTANT]
> Prima di continuare con uno qualsiasi dei tre approcci seguenti, assicurarsi che siano state gestite queste dipendenze.



**Connettività da sito a sito**: è necessario stabilire una connessione diretta tra il sito locale e la rete di Azure per consentire la comunicazione tra i server. Usare una connessione VPN da sito a sito sicura per una rete virtuale di Azure che viene usata come sito di ripristino di emergenza. Per altre informazioni, vedere [Creare una connessione da sito a sito nel portale di Azure](../vpn-gateway/tutorial-site-to-site-portal.md).

**Active Directory**: la replica DFS dipende da Active Directory. Ciò significa che la foresta Active Directory con i controller di dominio locali viene estesa al sito di ripristino di emergenza in Azure. Anche se non si usa la replica DFS, questi passaggi sono necessari se occorre concedere l'accesso agli utenti interessati o è richiesta la loro verifica per l'accesso. Per altre informazioni, vedere [Usare Azure Site Recovery per proteggere Active Directory e DNS](./site-recovery-active-directory.md).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Raccomandazioni sul ripristino di emergenza per le macchine virtuali IaaS di Azure

Se si configura e gestisce il ripristino di emergenza di file server ospitati in macchine virtuali IaaS di Azure, è possibile scegliere tra due opzioni, a seconda che si voglia o meno passare a [File di Azure](../storage/files/storage-files-introduction.md):

* [Usare Sincronizzazione file](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Usare Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Usare Sincronizzazione file per replicare i file ospitati in una macchina virtuale IaaS

È possibile usare File di Azure per sostituire completamente o integrare i dispositivi NAS o i file server locali tradizionali. Le condivisioni file di Azure possono anche essere replicate nei server Windows con Sincronizzazione file, in locale o nel cloud, per migliorare le prestazioni e per una memorizzazione nella cache distribuita dei dati nella posizione in cui vengono usati. I passaggi seguenti descrivono la raccomandazione sul ripristino di emergenza per le macchine virtuali di Azure che eseguono la stessa funzionalità dei file server tradizionali:
* Proteggere le macchine con Site Recovery. Seguire la procedura in [Replicare una macchina virtuale di Azure in un'altra area di Azure](azure-to-azure-quickstart.md).
* Usare Sincronizzazione file per replicare i file dalla macchina virtuale che funge da file server al cloud.
* Usare la funzionalità di [pianificazione del ripristino](site-recovery-create-recovery-plans.md) di Site Recovery per aggiungere gli script per [montare la condivisione file di Azure](../storage/files/storage-how-to-use-files-windows.md) e accedere alla condivisione nella macchina virtuale.

I passaggi seguenti descrivono brevemente come usare Sincronizzazione file:

1. [Creare un account di archiviazione in Microsoft Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Se è stata scelta l'archiviazione con ridondanza geografica per gli account di archiviazione, si ottiene l'accesso in lettura ai dati dall'area secondaria in caso di emergenza. Per altre informazioni, vedere [Ripristino di emergenza e failover dell'account di archiviazione](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Creare una condivisione file](../storage/files/storage-how-to-create-file-share.md).
3. [Avviare Sincronizzazione file](../storage/files/storage-sync-files-deployment-guide.md) nel file server di Azure.
4. Creare un gruppo di sincronizzazione. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure. Un gruppo di sincronizzazione deve contenere anche un endpoint server, che rappresenta un percorso in un server Windows.
5. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e il server locale.
6. In caso di emergenza nell'ambiente locale, eseguire un failover tramite un [piano di ripristino](site-recovery-create-recovery-plans.md). Aggiungere lo script per [montare la condivisione file di Azure](../storage/files/storage-how-to-use-files-windows.md) e accedere alla condivisione nella macchina virtuale.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replicare una macchina virtuale che funge da file server IaaS usando Site Recovery

In presenza di client in locale che accedono alla macchina virtuale del file server IaaS, eseguire tutti i passaggi seguenti. In caso contrario, andare direttamente al passaggio 3.

1. Stabilire una connessione VPN da sito a sito tra il sito locale e la rete Azure.
2. Estendere Active Directory locale.
3. [Configurare il ripristino di emergenza](azure-to-azure-tutorial-enable-replication.md) per il computer di file server IaaS in un'area secondaria.


Per altre informazioni sul ripristino di emergenza in un'area secondaria, vedere [questo articolo](./azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replicare un file server locale usando Site Recovery

I passaggi seguenti descrivono la replica per una macchina virtuale VMware. Per la procedura per replicare una macchina virtuale Hyper-V, vedere [questa esercitazione](./hyper-v-azure-tutorial.md).

1. [Preparare le risorse di Azure](tutorial-prepare-azure.md) per la replica dei computer locali.
2. Stabilire una connessione VPN da sito a sito tra il sito locale e la rete Azure. 
3. Estendere Active Directory locale.
4. [Preparare i server VMware locali](./vmware-azure-tutorial-prepare-on-premises.md).
5. [Configurare il ripristino di emergenza](./vmware-azure-tutorial.md) in Azure per le macchine virtuali locali.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Estendere la replica DFS a una macchina virtuale IaaS di Azure

1. Stabilire una connessione VPN da sito a sito tra il sito locale e la rete Azure. 
2. Estendere Active Directory locale.
3. [Creare ed eseguire il provisioning di una macchina virtuale per il file server](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) nella rete virtuale di Azure.
Verificare che la macchina virtuale sia stata aggiunta alla stessa rete virtuale di Azure, con connettività all'ambiente locale. 
4. Installare e [configurare la replica DFS](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877) in Windows Server.
5. [Implementare uno spazio dei nomi DFS](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Con lo spazio dei nomi DFS implementato, è possibile eseguire il failover delle cartelle condivise dalla produzione ai siti di ripristino di emergenza aggiornando le destinazioni di cartelle dello spazio dei nomi DFS. Dopo che queste modifiche dello spazio dei nomi DFS sono state replicate tramite Active Directory, gli utenti vengono connessi alle destinazioni di cartelle appropriate in modo trasparente.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Usare Sincronizzazione file per replicare i file locali
È possibile usare Sincronizzazione file per replicare i file nel cloud. In caso di emergenza e se il file server locale non è disponibile, è possibile montare i percorsi dei file desiderati dal cloud e continuare a gestire le richieste dai computer client.
Per integrare Sincronizzazione file con Site Recovery:

* Proteggere i computer che fungono da file server con Site Recovery. Seguire la procedura descritta in [questa esercitazione](./vmware-azure-tutorial.md).
* Usare Sincronizzazione file per replicare i file dal computer che funge da file server nel cloud.
* Usare la funzionalità del piano di ripristino in Site Recovery per aggiungere script per montare in Azure la condivisione file di Azure nella macchina virtuale del file server di cui è stato eseguito il failover.

Seguire questa procedura per usare Sincronizzazione file:

1. [Creare un account di archiviazione in Microsoft Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Se è stata scelta l'archiviazione con ridondanza geografica e accesso in lettura (scelta consigliata) per gli account di archiviazione, è disponibile l'accesso in lettura ai dati dall'area secondaria in caso di emergenza. Per altre informazioni, vedere [ripristino di emergenza e failover dell'account di archiviazione](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Creare una condivisione file](../storage/files/storage-how-to-create-file-share.md).
3. [Distribuire Sincronizzazione file ](../storage/files/storage-sync-files-deployment-guide.md) nel file server locale.
4. Creare un gruppo di sincronizzazione. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere almeno un endpoint cloud, che rappresenta una condivisione file di Azure. Il gruppo di sincronizzazione deve contenere anche un endpoint del server, che rappresenta un percorso nel server Windows locale.
5. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e il server locale.
6. In caso di emergenza nell'ambiente locale, eseguire un failover tramite un [piano di ripristino](site-recovery-create-recovery-plans.md). Aggiungere lo script per montare la condivisione file di Azure e accedere alla condivisione nella macchina virtuale.

> [!NOTE]
> Assicurarsi che la porta 445 sia aperta. File di Azure usa il protocollo SMB. SMB comunica sulla porta TCP 445. Verificare che il firewall non blocchi la porta TCP 445 da un computer client.


## <a name="do-a-test-failover"></a>Eseguire un failover di test

1. Passare al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2. Selezionare il piano di ripristino creato per l'ambiente del file server.
3. Selezionare **Failover di test**.
4. Selezionare il punto di recupero e la rete virtuale di Azure per avviare il processo di failover di test.
5. Quando l'ambiente secondario è disponibile, eseguire le convalide.
6. Al termine delle convalide, selezionare **Pulizia failover di test** nel piano di ripristino per pulire l'ambiente di failover di test.

Per altre informazioni su come eseguire un failover di test, vedere [Failover di test in Site Recovery](site-recovery-test-failover-to-azure.md).

Per le linee guida su come eseguire il failover di test per Active Directory e DNS, vedere [Usare Azure Site Recovery per proteggere Active Directory e DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Eseguire un failover

1. Passare al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2. Selezionare il piano di ripristino creato per l'ambiente del file server.
3. Selezionare **Failover**.
4. Selezionare il punto di recupero per avviare il processo di failover.

Per altre informazioni su come eseguire un failover, vedere [Failover in Site Recovery](site-recovery-failover.md).