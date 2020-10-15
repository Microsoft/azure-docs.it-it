---
title: Aumento automatico delle dimensioni di archiviazione-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come configurare l'aumento automatico delle dimensioni dell'archiviazione usando il portale di Azure nel database di Azure per PostgreSQL-server singolo
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 1853bbb0e44526988b34ed23126664c5a2285a7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907507"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Aumento automatico delle dimensioni di archiviazione usando il portale di Azure nel database di Azure per PostgreSQL-server singolo
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server PostgreSQL in modo che cresca senza alcun effetto sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server è contrassegnato come di sola lettura. Tuttavia, se si Abilita l'aumento automatico delle dimensioni di archiviazione, l'archiviazione del server aumenta per adattarsi ai dati in crescita. Per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dell'archiviazione con provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione di archiviazione con provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione con provisioning. Si applicano i limiti di archiviazione massimi come specificato [qui](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilita espansione automatica dell'archiviazione 

Per impostare l'aumento automatico delle dimensioni dell'archiviazione del server PostgreSQL, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server PostgreSQL esistente.

2. Nella pagina Server PostgreSQL fare clic su piano **tariffario** in **Impostazioni**per aprire la pagina piano tariffario.

3. Nella sezione **aumento automatico** selezionare **Sì** per abilitare l'aumento automatico delle dimensioni di archiviazione.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Database di Azure per PostgreSQL-Settings_Pricing_tier-aumento automatico":::

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica conferma che l'espansione automatica è stata abilitata correttamente.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Database di Azure per PostgreSQL-Settings_Pricing_tier-aumento automatico":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
