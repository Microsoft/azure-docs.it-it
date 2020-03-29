---
title: Visualizzare le modifiche nel contenuto dei file con Automazione di Azure
description: Usare la funzionalità di modifica del contenuto del file di Rilevamento modifiche per visualizzare il contenuto che è stato modificato.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 23c4f24e430d58895eb551c3e2cb62b5f0003ac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75418818"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Visualizza i contenuti di un file verificati con Rilevamento modifiche

La verifica del contenuto dei file consente di visualizzare i contenuti prima e dopo che una modifica viene rilevata con Rilevamento modifiche. A tale scopo, consente di salvare il contenuto del file in un account di archiviazione dopo ogni modifica.

## <a name="requirements"></a>Requisiti

* Un account di archiviazione standard con il modello di distribuzione di Gestione risorse è necessario per la memorizzazione del contenuto del file. Gli account di archiviazione dei modelli di distribuzione Premium e classico non devono essere utilizzati. Per altre informazioni sugli account di archiviazione, vedere Informazioni sugli account di archiviazione di [AzureFor more information](../storage/common/storage-create-storage-account.md) on storage accounts, see About Azure storage accounts

* L'account di archiviazione usato può avere solo 1 account di automazione collegato.

* [Il rilevamento delle modifiche](automation-change-tracking.md) è abilitato nell'account di automazione.

## <a name="enable-file-content-tracking"></a>Abilita il rilevamento del contenuto dei file

1. Nel portale di Azure, aprire l'account di Automazione e selezionare **Rilevamento modifiche**.
2. Nel menu in alto, selezionare **Modifica Impostazioni**.
3. Selezionare **Contenuto file** e fare clic su **Collegamento**. Si aprirà così il riquadro **Aggiungere la posizione del contenuto per Rilevamento modifiche**.

   ![abilitare](./media/change-tracking-file-contents/enable.png)

4. Selezionare la sottoscrizione e l'account di archiviazione da usare per memorizzare il contenuto del file. Se si desidera abilitare il rilevamento di contenuto dei file per tutti i file verificati esistenti, selezionare **On** per **Caricare il contenuto del file per tutte le impostazioni**. È possibile modificare questa impostazione per qualsiasi percorso di file in un secondo momento.

   ![impostare l'account di archiviazione](./media/change-tracking-file-contents/storage-account.png)

5. Una volta abilitati, vengono visualizzati l'account di archiviazione e gli URI di firma di accesso condiviso. Gli URI di firma di accesso condiviso scadono dopo 365 giorni e possono essere ricreati facendo clic su **Rigenera**.

   ![elencare le chiavi dell'account](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Aggiungere un file

I passaggi seguenti consentono di eseguire l'attivazione per il rilevamento delle modifiche su un file:

1. Nella pagina **Modifica impostazioni** di **Rilevamento modifiche**, selezionare la scheda **File di Windows** o **File di Linux** e fare clic su **Aggiungi**

1. Inserire le informazioni per il percorso del file e selezionare **True** sotto **Caricare il contenuto del file per tutte le impostazioni**. Questa impostazione abilita il rilevamento delle modifiche del file solo per quel determinato percorso.

   ![aggiungere un file di Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Visualizzazione del contenuto di un file rilevato

1. Una volta rilevata una modifica per il file o per un file nel percorso, appare nel portale. Selezionare la modifica del file nell'elenco delle modifiche. Viene visualizzato il riquadro **Dettagli modifiche**.

   ![elencare le modifiche](./media/change-tracking-file-contents/change-list.png)

1. Nella pagina **Dettagli modifiche**, è possibile visualizzare le informazioni standard sul contenuto di un file prima e dopo le modifiche; in alto a sinistra, fare clic su **Visualizzare le modifiche al contenuto del file**.

   ![dettagli modifiche](./media/change-tracking-file-contents/change-details.png)

1. La nuova pagina mostra il contenuto del file in una visualizzazione side-by-side. È anche possibile selezionare **Inline** per visualizzare una visualizzazione in linea delle modifiche.

   ![visualizzare modifiche dei file](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere l'esercitazione su Rilevamento modifiche per altre informazioni sull'uso della soluzione:

> [!div class="nextstepaction"]
> [Risolvere i problemi delle modifiche nell'ambiente](automation-tutorial-troubleshoot-changes.md)

* Usare [Ricerche log nei log di Monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare dati dettagliati sul rilevamento delle modifiche.

