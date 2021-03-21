---
title: Configurare la GPU per Desktop virtuale Windows - Azure
description: Come abilitare il rendering e la codifica con accelerazione GPU in Desktop virtuale Windows.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: c3a23276ce19f6d7b4cf341bac155ec84363fe5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018342"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configura l'accelerazione GPU (Graphics Processing Unit) per Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU per migliorare le prestazioni e la scalabilità delle app. L'accelerazione GPU è particolarmente importante per le app a elevato utilizzo di grafica.

Per creare una macchina virtuale di Azure ottimizzata per la GPU, aggiungerla al pool di host e configurarla per l'uso dell'accelerazione GPU per il rendering e la codifica, seguire le istruzioni riportate in questo articolo. Questo articolo presuppone che sia già stato configurato un tenant di Desktop virtuale Windows.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Selezionare le dimensioni di macchina virtuale di Azure ottimizzate per la GPU appropriate

Selezionare una delle macchine virtuali serie [NV](../virtual-machines/nv-series.md)di Azure, serie [NVv3](../virtual-machines/nvv3-series.md)o [NVv4](../virtual-machines/nvv4-series.md) . Queste soluzioni sono personalizzate per la virtualizzazione di app e desktop e consentono l'accelerazione della GPU per le app e l'interfaccia utente di Windows. La scelta corretta per il pool di host dipende da diversi fattori, tra cui i carichi di lavoro di app specifici, la qualità desiderata dell'esperienza utente e i costi. In generale, le GPU più grandi e più idonee offrono un'esperienza utente migliore a una determinata densità di utenti, mentre le dimensioni delle GPU più piccole e frazionarie consentono un controllo più granulare su costi e qualità.

>[!NOTE]
>Le macchine virtuali della serie NC, NCv2, NCv3 e NDv2 di Azure non sono in genere appropriate per gli host sessione desktop virtuale di Windows. Queste macchine virtuali sono personalizzate per strumenti di calcolo o di apprendimento automatico specializzati, ad alte prestazioni, come quelli compilati con NVIDIA CUDA. L'accelerazione generale di app e desktop con GPU NVIDIA richiede la gestione delle licenze NVIDIA GRID; Questa operazione viene fornita da Azure per le dimensioni di VM consigliate, ma deve essere disposta separatamente per le macchine virtuali della serie NC/ND.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creare un pool di host, effettuare il provisioning della macchina virtuale e configurare un gruppo di app

Creare un nuovo pool di host usando una macchina virtuale delle dimensioni selezionate. Per istruzioni, vedere [Esercitazione: Creare un pool di host con il portale di Azure](./create-host-pools-azure-marketplace.md).

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU nei sistemi operativi seguenti:

* Windows 10 versione 1511 o successive
* Windows Server 2016 o versioni successive

È anche necessario configurare un gruppo di app o usare il gruppo di app desktop predefinito (denominato "gruppo di applicazioni desktop") che viene creato automaticamente quando si crea un nuovo pool di host. Per istruzioni, vedere [Esercitazione: Gestire gruppi di app per Desktop virtuale Windows](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installare i driver grafici supportati nella macchina virtuale

Per sfruttare i vantaggi offerti dalle funzionalità della GPU delle macchine virtuali serie N di Azure in Desktop virtuale Windows, è necessario installare i driver grafici appropriati. Seguire le istruzioni in [Sistemi operativi e driver supportati](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) per installare i driver dal fornitore di grafica appropriato, manualmente o usando un'estensione della macchina virtuale di Azure.

Per Desktop virtuale Windows sono supportati solo i driver distribuiti da Azure. Per le macchine virtuali della serie NV di Azure con GPU NVIDIA, solo [i driver NVIDIA Grid](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)e non i driver NVIDIA Tesla (CUDA) supportano l'accelerazione GPU per app e desktop di uso generale.

Dopo l'installazione, è necessario eseguire il riavvio della macchina virtuale. Per verificare che i driver grafici siano stati installati correttamente, usare i passaggi di verifica nelle istruzioni sopra indicate.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurare il rendering delle app con accelerazione GPU

Per impostazione predefinita, le app e i desktop eseguiti in configurazioni con più sessioni vengono sottoposti a rendering con la CPU e non sfruttano le GPU disponibili per il rendering. Configurare Criteri di gruppo per l'host di sessione per abilitare il rendering con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando un account con privilegi di amministratore locale.
2. Aprire il menu Start e digitare "gpedit.msc" per aprire l'Editor Criteri di gruppo.
3. Spostarsi nell'albero **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows** > **Servizi Desktop remoto** > **Host sessione Desktop remoto** > **Ambiente sessione remota**.
4. Selezionare criteri **usa schede grafiche hardware per tutte le sessioni di Servizi Desktop remoto** e impostare questo criterio su **abilitato** per abilitare il rendering GPU nella sessione remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurare la codifica del frame con accelerazione GPU

Desktop remoto codifica tutti i grafici sottoposti a rendering dalle app e dai desktop (se sottoposti a rendering con GPU o CPU) per la trasmissione ai client Desktop remoto. Quando una parte della schermata viene aggiornata di frequente, questa parte dello schermo viene codificata con un codec video (H. 264/AVC). Per impostazione predefinita, Desktop remoto non sfrutta le GPU disponibili per questa codifica. Configurare Criteri di gruppo per l'host di sessione, per abilitare la codifica del frame con accelerazione GPU. Continuando la procedura precedente:

>[!NOTE]
>La codifica del frame con accelerazione GPU non è disponibile nelle macchine virtuali della serie NVv4.

1. Selezionare il criterio **Configura codifica hardware H.264/AVC per le connessioni Desktop remoto** e impostarlo su **Abilitato** per abilitare la codifica hardware per AVC/H.264 nella sessione remota.

    >[!NOTE]
    >In Windows Server 2016, impostare l'opzione **Preferisci la codifica hardware AVC** su **Tenta sempre**.

2. Ora che i criteri di gruppo sono stati modificati, forzare un aggiornamento dei criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```cmd
    gpupdate.exe /force
    ```

3. Disconnettersi dalla sessione Desktop remoto.

## <a name="configure-fullscreen-video-encoding"></a>Configurare la codifica video a schermo intero

Se si usano spesso applicazioni che producono contenuti con frequenza elevata, ad esempio la modellazione 3D, le applicazioni CAD/CAM e video, è possibile scegliere di abilitare una codifica video a schermo intero per una sessione remota. Il profilo video a schermo intero offre una frequenza dei fotogrammi più elevata e un'esperienza utente migliore per tali applicazioni a scapito della larghezza di banda di rete e delle risorse client e host della sessione. È consigliabile usare la codifica dei frame con accelerazione GPU per una codifica video a schermo intero. Configurare Criteri di gruppo per l'host sessione per abilitare la codifica video a schermo intero. Continuando la procedura precedente:

1. Selezionare il criterio **Assegna priorità alla modalità grafica H.264/AVC 444 per le connessioni Desktop remoto** e impostarlo su **Abilitato**, per forzare il codec H.264/AVC 444 nella sessione remota.
2. Ora che i criteri di gruppo sono stati modificati, forzare un aggiornamento dei criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```cmd
    gpupdate.exe /force
    ```

3. Disconnettersi dalla sessione Desktop remoto.
## <a name="verify-gpu-accelerated-app-rendering"></a>Verificare il rendering delle app con accelerazione GPU

Per verificare che le app stiano usando la GPU per il rendering, provare a eseguire una delle operazioni seguenti:

* Per le macchine virtuali di Azure con una GPU NVIDIA, usare l' `nvidia-smi` utilità come descritto in [verificare l'installazione del driver](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) per verificare l'utilizzo della GPU quando si eseguono le app.
* Nelle versioni supportate del sistema operativo, è possibile usare Gestione attività, per verificare l'utilizzo della GPU. Per vedere se le app utilizzano la GPU, selezionare la GPU nella scheda "Prestazioni".

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificare la codifica del frame con accelerazione GPU

Per verificare che Desktop remoto usi la codifica con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando il client Desktop virtuale Windows.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operativo**
3. Per determinare se viene usata la codifica con accelerazione GPU, cercare l'ID evento 170. Se si visualizza "Codifica hardware AVC abilitata: 1 ", allora la codifica GPU viene usata.

## <a name="verify-fullscreen-video-encoding"></a>Verifica codifica video a schermo intero

Per verificare che Desktop remoto usi la codifica video a schermo intero:

1. Connettersi al desktop della macchina virtuale usando il client Desktop virtuale Windows.
2. Avviare il Visualizzatore eventi e passare al nodo seguente: **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operativo**
3. Per determinare se viene usata la codifica video a schermo intero, cercare l'ID evento 162. Se viene visualizzato "AVC disponibile: 1 Profilo iniziale: 2048 ", allora AVC 444 viene usata.

## <a name="next-steps"></a>Passaggi successivi

Queste istruzioni dovrebbero essere in esecuzione con l'accelerazione GPU in un host di sessione (una macchina virtuale). Alcune considerazioni aggiuntive per l'abilitazione dell'accelerazione GPU in un pool di host più ampio:

* Per semplificare l'installazione e gli aggiornamenti dei driver in diverse macchine virtuali, prendere in considerazione l'uso di un'[estensione della macchina virtuale](../virtual-machines/extensions/overview.md). Usare l'[estensione del driver GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-windows.md) per le macchine virtuali con GPU NVIDIA e usare l'[estensione del driver GPU AMD](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) per le macchine virtuali con GPU AMD.
* Per semplificare la configurazione dei criteri di gruppo in diverse macchine virtuali, prendere in considerazione l'uso di Criteri di gruppo di Active Directory. Per informazioni sulla distribuzione di Criteri di gruppo nel dominio Active Directory, vedere [Working with Group Policy Objects](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)) (Utilizzo degli oggetti Criteri di gruppo).