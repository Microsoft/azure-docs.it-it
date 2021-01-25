---
title: Installare l'aggiornamento nel dispositivo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come applicare gli aggiornamenti usando il portale di Azure e l'interfaccia utente Web locale per Azure Stack dispositivo GPU Pro Edge e il cluster Kubernetes nel dispositivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: 7db2d2721359d9796b19b3fd6abe32ff16beaec2
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761833"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Aggiornare la GPU di Azure Stack Edge Pro 

Questo articolo descrive i passaggi necessari per installare l'aggiornamento in Azure Stack Edge Pro con GPU tramite l'interfaccia utente Web locale e tramite l'portale di Azure. Si applicano gli aggiornamenti software o gli hotfix per proteggere il dispositivo Azure Stack Edge Pro e il cluster Kubernetes associato nel dispositivo aggiornato.

La procedura descritta in questo articolo è stata eseguita utilizzando una versione diversa del software, ma il processo rimane invariato per la versione del software corrente.

> [!IMPORTANT]
> - L'aggiornamento **2101** è l'aggiornamento corrente e corrisponde a:
>   - Versione del software del dispositivo: **2.2.1473.2521**
>   - Versione del server Kubernetes- **v 1.17.3**
>   - Versione IoT Edge: **0.1.0-beta10**
>    
>    Per informazioni sulle novità di questo aggiornamento, vedere [Note sulla versione](azure-stack-edge-gpu-2101-release-notes.md).
> - Per applicare l'aggiornamento 2101, il dispositivo deve eseguire 2010.
> - Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Questo aggiornamento contiene gli aggiornamenti del software del dispositivo e gli aggiornamenti di Kubernetes. Dato che il Azure Stack Edge Pro è un dispositivo a nodo singolo, tutte le attività di I/O in corso vengono interrotte e il dispositivo riscontra un tempo di inattività di un massimo di 1,5 ore per l'aggiornamento.

Per installare gli aggiornamenti nel dispositivo, è prima di tutto necessario configurare il percorso del server di aggiornamento. Dopo aver configurato il server di aggiornamento, è possibile applicare gli aggiornamenti tramite l'interfaccia utente di portale di Azure o l'interfaccia utente Web locale.

Ogni passaggio viene descritto nelle sezioni seguenti.

## <a name="configure-update-server"></a>Configurare il server di aggiornamento

1. Nell'interfaccia utente Web locale passare a **Configuration**  >  **Update Server**.
   
    ![Configurare gli aggiornamenti 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. In **Selezione tipo di server di aggiornamento**, dall'elenco a discesa, scegliere da Microsoft Update Server (impostazione predefinita) o Windows Server Update Services.  
   
    Se si aggiorna dalla Windows Server Update Services, specificare l'URI del server. Il server in corrispondenza dell'URI distribuirà gli aggiornamenti in tutti i dispositivi connessi a questo server.

    ![Configurare gli aggiornamenti 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Il server WSUS viene utilizzato per gestire e distribuire gli aggiornamenti tramite una console di gestione. Un server WSUS può anche fungere da origine degli aggiornamenti per altri server WSUS all'interno dell'organizzazione. Quando il server WSUS svolge questa funzione, viene chiamato server upstream. In un'implementazione di WSUS, almeno un server WSUS nella rete deve essere in grado di connettersi a Microsoft Update per ottenere informazioni sugli aggiornamenti disponibili. L'amministratore può stabilire, in base alla sicurezza e alla configurazione della rete, quanti altri server WSUS possono connettersi direttamente a Microsoft Update.
    
    Per ulteriori informazioni, vedere [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Si consiglia di installare gli aggiornamenti tramite il portale di Azure. Il dispositivo verifica automaticamente, una volta al giorno, la disponibilità di aggiornamenti. Quando gli aggiornamenti sono disponibili, viene visualizzata una notifica nel portale. È quindi possibile scaricare e installare gli aggiornamenti.

> [!NOTE]
> Verificare che il dispositivo sia integro e che lo stato venga visualizzato come **online** prima di procedere con l'installazione degli aggiornamenti.

1. Quando gli aggiornamenti sono disponibili per il dispositivo, viene visualizzata una notifica. Selezionare la notifica o la barra dei comandi superiore, **Aggiorna dispositivo**. Questo consentirà di applicare gli aggiornamenti software del dispositivo.

    ![Versione del software dopo l'aggiornamento](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Nel pannello **aggiornamenti del dispositivo** verificare di aver esaminato le condizioni di licenza associate alle nuove funzionalità nelle note sulla versione.

    È possibile scegliere di **scaricare e installare** gli aggiornamenti o semplicemente **scaricare** gli aggiornamenti. Sarà così possibile installarli in un secondo momento.

    ![Versione del software dopo l'aggiornamento 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Se si desidera scaricare e installare gli aggiornamenti, selezionare l'opzione installa automaticamente aggiornamenti al termine del download.

    ![Versione del software dopo l'aggiornamento 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Viene avviato il download degli aggiornamenti. Viene visualizzata una notifica che indica che il download è in corso.

    ![Versione del software dopo l'aggiornamento 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Nel portale di Azure viene visualizzato anche un banner di notifica. Indica lo stato del download.

    ![Versione del software dopo l'aggiornamento 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    È possibile selezionare questa notifica oppure selezionare **Aggiorna dispositivo** per visualizzare lo stato dettagliato dell'aggiornamento.

    ![Versione del software dopo l'aggiornamento 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Al termine del download, il banner di notifica viene aggiornato per indicare il completamento. Se si sceglie di scaricare e installare gli aggiornamenti, l'installazione verrà avviata automaticamente.

    ![Versione del software dopo l'aggiornamento 7](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Se si sceglie di scaricare solo gli aggiornamenti, selezionare la notifica per aprire il pannello **aggiornamenti del dispositivo** . Selezionare **Installa**.
  
    ![Versione del software dopo l'aggiornamento 8](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Viene visualizzata una notifica che indica che l'installazione è in corso.

    ![Versione del software dopo l'aggiornamento 9](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    Il portale Visualizza inoltre un avviso informativo per indicare che l'installazione è in corso. Il dispositivo viene portato offline ed è in modalità di manutenzione.
   
    ![Versione del software dopo l'aggiornamento 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Poiché si tratta di un dispositivo a 1 nodo, il dispositivo viene riavviato dopo l'installazione degli aggiornamenti. L'avviso critico durante il riavvio indica che l'heartbeat del dispositivo è andato perso.

    ![Versione del software dopo l'aggiornamento 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Selezionare l'avviso per visualizzare l'evento del dispositivo corrispondente.
    
    ![Versione del software dopo l'aggiornamento 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Dopo il riavvio, il dispositivo viene nuovamente inserito nella modalità di manutenzione e viene visualizzato un avviso informativo per indicare che.

    Se si seleziona il **dispositivo di aggiornamento** dalla barra dei comandi superiore, è possibile visualizzare lo stato di avanzamento degli aggiornamenti.   

8. Lo stato del dispositivo viene aggiornato a **online** dopo l'installazione degli aggiornamenti. 

    ![Versione del software dopo l'aggiornamento 13](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    Dalla barra dei comandi superiore selezionare **aggiornamenti del dispositivo**. Verificare che l'aggiornamento sia stato installato correttamente e che la versione del software del dispositivo lo rispecchi.

    ![Versione del software dopo l'aggiornamento 14](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

<!--9. You will again see a notification that updates are available. These are the Kubernetes updates. Select the notification or select **Update device** from the top command bar.

    ![Software version after update 15](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Download the Kubernetes updates. You can see that the package size is different when compared to the previous update package.

    ![Software version after update 16](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    The process of installation is identical to that of device updates. First the updates are downloaded.

    ![Software version after update 17](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Once the updates are downloaded, you can then install the updates. 

    ![Software version after update 18](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    As the updates are installed, the device is put into maintenance mode. The device does not restart for the Kubernetes updates. -->

Una volta installati correttamente gli aggiornamenti del software del dispositivo e del Kubernetes, la notifica del banner scompare. Il dispositivo ha ora la versione più recente del software per dispositivi e Kubernetes.


## <a name="use-the-local-web-ui"></a>Usare l'interfaccia utente Web locale

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

* Scaricare l'aggiornamento o l'hotfix
* Installare l'aggiornamento o l'hotfix

Ogni passaggio viene descritto dettagliatamente nelle sezioni seguenti.


### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'hotfix

Per scaricare l'aggiornamento, seguire questa procedura. È possibile scaricare l'aggiornamento dal percorso fornito da Microsoft o dal catalogo Microsoft Update.


Eseguire i passaggi seguenti per scaricare l'aggiornamento dal catalogo Microsoft Update.

1. Avviare il browser e passare a [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Cercare nel catalogo](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Nella casella di ricerca del catalogo Microsoft Update, immettere il numero della Knowledge base (KB) dell'hotfix o le condizioni per l'aggiornamento che si desidera scaricare. Ad esempio, immettere **Azure stack Edge Pro**, quindi fare clic su **Cerca**.
   
    L'elenco di aggiornamenti viene visualizzato come **aggiornamento di Azure stack Edge 2101**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Selezionare **Download**. Sono disponibili due file da scaricare con *SoftwareUpdatePackage.exe* e *Kubernetes_Package.exe* suffissi che corrispondono rispettivamente agli aggiornamenti software del dispositivo e agli aggiornamenti Kubernetes. Scaricare i file in una cartella nel sistema locale. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi:

 - Di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete.
 - Lo stato del dispositivo è integro, come illustrato nella pagina **Panoramica** dell'interfaccia utente Web locale.

   ![aggiornamento dispositivo](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Il completamento di questa procedura richiede circa 20 minuti. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

1. Nell'interfaccia utente Web locale passare a **manutenzione**  >  **aggiornamento software**. Prendere nota della versione del software in esecuzione. 
   
   ![aggiornare il dispositivo 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Consente di specificare il percorso del file di aggiornamento. È anche possibile passare al file di installazione dell'aggiornamento, se inserito in una condivisione di rete. Selezionare il file di aggiornamento software con il suffisso *SoftwareUpdatePackage.exe* .

   ![aggiornare il dispositivo 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Selezionare **Applica**.

   ![aggiornare il dispositivo 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Quando viene richiesta la conferma, selezionare **Sì** per continuare. Dato che il dispositivo è un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento il dispositivo viene riavviato e si verifica un tempo di inattività. 
   
   ![aggiornare il dispositivo 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. L'aggiornamento si avvia. Dopo l'aggiornamento il dispositivo si riavvia in automatico. In questo periodo di tempo l'interfaccia utente locale non è accessibile.
   
6. Al termine del riavvio si viene indirizzati alla pagina **di accesso** . Per verificare che il software del dispositivo sia stato aggiornato, nell'interfaccia utente Web locale passare a **manutenzione**  >  **aggiornamento software**. Per la versione corrente, la versione del software visualizzata dovrebbe essere **Azure stack Edge 2101**. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Verrà ora aggiornata la versione del software Kubernetes. Ripetere i passaggi precedenti. Consente di specificare il percorso del file di aggiornamento Kubernetes con il suffisso *Kubernetes_Package.exe* .  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Selezionare **applica aggiornamento**.

   ![aggiornare il dispositivo 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Quando viene richiesta la conferma, selezionare **Sì** per continuare.

10. Al termine dell'installazione dell'aggiornamento Kubernetes, non viene apportata alcuna modifica al software visualizzato nell'  >  **aggiornamento software** di manutenzione.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [amministrazione di Azure stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).