---
title: Introduzione a Azure Data Lake Storage Gen1-Portal
description: Usare il portale di Azure per creare un account di Data Lake Storage Gen1 ed eseguire operazioni di base nell'account.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 7dac5976904263de40c7bc240bdb7deac1ec3df6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509201"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Iniziare a usare Azure Data Lake Storage Gen1 tramite il portale di Azure

> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Informazioni su come usare la portale di Azure per creare un account di Data Lake Storage Gen1 ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni, vedere [Panoramica di Azure Data Lake storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione sono necessari gli elementi seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).
2. Fare clic su **Crea una risorsa > Archiviazione > Data Lake Storage Gen1**.
3. Nel pannello **Nuovo account Data Lake Storage Gen1** specificare i valori come illustrato nello screenshot seguente:

    ![Crea un nuovo account Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Crea un nuovo account Data Lake Storage Gen1")

   * **Nome**. Immettere un nome univoco per l'account Data Lake Storage Gen1.
   * **Sottoscrizione**. Selezionare la sottoscrizione in cui si vuole creare un nuovo account Data Lake Storage Gen1.
   * **Gruppo di risorse**. Selezionare un gruppo di risorse esistente oppure selezionare l'opzione **Crea nuovo** per crearne uno. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Per altre informazioni, vedere [Gruppi di risorse in Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Località**: selezionare una località dove creare l'account Data Lake Storage Gen1.
   * **Impostazioni di crittografia**. Sono disponibili tre opzioni:

     * **Non abilitare la crittografia**.
     * **Usare chiavi gestite da Data Lake Storage Gen1**, se si vuole che Data Lake Storage Gen1 gestisca le chiavi di crittografia.
     * **Usare chiavi incluse nell'insieme di credenziali delle chiavi personale**. È possibile selezionare un'istanza di Azure Key Vault esistente oppure crearne una nuova. Per usare le chiavi di un'istanza di Key Vault, è necessario assegnare all'account Data Lake Storage Gen1 le autorizzazioni per accedere ad Azure Key Vault. Per istruzioni, vedere [Assegnare le autorizzazioni per Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Crittografia Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Crittografia Data Lake Storage Gen1")

        Fare clic su **OK** nel pannello **Impostazioni crittografia**.

        Per altre informazioni, vedere [Crittografia dei dati in Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Fare clic su **Crea**. Se si è scelto di aggiungere l'account al dashboard, viene visualizzato di nuovo il dashboard, dove è possibile visualizzare lo stato di avanzamento del provisioning dell'account Data Lake Storage Gen1. Una volta eseguito il provisioning dell'account Data Lake Storage Gen1, viene visualizzato il pannello dell'account.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Assegnare le autorizzazioni per Azure Key Vault

Se sono state usate le chiavi di un'istanza di Azure Key Vault per configurare la crittografia nell'account Data Lake Storage Gen1, è necessario configurare l'accesso tra l'account Data Lake Storage Gen1 e l'account Azure Key Vault. Seguire quindi questa procedura.

1. Se sono state usate le chiavi di Azure Key Vault, nella parte superiore del pannello dell'account Data Lake Storage Gen1 viene visualizzato un avviso. Fare clic sull'avviso per aprire **Crittografia**.

    ![Crittografia Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Crittografia Data Lake Storage Gen1")
2. Il pannello mostra due opzioni per configurare l'accesso.

    ![Crittografia Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Crittografia Data Lake Storage Gen1")

   * Nella prima opzione fare clic su **Concedi autorizzazioni** per configurare l'accesso. La prima opzione è abilitata solo quando l'utente che ha creato l'account Data Lake Storage Gen1 è anche un amministratore di Azure Key Vault.
   * L'altra opzione consiste nell'eseguire il cmdlet di PowerShell visualizzato nel pannello. È necessario essere il proprietario dell'insieme di credenziali delle chiavi di Azure o poter concedere le autorizzazioni per l'insieme di credenziali delle chiavi di Azure. Dopo avere eseguito il cmdlet, tornare al pannello e fare clic su **Abilita** per configurare l'accesso.

> [!NOTE]
> È anche possibile creare un account Data Lake Storage Gen1 usando modelli di Azure Resource Manager. Tali modelli sono accessibili dalla pagina [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store).
> * Senza crittografia dei dati: [Deploy Azure Data Lake Storage Gen1 account with no data encryption](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/) (Distribuire un account Azure Data Lake Storage Gen1 senza crittografia dei dati).
> * Con crittografia dei dati usando Data Lake Storage Gen1: [Deploy Data Lake Storage Gen1 account with encryption (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/) (Distribuire un account Data Lake Storage Gen1 con crittografia - Data Lake).
> * Con crittografia dei dati usando Azure Key Vault: [Deploy Data Lake Storage Gen1 account with encryption (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/) (Distribuire un account Data Lake Storage Gen1 con crittografia - Key Vault).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Creazione di cartelle

È possibile creare cartelle nell'account Data Lake Storage Gen1 per gestire e archiviare i dati.

1. Aprire l'account Data Lake Storage Gen1 creato. Nel riquadro sinistro fare clic su **tutte le risorse**e quindi nel pannello **tutte le risorse** fare clic sul nome dell'account in cui si desidera creare le cartelle. Se è stato aggiunto l'account alla schermata iniziale, fare clic sul riquadro dell’account.
2. Nel pannello dell'account Data Lake Storage Gen1 fare clic su **Esplora dati**.

    ![Creare cartelle in un account Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Creare cartelle in un account Data Lake Storage Gen1")
3. Nel pannello Esplora dati fare clic su **Nuova cartella**, immettere un nome per la nuova cartella e quindi fare clic su **OK**.

    ![Creare cartelle in un account Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Creare cartelle in un account Data Lake Storage Gen1")

    La cartella appena creata verrà elencata nel pannello **Esplora dati**. È possibile creare cartelle nidificate per qualsiasi livello.

    ![Creazione di cartelle in un account Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Creazione di cartelle in un account Data Lake")

## <a name="upload-data"></a><a name="uploaddata"></a>Caricare dati

È possibile caricare i dati in un account Data Lake Storage Gen1 direttamente al livello radice o in una cartella creata nell'account.

1. Nel pannello **Esplora dati** fare clic su **Carica**.
2. Nel pannello **Carica file** passare ai file da caricare e quindi fare clic su **Aggiungi file selezionati**. È anche possibile selezionare più di un file da caricare.

    ![Caricare dati](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Caricare dati")

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Azioni disponibili sui dati archiviati

Fare clic sull'icona con i puntini di sospensione per un file e dal menu a comparsa scegliere l'azione da eseguire sui dati.

![Proprietà sui dati](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Proprietà sui dati")

## <a name="secure-your-data"></a>Protezione dei dati

È possibile proteggere i dati archiviati nell'account Data Lake Storage Gen1 usando Azure Active Directory e il controllo di accesso (ACL). Per istruzioni su come eseguire questa operazione, vedere [Protezione dei dati in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Elimina l'account

Per eliminare un account Data Lake Storage Gen1, dal pannello di Data Lake Storage Gen1 fare clic su **Elimina**. Per confermare l'azione, verrà chiesto di immettere il nome dell'account che si desidera eliminare. Immettere il nome dell’account e quindi fare clic su **Elimina**.

![Elimina account Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminare account di Data Lake")

## <a name="next-steps"></a>Passaggi successivi

* [Usare Azure Data Lake Storage Gen1 per i requisiti di Big Data](data-lake-store-data-scenarios.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
