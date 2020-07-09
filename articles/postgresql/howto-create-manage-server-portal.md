---
title: Gestire database di Azure per PostgreSQL-portale di Azure
description: Informazioni su come gestire un server di database di Azure per PostgreSQL dalla portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: d6cdef248663504a674f47eea2a8031b643f68a0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102396"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gestire un database di Azure per il server PostgreSQL usando il portale di Azure
Questo articolo illustra come gestire i server del database di Azure per PostgreSQL. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="sign-in"></a>Accedi
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Creare un server
Visitare la [Guida introduttiva](quickstart-create-server-database-portal.md) per informazioni su come creare e iniziare a usare un database di Azure per il server PostgreSQL.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

Dopo la creazione del server è possibile scalare tra i livelli per utilizzo generico e con ottimizzazione per la memoria in base alle esigenze. È anche possibile ridimensionare il calcolo e la memoria aumentando o diminuendo vcore. L'archiviazione può essere aumentata (Tuttavia, non è possibile ridimensionare lo spazio di archiviazione).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Scalabilità tra per utilizzo generico e livelli con ottimizzazione per la memoria

È possibile eseguire la scalabilità da per utilizzo generico a con ottimizzazione per la memoria e viceversa. La modifica da e verso il livello Basic dopo la creazione del server non è supportata. 

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

2. Selezionare **per utilizzo generico** o con ottimizzazione per la **memoria**, a seconda di ciò che si sta ridimensionando. 

    ![modifica-piano tariffario](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > La modifica di livelli comporta il riavvio del server.

4. Selezionare **OK** per salvare le modifiche.


### <a name="scale-vcores-up-or-down"></a>Ridimensionare vcore

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

2. Modificare l'impostazione di **vCore** spostando il dispositivo di scorrimento sul valore desiderato.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Il ridimensionamento di Vcore causa un riavvio del server.

3. Selezionare **OK** per salvare le modifiche.


### <a name="scale-storage-up"></a>Ridimensionare l'archiviazione

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

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

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Selezionare **OK** per salvare la nuova password.


## <a name="delete-a-server"></a>Eliminazione di un server

Se non è più necessario, è possibile eliminare il server. 

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Elimina**.

    ![eliminare](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digitare il nome del server nella casella di input per confermare che si tratta del server che si desidera eliminare.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > L'eliminazione di un server è irreversibile.

3. Selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [backup e sul ripristino del server](howto-restore-server-portal.md)
- Informazioni sulle [Opzioni di ottimizzazione e monitoraggio in database di Azure per PostgreSQL](concepts-monitoring.md)
