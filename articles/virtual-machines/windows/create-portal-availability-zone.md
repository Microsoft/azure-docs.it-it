---
title: Creare una VM con zone con la portale di Azure
description: Creare una VM Windows in una zona di disponibilità con il portale di Azure
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 7c7f135d4033a31f855342c172d73f51478931ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501686"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Creare una macchina virtuale in una zona di disponibilità usando il portale di Azure

Questo articolo descrive come usare il portale di Azure per creare una macchina virtuale Linux in una zona di disponibilità di Azure. Una [zona di disponibilità](../../availability-zones/az-overview.md) è una zona fisicamente separata in un'area di Azure. Usare le zone di disponibilità per proteggere app e dati da un poco probabile errore o perdita di un intero data center.

Per usare una zona di disponibilità, creare la macchina virtuale in un'[area di Azure supportata](../../availability-zones/az-region.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 

3. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm). Scegliere una località, ad esempio Stati Uniti orientali 2, che supporti le zone di disponibilità. Al termine, fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Scegliere una dimensione per la VM. Selezionare una dimensione consigliata o filtrare in base alle funzionalità. Confermare che la dimensione è disponibile nella zona che si vuole usare.

    ![Selezionare una dimensione di VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. In **Impostazioni**  >  **disponibilità elevata** selezionare una delle zone numerate dall'elenco a discesa **zona di disponibilità** , mantenere le impostazioni predefinite rimanenti e fare clic su **OK**.

    ![Selezionare una zona di disponibilità](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Nella pagina di riepilogo fare clic su **Crea** per avviare la distribuzione della macchina virtuale.

7. La macchina virtuale verrà aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confermare la zona per il disco gestito e un indirizzo IP

Se la macchina virtuale è distribuita in una zona di disponibilità, viene creato un disco gestito per la macchina virtuale nella stessa zona di disponibilità. Per impostazione predefinita, in questa zona viene creato anche un indirizzo IP pubblico.

È possibile confermare le impostazioni di zona per queste risorse nel portale.  

1. Fare clic su **Gruppi di risorse** e quindi sul nome del gruppo di risorse per la macchina virtuale, ad esempio *myResourceGroup*.

2. Fare clic sul nome della risorsa disco. La pagina **Panoramica** include informazioni dettagliate sulla località e sulla zona di disponibilità della risorsa.

    ![Zona di disponibilità per il disco gestito](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Fare clic sul nome della risorsa dell'indirizzo IP pubblico. La pagina **Panoramica** include informazioni dettagliate sulla località e sulla zona di disponibilità della risorsa.

    ![Zona di disponibilità per l'indirizzo IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare una macchina virtuale in una zona di disponibilità. Altre informazioni sulla [disponibilità](../availability.md) per le macchine virtuali di Azure.