---
title: Replicare le macchine virtuali di Azure Stack in Azure tramite Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure per macchine virtuali di Azure Stack con il servizio Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 300b239de74c7d21e03eb0c3b059663b5074964c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728386"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicare le macchine virtuali di Azure Stack in Azure

Questo articolo illustra come configurare il ripristino di emergenza di macchine virtuali Azure Stack in Azure usando il [servizio Azure Site Recovery](site-recovery-overview.md).

Site Recovery contribuisce alla strategia di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery). Il servizio assicura che i carichi di lavoro delle macchine virtuali rimangano disponibili quando si verificano interruzioni previste e impreviste.

- Site Recovery orchestra e gestisce la replica di macchine virtuali in Archiviazione di Azure.
- Quando nel sito primario si verifica un'interruzione, per eseguire il failover in Azure si usa Site Recovery.
- In caso di failover vengono create macchine virtuali di Azure a partire dai dati archiviati delle macchine virtuali e gli utenti possono continuare ad accedere ai carichi di lavoro in esecuzione sulle macchine virtuali di Azure.
- Quando l'esecuzione è ripresa del tutto, è possibile eseguire il failback delle macchine virtuali di Azure nel sito primario e avviare nuovamente la replica in Archiviazione di Azure.


In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * **Passaggio 1: Preparare le macchine virtuali di Azure Stack per la replica**. Verificare che le macchine virtuali siano conformi ai requisiti di Site Recovery e preparare il servizio Mobility di Site Recovery. Il servizio è installato in ogni macchina virtuale da replicare.
> * **Passaggio 2: Configurare un insieme di credenziali di Servizi di ripristino**. Configurare un insieme di credenziali per Site Recovery e specificare gli elementi da replicare. Azioni e componenti di Site Recovery sono configurati e gestiti nell'insieme di credenziali.
> * **Passaggio 3: Configurare l'ambiente di origine della replica**. Configurare un server di configurazione di Site Recovery. Il server di configurazione è una singola macchina virtuale di Azure Stack che esegue tutti i componenti necessari per Site Recovery. Dopo aver impostato il server di configurazione, registrarlo nell'insieme di credenziali.
> * **Passaggio 4: Configurare l'ambiente di destinazione della replica**. Selezionare l'account di Azure, l'account di Archiviazione di Azure e la rete da usare. Durante la replica, i dati della macchina virtuale vengono copiati in Archiviazione di Azure. Dopo il failover, le macchine virtuali di Azure vengono aggiunte alla rete specificata.
> * **Passaggio 5: abilitare la replica**. Configurare le impostazioni di replica e abilitare la replica per le macchine virtuali. Il servizio Mobility verrà installato in una macchina virtuale quando è abilitata la replica. Site Recovery esegue una replica iniziale della macchina virtuale e quindi viene avviata la replica in corso.
> * **Passaggio 6: Eseguire un'analisi del ripristino di emergenza**. Dopo che la replica è configurata e in esecuzione, eseguire una verifica per controllare che il failover funzioni nel modo previsto. Per avviare l'analisi, eseguire un failover di test in Site Recovery. Il failover di test non ha alcun impatto sull'ambiente di produzione.

Quando la procedura è completata, è quindi possibile eseguire un failover completo in Azure nel modo e nel momento necessari.

## <a name="architecture"></a>Architettura

![Il diagramma Mostra gli insiemi di credenziali dei servizi di ripristino per due tenant in cloud associati a sottoscrizioni tenant sia in un'infrastruttura Azure Stack comune.](./media/azure-stack-site-recovery/architecture.png)

**Posizione** | **Componente** |**Dettagli**
--- | --- | ---
**Server di configurazione** | Viene eseguito in una singola macchina virtuale di Azure Stack. | In ogni sottoscrizione viene impostata una macchina virtuale del server di configurazione. Tale macchina virtuale esegue questi componenti di Site Recovery:<br/><br/> - Server di configurazione: coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati. - Server di elaborazione: agisce come un gateway di replica. Riceve i dati di replica, li ottimizza tramite la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure.<br/><br/> Se le macchine virtuali da replicare superano i limiti indicati di seguito, è possibile configurare un server di elaborazione autonomo separato. [Altre informazioni](vmware-azure-set-up-process-server-scale.md)
**Servizio Mobility** | Installato in ogni macchina virtuale da replicare. | Nei passaggi descritti in questo articolo si procede alla preparazione di un account in modo che il servizio Mobility venga installato automaticamente in una macchina virtuale quando è abilitata la replica. Se non si intende installare automaticamente il servizio, è possibile usare altri metodi. [Altre informazioni](vmware-azure-install-mobility-service.md)
**Azure** | In Azure è necessario disporre di un insieme di credenziali di Servizi di ripristino, di un account di archiviazione e di una rete virtuale. |  I dati replicati vengono archiviati nell'account di archiviazione. Quando si verifica il failover, alla rete di Azure vengono aggiunte macchine virtuali di Azure.


La replica funziona nel modo indicato di seguito:

1. Nell'insieme di credenziali è necessario specificare l'origine e la destinazione della replica, impostare il server di configurazione, creare criteri di replica e abilitare la replica.
2. Il servizio Mobility viene installato nel computer (se è stata usata l'installazione push) e la replica viene avviata in conformità con i criteri.
3. Una copia iniziale dei dati del server viene replicata in Archiviazione di Azure.
4. Al termine della replica iniziale, viene avviata la replica differenziale in Azure. Le modifiche rilevate vengono salvate in un file HRL.
5. Il server di configurazione orchestra la gestione delle repliche con Azure (porta HTTPS 443 in uscita).
6. Il server di elaborazione riceve i dati dai computer di origine, li ottimizza e li crittografa, quindi li invia ad Archiviazione di Azure (porta 443 in uscita).
7. I computer di replica comunicano con il server di configurazione (porta HTTPS 443 in ingresso), per la gestione delle repliche. I computer inviano i dati di replica al server di elaborazione sulla porta HTTPS 9443 in ingresso (che può essere modificata).
8. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. In alternativa, è possibile usare il peering pubblico di Azure ExpressRoute. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.

## <a name="prerequisites"></a>Prerequisiti

Di seguito vengono indicati gli elementi necessari per configurare questo scenario.

**Requisito** | **Dettagli**
--- | ---
**Account di sottoscrizione di Azure** | Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
**Autorizzazioni dell'account di Azure** | L'account di Azure in uso ha bisogno delle autorizzazioni per le operazioni seguenti:<br/><br/> - Creare un insieme di credenziali di Servizi di ripristino<br/><br/> - Creare una macchina virtuale nel gruppo di risorse e nella rete virtuale usata per lo scenario<br/><br/> - Scrivere sull'account di archiviazione specificato<br/><br/> Tenere presente quanto segue:<br/><br/> - Se si crea un account, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> - Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.<br/><br/> - Se sono necessarie autorizzazioni più granulari, leggere [questo articolo](site-recovery-role-based-linked-access-control.md).
**Macchina virtuale di Azure Stack** | È necessaria una macchina virtuale di Azure Stack nella sottoscrizione del tenant, che verrà distribuita come server di configurazione di Site Recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Prerequisiti per il server di configurazione

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Passaggio 1: Preparare le macchine virtuali di Azure Stack

### <a name="verify-the-operating-system"></a>Verificare il sistema operativo

Verificare che le macchine virtuali siano in esecuzione in uno dei sistemi operativi riepilogati nella tabella.


**Sistema operativo** | **Dettagli**
--- | ---
**Windows a 64 bit** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (da SP1)
**CentOS** | Da 5.2 a 5.11, da 6.1 a 6.9, da 7.0 a 7.3
**Ubuntu** | Server LTS 14.04, server LTS 16.04. Esaminare i [kernel supportati](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparare l'installazione del servizio Mobility

In tutte le macchine virtuali da replicare deve essere installato il servizio Mobility. Per l'installazione automatica del servizio nella macchina virtuale da parte del server di elaborazione quando la replica è abilitata, verificare le impostazioni della macchina virtuale.

#### <a name="windows-machines"></a>Computer Windows

- È necessario disporre di connettività di rete tra la macchina virtuale in cui si vuole abilitare la replica e il computer che esegue il server di elaborazione (per impostazione predefinita si tratta della macchina virtuale del server di configurazione).
- È necessario un account con diritti di amministratore (di dominio oppure in locale) nel computer per cui si abilita la replica.
    - Tale account viene specificato durante la configurazione di Site Recovery. Il server di elaborazione usa quindi l'account per installare il servizio Mobility quando è abilitata la replica.
    - L'account verrà usato solo da Site Recovery per l'installazione push e per aggiornare il servizio Mobility.
    - Se non si usa un account di dominio, è necessario disabilitare il controllo di accesso di utente remoto nella macchina virtuale:
        - Nel Registro di sistema creare il valore DWORD **LocalAccountTokenFilterPolicy** in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Impostare il valore su 1.
        - Per eseguire questa operazione al prompt dei comandi, digitare **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- In Windows Firewall nella macchina virtuale da replicare consentire la condivisione di file e stampanti e WMI.
    - A tale scopo, eseguire **wf.msc** per aprire la console di Windows Firewall. Fare clic con il pulsante destro del mouse su **regole in ingresso**  >  **nuova regola** Selezionare **Predefinita** e quindi scegliere **Condivisione file e stampanti** nell'elenco. Completare la procedura guidata, selezionare le opzioni per consentire la connessione e quindi fare clic su **Fine**.
    - Per i computer di dominio, per eseguire questa operazione è possibile usare un oggetto Criteri di gruppo.


#### <a name="linux-machines"></a>Computer Linux

- Verificare che tra il computer Linux e il server di elaborazione sia attiva la connettività di rete.
- Nel computer per cui si abilita la replica è necessario un account che sia un utente ROOT nel server Linux di origine:
    - Tale account viene specificato durante la configurazione di Site Recovery. Il server di elaborazione usa quindi l'account per installare il servizio Mobility quando è abilitata la replica.
    - L'account verrà usato solo da Site Recovery per l'installazione push e per aggiornare il servizio Mobility.
- Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
- Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
- Assicurarsi che SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
- Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config:
    1. A tale scopo, accedere come utente ROOT.
    2. Nel file /etc/ssh/sshd_config trovare la riga che inizia con **PasswordAuthentication**. Rimuovere il commento dalla riga e modificare il valore in **yes**.
    3. Trovare la riga che inizia con il **sottosistema** e rimuovere il commento dalla riga.

        ![Servizio Mobility di Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Riavviare il servizio sshd.


### <a name="note-the-vm-private-ip-address"></a>Prendere nota dell'indirizzo IP privato della macchina virtuale

Per ogni computer da replicare, trovare l'indirizzo IP:

1. Nel portale di Azure Stack fare clic sulla macchina virtuale.
2. Nel menu **Risorsa** fare clic su **Interfacce di rete**.
3. Prendere nota dell'indirizzo IP privato.

    ![Indirizzo IP privato](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Passaggio 2: Creare un insieme di credenziali e selezionare un obiettivo di replica

1. Nella portale di Azure selezionare **Crea una risorsa**  >  **strumenti di gestione**  >  **backup e Site Recovery**.
2. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.
3. In **Gruppo di risorse** creare o selezionare un gruppo di risorse. Viene usato il gruppo **contosoRG**.
4. In **Località** immettere l'area di Azure. Viene usato **Europa occidentale**.
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard** > **Crea**.

   ![Creare un nuovo insieme di credenziali](./media/azure-stack-site-recovery/new-vault-settings.png)

   Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

### <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **Insiemi di credenziali dei servizi di ripristino** specificare un nome per l'insieme di credenziali. Viene usato **ContosoVMVault**.
2. In **Attività iniziali** selezionare su Site Recovery. Selezionare quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **I computer sono virtualizzati** selezionare **Non virtualizzato/Altro**. Selezionare **OK**.

    ![Obiettivo di protezione](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Passaggio 3: Configurare l'ambiente di origine

Impostare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le macchine virtuali da replicare.

1. Fare clic su **Preparare l'infrastruttura** > **Origine**.
2. In **Prepara origine** fare clic su **+Server di configurazione**.

    ![Screenshot della finestra di dialogo + server di configurazione con il messaggio "fare clic su + server di configurazione nella barra dei comandi in alto per impostarne uno...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
5. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
6. Scaricare la chiave di registrazione dell'insieme di credenziali, Per eseguire l'installazione unificata, è necessaria la chiave di registrazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Screenshot della finestra di dialogo Aggiungi server con tipo di server impostato su server di configurazione e il pulsante Scarica chiave di registrazione dell'insieme di credenziali evidenziato.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Eseguire l'Installazione unificata di Azure Site Recovery

Per installare e registrare il server di configurazione, eseguire una connessione RDP alla macchina virtuale da usare per il server di configurazione ed eseguire il programma di installazione unificata.

Prima di iniziare, verificare che l'orologio sia [sincronizzato con un server di riferimento ora](/windows-server/networking/windows-time-service/windows-time-service-top) nella macchina virtuale. L'istallazione non riesce se l'ora differisce di più di cinque minuti rispetto all'ora locale.

Installare il server di configurazione:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato anche dalla riga di comando. [Altre informazioni](physical-manage-configuration-server.md#install-from-the-command-line)
>
> Possono trascorrere 15 minuti o più prima che il nome dell'account venga visualizzato nel portale. Per eseguire immediatamente l'aggiornamento, selezionare **Server di configurazione** > **_nome server_ *_ > _* Aggiorna server**.

## <a name="step-4-set-up-the-target-environment"></a>Passaggio 4: Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. In **preparare l'infrastruttura**  >  **destinazione** selezionare la sottoscrizione di Azure che si vuole usare.
2. Specificare il modello di distribuzione di destinazione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili. Se non li trova, per completare la procedura guidata è necessario creare almeno un account di archiviazione e una rete virtuale.


## <a name="step-5-enable-replication"></a>Passaggio 5: Abilitare la replica

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Fare clic su **preparare l'infrastruttura**  >  **impostazioni di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**, specificare il limite dell'obiettivo del punto di ripristino (RPO).
    - I punti di ripristino per i dati replicati vengono creati in conformità con il tempo stabilito.
    - Questa impostazione non influisce sulla replica, che è continua. L'impostazione genera solo un avviso se viene raggiunto il limite di soglia senza che venga creato un punto di ripristino.
4. In **Conservazione del punto di ripristino** specificare il tempo di conservazione di ogni punto di ripristino. Le macchine virtuali replicate possono essere ripristinate in qualsiasi punto dell'intervallo di tempo specificato.
5. In **Frequenza snapshot coerenti con l'app** specificare la frequenza di snapshot coerenti con l'applicazione.

    - Uno snapshot coerente con l'app è uno snapshot temporizzato dei dati dell'app all'interno della macchina virtuale.
    - Il servizio Copia Shadow del volume (VSS) assicura che lo stato delle app nella macchina virtuale sia coerente quando viene creato lo snapshot.
6. Fare clic su **OK** per creare i criteri.


### <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

È possibile ignorare subito questo passaggio. Nell'elenco a discesa **Pianificazione della distribuzione** fare clic su **Operazione completata**.



### <a name="enable-replication"></a>Abilitare la replica

Verificare di aver completato tutte le attività nel [Passaggio 1: Preparare il computer](#step-1-prepare-azure-stack-vms). Abilitare la replica nel modo indicato di seguito:

1. Selezionare **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer**, selezionare **Computer fisici**.
4. Selezionare il server di elaborazione (server di configurazione). Fare quindi clic su **OK**.
5. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui creare le macchine virtuali dopo il failover. Scegliere il modello di distribuzione da usare per le macchine virtuali sottoposte a failover.
6. Selezionare l'account di archiviazione di Azure in cui si desiderano archiviare i dati replicati.
7. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure create dopo il failover.
8. Selezionare **Configura ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Selezionare **Configura in seguito** se si intende selezionare la rete di Azure separatamente per ogni macchina.
9. In **Computer fisici** fare clic su **+Computer fisico**. Specificare il nome, l'indirizzo IP e il tipo di sistema operativo di ciascun computer che si desidera replicare.

    - Usare l'indirizzo IP interno del computer.
    - Se si specifica l'indirizzo IP pubblico, la replica potrebbe non funzionare come previsto.

10. In **Proprietà**  >  **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. In **Impostazioni replica**  >  **configurare le impostazioni di replica** verificare che siano selezionati i criteri di replica corretti.
12. Fare clic su **Abilita la replica**.
13. Tenere traccia dello stato di avanzamento del processo di **Abilitazione della protezione** in **Impostazioni**  >  **processi**  >  **Site Recovery processi**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

> [!NOTE]
> Quando viene abilitata la replica per una macchina virtuale, Site Recovery installa il servizio Mobility.
>
> Le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.
>
> Per eseguire il monitoraggio delle macchine virtuali aggiunte, è possibile controllare l'ora dell'ultima individuazione di macchine virtuali in **Server di configurazione** > **Ora ultimo contatto**. Per aggiungere macchine virtuali senza attendere l'individuazione pianificata, evidenziare il server di configurazione senza selezionarlo e quindi selezionare **Aggiorna**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Passaggio 6: Eseguire un'analisi del ripristino di emergenza

Eseguire un failover di test in Azure per verificare che tutto funzioni correttamente. Tale failover non influisce sull'ambiente di produzione.

### <a name="verify-machine-properties"></a>Verificare le proprietà del computer

Prima di eseguire un failover di test, verificare le proprietà del computer e assicurarsi che siano conformi ai [requisiti di Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). È possibile visualizzare e modificare le proprietà come indicato di seguito:

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** modificare le impostazioni in base alle esigenze.

    - È possibile modificare il nome della macchina virtuale di Azure, il gruppo di risorse, le dimensioni di destinazione, i [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le impostazioni del disco gestito.
    - È anche possibile visualizzare e modificare le impostazioni di rete. Tali impostazioni includono la rete/subnet a cui è stata aggiunta la macchina virtuale di Azure dopo il failover e l'indirizzo IP che verrà assegnato alla macchina virtuale.
1. In **Dischi** visualizzare le informazioni sul sistema operativo e sui dischi dati della macchina virtuale.


### <a name="run-a-test-failover"></a>Eseguire un failover di test

Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. Il failover elabora i dati usando il punto di ripristino specificato:
    - **Elaborato più recente**: il computer esegue il failover sull'ultimo punto di ripristino elaborato da Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
    - **Coerente con l'app più recente**: il computer esegue il failover al punto di ripristino coerente con l'app più recente.
    - **Personalizzata**: selezionare il punto di ripristino usato per il failover.

3. Viene creata una macchina virtuale di Azure usando i dati elaborati.
4. Il failover di test può pulire automaticamente le macchine virtuali di Azure create durante l'analisi.

Eseguire un failover di test per una macchina virtuale come indicato di seguito:

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **+Failover di test**.
2. Per questa procedura dettagliata, viene scelto di usare il punto di ripristino con valore **Elaborato più recente**.
3. In **Failover di test** selezionare la rete Azure di destinazione.
4. Fare clic su **OK** per iniziare il failover.
5. Per tenere traccia dell'avanzamento, fare clic sulla macchina virtuale per visualizzarne le proprietà. In alternativa, fare clic sul processo **Failover di test** in *Nome dell'insieme di credenziali* > **Impostazioni** > **Processi** >**Processi di Site Recovery**.
6. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate e che sia connessa alla rete corretta e in esecuzione.
7. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)
8. Per eliminare le macchine virtuali di Azure create durante il failover di test, fare clic su **Pulisci failover di test** nella VM. In **Note** salvare le osservazioni associate al failover di test.

## <a name="fail-over-and-fail-back"></a>Effettuare il failover e il failback

Dopo aver configurato la replica ed eseguito un'analisi per verificare che tutto funzioni correttamente, è possibile eseguire il failover dei computer in Azure in base alle esigenze.

Prima di eseguire un failover, se si intende connettersi al computer in Azure dopo il failover, [preparare la connessione](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) prima di iniziare.

Eseguire quindi un failover come indicato di seguito:


1. In **Impostazioni**  >  **elementi replicati** fare clic sul computer > **failover**.
2. Selezionare il punto di ripristino da utilizzare.
3. In **Failover di test** selezionare la rete Azure di destinazione.
4. Selezionare **Arrestare la macchina prima di iniziare il failover**. Con questa impostazione, Site Recovery tenta di arrestare la macchina di origine prima di avviare il failover. Il failover tuttavia continua anche se l'arresto ha esito negativo.
5. Fare clic su **OK** per iniziare il failover. Nella pagina **Processi** è possibile seguire lo stato del failover.
6. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Se è stata preparata la connessione dopo il failover, verificare che la macchina virtuale sia delle dimensioni appropriate, connessa alla rete corretta e in esecuzione.
7. Dopo aver verificato la macchina virtuale, fare clic su **Commit** per completare il failover. In questo modo tutti i punti di ripristino disponibili vengono eliminati.

> [!WARNING]
> Non annullare un failover in corso: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.


### <a name="fail-back-to-azure-stack"></a>Eseguire il failback in Azure Stack

Quando il sito primario è operativo, è possibile eseguire il failback da Azure ad Azure Stack. A tale scopo, seguire i passaggi elencati [qui](/azure-stack/operator/site-recovery-failback).

## <a name="conclusion"></a>Conclusione

In questo articolo le macchine virtuali di Azure Stack sono state replicate in Azure. Dopo che la replica è stata completata, è stato eseguito un ripristino di emergenza per verificare che il failover in Azure funzioni come previsto. L'articolo contiene anche i passaggi per eseguire un failover completo in Azure e per eseguire il failback in Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

Dopo il failback, è possibile proteggere nuovamente la macchina virtuale e avviare ancora la replica in Azure. A tale scopo, ripetere i passaggi descritti in questo articolo.