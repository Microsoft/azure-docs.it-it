---
title: Importare ed eseguire notebook da un repository GitHub in Azure Cosmos DB
description: Informazioni su come connettersi a GitHub e importare i notebook da un repository GitHub in un account Azure Cosmos. Dopo averli importati, è possibile eseguirli, modificarli e salvare le modifiche in GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690652"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importare notebook da un repository GitHub in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dopo aver [abilitato il supporto dei notebook](enable-notebooks.md) per gli account Azure Cosmos, è possibile creare nuovi notebook, caricare nuovi notebook da un computer locale o importare notebook esistenti da account GitHub. Questo articolo illustra come connettere l'area di lavoro dei notebook a GitHub e importare i notebook da un repository GitHub in un account Azure Cosmos. Dopo averli importati, è possibile eseguirli, apportare modifiche e salvare le modifiche in GitHub.

## <a name="get-notebooks-from-github"></a>Acquisire notebook da GitHub

È possibile connettersi ai repository GitHub personali o ad altri repository GitHub pubblici per leggere, creare e condividere notebook in Azure Cosmos DB. Per connettersi a un account GitHub, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'account Azure Cosmos.

1. Aprire la scheda **Esplora dati**. In questa scheda vengono visualizzati tutti i database, i contenitori e i notebook esistenti.

1. Selezionare la voce di menu **Connetti a GitHub**.

1. Viene visualizzata una scheda in cui è possibile scegliere di connettersi solo a **repository pubblici** o a **repository pubblici e privati**.  Dopo aver scelto l'opzione appropriata, selezionare il pulsante di **autorizzazione dell'accesso**. L'autorizzazione è necessaria per consentire ad Azure Cosmos DB di accedere ai repository nell'account GitHub in uso.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autorizzare Azure Cosmos DB ad accedere ai repository GitHub":::

1. Si verrà reindirizzati alla pagina Web "github.com" in cui è possibile confermare l'autorizzazione. Selezionare il pulsante **Autorizza AzureCosmosDBNotebooks** e, quando richiesto, immettere la password dell'account GitHub.

1. Se l'autorizzazione viene concessa, si torna all'account Azure Cosmos, in cui è possibile visualizzare tutti i repository pubblici/privati importati dall'account GitHub. È possibile selezionare un repository dall'elenco visualizzato oppure aggiungere direttamente un repository attraverso il relativo URL.

1. Dopo aver selezionato il repository necessario, la voce corrispondente viene spostata dalla sezione **Unpinned repos** (Repository rimossi) alla sezione **Pinned repos** (Repository aggiunti). Se necessario, è possibile anche scegliere un ramo specifico del repository da cui importare i notebook.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Scegliere un repository e un ramo":::

1. Selezionare **OK** per completare l'operazione di importazione. Tutti i notebook disponibili nel ramo selezionato del repository vengono importati nell'account Azure Cosmos.

Dopo aver eseguito l'integrazione con un account GitHub, solo l'utente può visualizzare l'elenco dei repository e dei notebook presenti nel proprio account Azure Cosmos. Questa affermazione è vera anche se più utenti accedono all'account Azure Cosmos DB e aggiungono gli account personali. In altre parole, più utenti possono usare contemporaneamente lo stesso account Azure Cosmos per connettere l'area di lavoro del notebook a GitHub. Ogni utente, tuttavia, vedrà solo l'elenco dei repository e dei notebook che ha importato, mentre non saranno visibili i notebook importati da altri utenti.

Per disconnettere l'account GitHub dall'area di lavoro dei notebook, aprire la scheda **Esplora dati**, selezionare `…` accanto a **Repository GitHub** e selezionare **Disconnettersi da GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Modificare un notebook ed eseguire il push delle modifiche in GitHub

È possibile modificare un notebook esistente o aggiungere un nuovo notebook al repository e salvare le modifiche in GitHub.

Dopo aver modificato un notebook esistente, selezionare **Salva**. Verrà visualizzata una finestra di dialogo in cui è possibile immettere il messaggio di commit per le modifiche apportate. Selezionare **Commit** per aggiornare il notebook in GitHub. Per convalidare gli aggiornamenti, effettuare l'accesso all'account GitHub e verificare la cronologia dei commit.

Nel tradizionale flusso di GitHub, dopo aver eseguito il commit delle modifiche, se ne esegue il push in un computer remoto. In questo caso, tuttavia, con l'opzione di commit si esegue "la gestione temporanea, il commit e il push" degli aggiornamenti in GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Modificare i notebook ed eseguire il commit delle modifiche in GitHub":::

Dopo aver modificato il notebook, è possibile [pubblicarlo nella raccolta di notebook](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Passaggi successivi

* Scoprire i vantaggi dei [notebook Jupyter di Azure Cosmos DB](cosmosdb-jupyter-notebooks.md).
* [Esplora la raccolta di esempi di notebook](https://cosmos.azure.com/gallery.html)
* [Pubblicare notebook nella raccolta Azure Cosmos DB notebook](publish-notebook-gallery.md)
* [Usare funzionalità e comandi del notebook Python](use-python-notebook-features-and-commands.md)
* [Usare funzionalità e comandi dei notebook C#](use-csharp-notebook-features-and-commands.md)
