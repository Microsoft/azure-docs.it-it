---
title: Soluzione VMware di Azure di CloudSimple-gestire le macchine virtuali del cloud privato in Azure
description: Viene descritto come gestire le macchine virtuali del cloud privato CloudSimple nel portale di Azure, tra cui l'aggiunta di dischi, la modifica della capacità della macchina virtuale e l'aggiunta di interfacce di rete
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77014998"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gestire le macchine virtuali del cloud privato CloudSimple in Azure

Per gestire le macchine virtuali [create per il cloud privato CloudSimple](azure-create-vm.md), accedere al [portale di Azure](https://portal.azure.com). Cercare e selezionare il virtuale (cercare tutti i **Servizi** o le **macchine virtuali** nel menu laterale).

## <a name="control-virtual-machine-operation"></a>Controllare l'operazione della macchina virtuale

I controlli seguenti sono disponibili nella pagina **Panoramica** relativa alla macchina virtuale selezionata.

| Controllo | Description |
| ------------ | ------------- |
| Connessione | Connettersi alla macchina virtuale specificata.  |
| Inizia | Avviare la macchina virtuale specificata.  |
| Riavvia | Arrestare e quindi accendere la macchina virtuale specificata.  |
| Arresto | Arrestare la macchina virtuale specifica.  |
| Acquisizione | Acquisire un'immagine della macchina virtuale specificata in modo che possa essere usata come immagine per creare altre macchine virtuali. Vedere [creare un'immagine gestita di una macchina virtuale generalizzata in Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Spostamento | Passare alla macchina virtuale specificata.  |
| Elimina | Rimuovere la macchina virtuale specificata.  |
| Aggiorna | Aggiornare i dati nella visualizzazione.  |

### <a name="view-performance-information"></a>Visualizzare le informazioni sulle prestazioni

I grafici nell'area inferiore della pagina **Panoramica** presentano i dati sulle prestazioni per l'intervallo selezionato (l'ultima ora negli ultimi 30 giorni. l'impostazione predefinita è l'ultima ora). All'interno di ogni grafico è possibile visualizzare i valori numerici in qualsiasi momento all'interno dell'intervallo spostando il cursore avanti e indietro sul grafico.

Vengono visualizzati i grafici seguenti.

| Item | Descrizione |
| ------------ | ------------- |
| CPU (media) | Utilizzo medio della CPU in percentuale rispetto all'intervallo selezionato.   |
| Rete | Traffico all'interno e all'esterno della rete (MB) nell'intervallo selezionato.  |
| Byte disco | Dati totali letti dal disco e scritti su disco (MB) nell'intervallo selezionato.  |
| Operazioni su disco | Frequenza media delle operazioni su disco (operazioni al secondo) nell'intervallo selezionato. |

## <a name="manage-vm-disks"></a>Gestire i dischi delle macchine virtuali

Per aggiungere un disco della macchina virtuale, aprire la pagina **dischi** per la macchina virtuale selezionata. Per aggiungere un disco, fare clic su **Aggiungi disco**. Per configurare ognuna delle impostazioni seguenti, immettere o selezionare un'opzione inline. Fare clic su **Salva**.

   | Item | Descrizione |
   | ------------ | ------------- |
   | Nome | Immettere un nome per identificare il disco.  |
   | Dimensione | Selezionare una delle dimensioni disponibili.  |
   | Controller SCSI | Selezionare un controller SCSI. I controller disponibili variano per i diversi sistemi operativi supportati.  |
   | Mode | Determina la modalità di partecipazione del disco negli snapshot. Scegliere una delle opzioni seguenti: <br> -Permanente indipendente: tutti i dati scritti sul disco vengono scritti in modo permanente.<br> -Indipendente, non persistente: le modifiche scritte nel disco vengono eliminate quando si spegne o si reimposta la macchina virtuale.  Questa modalità consente di riavviare sempre la macchina virtuale nello stesso stato. Per altre informazioni, vedere la [documentazione di VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Per eliminare un disco, selezionarlo e fare clic su **Elimina**.

## <a name="change-the-capacity-of-the-vm"></a>Modificare la capacità della VM

Per modificare la capacità della macchina virtuale, aprire la pagina **dimensioni** per la macchina virtuale selezionata. Specificare una delle opzioni seguenti e fare clic su **Salva**.

| Item | Descrizione |
| ------------ | ------------- |
| Numero di core | Numero di core assegnati alla macchina virtuale.  |
| Virtualizzazione hardware | Selezionare la casella di controllo per esporre la virtualizzazione hardware al sistema operativo guest. Vedere l'articolo VMware [esporre la virtualizzazione assistita con hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Dimensione della memoria | Selezionare la quantità di memoria da allocare alla macchina virtuale.  

## <a name="manage-network-interfaces"></a>Gestire le interfacce di rete

Per aggiungere un'interfaccia, fare clic su **Aggiungi interfaccia di rete**. Configurare ognuna delle impostazioni seguenti immettendo o selezionando un'opzione inline. Fare clic su **Salva**.

   | Controllo | Descrizione |
   | ------------ | ------------- |
   | Nome | Immettere un nome per identificare l'interfaccia.  |
   | Rete | Selezionare dall'elenco di reti configurate nel cloud privato vSphere.  |
   | Adattatore | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurato per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware [scelta di una scheda di rete per la macchina virtuale](https://kb.vmware.com/s/article/1001805). |
   | Power on at Boot (Accendi all'avvio) | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

Per eliminare un'interfaccia di rete, selezionarla e fare clic su **Elimina**.
