---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96029991"
---
## <a name="prerequisites"></a>Prerequisiti

### <a name="get-sample-code"></a>Ottenere il codice di esempio

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](../articles/container-registry/container-registry-tutorial-quick-task.md) e che siano state eseguite la copia tramite fork e la clonazione del repository di esempio. Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) dell'esercitazione precedente.

### <a name="container-registry"></a>Registro contenitori

Per completare questa esercitazione è necessario che la sottoscrizione di Azure includa un Registro Azure Container. Se è necessario un registro, vedere l'[esercitazione precedente](../articles/container-registry/container-registry-tutorial-quick-task.md) oppure [Guida introduttiva: Creare un registro contenitori con l'interfaccia della riga di comando di Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Creare un token di accesso personale GitHub

Per attivare una compilazione in caso di commit in un repository Git, Attività del Registro Azure Container richiede un token di accesso personale per accedere al repository. Se non si ha già un token di accesso personale, completare questi passaggi per generarne uno in GitHub:

1. Passare alla pagina per la creazione di token di accesso personali in GitHub all'indirizzo https://github.com/settings/tokens/new
1. Immettere una breve **descrizione** del token, ad esempio "ACR Tasks Demo".
1. Selezionare gli ambiti per l'accesso di Registro Azure Container al repository. Per accedere a un repository pubblico come in questa esercitazione, in **repo** abilitare **repo:status** e **public_repo**.

   ![Screenshot della pagina per la generazione di token di accesso personali in GitHub][build-task-01-new-token]

   > [!NOTE]
   > Per generare un token di accesso personale per accedere a un repository *privato*, selezionare l'ambito per il controllo **repo** completo.

1. Selezionare il pulsante **Generate token** (Genera token). Potrebbe essere richiesto di confermare la password.
1. Copiare e salvare il token generato in una **posizione sicura**. Questo token verrà usato per definire un'attività nella sezione seguente.

   ![Screenshot del token di accesso personale generato in GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
