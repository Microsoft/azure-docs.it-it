---
title: Creare una raccolta contenuto per distribuire macchine virtuali in una soluzione VMware di Azure
description: Creare una raccolta contenuto per distribuire una macchina virtuale in un cloud privato della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: a50b12ef8e139bf7de171398fd28f74fc3f310c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382005"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Creare una raccolta contenuto per distribuire macchine virtuali in una soluzione VMware di Azure

Una raccolta contenuto archivia e gestisce il contenuto sotto forma di elementi della raccolta. Un singolo elemento della raccolta è costituito da uno o più file usati per distribuire macchine virtuali. 

Questo articolo illustra la procedura per la creazione di una raccolta contenuto.  Verrà quindi illustrata la distribuzione di una macchina virtuale usando un'immagine ISO dalla raccolta contenuto.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari un segmento NSX-T (Commuter logico) e un servizio DHCP gestito.  Per altre informazioni, vedere l'articolo [How to Manage DHCP in Azure VMware Solution](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Creare una raccolta contenuto

1. Dal client vSphere locale selezionare **Menu > Content Libraries** (Menu > Raccolte contenuto).

   ![Selezionare Menu -> Content Libraries](./media/content-library/vsphere-menu-content-libraries.png)

1. Selezionare il pulsante **Aggiungi** per creare una nuova raccolta contenuto.

   ![Selezionare il pulsante Aggiungi per creare una nuova raccolta contenuto.](./media/content-library/create-new-content-library.png)

1. Specificare un nome e confermare l'indirizzo IP del server vCenter e selezionare **Next** (Avanti).

   ![Specificare un nome, aggiungere eventuali note e quindi fare clic su Next.](./media/content-library/new-content-library-step1.png)

1. Selezionare **Local content library** (Raccolta contenuto locale) e quindi **Next** (Avanti).

   ![Per questo esempio verrà creata una raccolta contenuto locale. Selezionare Next.](./media/content-library/new-content-library-step2.png)

1. Selezionare l'archivio dati in cui verrà archiviata la raccolta contenuto e quindi selezionare **Next** (Avanti).

   ![Selezionare l'archivio dati in cui si vuole ospitare la raccolta contenuto e selezionare Next.](./media/content-library/new-content-library-step3.png)

1. Rivedere e verificare le impostazioni della raccolta contenuto e quindi selezionare **Finish** (Fine).

   ![Verificare le impostazioni e fare clic su Finish.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Caricare un'immagine ISO nella raccolta contenuto

Ora che la raccolta contenuto è stata creata, è possibile aggiungere un'immagine ISO per distribuire una macchina virtuale in un cluster di cloud privato. 

1. Dal client vSphere selezionare **Menu > Content Libraries** (Menu > Raccolte contenuto).

1. Fare clic con il pulsante destro del mouse sulla raccolta contenuto da usare per la nuova immagine ISO e selezionare **Import element** (Importa elemento).

1. Importare un elemento della raccolta per l'origine eseguendo una delle operazioni seguenti e quindi selezionare **Import** (Importa):
   1. Selezionare URL e specificare un URL per scaricare un'immagine ISO.

   1. Selezionare **Local File** (File locale) per scaricare dal sistema locale.

   > [!TIP]
   > Facoltativamente, è possibile definire un nome di elemento personalizzato e aggiungere le note per la destinazione.

1. Aprire la raccolta e selezionare la scheda **Other Types** (Altri tipi) per verificare che l'immagine ISO sia stata caricata correttamente.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Distribuire una macchina virtuale in un cluster di cloud privato

1. Dal client vSphere selezionare **Menu > Hosts and Clusters** (Menu > Host e cluster).

1. Nel pannello di sinistra espandere l'albero e selezionare un cluster.

1. Selezionare **Actions > New Virtual Machine** (Azioni > Nuova macchina virtuale).

1. Eseguire la procedura guidata e modificare le impostazioni desiderate.

1. Selezionare **New CD/DVD Drive > Client Device > Content Library ISO File** (Nuova unità CD/DVD > Dispositivo client > File ISO raccolta contenuto).

1. Selezionare l'immagine ISO caricata nella sezione precedente e quindi selezionare **OK**.

1. Selezionare la casella di controllo **Connect** (Connetti) in modo che l'immagine ISO venga montata in fase di accensione.

1. Selezionare **New Network > Select dropdown > Browse** (Nuova rete > Elenco a discesa Seleziona > Sfoglia).

1. Selezionare il **commutatore logico (segmento)** e quindi **OK**.

1. Modificare eventuali altre impostazioni hardware e selezionare **Next** (Avanti).

1. Rivedere le impostazioni e selezionare **Finish** (Fine).


## <a name="next-steps"></a>Passaggi successivi

Ora che è stata illustrata la creazione di una raccolta contenuto per la distribuzione di macchine virtuali in una soluzione VMware di Azure, è consigliabile:

- [Distribuzione e configurazione di VMware HCX](tutorial-deploy-vmware-hcx.md) per la migrazione dei carichi di lavoro delle macchine virtuali nel cloud privato.
- [Gestione del ciclo di vita delle macchine virtuali della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
