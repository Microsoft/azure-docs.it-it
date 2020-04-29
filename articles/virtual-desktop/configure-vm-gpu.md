---
title: Configurare GPU per desktop virtuale Windows-Azure
description: Come abilitare il rendering e la codifica con accelerazione GPU nel desktop virtuale di Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767035"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configura accelerazione GPU (Graphics Processing Unit) per desktop virtuale Windows

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU per migliorare le prestazioni e la scalabilità delle app. L'accelerazione GPU è particolarmente importante per le app a elevato utilizzo di grafica.

Seguire le istruzioni riportate in questo articolo per creare una macchina virtuale di Azure ottimizzata per la GPU, aggiungerla al pool host e configurarla per l'uso dell'accelerazione GPU per il rendering e la codifica. Questo articolo presuppone che sia già stato configurato un tenant di desktop virtuali Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selezionare una dimensione della macchina virtuale di Azure ottimizzata per la GPU

Azure offre una serie di [dimensioni di macchine virtuali ottimizzate per GPU](/azure/virtual-machines/windows/sizes-gpu). La scelta corretta per il pool host dipende da diversi fattori, tra cui i carichi di lavoro di app specifici, la qualità desiderata dell'esperienza utente e i costi. In generale, le GPU più grandi e più idonee offrono un'esperienza utente migliore a una determinata densità utente.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Creare un pool host, effettuare il provisioning della macchina virtuale e configurare un gruppo di app

Creare un nuovo pool host usando una macchina virtuale di dimensioni selezionate. Per istruzioni, vedere [esercitazione: creare un pool di host con Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Desktop virtuale Windows supporta il rendering e la codifica con accelerazione GPU nei sistemi operativi seguenti:

* Windows 10 versione 1511 o successiva
* Windows Server 2016 o versioni successive

È anche necessario configurare un gruppo di app o usare il gruppo di app desktop predefinito (denominato "gruppo di applicazioni desktop") che viene creato automaticamente quando si crea un nuovo pool host. Per istruzioni, vedere [esercitazione: gestire i gruppi di app per desktop virtuale di Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installare i driver grafici supportati nella macchina virtuale

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure in desktop virtuali Windows, è necessario installare i driver grafici appropriati. Seguire le istruzioni disponibili in [sistemi operativi e driver supportati](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) per installare i driver dal fornitore di grafica appropriato, manualmente o usando un'estensione di VM di Azure.

Per desktop virtuale Windows sono supportati solo i driver distribuiti da Azure. Per le macchine virtuali di Azure con GPU NVIDIA sono supportati solo [driver NVIDIA Grid](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) per desktop virtuale di Windows.

Dopo l'installazione del driver, è necessario riavviare la macchina virtuale. Utilizzare i passaggi di verifica nelle istruzioni sopra indicate per verificare che i driver grafici siano stati installati correttamente.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurare il rendering delle app con accelerazione GPU

Per impostazione predefinita, le app e i desktop eseguiti in configurazioni con più sessioni vengono sottoposti a rendering con la CPU e non sfruttano le GPU disponibili per il rendering. Configurare Criteri di gruppo per l'host sessione per abilitare il rendering con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando un account con privilegi di amministratore locale.
2. Aprire il menu Start e digitare "gpedit. msc" per aprire l'Editor Criteri di gruppo.
3. Spostarsi nell'albero a **configurazione** > computer**modelli amministrativi** > **componenti** > di Windows**Servizi Desktop remoto** > **host sessione Desktop remoto** > **ambiente sessione remota**.
4. Selezionare criterio **usare la scheda grafica predefinita hardware per tutte le sessioni di Servizi Desktop remoto** e impostare questo criterio su **abilitato** per abilitare il rendering della GPU nella sessione remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurare la codifica del frame con accelerazione GPU

Desktop remoto codifica tutti i grafici sottoposti a rendering dalle app e dai desktop (se sottoposti a rendering con GPU o CPU) per la trasmissione ai client Desktop remoto. Per impostazione predefinita, Desktop remoto non sfrutta le GPU disponibili per questa codifica. Configurare Criteri di gruppo per l'host sessione per abilitare la codifica dei frame con accelerazione GPU. Continuando la procedura precedente:

1. Selezionare criterio **assegnare priorità alla modalità grafica h. 264/avc 444 per le connessioni Desktop remoto** e impostare questo criterio su **abilitato** per forzare il codec h. 264/AVC 444 nella sessione remota.
2. Selezionare criteri **configurare la codifica hardware H. 264/AVC per le connessioni Desktop remoto** e impostare questo criterio su **abilitato** per abilitare la codifica hardware per AVC/H. 264 nella sessione remota.

    >[!NOTE]
    >In Windows Server 2016, l'opzione set **preferisce la codifica hardware AVC** per **provare sempre**.

3. Ora che i criteri di gruppo sono stati modificati, forzare un aggiornamento di criteri di gruppo. Aprire il prompt dei comandi e digitare:

    ```batch
    gpupdate.exe /force
    ```

4. Disconnettersi dalla sessione di Desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificare il rendering delle app con accelerazione GPU

Per verificare che le app stiano usando la GPU per il rendering, provare a eseguire una delle operazioni seguenti:

* Per le macchine virtuali di Azure con una GPU NVIDIA `nvidia-smi` , usare l'utilità come descritto in [verificare l'installazione del driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) per verificare l'utilizzo della GPU quando si eseguono le app.
* Nelle versioni supportate del sistema operativo, è possibile utilizzare Gestione attività per verificare l'utilizzo della GPU. Selezionare la GPU nella scheda "prestazioni" per vedere se le app utilizzano la GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificare la codifica del frame con accelerazione GPU

Per verificare che Desktop remoto usi la codifica con accelerazione GPU:

1. Connettersi al desktop della macchina virtuale usando il client desktop virtuale di Windows.
2.  > Avviare il Visualizzatore eventi e passare al nodo seguente: **registri applicazioni e servizi****Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **operativo**
3. Per determinare se viene usata la codifica con accelerazione GPU, cercare l'ID evento 170. Se viene visualizzato "AVC hardware encoder Enabled: 1", viene usata la codifica GPU.
4. Per determinare se viene utilizzata la modalità AVC 444, cercare l'ID evento 162. Se viene visualizzato "AVC disponibile: 1 profilo iniziale: 2048", viene usato AVC 444.

## <a name="next-steps"></a>Passaggi successivi

Queste istruzioni dovrebbero essere in esecuzione con l'accelerazione della GPU in un host di sessione (una macchina virtuale). Alcune considerazioni aggiuntive per l'abilitazione dell'accelerazione GPU in un pool host più ampio:

* Prendere in considerazione l'uso di un' [estensione VM](/azure/virtual-machines/extensions/overview) per semplificare l'installazione e gli aggiornamenti dei driver in diverse macchine virtuali. Utilizzare l' [estensione driver GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) per le macchine virtuali con GPU NVIDIA e utilizzare l'estensione driver GPU AMD (disponibile a breve) per le macchine virtuali con GPU AMD.
* Si consiglia di usare Active Directory Criteri di gruppo per semplificare la configurazione di criteri di gruppo in diverse macchine virtuali. Per informazioni sulla distribuzione di Criteri di gruppo nel dominio Active Directory, vedere [utilizzo di oggetti di criteri di gruppo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
