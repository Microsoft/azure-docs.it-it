---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il server singolo database di Azure per PostgreSQL
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727108"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel server singolo database di Azure per PostgreSQL usando portale di Azure

Questo articolo descrive come configurare un singolo server di database di Azure per PostgreSQL per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [server singolo database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) con piano tariffario per utilizzo generico o con ottimizzazione per la memoria.

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Seguire questa procedura per impostare PostgreSQL Single Server Deny Public Network Access:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server singolo database di Azure per PostgreSQL.

1. Nella pagina server singolo PostgreSQL, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In **Nega accesso alla rete pubblica** selezionare **Sì** per abilitare Nega accesso pubblico per il server singolo PostgreSQL.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Server singolo database di Azure per PostgreSQL Nega accesso alla rete":::

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Il server singolo database di Azure per PostgreSQL nega l'accesso alla rete riuscito":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
