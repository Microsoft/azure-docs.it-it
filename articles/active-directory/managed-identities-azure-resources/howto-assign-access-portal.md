---
title: Assegnare un accesso di identità gestita a una risorsa tramite il portale di Azure - Azure ADAssign a managed identity access to a resource using the Azure portal - Azure AD
description: Istruzioni dettagliate su come assegnare a un'identità gestita in una risorsa l'accesso a un'altra risorsa tramite il portale di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547277"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Assegnare a un'identità gestita l'accesso a una risorsa tramite il portale di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità gestita l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. Questo articolo descrive come concedere all'identità gestita di una macchina virtuale o di un set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione di Azure tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'identità gestita l'accesso a un'altra risorsa

Dopo aver abilitato l'identità gestita in una risorsa di Azure, ad esempio una [macchina virtuale di Azure](qs-configure-portal-windows-vm.md) o un [set di scalabilità di macchine virtuali di Azure](qs-configure-portal-windows-vmss.md):

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure in cui è stata configurata l'identità gestita.

2. Passare alla risorsa desiderata in cui si desidera modificare il controllo di accesso. Poiché in questo esempio viene concesso a una macchina virtuale di Azure l'accesso a un account di archiviazione, passare all'account di archiviazione.

3. Selezionare la pagina **Controllo di accesso (IAM)** della risorsa e quindi **+ Aggiungi assegnazione di ruolo**. Specificare quindi i valori per **Ruolo** e **Assegna accesso a** e specificare la sottoscrizione corrispondente in **Sottoscrizione**. Nell'area per i criteri di ricerca viene visualizzata la risorsa. Selezionare la risorsa e premere **Salva**. 

   ![Schermata del Controllo di accesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare identità gestite per risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md).
- Per abilitare l'identità gestita in un set di scalabilità di macchine virtuali di Azure, vedere [Configurare identità gestite per risorse di Azure in un set di scalabilità di macchine virtuali tramite il portale di Azure](qs-configure-portal-windows-vmss.md).


