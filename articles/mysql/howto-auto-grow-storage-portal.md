---
title: Aumento automatico delle dimensioni di archiviazione-portale di Azure-database di Azure per MySQL
description: Questo articolo descrive come abilitare l'espansione automatica dell'archiviazione per database di Azure per MySQL usando portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: d4dc5c2690be7b9abbda685e78ea562878626b5c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902848"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Espandere automaticamente l'archiviazione nel database di Azure per MySQL usando il portale di Azure
Questo articolo descrive come configurare l'archiviazione di un database di Azure per il server MySQL in modo che cresca senza compromettere il carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server è contrassegnato come di sola lettura. Tuttavia, se si Abilita l'aumento automatico delle dimensioni di archiviazione, l'archiviazione del server aumenta per adattarsi ai dati in crescita. Per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dell'archiviazione con provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione di archiviazione con provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione con provisioning. Si applicano i limiti di archiviazione massimi come specificato [qui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilita espansione automatica dell'archiviazione 

Seguire questa procedura per impostare l'aumento automatico delle dimensioni di archiviazione del server MySQL:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MySQL esistente.

2. Nella pagina server MySQL, in **Impostazioni** intestazione, fare clic su piano **tariffario** per aprire la pagina piano tariffario.

3. Nella sezione aumento automatico selezionare **Sì** per abilitare l'aumento automatico delle dimensioni di archiviazione.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Database di Azure per MySQL-Settings_Pricing_tier-aumento automatico":::

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica conferma che l'espansione automatica è stata abilitata correttamente.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Database di Azure per MySQL-crescita automatica riuscita":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
