---
title: Integrare Data Lake Storage Gen1 con Azure Data Catalog
description: Informazioni su come registrare i dati da Azure Data Lake Storage Gen1 in Azure Data Catalog per rendere individuabili i dati nell'organizzazione.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106621"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrare i dati di Azure Data Lake Storage Gen1 in Azure Data Catalog
Questo articolo descrive come integrare Azure Data Lake Storage Gen1 con Azure Data Catalog per rendere i dati individuabili all'interno dell'organizzazione integrandoli con Azure Data Catalog. Per altre informazioni sulla catalogazione dei dati, vedere [Azure Data Catalog](../data-catalog/overview.md). Per informazioni sugli scenari in cui è possibile usare il catalogo dati, vedere l'articolo relativo agli [scenari comuni di Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Abilitare la sottoscrizione di Azure** per Data Lake Storage Gen1. Vedere [le istruzioni](data-lake-store-get-started-portal.md).
* **Un account Data Lake Storage Gen1**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md). Per questa esercitazione, creare un account Data Lake Storage Gen1 denominato **datacatalogstore**.

    Dopo aver creato l'account, caricare un set di dati di esempio. Per questa esercitazione, caricare tutti i file con estensione csv nella cartella **AmbulanceData** del [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). È possibile usare vari tipi di client, ad esempio [Azure Storage Explorer](https://storageexplorer.com/), per caricare i dati in un contenitore BLOB.
* **Azure Data Catalog**. È necessario che per l'organizzazione sia già stato creato un catalogo di Azure Data Catalog. Per ogni organizzazione è consentito un solo catalogo.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrare Data Lake Storage Gen1 come origine per Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Passare a `https://azure.microsoft.com/services/data-catalog`e fare clic su **Introduzione**.
1. Accedere al portale di Azure Data Catalog e fare clic su **Pubblica dati**.

    ![Registrazione di un'origine dati](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrazione di un'origine dati")
1. Nella pagina successiva fare clic su **Avvia applicazione**. Verrà scaricato il file manifesto dell'applicazione nel computer in uso. Fare doppio clic sul file manifesto per avviare l'applicazione.
1. Nella pagina di benvenuto fare clic su **Accedi** e immettere le credenziali.

    ![Schermata iniziale](./media/data-lake-store-with-data-catalog/welcome.screen.png "Schermata iniziale")
1. Nella pagina Selezionare un'origine dati selezionare **Azure Data Lake Store** e quindi fare clic su **Avanti**.

    ![Seleziona origine dati](./media/data-lake-store-with-data-catalog/select-source.png "Selezionare l'origine dati")
1. Nella pagina successiva specificare il nome dell'account Data Lake Storage Gen1 che si vuole registrare in Data Catalog. Lasciare l'impostazione predefinita per le altre opzioni e quindi fare clic su **Connetti**.

    ![Connessione a un'origine dati](./media/data-lake-store-with-data-catalog/connect-to-source.png "Connessione a un'origine dati")
1. La pagina successiva può essere suddivisa nei segmenti seguenti.

    a. La casella **Gerarchia server** rappresenta la struttura della cartella dell'account Data Lake Storage Gen1. **$Root** rappresenta la radice dell'account Data Lake Storage Gen1, mentre **AmbulanceData** rappresenta la cartella creata nella radice dell'account Data Lake Storage Gen1.

    b. La casella **Oggetti disponibili** elenca i file e le cartelle sotto la cartella **AmbulanceData**.

    c. Nella casella **oggetti da** registrare sono elencati i file e le cartelle che si desidera registrare in Azure Data Catalog.

    ![Screenshot della finestra di dialogo Microsoft Azure account di archiviazione Data Catalog.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visualizzazione della struttura dei dati")
1. Per questa esercitazione, è necessario registrare tutti i file nella directory. A tale scopo, fare clic sul pulsante (![spostamento di oggetti](./media/data-lake-store-with-data-catalog/move-objects.png "Spostare gli oggetti")) per spostare tutti i file nella casella **Oggetti da registrare** .

    Poiché i dati vengono registrati in un catalogo dati dell'organizzazione, è consigliabile aggiungere alcuni metadati che è possibile usare in un secondo momento per individuare rapidamente i dati. Ad esempio, è possibile aggiungere un indirizzo di posta elettronica per il proprietario dei dati (ad esempio, uno che si occupa del caricamento dei dati) o aggiungere un tag per identificare i dati. La schermata seguente mostra un tag aggiunto ai dati.

    ![Screenshot della finestra di dialogo Microsoft Azure account di archiviazione Data Catalog con il tag aggiunto ai dati chiamati.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visualizzazione della struttura dei dati")

    Fare clic su **Registra**.
1. La schermata seguente indica che i dati sono stati registrati correttamente nel catalogo dati.

    ![Registrazione completata](./media/data-lake-store-with-data-catalog/registration-complete.png "Visualizzazione della struttura dei dati")
1. Fare clic su **Visualizza portale** per tornare al portale di Data Catalog e verificare che sia quindi possibile accedere ai dati registrati dal portale. Per cercare i dati, è possibile usare il tag che è stato usato durante la registrazione dei dati.

     ![Ricerca dei dati nel catalogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Ricerca dei dati nel catalogo")
1. È ora possibile eseguire operazioni come l'aggiunta di annotazioni e documentazione ai dati. Per altre informazioni, vedere i collegamenti seguenti:

    * [Annotare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Vedi anche
* [Annotare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrare Data Lake Storage Gen1 con altri servizi di Azure](data-lake-store-integrate-with-other-services.md)