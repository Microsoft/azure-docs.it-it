---
title: Condividere all'esterno dell'organizzazione (portale di Azure) - Avvio rapido su Condivisione dati di Azure
description: Questa guida di avvio rapido illustra come condividere dati con clienti e partner usando Condivisione dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 10/30/2020
ms.openlocfilehash: 1442720fdf48aaa7da76e181b168a04306ff3e33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96186457"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Avvio rapido: Condividere dati con Condivisione dati di Azure nel portale di Azure

Questa guida di avvio rapido illustra come configurare una nuova istanza di Condivisione dati di Azure per condividere i dati dell'account di archiviazione tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella che verrà usata per creare la risorsa Condivisione dati, registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui risiede l'archivio dati di Azure. 

### <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazione

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di archiviazione di Azure](../storage/common/storage-account-create.md).
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo di **proprietario**. 

## <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale.

1. Cercare *Condivisione dati*.

1. Selezionare **Condivisione dati** e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

   **Impostazione** | **Valore consigliato** | **Descrizione campo**
   |---|---|---|
   | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
   | Gruppo di risorse | *test-resource-group* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
   | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
   | Nome | *datashareaccount* | Specificare un nome per l'account di condivisione dati. |

1. Selezionare **Rivedi e crea**, quindi **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti.

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

## <a name="create-a-share"></a>Creare una condivisione

1. Passare alla pagina di panoramica della condivisione dati.

   ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Crea**.

1. Immettere i dettagli della condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

   ![Immettere i dettagli della condivisione](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**.

1. Per aggiungere set di dati alla condivisione, selezionare **Aggiungi set di dati**. 

   ![Aggiungere set di dati alla condivisione](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. Verrà visualizzato un elenco di tipi di set di dati diverso a seconda del tipo di condivisione (snapshot o sul posto) selezionato nel passaggio precedente. 

   ![Aggiungere i set di dati](./media/add-datasets.png "Aggiungere i set di dati")    

1. Passare all'oggetto che si vuole condividere e selezionare "Add Datasets" (Aggiungi set di dati). 

   ![Selezionare i set di dati](./media/select-datasets.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario".

   ![Aggiungere i destinatari](./media/add-recipient.png "Aggiungere destinatari") 

1. Selezionare **Continua**.

1. Se è stato selezionato tipo di condivisione snapshot, è possibile configurare la pianificazione degli snapshot per fornire aggiornamenti dei dati al consumer di dati. 

   ![Abilitare gli snapshot](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**.

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando la risorsa non è più necessaria, passare alla pagina **Panoramica di Condivisione dati** e selezionare **Elimina** per rimuoverla.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare una condivisione dati di Azure. Per informazioni su come un consumer di dati può accettare e ricevere una condivisione dati, continuare con l'esercitazione [Accettare e ricevere dati](subscribe-to-data-share.md). 
