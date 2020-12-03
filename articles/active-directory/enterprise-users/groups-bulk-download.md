---
title: Scaricare un elenco di gruppi nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco le proprietà del gruppo nell'interfaccia di amministrazione di Azure in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98c61679fed04c0a696b60bb7ee53009a8a530e0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547679"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Scaricare in blocco un elenco di gruppi in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile eseguire il download bulk dell'elenco di tutti i gruppi dell'organizzazione in un file con valori delimitati da virgole (CSV).

## <a name="to-download-a-list-of-groups"></a>Per scaricare un elenco di gruppi

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure ad selezionare **gruppi** gruppi di  >  **download**.
1. Nella pagina di **download dei gruppi** selezionare **Avvia** per ricevere un file CSV che elenca i gruppi.

   ![Il comando Scarica gruppi si trova nella pagina tutti i gruppi](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifica lo stato di download

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

[![Verificare lo stato nella pagina Risultati operazioni bulk.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per il download di un elenco di gruppi può essere eseguita per un massimo di un'ora. In questo modo è possibile scaricare un elenco di almeno 300.000 gruppi.

## <a name="next-steps"></a>Passaggi successivi

- [Rimozione in blocco di membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
