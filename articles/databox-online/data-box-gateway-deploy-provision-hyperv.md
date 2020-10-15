---
title: Esercitazione sul provisioning di Azure Data Box Gateway in Hyper-V | Microsoft Docs
description: La seconda esercitazione per la distribuzione di Azure Data Box Gateway implica il provisioning di un dispositivo virtuale in Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: ffbfd3214242d8df5fe33faf465bc1da3eb9986d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84196626"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Esercitazione: Effettuare il provisioning di Azure Data Box Gateway in Hyper-V

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come effettuare il provisioning di un Data Box Gateway in un sistema host che esegue Hyper-V in Windows Server 2016, Windows Server 2012 R2 o Windows Server 2012.

È necessario disporre dei privilegi di amministratore per effettuare il provisioning di un dispositivo virtuale e configurarlo. Il tempo previsto per il completamento di provisioning e installazione iniziale è di circa 10 minuti. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Verificare che l'host soddisfi i requisiti minimi del dispositivo
> * Effettuare il provisioning di un dispositivo virtuale nell'hypervisor
> * Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per il provisioning di un dispositivo virtuale in un sistema host che esegue Hyper-V in Windows Server 2016 o Windows Server 2012 R2 sono i seguenti.

### <a name="for-the-data-box-gateway-resource"></a>Per la risorsa Data Box Gateway

Prima di iniziare, verificare che:

* Tutti i passaggi di [preparazione del portale per Data Box Gateway](data-box-gateway-deploy-prep.md) siano stati completati.
* L'immagine del dispositivo virtuale per Hyper-V sia stata scaricata dal portale di Azure come descritto in [Esercitazione: Preparare la distribuzione di Azure Data Box Gateway (anteprima)](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Il software in esecuzione nel Data Box Gateway può essere usato solo con la risorsa Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Per il dispositivo virtuale Data Box Gateway

Prima di distribuire un dispositivo, verificare che:

* Si abbia accesso a un sistema host che esegue Hyper-V in Windows Server 2012 R2 o versioni successive da poter usare per il provisioning di un dispositivo.
* Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning del dispositivo virtuale:

  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM.
  * Un'interfaccia di rete.
  * Un disco del sistema operativo da 250 GB.
  * Un disco virtuale da 2 TB per i dati.

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

* Verificare i requisiti di rete per distribuire un Data Box Gateway e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
* Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per consentire il funzionamento ottimale del dispositivo.

## <a name="check-the-host-system"></a>Controllare il sistema host

Per creare un dispositivo virtuale, è necessario quanto segue:

* Ruolo di Hyper-V installato in Windows Server 2016, Windows Server 2012 R2 o Windows Server 2012.
* Console di gestione di Hyper-V di Microsoft in un client Microsoft Windows connesso all'host.
* Verificare che l'hardware sottostante (sistema host) in cui si sta creando il dispositivo virtuale sia in grado di dedicare le risorse seguenti al dispositivo virtuale:

  * Almeno 4 processori virtuali.
  * Almeno 8 GB di RAM.
  * Un'interfaccia di rete connessa alla rete in grado di indirizzare il traffico a Internet.
  * Un disco del sistema operativo da 250 GB.
  * Un disco virtuale da 2 TB per i dati di sistema.

## <a name="bitlocker-considerations"></a>Considerazioni su BitLocker

* Si consiglia di abilitare BitLocker nella macchina virtuale Data Box Gateway. Per impostazione predefinita, BitLocker non è abilitato. Per altre informazioni, vedere:
  * [Impostazioni di supporto della crittografia nella console di gestione di Hyper-V](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Supporto di BitLocker in una macchina virtuale](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Effettuare il provisioning di un dispositivo virtuale nell'hypervisor

Eseguire i passaggi seguenti per il provisioning di un dispositivo in hypervisor.

1. Nell'host di Windows Server copiare l'immagine del dispositivo virtuale in un'unità locale. Si tratta dell'immagine VHDX scaricata tramite il portale di Azure. Prendere nota della posizione in cui è stata copiata l'immagine da usare più avanti nella procedura.
2. Aprire **Gestione server**. Nell'angolo in alto a destra fare clic su **Strumenti** e selezionare **Console di gestione di Hyper-V**.

    ![Selezionare Console di gestione di Hyper-V in Server Manager](./media/data-box-gateway-deploy-provision-hyperv/image1.png)  
  
3. Nella **Console di gestione di Hyper-V**, nel riquadro dell'ambito, fare clic con il pulsante destro del mouse sul nodo del sistema per aprire il menu di scelta rapida e quindi fare clic su **Nuovo** > **Macchina virtuale**.

   ![Creare una nuova macchina virtuale nella console di gestione di Hyper-V](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Nella pagina **Before you begin** (Prima di iniziare) della Creazione guidata macchina virtuale fare clic su **Avanti**.
5. Nella pagina **Impostazione nome e percorso** specificare un **Nome** da assegnare al dispositivo virtuale. Fare clic su **Avanti**.

   ![Pagina Impostazione nome e percorso](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Nella pagina **Impostazione generazione** scegliere **Generazione 2** per il tipo di immagine del dispositivo VHDX e fare clic su **Avanti**.    

   ![Pagina Impostazione generazione](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Nella pagina **Assegnazione memoria** specificare una **Memoria di avvio** di almeno **8192 MB**, non abilitare la memoria dinamica e quindi fare clic su **Avanti**.

   ![Pagina Assegnazione memoria](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Nella pagina **Configurazione della rete** selezionare il commutatore virtuale connesso a Internet e quindi fare clic su **Avanti**.

   ![Pagina Configurazione della rete](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Nella pagina **Connessione disco rigido virtuale** scegliere **Usa disco rigido virtuale esistente**, specificare il percorso dell'immagine del dispositivo virtuale e quindi fare clic su **Avanti**.

   ![Pagina Connessione disco rigido virtuale](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Esaminare la pagina **Riepilogo** e quindi fare clic su **Fine** per creare la macchina virtuale.

    ![Pagina Completamento della Creazione guidata macchina virtuale](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Per soddisfare i requisiti minimi, sono necessari 4 processori virtuali. Per aggiungere 4 processori virtuali, selezionare il sistema host nella finestra **Console di gestione di Hyper-V**. Nel riquadro di destra sotto l'elenco di **macchine virtuali** individuare la macchina virtuale appena creata. Selezionare e fare clic con il pulsante destro del mouse sul nome della macchina, quindi selezionare **Impostazioni**.

    ![Impostazioni della macchina virtuale](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Nella pagina **Impostazioni**, nel riquadro di sinistra, fare clic su **Processore**. Nel riquadro di destra, impostare **Numero di processori virtuali** su 4 (o oltre). Fare clic su **Applica**.

    ![Impostare il numero di processori virtuali nella pagina Impostazioni](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Per soddisfare i requisiti minimi, è necessario anche aggiungere un disco dati virtuale da 2 TB. Nella pagina **Impostazioni** :

    1. Nel riquadro di sinistra selezionare **Controller SCSI**.
    2. Nel riquadro di destra selezionare **Disco rigido** e fare clic su **Aggiungi**.

    ![Aggiungere un disco rigido nella pagina Impostazioni](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Nella pagina **Disco rigido** selezionare l'opzione **Disco rigido virtuale** e fare clic su **Nuovo**. Viene avviata la **Creazione guidata disco rigido virtuale**.

    ![Creazione guidata disco rigido virtuale](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Nella pagina **Before you begin** (Prima di iniziare) della Creazione guidata disco rigido virtuale fare clic su **Avanti**.
16. Nella pagina **Selezione formato disco** accettare l'opzione di formato predefinita **VHDX**. Fare clic su **Avanti**.
17. Nella pagina **Selezione tipo di disco** impostare il tipo di disco rigido virtuale su **A espansione dinamica** (consigliato). Il funzionamento di un disco **A dimensione fissa** è garantito, ma può comportare tempi lunghi di attesa. Si consiglia di non usare l'opzione **Differenze** . Fare clic su **Avanti**.

    ![Pagina Selezione tipo di disco](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Nella pagina **Impostazione nome e percorso** fornire un **nome** e un **percorso** (anche accedendovi) da assegnare al disco dati. Fare clic su **Avanti**.

    ![Pagina Impostazione nome e percorso](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Nella pagina **Configurazione disco** selezionare l'opzione **Crea un nuovo disco rigido virtuale vuoto** e specificare la dimensione di **2 TB** (o superiore).

    2 TB è il requisito minimo, ma è sempre possibile effettuare il provisioning di un disco più grande. Si noti che, una volta effettuato il provisioning del disco, non è possibile ridurlo. Il tentativo di ridurre il disco comporta la perdita di tutti i dati locali sul dispositivo. L'espansione del disco dati non è supportata. Fare clic su **Avanti**.

    ![Pagina Configurazione disco](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Nella pagina **Riepilogo** esaminare i dettagli del disco dati virtuale e, se conformi alle proprie esigenze, fare clic su **Fine** per creare il disco. La procedura guidata viene chiusa e un disco rigido virtuale viene aggiunto al computer.

    ![Pagina Completamento della Creazione guidata disco rigido virtuale](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Viene nuovamente visualizzata la pagina **Impostazioni**. Fare clic su **OK** per chiudere la pagina **Impostazioni** e tornare alla finestra Console di gestione di Hyper-V.

    ![Pagina Impostazioni](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

## <a name="start-the-virtual-device-and-get-the-ip"></a>Avviare il dispositivo virtuale e ottenere l'IP

Eseguire i passaggi seguenti per avviare il dispositivo virtuale a cui connettersi.

#### <a name="to-start-the-virtual-device"></a>Per avviare il dispositivo virtuale

1. Avviare il dispositivo virtuale.

   ![Avviare il dispositivo virtuale](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Quando il dispositivo è in esecuzione, selezionarlo, fare clic con il pulsante destro del mouse e selezionare **Connetti**.

3. Potrebbe essere necessario attendere 10-15 minuti prima che il dispositivo sia pronto. Un messaggio di stato viene visualizzato nella console per indicare l'avanzamento. Quando il dispositivo è pronto, passare ad **Azione**. Premere `Ctrl + Alt + Delete` per accedere al dispositivo virtuale. L'utente e la password predefiniti sono rispettivamente *EdgeUser* e *Password1*.

   ![Accedere al dispositivo virtuale](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. I passaggi da 5 a 7 devono essere eseguiti solo in caso di avvio in un ambiente non DHCP. In un ambiente DHCP, ignorare questi passaggi. Se il dispositivo è stato avviato in un ambiente non DHCP, viene visualizzato un messaggio.

5. Per configurare la rete, usare il comando `Get-HcsIpAddress` per elencare le interfacce di rete abilitate nel dispositivo virtuale. Se il dispositivo dispone di una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

6. Usare il cmdlet `Set-HcsIpAddress` per configurare la rete. Vedere l'esempio seguente:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. Dopo aver completato l'installazione iniziale e avviato il dispositivo, viene visualizzato il relativo testo intestazione. Prendere nota dell'indirizzo IP e dell'URL visualizzati nel testo intestazione per gestire il dispositivo. Usare questo indirizzo IP per connettersi all'interfaccia utente Web del dispositivo virtuale e completare l'installazione locale e l'attivazione.

   ![Banner del dispositivo virtuale con URL di connessione e indirizzo IP](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Se il dispositivo non soddisfa i requisiti minimi di configurazione, viene visualizzato un errore nel testo intestazione. Modificare la configurazione del dispositivo per garantire la presenza di risorse adeguate a soddisfare i requisiti minimi della macchina. È quindi possibile riavviare il dispositivo a cui connettersi. Vedere i requisiti minimi di configurazione in [Verificare che il sistema host soddisfi i requisiti minimi del dispositivo virtuale](#check-the-host-system).

In presenza di altri errori durante la configurazione iniziale con l'interfaccia utente Web locale, vedere i flussi di lavoro seguenti:

* [Eseguire test diagnostici per risolvere i problemi relativi alla configurazione nell'interfaccia utente Web](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Generare un pacchetto di log e visualizzare i file di log](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Verificare che l'host soddisfi i requisiti minimi del dispositivo
> * Effettuare il provisioning di un dispositivo virtuale nell'hypervisor
> * Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo virtuale.

> [!div class="nextstepaction"]
> [Connettere e configurare Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md)
