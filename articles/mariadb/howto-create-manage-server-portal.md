---
title: Gestire server-portale di Azure-database di Azure per MariaDB
description: Informazioni su come gestire un database di Azure per il server MariaDB dalla portale di Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ade70c884a3ef01db45273e4789f34b629473661
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537986"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Gestire un database di Azure per il server MariaDB usando il portale di Azure
Questo articolo illustra come gestire il database di Azure per i server MariaDB. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="sign-in"></a>Accesso
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Creare un server
Per informazioni su come creare e iniziare a usare un database di Azure per il server MariaDB, vedere la [Guida introduttiva](quickstart-create-mariadb-server-database-using-azure-portal.md) .

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

Dopo la creazione del server è possibile scalare tra i livelli per utilizzo generico e con ottimizzazione per la memoria in base alle esigenze. È anche possibile ridimensionare il calcolo e la memoria aumentando o diminuendo vcore. L'archiviazione può essere aumentata (Tuttavia, non è possibile ridimensionare lo spazio di archiviazione).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Scalabilità tra per utilizzo generico e livelli con ottimizzazione per la memoria

È possibile eseguire la scalabilità da per utilizzo generico a con ottimizzazione per la memoria e viceversa. La modifica da e verso il livello Basic dopo la creazione del server non è supportata. 

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario** , che si trova nella sezione **Impostazioni** .

2. Selezionare **per utilizzo generico** o con ottimizzazione per la **memoria** , a seconda di ciò che si sta ridimensionando. 

    ![Screenshot mostra i portale di Azure con il piano tariffario selezionato e il valore di ottimizzato per la memoria selezionato.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > La modifica di livelli comporta il riavvio del server.

4. Selezionare **OK** per salvare le modifiche.


### <a name="scale-vcores-up-or-down"></a>Ridimensionare vcore

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario** , che si trova nella sezione **Impostazioni** .

2. Modificare l'impostazione di **vCore** spostando il dispositivo di scorrimento sul valore desiderato.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Il ridimensionamento di Vcore causa un riavvio del server.

3. Selezionare **OK** per salvare le modifiche.


### <a name="scale-storage-up"></a>Ridimensionare l'archiviazione

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario** , che si trova nella sezione **Impostazioni** .

2. Modificare l'impostazione di **archiviazione** spostando il dispositivo di scorrimento fino al valore desiderato.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Non è possibile ridimensionare l'archiviazione.

3. Selezionare **OK** per salvare le modifiche.


## <a name="update-admin-password"></a>Aggiorna password amministratore
È possibile modificare la password del ruolo amministratore utilizzando la portale di Azure.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Reimposta password**.

   ![cenni preliminari](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Immettere una nuova password e confermarla. Nella casella di testo vengono visualizzati i requisiti di complessità delle password.

   ![Screenshot mostra la finestra di dialogo Reimposta password con password e conferma password.](./media/howto-create-manage-server-portal/reset-password.png)

3. Selezionare **OK** per salvare la nuova password.


## <a name="delete-a-server"></a>Eliminazione di un server

Se non è più necessario, è possibile eliminare il server. 

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Elimina**.

    ![eliminare](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digitare il nome del server nella casella di input per confermare che si tratta del server che si desidera eliminare.

    ![Screenshot mostra una finestra di dialogo che verifica se si desidera eliminare un database irreversibile.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > L'eliminazione di un server è irreversibile.

3. Selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [backup e sul ripristino del server](howto-restore-server-portal.md)
- Informazioni sulle [Opzioni di ottimizzazione e monitoraggio nel database di Azure per MariaDB](concepts-monitoring.md)
