---
title: Creare un'offerta di macchina virtuale in Azure Marketplace.
description: Scopri come creare un'offerta di macchina virtuale in Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: 827e4d883fd9e80ae84845d620cc4ca00816f56e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551315"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Come creare un'offerta di macchina virtuale in Azure Marketplace

Questo articolo descrive come creare un'offerta di macchina virtuale di Azure per [Azure Marketplace](https://azuremarketplace.microsoft.com/). Fa riferimento alle macchine virtuali basate su Windows e a quelle basate su Linux con un sistema operativo, un disco rigido virtuale e fino a 16 dischi dati.

Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](partner-center-portal/create-account.md). Assicurarsi che l'account sia registrato al programma del marketplace commerciale.

## <a name="before-you-begin"></a>Prima di iniziare

Se non è ancora stato fatto, rivedere [pianificare un'offerta di macchina virtuale](marketplace-virtual-machines.md). Verranno illustrati i requisiti tecnici per la macchina virtuale e verranno elencate le informazioni e gli asset necessari per la creazione dell'offerta.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel riquadro a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina **Panoramica** selezionare **+ nuova offerta**  >  **macchina virtuale di Azure**.

    ![Screenshot delle opzioni di menu del riquadro sinistro e del pulsante "Nuova offerta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Le eventuali modifiche apportate dal Centro per i partner all'offerta in seguito alla sua pubblicazione vengono visualizzate in Azure Marketplace solo dopo che l'offerta viene ripubblicata. Assicurarsi di ripubblicare sempre un'offerta dopo averla modificata.

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta di Azure Marketplace, in Azure PowerShell e nell'interfaccia della riga di comando di Azure, se applicabile.
- Usare solo lettere minuscole e numeri. L'ID può includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. L'alias dell'offerta corrisponde al nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato in Azure Marketplace. ed è diverso dal nome dell'offerta e da altri valori visualizzati dai clienti.

Selezionare **Crea** per generare l'offerta e continuare. Il centro per i partner apre la pagina **installazione offerta** .

## <a name="enable-a-test-drive-optional"></a>Abilitare una test drive (facoltativo)

Un test drive è un ottimo modo per presentare l'offerta a potenziali clienti, offrendo loro l'accesso a un ambiente preconfigurato per un numero fisso di ore. L'offerta di un test drive comporta un aumento della velocità di conversione e genera lead altamente qualificati. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](./what-is-test-drive.md).

> [!TIP]
> Una test drive è diversa da una versione di valutazione gratuita. È possibile offrire una test drive, una versione di valutazione gratuita o entrambe. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

Per abilitare una test drive, selezionare la casella di controllo **Abilita una test drive** . Il test drive sarà configurato in un secondo momento. Con test drive, è necessario configurare un CRM (vedere la sezione successiva).

## <a name="configure-customer-leads-management"></a>Configurare la gestione dei lead dei clienti

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di navigazione a sinistra, **Proprietà**.

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare le proprietà di un'offerta di macchina virtuale](azure-vm-create-properties.md)
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)