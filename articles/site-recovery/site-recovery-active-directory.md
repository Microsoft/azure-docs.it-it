---
title: Configurare il ripristino di emergenza di Active Directory/DNS con Azure Site RecoverySet up Active Directory/DNS disaster recovery with Azure Site Recovery
description: Questo articolo descrive come implementare una soluzione di ripristino di emergenza per Active Directory e DNS con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257810"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurare il ripristino di emergenza per Active Directory e DNS

Le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di Active Directory e DNS per funzionare correttamente. Quando si configura il ripristino di emergenza per le applicazioni, spesso è necessario ripristinare Active Directory e DNS prima di ripristinare altri componenti dell'applicazione in modo da garantirne il corretto funzionamento.

È possibile usare [Site Recovery](site-recovery-overview.md) per creare un piano di ripristino di emergenza per Active Directory. Quando si verifica un'interruzione, è possibile avviare un failover. Active Directory può entrare in funzione in pochi minuti. Se è stato distribuito Active Directory per più applicazioni nel sito principale, ad esempio per SharePoint e SAP, si potrebbe voler eseguire il failover del sito completo. È possibile eseguire in primis il failover di Active Directory attraverso Site Recovery. Quindi eseguire il failover delle altre applicazioni usando i piani di ripristino specifici delle applicazioni.

Questo articolo spiega come creare una soluzione di ripristino di emergenza per Active Directory. Include i prerequisiti e le istruzioni di failover. È necessario conoscere Active Directory e Site Recovery prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Se si esegue la replica in Azure, [preparare le risorse di Azure](tutorial-prepare-azure.md), inclusi una sottoscrizione, una rete virtuale di Azure, un account di archiviazione e un insieme di credenziali di Servizi di ripristino.
* Esaminare i [requisiti di supporto](site-recovery-support-matrix-to-azure.md) per tutti i componenti.

## <a name="replicate-the-domain-controller"></a>Replicare il controller di dominio

- È necessario configurare la replica di Site Recovery in almeno una VM che ospita un controller di dominio o un DNS.
- Se si dispone di più controller di dominio nell'ambiente in uso, è necessario inoltre configurare un controller di dominio aggiuntivo nel sito di destinazione. Il controller di dominio aggiuntivo può essere in Azure o in un data center secondario locale.
- Se si hanno soltanto poche applicazioni e un controller di dominio, si potrebbe voler eseguire contestualmente il failover dell'intero sito. In questo caso si consiglia di usare Site Recovery per replicare il controller di dominio nel sito di destinazione (in Azure o in un data center secondario locale). È possibile usare la stessa macchina virtuale controller di dominio o DNS replicata anche per il [failover di test](#test-failover-considerations).
- - Se l'ambiente presenta molte applicazioni e più controller di dominio o se si intende eseguire il failover di poche applicazioni alla volta, oltre a replicare la macchina virtuale controller di dominio con Site Recovery si consiglia anche di configurare un controller di dominio aggiuntivo nel sito di destinazione (in Azure o in un data center locale secondario). Per il [failover di test](#test-failover-considerations) è possibile usare i controller di dominio replicati da Site Recovery. Per il failover è possibile usare il controller di dominio aggiuntivo nel sito di destinazione.

## <a name="enable-protection-with-site-recovery"></a>Abilitare la protezione con Site Recovery

È possibile usare Site Recovery per proteggere la macchina virtuale che ospita il controller di dominio o DNS.

### <a name="protect-the-vm"></a>Proteggere la VM
Il controller di dominio replicato con Site Recovery viene usato per il [failover di test](#test-failover-considerations). Assicurarsi che soddisfi i requisiti seguenti:

1. Il controller di dominio è un server di catalogo globale.
2. Il controller di dominio deve essere il proprietario del ruolo FSMO per i ruoli necessari durante un failover di test. In caso contrario questi ruoli dovranno essere [riassegnati](https://aka.ms/ad_seize_fsmo) dopo il failover.

### <a name="configure-vm-network-settings"></a>Configurare le impostazioni di rete della VM
Per la macchina virtuale che ospita il controller di dominio o DNS, in Site Recovery configurare le impostazioni di rete nelle impostazioni **Calcolo e rete** della macchina virtuale replicata. In questo modo si garantisce che la macchina virtuale sia collegata alla rete corretta dopo il failover.

## <a name="protect-active-directory"></a>Proteggere Active Directory

### <a name="site-to-site-protection"></a>Protezione da sito a sito
Creare un controller di dominio nel sito secondario. Quando si alza di livello il server al ruolo di controller di dominio, specificare il nome dello stesso dominio usato nel sito primario. È possibile usare lo snap-in **Siti e servizi di Active Directory** per configurare le impostazioni nell'oggetto collegamento di sito a cui i siti vengono aggiunti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. Per altre informazioni, vedere [Pianificazione della replica tra siti](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Protezione da sito ad Azure
Creare innanzitutto un controller di dominio in una rete virtuale di Azure. Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

Riconfigurare il server DNS per la rete virtuale per usare il server DNS in Azure.

![Azure Network](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Protezione da Azure a Azure
Creare innanzitutto un controller di dominio in una rete virtuale di Azure. Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

Riconfigurare il server DNS per la rete virtuale per usare il server DNS in Azure.

## <a name="test-failover-considerations"></a>considerazioni sul failover di test
Per evitare conseguenze sui carichi di lavoro di produzione, il failover di test viene eseguito in una rete isolata dalla rete di produzione.

Molte applicazioni richiedono la presenza di un controller di dominio e di un server DNS. Perciò, prima del failover di un'applicazione, è necessario creare un controller di dominio nella rete isolata da usare per il failover di test. Il modo più semplice per eseguire questa operazione è usare Site Recovery per replicare una macchina virtuale che ospita un controller di dominio o DNS. Eseguire quindi un failover di test della macchina virtuale controller di dominio prima di eseguire un failover di test del piano di ripristino per l'applicazione. Di seguito viene indicato come procedere:

1. Usare Site Recovery per [replicare](vmware-azure-tutorial.md) la macchina virtuale che ospita il controller di dominio o DNS.
2. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure è isolata dalle altre reti per impostazione predefinita. Si consiglia di usare per questa rete lo stesso intervallo di indirizzi IP della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
3. Fornire un indirizzo IP DNS nella rete isolata. Usare l'indirizzo IP che si prevede sarà ottenuto dalla macchina virtuale DNS. Se si esegue la replica in Azure, fornire l'indirizzo IP per la macchina virtuale che viene usata in caso di failover. Per immettere l'indirizzo IP, nella macchina virtuale replicata, nelle impostazioni **Calcolo e rete**, selezionare le impostazioni **IP di destinazione**.

    ![Rete di test di Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso indirizzo IP specificato nelle impostazioni **Calcolo e rete** della macchina virtuale. Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico.
    >
    > Se l'indirizzo IP di destinazione fa parte della subnet selezionata, Site Recovery tenta di creare la macchina virtuale per il failover di test usando l'indirizzo IP di destinazione. Se l'indirizzo IP di destinazione non fa parte della subnet selezionata, la macchina virtuale del failover di test viene creata usando l'indirizzo IP disponibile successivo nella subnet selezionata.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Failover di test in un sito secondario

1. Se si esegue la replica in un altro sito locale e si usa DHCP, [configurare DNS e DHCP per il failover di test](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Eseguire un failover di test della macchina virtuale controller di dominio eseguita nella rete isolata. Per eseguire il failover di test, usare il più recente punto di ripristino *coerente con l'applicazione* disponibile della macchina virtuale controller di dominio.
3. Eseguire un failover di test per il piano di ripristino che contiene le macchine virtuali in cui l'applicazione viene eseguita.
4. Al termine del test eseguire la *pulizia del failover di test* nella macchina virtuale controller di dominio. Questo passaggio consente di eliminare il controller di dominio creato per il failover di test.


### <a name="remove-references-to-other-domain-controllers"></a>Rimuovere riferimenti ad altri controller di dominio
Quando si avvia un failover di test, non includere tutti i controller di dominio nella rete di test. Per rimuovere i riferimenti ad altri controller di dominio presenti nell'ambiente di produzione, è necessario [riassegnare i ruoli FSMO](https://aka.ms/ad_seize_fsmo) ed [eseguire la pulizia](https://technet.microsoft.com/library/cc816907.aspx) dei metadati per i controller di dominio mancanti.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problemi causati dalle misure di sicurezza della virtualizzazione

> [!IMPORTANT]
> Alcune configurazioni descritte in questa sezione non sono le configurazioni di controller di dominio standard o predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato per Site Recovery da usare per il failover di test. Apportare queste modifiche solo a quel controller di dominio.  
>
>

A partire da Windows Server 2012, [misure di sicurezza aggiuntive sono integrate in Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Queste misure di sicurezza consentono di proteggere i controller di dominio virtualizzati dal ripristino dello stato precedente USN se la piattaforma hypervisor sottostante supporta **VM-GenerationID**. Azure supporta **VM-GenerationID**, perciò nei controller di dominio che eseguono Windows Server 2012 o una versione successiva in macchine virtuali di Azure sono presenti queste misure di sicurezza aggiuntive.


Quando **VM-GenerationID** viene reimpostato, viene reimpostato anche il valore **InvocationID** del database di Active Directory Domain Services. Inoltre, il pool RID viene eliminato e la cartella sysvol viene contrassegnata come non autorevole. Per altre informazioni, vedere [Introduzione alla virtualizzazione (AD DS) di servizi di dominio Active Directory](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e il blog relativo alla [virtualizzazione di DFSR in modo sicuro](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Il failover in Azure potrebbe provocare la reimpostazione di **VM-GenerationID**. La reimpostazione di **VM-GenerationID** attiva misure di sicurezza aggiuntive quando la macchina virtuale controller di dominio viene avviata in Azure. Ciò potrebbe causare un *ritardo significativo* nella possibilità di accedere alla macchina virtuale del controller di dominio.

Poiché questo controller di dominio viene usato solo in un failover di test, non sono necessarie misure di sicurezza della virtualizzazione. Per assicurarsi che il valore **VM-GenerationID** per la macchina virtuale controller di dominio non cambi, è possibile cambiare il valore DWORD seguente in **4** nel controller di dominio locale:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomi delle misure di sicurezza della virtualizzazione

Se le misure di sicurezza della virtualizzazione vengono attivate dopo un failover di test, possono verificarsi uno o più dei seguenti sintomi:  

* Il valore **GenerationID** cambia.

    ![Modifica dell'ID di generazione](./media/site-recovery-active-directory/Event2170.png)

* Il valore **InvocationID** cambia.

    ![Modifica dell'ID di chiamata](./media/site-recovery-active-directory/Event1109.png)

* La cartella Sysvol e le condivisioni NETLOGON non sono disponibili.

    ![Condivisione di cartelle Sysvol](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol cartella](./media/site-recovery-active-directory/Event13565.png)

* I database DFSR vengono eliminati.

    ![I database DFSR vengono eliminati](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Risolvere i problemi del controller di dominio durante il failover di test

> [!IMPORTANT]
> Alcune configurazioni descritte in questa sezione non sono le configurazioni di controller di dominio standard o predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato per il failover di test di Site Recovery. Apportare le modifiche solo a quel controller di dominio dedicato.  
>
>

1. Al prompt dei comandi, eseguire il comando seguente per verificare se la cartella sysvol e la cartella NETLOGON sono condivise:

    `NET SHARE`

2. Al prompt dei comandi eseguire questo comando per verificare che il controller di dominio funzioni correttamente:

    `dcdiag /v > dcdiag.txt`

3. Nel log di output cercare il testo seguente. Il testo conferma che il controller di dominio funziona correttamente.

    * "passed test Connectivity"
    * "passed test Advertising"
    * "passed test MachineAccount"

Se le condizioni precedenti sono soddisfatte, è probabile che il controller di dominio stia funzionando correttamente. In caso contrario, completare i passaggi seguenti:

1. Eseguire un ripristino autorevole del controller di dominio. Tenere presenti le seguenti informazioni:
    * Anche se la [replica FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/) non è consigliata, se si usa la replica FRS, seguire i passaggi per il ripristino autorevole. La procedura è descritta in [Using the BurFlags registry key to reinitialize File Replication Service](https://support.microsoft.com/kb/290762) (Uso della chiave del Registro di sistema BurFlags per reinizializzare il servizio Replica file).

        Per altre informazioni su BurFlags, vedere il post di blog [D2 and D4: What is it for?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/) (D2 e D4: a cosa servono?).
    * Se si usa la replica DFSR, completare i passaggi per il ripristino autorevole. Il processo è descritto in [Forzare una sincronizzazione autorevole e non autorevole per la cartella sysvol replicata da DFSR (ad esempio "D4/D2" per FRS).](https://support.microsoft.com/kb/2218556)

        È anche possibile usare le funzioni di PowerShell. Per altre informazioni, vedere [DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) (Funzioni di PowerShell per il ripristino autorevole/non autorevole di DFSR-SYSVOL).

2. Aggirare il requisito di sincronizzazione iniziale impostando la chiave del Registro di sistema seguente su **0** nel controller di dominio locale. Se il valore DWORD non esiste, può essere creato nel nodo **Parameters**.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Per altre informazioni, vedere [Troubleshoot DNS Event ID 4013: The DNS server was unable to load AD integrated DNS zones](https://support.microsoft.com/kb/2001093) (Risoluzione dei problemi per l'evento DNS ID 4013: il server DNS non è riuscito a caricare zone DNS integrate in AD).

3. Disabilitare il requisito della disponibilità di un server di catalogo globale per la convalida dell'accesso utente. A tale scopo, nel controller di dominio locale impostare la seguente chiave del Registro di sistema su **1**. Se il valore DWORD non esiste, può essere creato nel nodo **Lsa**.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Per altre informazioni, vedere [Disabilitare il requisito della disponibilità di un server di catalogo globale per la convalida dell'accesso utente](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controller di dominio su computer diversi

Se si esegue il controller di dominio e il DNs nella stessa macchina virtuale, è possibile ignorare questa procedura.


Se DNS non è presente nella stessa macchina virtuale del controller di dominio, è necessario creare una VM DNS per il failover di test. È possibile usare un server DNS nuovo e creare tutte le zone necessarie. Ad esempio, se il dominio di Active Directory è contoso.com, è possibile creare una zona DNS con il nome contoso.com. Le voci corrispondenti a Active Directory devono essere aggiornate in DNS, come segue:

1. Verificare che queste impostazioni siano state definite prima che venga avviata qualsiasi altra macchina virtuale del piano di ripristino:
   * La zona deve avere il nome radice della foresta.
   * La zona deve essere sottoposta a backup.
   * La zona deve essere abilitata per aggiornamenti protetti e non protetti.
   * Il sistema di risoluzione della macchina virtuale che ospita il controller di dominio deve puntare all'indirizzo IP della macchina virtuale DNS.

2. Eseguire il comando seguente nella VM che ospita il controller di dominio:

    `nltest /dsregdns`

3. Eseguire i comandi seguenti per aggiungere una zona nel server DNS, consentire gli aggiornamenti non sicuri e aggiungere una voce per la zona in DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla protezione, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md).
