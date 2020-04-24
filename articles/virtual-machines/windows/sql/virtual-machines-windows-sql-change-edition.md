---
title: Modifica sul posto dell'edizione SQL Server
description: Informazioni su come modificare l'edizione del SQL Server macchina virtuale in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605453"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Modifica sul posto dell'edizione SQL Server in una macchina virtuale di Azure

Questo articolo descrive come modificare l'edizione di SQL Server in una macchina virtuale Windows in Azure. 

L'edizione di SQL Server è determinata dal codice Product Key e viene specificata durante il processo di installazione utilizzando il supporto di installazione. L'edizione impone le [funzionalità](/sql/sql-server/editions-and-components-of-sql-server-2017) disponibili nel prodotto SQL Server. È possibile modificare l'edizione di SQL Server con il supporto di installazione di ed eseguire il downgrade per ridurre i costi o l'aggiornamento per abilitare altre funzionalità.

Una volta che l'edizione di SQL Server è stata modificata internamente alla VM SQL Server, è necessario aggiornare la proprietà Edition di SQL Server nel portale di Azure ai fini della fatturazione. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire una modifica sul posto dell'edizione di SQL Server, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server in Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Configurare i supporti con l' **edizione desiderata** di SQL Server. I clienti che dispongono di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere i supporti di installazione da [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di installazione di Azure Marketplace SQL Server immagine di macchina virtuale con l'edizione desiderata (in `C:\SQLServerFull`genere si trova in). 


## <a name="upgrade-an-edition"></a>Aggiornare un'edizione

> [!WARNING]
> Con l'aggiornamento dell'edizione di SQL Server il servizio viene riavviato per SQL Server, insieme a tutti i servizi associati, ad esempio Analysis Services e R Services. 

Per aggiornare l'edizione di SQL Server, ottenere i supporti di installazione SQL Server per l'edizione desiderata di SQL Server, quindi eseguire le operazioni seguenti:

1. Aprire Setup. exe dal supporto di installazione di SQL Server. 
1. Passare a **manutenzione** e scegliere l'opzione di **aggiornamento edizione** . 

   ![Selezione per l'aggiornamento dell'edizione di SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selezionare **Avanti** fino a quando non si raggiunge la pagina **pronto per l'aggiornamento dell'edizione** , quindi selezionare **Aggiorna**. La finestra di installazione potrebbe smettere di rispondere per alcuni minuti mentre la modifica avrà effetto. In una pagina **completa** verrà confermato che l'aggiornamento dell'edizione è terminato. 

Dopo l'aggiornamento dell'edizione SQL Server, modificare la proprietà Edition della macchina virtuale SQL Server nel portale di Azure. I metadati e la fatturazione associati alla macchina virtuale vengono aggiornati.

## <a name="downgrade-an-edition"></a>Downgrade di un'edizione

Per effettuare il downgrade dell'edizione di SQL Server, è necessario disinstallare completamente SQL Server e reinstallarla nuovamente con i supporti di installazione dell'edizione desiderata. 

> [!WARNING]
> La disinstallazione di SQL Server può comportare tempi di inattività aggiuntivi. 

È possibile effettuare il downgrade dell'edizione di SQL Server attenendosi alla procedura seguente:

1. Eseguire il backup di tutti i database, inclusi i database di sistema. 
1. Spostare i database di sistema (Master, Model e msdb) in una nuova posizione. 
1. Disinstallare completamente SQL Server e tutti i servizi associati. 
1. Riavviare la macchina virtuale. 
1. Installare SQL Server usando i supporti con l'edizione desiderata di SQL Server.
1. Installare i Service Pack e gli aggiornamenti cumulativi più recenti.  
1. Sostituire i nuovi database di sistema creati durante l'installazione con i database di sistema spostati in precedenza in un percorso diverso. 

Dopo il downgrade dell'edizione SQL Server, modificare la proprietà Edition della macchina virtuale SQL Server nel portale di Azure. I metadati e la fatturazione associati alla macchina virtuale vengono aggiornati.

## <a name="change-edition-in-portal"></a>Modificare l'edizione nel portale 

Dopo aver modificato l'edizione di SQL Server usando il supporto di installazione e aver registrato la VM SQL Server con il provider di [risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md), è possibile usare la portale di Azure per modificare la proprietà Edition della macchina virtuale SQL Server a scopo di fatturazione. A questo scopo, attenersi alla procedura seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa della macchina virtuale SQL Server. 
1. In **Impostazioni**selezionare **Configura**. Selezionare quindi l'edizione desiderata di SQL Server dall'elenco a discesa in **edizione**. 

   ![Modificare i metadati dell'edizione](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Esaminare l'avviso che indica che è necessario modificare prima l'edizione SQL Server e che la proprietà Edition deve corrispondere all'edizione SQL Server. 
1. Selezionare **applica** per applicare le modifiche ai metadati dell'edizione. 


## <a name="remarks"></a>Osservazioni

- La proprietà Edition della macchina virtuale SQL Server deve corrispondere all'edizione dell'istanza di SQL Server installata per tutte le macchine virtuali SQL Server, inclusi i tipi di licenze con pagamento in base al consumo e Bring your own License.
- Se si elimina la risorsa SQL Server VM, si tornerà all'impostazione dell'edizione hardcoded dell'immagine.
- La possibilità di modificare l'edizione è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica SQL Server dovranno registrare manualmente [la macchina virtuale SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- Per aggiungere una macchina virtuale SQL Server a un set di disponibilità è necessario ricreare la macchina virtuale. Tutte le VM aggiunte a un set di disponibilità verranno riportate all'edizione predefinita ed è necessario modificare di nuovo l'edizione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


