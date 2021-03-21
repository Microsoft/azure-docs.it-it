---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per MySQL
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il database di Azure per MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b6fd5b5f70eb813792be003836790752db1d071f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732820"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel database di Azure per MySQL usando portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MySQL per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) con piano tariffario per utilizzo generico o con ottimizzazione per la memoria

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla procedura seguente per impostare il server MySQL per negare l'accesso alla rete pubblica:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MySQL esistente.

1. Nella pagina server MySQL, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In **Nega accesso alla rete pubblica** selezionare **Sì** per abilitare Nega accesso pubblico per il server MySQL.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Accesso alla rete negato per database di Azure per MySQL":::

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Il database di Azure per MySQL ha negato l'accesso alla rete":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
