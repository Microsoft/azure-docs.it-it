---
title: Negare l'accesso alla rete pubblica-portale di Azure-database di Azure per MariaDB
description: Informazioni su come configurare l'accesso negato alla rete pubblica usando portale di Azure per il database di Azure per MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 7925107f4334df7a844b3f3e029f3769eef51a9c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665073"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Negare l'accesso alla rete pubblica nel database di Azure per MariaDB usando portale di Azure

Questo articolo descrive come configurare un database di Azure per il server MariaDB per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

* Un [database di Azure per MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Impostare Nega accesso alla rete pubblica

Attenersi alla procedura seguente per impostare il server MariaDB per negare l'accesso alla rete pubblica:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server MariaDB esistente.

1. Nella pagina Server MariaDB, in **Impostazioni**, fare clic su **sicurezza connessione** per aprire la pagina Configurazione sicurezza connessione.

1. In Nega accesso alla rete pubblica selezionare **Sì** per abilitare Nega accesso pubblico per il server MariaDB.

    ![Accesso alla rete negato per database di Azure per MariaDB](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Fare clic su **Salva** per salvare le modifiche.

1. Una notifica conferma che l'impostazione di sicurezza della connessione è stata abilitata correttamente.

    ![Il database di Azure per MariaDB nega l'accesso alla rete riuscito](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-metric.md).