---
title: Creazione di un'immagine di macchina virtuale per Azure Marketplace | Documentazione Microsoft
description: Istruzioni dettagliate su come creare un'immagine di macchina virtuale per Azure Marketplace acquistabile dagli utenti.
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 8f87f2c0caccded5b0f24052deee6cefaad08013
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Guida alla creazione di un'immagine di macchina virtuale per Azure Marketplace
Questo articolo, **Passaggio 2**, illustra la preparazione di dischi rigidi virtuali (VHD) da distribuire in Azure Marketplace. I dischi rigidi virtuali costituiscono la base dello SKU. Il processo varia a seconda che si stia offrendo uno SKU basato su Linux o su Windows. In questo articolo vengono descritti entrambi gli scenari. Questo processo può essere eseguito parallelamente alla [creazione e registrazione dell'account][link-acct-creation].

## <a name="1-define-offers-and-skus"></a>1. Definire le offerte e gli SKU
Questa sezione illustra come definire le offerte e i relativi SKU associati.

Un'offerta è l'elemento "padre" di tutti i relativi SKU. È possibile disporre di più offerte. Il modo in cui si sceglie di strutturarle è assolutamente personale. Quando un'offerta passa all'ambiente di staging, passano a tale fase anche tutti i relativi SKU. Considerare attentamente gli identificatori degli SKU, perché saranno visibili nell'URL:

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Portale di anteprima di Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

SKU è il nome commerciale per un'immagine di macchina virtuale. Un'immagine di macchina virtuale contiene un disco del sistema operativo e zero o più dischi dati. Si tratta essenzialmente del profilo di archiviazione completo per una macchina virtuale. È necessario un disco rigido virtuale per ogni disco. È necessario crearne uno anche per i dischi dati vuoti.

Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per lo SKU. In fase di distribuzione i clienti non possono rimuovere i dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione se necessario.

> [!IMPORTANT]
> **Non modificare il numero di dischi in una nuova versione dell'immagine.** Se è necessario riconfigurare i dischi dati nell'immagine, definire un nuovo SKU. La pubblicazione di una nuova versione dell'immagine con un numero di dischi diverso può compromettere le attività di sviluppo basate sulla nuova versione dell'immagine in caso di scalabilità automatica, distribuzione automatica di soluzioni tramite modelli di Azure Resource Manager e altri scenari.
>
>

### <a name="11-add-an-offer"></a>1.1 Aggiungere un'offerta
1. Accedere al [portale di pubblicazione][link-pubportal] usando l'account venditore.
2. Selezionare la scheda **Macchine virtuali** del portale di pubblicazione. Immettere il nome dell'offerta nel campo visualizzato. Il nome dell'offerta corrisponde in genere al nome del prodotto o servizio da vendere in Azure Marketplace.
3. Selezionare **Crea**.

### <a name="12-define-a-sku"></a>1.2 Definire uno SKU
Dopo aver aggiunto l'offerta, è necessario definire e identificare gli SKU. È possibile avere più offerte e ogni offerta può includere più SKU. Quando un'offerta passa all'ambiente di staging, passano a tale fase anche tutti i relativi SKU.

1. **Aggiungere uno SKU.** Per lo SKU è necessario un identificatore, che viene usato nell'URL. L'identificatore deve essere univoco nel profilo di pubblicazione, ma non vi è alcun rischio di conflitto tra identificatori con altri editori.

   > [!NOTE]
   > L'identificatore dell'offerta e quello dello SKU vengono visualizzati nell'URL dell'offerta nel Marketplace.
   >
   >
2. **Aggiungere una descrizione di riepilogo per lo SKU.** Le descrizioni di riepilogo sono visibili ai clienti, è quindi consigliabile che siano facili da leggere. Non è necessario bloccare queste informazioni fino al raggiungimento della fase "Passa a gestione temporanea". Fino a quel momento, è possibile modificarle liberamente.
3. Se si usano SKU basati su Windows, seguire i collegamenti consigliati per acquistare le versioni approvate di Windows Server.

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2. Creare un VHD compatibile con Azure (basato su Linux)
Questa sezione è incentrata sulle procedure consigliate per la creazione di un'immagine di macchina virtuale basata su Linux per Azure Marketplace. Per una procedura dettagliata, fare riferimento alla documentazione seguente: [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>3. Creare un VHD compatibile con Azure (basato su Windows)
Questa sezione è incentrata sui passaggi necessari per creare uno SKU basato su Windows Server per Azure Marketplace.

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 Assicurarsi di usare i VHD di base corretti
Il VHD del sistema operativo per l'immagine di macchina virtuale deve essere basato su un'immagine di base approvata per Azure contenente Windows Server o SQL Server.

Per iniziare, creare una macchina virtuale da una delle immagini seguenti, disponibili nel [portale di Microsoft Azure][link-azure-portal]:

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Questi collegamenti sono disponibili anche nella pagina degli SKU del portale di pubblicazione.

> [!TIP]
> Se si usa l'attuale portale di Azure o PowerShell, sono approvate le immagini di Windows Server pubblicate a partire dall'8 settembre 2014.
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Creare la macchina virtuale basata su Windows
Con pochi semplici passaggi, dal portale di Microsoft Azure è possibile creare la macchina virtuale basata sull'immagine di base approvata. Di seguito viene illustrata una panoramica del processo.

1. Nella pagina delle immagini di base selezionare **Crea macchina virtuale** per essere reindirizzati al nuovo [portale di Microsoft Azure][link-azure-portal].

    ![disegno][img-acom-1]
2. Accedere al portale con l'account Microsoft e la password per la sottoscrizione di Azure che si vuole usare.
3. Seguire le istruzioni per la creazione di una macchina virtuale usando l'immagine di base selezionata. È necessario specificare un nome host (nome del computer), un nome utente (registrato come amministratore) e una password per la macchina virtuale.

    ![disegno][img-portal-vm-create]
4. Selezionare la dimensione della macchina virtuale da distribuire:

    a.    Se si prevede di sviluppare il VHD in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.

    b.    Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

    c.    Per informazioni sui prezzi, vedere il selettore del **piano tariffario consigliato** visualizzato nel portale. Verranno indicate le tre dimensioni consigliate, specificate dall'editore. In questo caso, l'editore è Microsoft.

    ![disegno][img-portal-vm-size]
5. Impostare le proprietà:

    a.    Per una distribuzione rapida, è possibile mantenere i valori predefiniti per le proprietà in **Configurazione facoltativa** e **Gruppo di risorse**.

    b.    Facoltativamente, in **Account di archiviazione**è possibile selezionare l'account di archiviazione in cui archiviare il VHD del sistema operativo.

    c.    Facoltativamente, in **Gruppo di risorse**è possibile selezionare il gruppo logico in cui inserire la macchina virtuale.
6. Selezionare la **Località** in cui eseguire la distribuzione:

    a.    Se si prevede di sviluppare il VHD in locale, la località non è rilevante, perché l'immagine verrà caricata in Azure successivamente.

    b.    Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle aree di Microsoft Azure negli Stati Uniti sin dall'inizio. In questo modo, è possibile accelerare il processo di copia del VHD eseguito da Microsoft quando si invia l'immagine per la certificazione.

    ![disegno][img-portal-vm-location]
7. Fare clic su **Crea**. Viene avviata la distribuzione della macchina virtuale. La distribuzione verrà completata in pochi minuti e sarà possibile iniziare a creare l'immagine per lo SKU.

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 Sviluppare il VHD nel cloud
È consigliabile sviluppare il VHD nel cloud usando Remote Desktop Protocol (RDP). Per connettersi a RDP, usare il nome utente e la password specificati durante il provisioning.

> [!IMPORTANT]
> **Non utilizzare dischi managed.** La virutal machine utilizzata per sviluppare il VHD nel cloud non deve avere essere basata su dischi managed in quanto al momento non è supportata la creazione di una immagine a partire da essi.

Creando la virtual machine nelle optional feature cambiare il default per i dischi managed.   
>
>

> [!IMPORTANT]
> Se si sviluppa il VHD in locale (scelta non consigliata), vedere l'articolo relativo alla [creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md). Il download del VHD non è necessario se lo sviluppo viene eseguito nel cloud.
>
>

**Connettersi tramite RDP usando il [portale di Microsoft Azure][link-azure-portal]**

1. Selezionare **Sfoglia** > **Macchine virtuali**.
2. Verrà visualizzato il pannello Macchine virtuali. Assicurarsi che la macchina virtuale a cui connettersi sia in esecuzione e selezionarla nell'elenco delle macchine virtuali distribuite.
3. Viene visualizzato un pannello contenente la descrizione della macchina virtuale selezionata. Nella parte superiore fare clic su **Connetti**.
4. Verrà richiesto di immettere il nome utente e la password specificati durante il provisioning.

**Connettersi tramite RDP usando PowerShell**

Per scaricare un file desktop remoto in una macchina locale, usare il [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Per usare questo cmdlet, è necessario conoscere il nome del servizio e quello della macchina virtuale. Se la macchina virtuale è stata creata nel [portale di Microsoft Azure][link-azure-portal], queste informazioni sono disponibili nella sezione delle proprietà della macchina virtuale:

1. Nel portale di Microsoft Azure selezionare **Sfoglia** > **Macchine virtuali**.
2. Verrà visualizzato il pannello Macchine virtuali. Selezionare la macchina virtuale distribuita.
3. Viene visualizzato un pannello contenente la descrizione della macchina virtuale selezionata.
4. Fare clic su **Proprietà**.
5. La prima parte del nome di dominio è costituita dal nome del servizio. Il nome host è il nome della macchina virtuale.

    ![disegno][img-portal-vm-rdp]
6. Il cmdlet per scaricare il file RDP per la macchina virtuale creata nel desktop locale dell'amministratore è il seguente:

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Altre informazioni su RDP sono disponibili nell'articolo di MSDN [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Configurare una macchina virtuale e creare lo SKU**

Dopo aver scaricato il VHD del sistema operativo, usare Hyper-V e configurare una macchina virtuale per iniziare a creare lo SKU. I passaggi dettagliati sono disponibili nell'articolo di TechNet [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="34-choose-the-correct-vhd-size"></a>3.4 Scegliere la dimensione corretta per il VHD
Il VHD del sistema operativo Windows nell'immagine di macchina virtuale deve essere creato come VHD con formato fisso da 128 GB.  

Se la dimensione fisica è inferiore a 128 GB, il VHD deve essere di tipo sparse. Le immagini Windows e SQL Server di base fornite soddisfano già questi requisiti. Evitare di modificare il formato o la dimensione del VHD ottenuto.  

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilire le dimensioni dei dischi, tenere presente che i clienti non possono ridimensionare i VHD all'interno di un'immagine in fase di distribuzione. I VHD dei dischi dati devono essere creati come VHD con formato fisso, ma devono anche essere di tipo sparse. I dischi dati possono essere vuoti o contenere dati.

### <a name="35-install-the-latest-windows-patches"></a>3.5 Installare le patch per Windows più recenti
Le immagini di base contengono le patch più recenti fino al momento della data di pubblicazione. Prima di pubblicare il VHD del sistema operativo creato, assicurarsi che sia stato eseguito Windows Update e che siano stati installati i più recenti aggiornamenti della sicurezza contrassegnati come critici e importanti.

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 Eseguire eventuali configurazioni aggiuntive e pianificare le attività nel modo necessario
Se si rendono necessarie configurazioni aggiuntive, valutare l'opportunità di usare un'attività pianificata da eseguire all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione.

* È una procedura consigliata fare in modo che l'attività elimini se stessa al termine dell'esecuzione.
* Evitare di eseguire configurazioni su unità diverse da C o D, perché queste sono le uniche due unità la cui presenza è sempre garantita. L'unità C corrisponde al disco del sistema operativo e l'unità D al disco locale temporaneo.

### <a name="37-generalize-the-image"></a>3.7 Generalizzare l'immagine
Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. In altri termini, il VHD del sistema operativo deve essere generalizzato.

* Per Windows, l'immagine deve essere preparata con sysprep e non è possibile eseguire configurazioni che non supportano il comando **sysprep** .
* È possibile eseguire il comando seguente dalla directory %windir%\System32\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Le indicazioni su come preparare con sysprep il sistema operativo sono incluse in un passaggio dell'articolo di MSDN relativo a [Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="4-deploy-a-vm-from-your-vhds"></a>4. Distribuire una macchina virtuale dai VHD
Dopo aver caricato uno o più VHD (ovvero il VHD del sistema operativo generalizzato e zero o più VHD dei dischi dati) in un account di archiviazione di Azure, è possibile registrarli come immagine di macchina virtuale degli utenti ed eseguirne il test. Dal momento che il VHD del sistema operativo è generalizzato, non è possibile distribuire direttamente la macchina virtuale specificando l'URL del VHD.

Per altre informazioni sulle immagini di macchina virtuale, vedere i post di blog seguenti:

* [Immagine VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [Procedure di PowerShell per immagini VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Informazioni sulle immagini di macchine virtuali in Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>Configurare gli strumenti necessari, PowerShell e l'interfaccia della riga di comando di Azure
* [Come configurare PowerShell](/powershell/azure/overview)
* [Come configurare l'interfaccia della riga di comando](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 Creare un'immagine di macchina virtuale degli utenti
#### <a name="capture-vm"></a>Acquisire la macchina virtuale
Fare riferimento ai collegamenti seguenti per informazioni su come acquisire la macchina virtuale usando, rispettivamente, l'API, PowerShell e l'interfaccia della riga di comando di Azure.

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>Generalizzare l'immagine
Fare riferimento ai collegamenti seguenti per informazioni su come acquisire la macchina virtuale usando, rispettivamente, l'API, PowerShell e l'interfaccia della riga di comando di Azure.

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/windows/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 Distribuire una macchina virtuale da un'immagine di macchina virtuale degli utenti
Per distribuire una macchina virtuale da un'immagine di macchina virtuale degli utenti, è possibile usare l'attuale [portale di Azure](https://manage.windowsazure.com) o PowerShell.

**Distribuire una macchina virtuale dall'attuale portale di Azure**

1. Selezionare **Nuovo** > **Elabora** > **Macchina virtuale** > **Da raccolta**.

    ![disegno][img-manage-vm-new]
2. Passare a **Immagini personali**e selezionare l'immagine di macchina virtuale da cui distribuire una macchina virtuale.

   1. Selezionare con attenzione l'immagine, perché la visualizzazione **Immagini personali** contiene sia immagini del sistema operativo che immagini di macchina virtuale.
   2. Esaminando il numero di dischi è possibile determinare il tipo di immagine distribuita, perché la maggior parte delle immagini di macchina virtuale contiene più di un disco. Tuttavia, può comunque essere presente un'immagine di macchina virtuale con un solo disco del sistema operativo. In questo caso la voce **Numero di dischi** è impostata su 1.

      ![disegno][img-manage-vm-select]
3. Completare i passaggi della procedura guidata di creazione della macchina virtuale e specificare il nome, la dimensione e la posizione della macchina virtuale, nonché il nome utente e la password.

**Distribuire una macchina virtuale da PowerShell**

Per distribuire una macchina virtuale di grandi dimensioni dall'immagine di macchina virtuale generalizzata appena creata, è possibile usare i cmdlet seguenti.

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> Per maggiori informazioni, consultare l'articolo Risoluzione di problemi comuni incontrati durante la creazione del disco rigido virtuale.
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5. Ottenere la certificazione per l'immagine di macchina virtuale
Il passaggio successivo per la preparazione dell'immagine di macchina virtuale per Azure Marketplace consiste nell'ottenere la relativa certificazione.

Il processo include l'esecuzione di uno speciale strumento di certificazione, il caricamento dei risultati della verifica nel contenitore di Azure in cui si trovano i VHD, l'aggiunta di un'offerta, la definizione dello SKU e l'invio dell'immagine di macchina virtuale per la certificazione.

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Scaricare ed eseguire lo strumento di test della certificazione per Azure Certified
Lo strumento di certificazione viene eseguito su una macchina virtuale a sua volta in esecuzione, di cui è stato effettuato il provisioning dall'immagine di macchina virtuale degli utenti, per garantire che l'immagine di macchina virtuale sia compatibile con Microsoft Azure. Lo strumento verifica che siano stati osservati i requisiti e le indicazioni sulla preparazione del VHD. L'output dello strumento è un report compatibilità, che deve essere caricato nel portale di pubblicazione durante la richiesta di certificazione.

Lo strumento di certificazione può essere usato con macchine virtuali Windows e Linux. Lo strumento si connette alle macchine virtuali basate su Windows usando PowerShell e alle macchine virtuali Linux usando SSH.Net.

1. Scaricare prima di tutto lo strumento di certificazione dal [sito di download Microsoft][link-msft-download].
2. Aprire lo strumento di certificazione e fare clic sul pulsante **Start New Test** .
3. Nella schermata **Test Information** immettere un nome per l'esecuzione dei test.
4. Specificare se la macchina virtuale è su Linux o Windows. A seconda dell'opzione scelta, selezionare le opzioni successive.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Connettere lo strumento di certificazione a un'immagine di macchina virtuale Linux**
1. Selezionare la modalità di autenticazione SSH, ovvero password o file della chiave.
2. Se si usa l'autenticazione basata su password, immettere il nome DNS (Domain Name System), il nome utente e la password.
3. Se si usa l'autenticazione basata su file della chiave, immettere il nome DNS, il nome utente e il percorso della chiave privata.

   ![Autenticazione della password dell'immagine VM Linux][img-cert-vm-pswd-lnx]

   ![Autenticazione del file della chiave dell'immagine VM Linux][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connettere lo strumento di certificazione a un'immagine di macchina virtuale basata su Windows**
1. Immettere il nome DNS completo della macchina virtuale, ad esempio MyVMName.Cloudapp.net.
2. Immettere il nome utente e la password.

   ![Autenticazione della password dell'immagine VM Windows][img-cert-vm-pswd-win]

Dopo aver selezionato le opzioni corrette per l'immagine di macchina virtuale basata su Windows o Linux, fare clic su **Test Connection** per verificare che SSH.Net o PowerShell sia connesso correttamente a scopo di test. Dopo che è stata stabilita una connessione, fare clic su **Next** per avviare il test.

Al termine del test, si riceveranno i risultati (Pass/Fail/Warning) per ogni elemento del test.

![Test case per l'immagine VM Linux][img-cert-vm-test-lnx]

![Test case per l'immagine VM Windows][img-cert-vm-test-win]

Se uno dei test non viene superato, l'immagine non verrà certificata. In tal caso, riesaminare i requisiti e apportare le modifiche necessarie.

Dopo il test automatizzato, viene chiesto di fornire altre informazioni sull'immagine di macchina virtuale rispondendo a un questionario.  Rispondere alle domande e fare clic su **Next**.

![Questionario dello strumento di certificazione][img-cert-vm-questionnaire]

![Questionario dello strumento di certificazione][img-cert-vm-questionnaire-2]

Dopo aver completato il questionario, è possibile immettere informazioni aggiuntive, ad esempio le informazioni di accesso SSH per l'immagine di macchina virtuale Linux e una spiegazione per le eventuali valutazioni non superate. È possibile scaricare i risultati dei test e i file di log per i test case eseguiti oltre alle risposte date al questionario. Salvare i risultati nello stesso contenitore dei VHD.

![Salvare i risultati del test di certificazione][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 Ottenere l'URI di firma di accesso condiviso per le immagini di macchina virtuale
Durante il processo di pubblicazione vengono specificati gli URI (Uniform Resource Identifier) di ciascun VHD creato per lo SKU. Microsoft deve accedere a tali VHD durante il processo di certificazione. È quindi necessario creare un URI di firma di accesso condiviso per ogni VHD. Si tratta dell'URI che deve essere immesso nella scheda **Immagini** nel portale di pubblicazione.

L'URI di firma di accesso condiviso deve soddisfare i requisiti seguenti:

* Per la generazione degli URI di firma di accesso condiviso per i VHD, sono sufficienti le autorizzazioni List e Read­. Evitare di fornire accesso con autorizzazioni di scrittura o eliminazione.
* La durata dell'accesso deve essere di un minimo di tre (3) settimane dalla creazione dell'URI di firma di accesso condiviso.
* Per tenere conto dell'ora UTC, selezionare il giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2014 selezionare 5/10/2014.

L'URL SAS può essere generato in diversi modi per condividere il disco rigido virtuale per Azure Marketplace.
Di seguito vengono riportati tre strumenti consigliati:

1.  Azure Storage Explorer
2.  Microsoft Storage Explorer
3.  Interfaccia della riga di comando di Azure

**Azure Storage Explorer (scelta consigliata per gli utenti di Windows)**

Di seguito viene descritta la procedura per generare l'URL SAS tramite Azure Storage Explorer

1. Scaricare [Azure Storage Explorer 6 Anteprima 3](https://azurestorageexplorer.codeplex.com/) da CodePlex. Andare in [Azure Storage Explorer 6 Anteprima 3](https://azurestorageexplorer.codeplex.com/) e fare clic su **"Download"**.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. Scaricare [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) e installarlo dopo averlo decompresso.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. Al termine dell'installazione, aprire l'applicazione.
4. Fare clic su **Add Account**.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. Specificare il nome dell'account di archiviazione, la chiave dell'account di archiviazione e il dominio degli endpoint di archiviazione. Si tratta dell'account di archiviazione nella sottoscrizione di Azure in cui è stato conservato il disco rigido virtuale nel Portale di Azure.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Dopo aver eseguito la connessione di Azure Storage Explorer all'account di archiviazione specifico, questo mostrerà tutti i contenuti all'interno dell'account di archiviazione. Selezionare il contenitore in cui è stato copiato il file con estensione vhd del disco del sistema operativo (nonché i dischi dati se sono applicabili allo scenario).

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. Dopo aver selezionato il contenitore BLOB, i file all'interno del contenitore sono visibili in Azure Storage Explorer. Selezionare il file di immagine (vhd) da inviare.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.  Dopo aver selezionato il file con estensione vhd nel contenitore, fare clic sulla scheda **Security** .

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.  Nella finestra di dialogo **Blob Container Security** (Sicurezza del contenitore BLOB), mantenere le impostazioni predefinite nella scheda **Livello di accesso** e quindi fare clic sulla scheda **Shared Access Signatures** (Firme di accesso condiviso).

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10. Seguire questa procedura per generare un URI di firma di accesso condiviso per l'immagine con estensione vhd:

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **Access permitted from** (Accesso consentito dal): per tenere conto dell'ora UTC, selezionare il giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2014 selezionare 5/10/2014.

    b. **Access permitted to** (Accesso consentito fino al): selezionare una data che sia di almeno 3 settimane successiva alla data **Access permitted from** (Accesso consentito a partire dal).

    c. **Actions permitted** (Azioni consentite): selezionare le autorizzazioni **Elenco** e **Lettura**.

    d. Se il file VHD è stato selezionato correttamente, in **Blob name to access** viene visualizzata l'estensione ".vhd".

    e. Fare clic su **Generate Signature**.

    f. In **Generated Shared Access Signature URI of this container**verificare quanto segue, come evidenziato sopra.

       - Assicurarsi che il nome del file di immagine e del file con estensione **"vhd"** siano nell'URI.
       - Assicurarsi che **"=rl"** sia presente alla fine della firma. Questo indica che le autorizzazioni Read e List sono state fornite correttamente.
       - Assicurarsi che **"sr=c"** sia presente al centro della firma. Questo dimostra che l'utente dispone dell'accesso al livello contenitore

11. Per verificare il corretto funzionamento dell'URI di firma di accesso condiviso generato, fare clic su **Test in Browser**. Verrà avviato il processo di download.

12. Copiare l'URI di firma di accesso condiviso. Questo URI deve essere incollato nel portale di pubblicazione.

13. Ripetere i passaggi 6-10 per ogni disco rigido virtuale nello SKU.

**Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Di seguito viene descritta la procedura per generare l'URL SAS tramite Microsoft Azure Storage Explorer

1.  Scaricare Microsoft Azure Storage Explorer dal sito Web [http://storageexplorer.com/](http://storageexplorer.com/). Andare in [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) e fare clic su **"Download per Windows"**.

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.  Al termine dell'installazione, aprire l'applicazione.

3.  Fare clic su **Add Account**.

4.  Configurare Microsoft Azure Storage Explorer nella sottoscrizione effettuando l'accesso al proprio account

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.  Passare all'account di archiviazione e selezionare il contenitore

6.  Selezionare **"Get Share Access Signature.."** ("Richiedi firma di accesso condiviso...") facendo clic con il tasto destro del mouse sul **contenitore**

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.  Aggiornare la data di inizio, la scadenza e le autorizzazioni come indicato di seguito

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.  **Ora di inizio**: per tenere conto dell'ora UTC, selezionare il giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2014 selezionare 5/10/2014.

    b.  **Scadenza:** selezionare una data che sia di almeno 3 settimane successiva alla **data di inizio**.

    c.  **Autorizzazioni**: selezionare le autorizzazioni **Elenco** e **Lettura**

8.  Copiare l'URI della firma di accesso condiviso per il contenitore

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    L'URL SAS generato è per il livello contenitore e a questo punto è necessario aggiungere il nome del file con estensione vhd.

    Formato dell'URL SAS livello contenitore: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Inserire il nome del file con estensione vhd dopo il nome del contenitore nell'URL SAS come indicato di seguito `https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    Esempio:

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd è il nome del file con estensione vhd, pertanto l'URL SAS del file con estensione vhd sarà `https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - Assicurarsi che il nome del file di immagine e del file con estensione **"vhd"** siano nell'URI.
    - Assicurarsi che **"sp=rl"** sia presente al centro della firma. Questo indica che le autorizzazioni Read e List sono state fornite correttamente.
    - Assicurarsi che **"sr=c"** sia presente al centro della firma. Questo dimostra che l'utente dispone dell'accesso al livello contenitore

9.  Per verificare il corretto funzionamento dell'URI della firma di accesso condiviso generato, testarlo nel browser. Verrà avviato il processo di download

10. Copiare l'URI di firma di accesso condiviso. Questo URI deve essere incollato nel portale di pubblicazione.

11. Ripetere questi passaggi per ogni VHD nello SKU.

**Interfaccia della riga di comando di Azure (scelta consigliata per integrazione continua e utenti non Windows)**

Di seguito viene descritta la procedura per generare l'URL SAS tramite l'interfaccia della riga di comando di Azure

1.  Scaricare l'interfaccia della riga di comando di Azure da [qui](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/). Esistono diversi link per **[Windows](http://aka.ms/webpi-azure-cli)** e **[MAC OS](http://aka.ms/mac-azure-cli)**.

2.  Dopo averlo scaricato, installarlo

3.  Creare un file di PowerShell con il codice seguente e salvarlo in locale

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    Aggiornare i parametri seguenti nella schermata precedente

    a. **`<StorageAccountName>`**: per dare un nome all'account di archiviazione

    b. **`<Storage Account Key>`**: per dare una chiave all'account di archiviazione

    c. **`<Permission Start Date>`**: per tenere conto dell'ora UTC, selezionare il giorno prima della data corrente. Ad esempio, se la data corrente è il 26 ottobre 2016, selezionare il 25/10/2016

    d. **`<Permission End Date>`**: selezionare una data che sia di almeno 3 settimane successiva alla **data di inizio**. Il valore quindi sarà **02/11/2016**.

    Di seguito viene riportato il codice di esempio dopo aver aggiornato i parametri appropriati

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.  Aprire l'editor di Powershell con la modalità "Esegui come amministratore" e aprire i file nel passaggio #3.

5.  Eseguire lo script che fornirà l'URL SAS per l'accesso a livello di contenitore

    Di seguito viene riportato l'output della firma SAS, copiare la parte evidenziata nel blocco note

    ![disegno](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.  A questo punto viene visualizzato l'URL SAS del livello contenitore. È necessario aggiungere il nome del file con estensione vhd.

    URL SAS a livello di contenitore

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.  Inserire il nome del file con estensione vhd dopo il nome del contenitore nell'URL SAS come mostrato di seguito `https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    Esempio:

    TestRGVM201631920152.vhd è il nome del file con estensione vhd, pertanto l'URL SAS del file con estensione vhd sarà

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - Assicurarsi che il nome file di immagine e ".vhd" siano nell'URI.
    -   Assicurarsi che "sp=rl" sia presente al centro della firma. Questo indica che le autorizzazioni Read e List sono state fornite correttamente.
    -   Assicurarsi che "sr=c" sia presente al centro della firma. Questo dimostra che l'utente dispone dell'accesso al livello contenitore

8.  Per verificare il corretto funzionamento dell'URI della firma di accesso condiviso generato, testarlo nel browser. Verrà avviato il processo di download

9.  Copiare l'URI di firma di accesso condiviso. Questo URI deve essere incollato nel portale di pubblicazione.

10. Ripetere questi passaggi per ogni VHD nello SKU.


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 Fornire le informazioni sull'immagine di macchina virtuale e richiedere la certificazione nel portale di pubblicazione
Dopo aver creato l'offerta e lo SKU, è necessario immettere i dettagli relativi all'immagine associati allo SKU.

1. Accedere al [portale di pubblicazione][link-pubportal] usando l'account venditore.
2. Selezionare la scheda **Immagini VM** .
3. L'identificatore visualizzato nella parte superiore della pagina è in realtà l'identificatore dell'offerta e non quello dello SKU.
4. Impostare le proprietà nella sezione **SKU** .
5. In **Famiglia di sistemi operativi**selezionare il tipo di sistema operativo associato al VHD del sistema operativo.
6. Nella casella **Sistema operativo** descrivere il sistema operativo. Usare preferibilmente il formato famiglia di sistemi operativi, tipo, versione e aggiornamenti. Esempio: "Windows Server Datacenter 2014 R2".
7. Selezionare fino a sei dimensioni della macchina virtuale. Questi consigli vengono visualizzati dal cliente nel pannello Piano tariffario del portale di Azure quando sceglie di acquistare e distribuire l'immagine. **Si tratta esclusivamente di consigli. Il cliente può selezionare qualsiasi dimensione di macchina virtuale appropriata ai dischi specificati nell'immagine.**
8. Immettere la versione. Il campo della versione incapsula una versione semantica per identificare il prodotto e gli aggiornamenti.
   * Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi.
   * Le immagini in SKU diversi possono avere versioni principali e secondarie diverse.
   * Le versioni in uno SKU dovrebbero essere solo modifiche incrementali, che aumentano la versione della patch (da X.Y.Z a Z).
9. In **URL VHD sistema operativo** immettere l'URI di firma di accesso condiviso creato per il VHD del sistema operativo.
10. Se allo SKU sono associati dischi dati, selezionare il numero di unità logica (LUN) su cui deve essere montato il disco dati in fase di distribuzione.
11. In **URL VHD LUN X** immettere l'URI di firma di accesso condiviso creato per il primo VHD dei dati.

    ![disegno](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>Problemi e correzioni comuni dell'URL SAS

|Problema|Messaggio di errore|Correzione|Link alla documentazione|
|---|---|---|---|
|Errore durante la copia di immagini: "?" non è presente nell'URL SAS|Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.|Aggiornare l'URL SAS con gli strumenti consigliati|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Errore durante la copia di immagini: parametri "st" e "se" non presenti nell'URL SAS|Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.|Aggiornare le date di inizio e fine nell'URL SAS|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Errore durante la copia di immagini: "sp=rl" non è presente nell'URL SAS|Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS|Aggiornare l'URL SAS con le autorizzazioni impostate come "Lettura" ed "Elenco"|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Errore nella copia di immagini: l'URL SAS presenta spazi vuoti nel nome del file con estensione vhd|Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.|Aggiornare l'URL SAS eliminando gli spazi vuoti|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|Errore nella copia di immagini: errore di autorizzazione URL SAS|Errore: copia di immagini. Non è possibile scaricare il BLOB a causa dell'errore di autorizzazione|Rigenerare l'URL SAS|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|


## <a name="next-step"></a>Passaggio successivo
Dopo aver specificato i dettagli dello SKU, passare alla [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging]. In questo passaggio del processo di pubblicazione vengono forniti i contenuti marketing, i prezzi e le altre informazioni necessarie prima di continuare con il **Passaggio 3: Test dell'offerta di macchina virtuale nell'ambiente di staging**, dove vengono testati diversi scenari di casi d'uso prima di distribuire l'offerta in Azure Marketplace per la visibilità pubblica e l'acquisto.  

## <a name="see-also"></a>Vedere anche
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]:../storage/blobs/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]:../storage/common/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/

