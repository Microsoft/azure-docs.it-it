---
title: Configurare i parametri del server-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come configurare i parametri Postgres nel database di Azure per il server flessibile PostgreSQL tramite il portale di Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607440"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configurare i parametri del server nel database di Azure per PostgreSQL-server flessibile tramite il portale di Azure 

È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un server flessibile di database di Azure per PostgreSQL tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, è richiesto:
- [Server flessibile per database di Azure per PostgreSQL](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visualizzazione e modifica dei parametri

1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare il server flessibile.

3. Nella sezione **IMPOSTAZIONI** selezionare **Parametri del server**. La pagina mostra un elenco di parametri, i relativi valori e le descrizioni.
![Pagina di panoramica per i parametri](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selezionare il pulsante dell'**elenco a discesa** per vedere i valori possibili per i parametri di tipo enumerato, come client_min_messages.
![Elenco a discesa dei parametri di tipo enumerato](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selezionare o passare il puntatore sul pulsante **i** (informazioni) per vedere l'intervallo di valori possibili per i parametri numerici come cpu_index_tuple_cost.
![Pulsante informazioni](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessario, usare la **casella di ricerca** per restringere l'elenco a un parametro specifico. La ricerca viene effettuata sul nome e sulla descrizione dei parametri.
![Risultati della ricerca](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Modificare i valori dei parametri da regolare. Tutte le modifiche apportate in una sessione vengono evidenziate in viola. Dopo aver modificato i valori, è possibile selezionare **Salva**. In alternativa è possibile **annullare** le modifiche.
![Salvataggio o rimozione delle modifiche](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se sono stati salvati nuovi valori per i parametri, è possibile ripristinare i valori predefiniti in qualsiasi momento selezionando **Ripristina tutte le impostazioni predefinite**.
![Ripristino di tutte le impostazioni predefinite](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su:

- [Panoramica dei parametri del server nel database di Azure per PostgreSQL](concepts-servers.md)
- [Configurazione dei parametri usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md)
