---
title: Accedere ai log di query lente-portale di Azure database di Azure per MariaDB
description: Questo articolo descrive come configurare e accedere ai log di query lente nel database di Azure per MariaDB dalla portale di Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: c5ee948daecafc061910f36d2ac95d15338bfb38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662509"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Configurare e accedere ai log di query lente del database di Azure per Maria dal portale di Azure

È possibile configurare, elencare e scaricare i [log di query lente del database di Azure per MariaDB](concepts-server-logs.md) dalla portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per la procedura descritta in questo articolo è necessario disporre di [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurare la registrazione
Configurare l'accesso al log di query lente. 

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server del Database di Azure per MariaDB.

3. Nella sezione **monitoraggio** della barra laterale selezionare log del **Server**. 
   ![Screenshot delle opzioni dei log del server](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Per visualizzare i parametri del server, selezionare **fare clic qui per abilitare i log e configurare i parametri del log**.

5. Attivare **slow_query_log** . 

6. Consente di selezionare il percorso di output dei log utilizzando **log_output**. Per inviare i log ai log di diagnostica di monitoraggio di Azure e di archiviazione locale, selezionare **file**. 

7. Modificare gli altri parametri necessari. 

8. Selezionare **Salva**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Screenshot dei parametri di log di query lente e Salva.":::

Dalla pagina **parametri server** è possibile tornare all'elenco dei log chiudendo la pagina.

## <a name="view-list-and-download-logs"></a>Visualizzare l'elenco e scaricare i log
Una volta iniziata la registrazione, è possibile visualizzare un elenco di log di query lente disponibili e scaricare i singoli file di log. 

1. Aprire il portale di Azure.

2. Selezionare il server del Database di Azure per MariaDB.

3. Nella sezione **monitoraggio** della barra laterale selezionare log del **Server**. La pagina Visualizza un elenco dei file di log.

   ![Screenshot della pagina log del server con elenco dei log evidenziato](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > La convenzione di denominazione del log è **mysql-slow-< nome server>-yyyymmddhh.log**. La data e l'ora utilizzate nel nome del file corrispondono all'ora in cui è stato emesso il log. I file di log vengono ruotati ogni 24 ore o 7,5 GB, a seconda di quale si verifichi per primo.

4. Se necessario, usare la casella di ricerca per restringere rapidamente a un log specifico, in base alla data e all'ora. La ricerca viene eseguita in base al nome del log.

5. Per scaricare i singoli file di log, selezionare l'icona della freccia rivolta verso il basso accanto a ogni file di log nella riga della tabella.

   ![Screenshot della pagina log del server con icona con la freccia giù evidenziata](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

1. Nella sezione **monitoraggio** della barra laterale selezionare impostazioni di **diagnostica**  >  **Aggiungi impostazione di diagnostica**.

   ![Screenshot delle opzioni delle impostazioni di diagnostica](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Specificare un nome per l'impostazione di diagnostica.

1. Specificare i sink di dati per l'invio dei log delle query lente (account di archiviazione, Hub eventi o area di lavoro Log Analytics).

1. Selezionare **MySqlSlowLogs** come tipo di log.
![Screenshot delle opzioni di configurazione delle impostazioni di diagnostica](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Dopo aver configurato i sink di dati per inviare tramite pipe i log delle query lente a, selezionare **Salva**.
![Screenshot delle opzioni di configurazione delle impostazioni di diagnostica con il salvataggio evidenziato](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Per accedere ai log di query lente, esplorarli nei sink di dati configurati. Possono essere necessari fino a 10 minuti per la visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi
- Vedere [accedere ai log di query lente nell'interfaccia della](howto-configure-server-logs-cli.md) riga di comando per informazioni su come scaricare i log di query lente a livello di codice
- Altre informazioni sui [log di query lente](concepts-server-logs.md) nel database di Azure per MariaDB.
- Per ulteriori informazioni sulle definizioni di parametri e sulla registrazione, vedere la documentazione di MariaDB nei [log](https://mariadb.com/kb/en/library/slow-query-log-overview/).