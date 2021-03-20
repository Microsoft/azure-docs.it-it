---
title: Analizzare i dati in Azure Data Lake Storage Gen1 - Power BI
description: Informazioni su come usare Power BI Desktop per analizzare e visualizzare i dati archiviati nel Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91574195"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analizzare i dati in Azure Data Lake Storage Gen1 con Power BI
Questo articolo spiega come usare Power BI Desktop per analizzare e visualizzare i dati archiviati in Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Data Lake Storage Gen1**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md). Questo articolo presuppone che sia già stato creato un account Data Lake Storage Gen1 denominato **myadlsg1** e sia stato caricato un file di dati di esempio (**Drivers.txt**) nell'account. Il file di esempio può essere scaricato dal [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Può essere scaricato dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop
1. Avviare Power BI Desktop sul computer.
2. Nella barra multifunzione **Home** fare clic su **Recupera dati**, quindi su Altro. Nella finestra di dialogo **Recupera dati** fare clic su **Azure**, **Azure Data Lake Store** e quindi su **Connetti**.
   
    ![Screenshot della finestra di dialogo Ottieni dati con l'opzione Azure Data Lake Store evidenziata e l'opzione Connetti.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Connettersi a Data Lake Storage Gen1")
3. Se viene visualizzata una finestra di dialogo relativa al connettore in una fase di sviluppo, scegliere di continuare.
4. Nella finestra di dialogo **Azure Data Lake Store** indicare l'URL del proprio account Data Lake Storage Gen1 e fare clic su **OK**.
   
    ![URL per Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL per Data Lake Storage Gen1")
5. Nella finestra di dialogo successiva fare clic su **Accedi** per accedere all'account Data Lake Storage Gen1. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. Seguire le istruzioni per accedere all'account.
   
    ![Accedere a Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Accedere a Data Lake Storage Gen1")
6. Dopo aver completato l'accesso, fare clic su **Connetti**.
   
    ![Screenshot della finestra di dialogo Azure Data Lake Store con l'opzione Connect denominata out.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Connettersi a Data Lake Storage Gen1")
7. Nella finestra di dialogo successiva viene visualizzato il file caricato nell'account Data Lake Storage Gen1. Verificare le informazioni e fare clic su **Carica**.
   
    ![Caricare i dati da Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Caricare i dati da Data Lake Storage Gen1")
8. Dopo il caricamento dei dati in Power BI, nella scheda **Campi** vengono visualizzati i campi indicati di seguito.
   
    ![Campi importati](./media/data-lake-store-power-bi/imported-fields.png "Campi importati")
   
    Al fine della visualizzazione e dell'analisi è tuttavia preferibile che i dati siano organizzati in questi campi:
   
    ![Campi desiderati](./media/data-lake-store-power-bi/desired-fields.png "Campi desiderati")
   
    La procedura che segue descrive come aggiornare la query per convertire i dati importati nel formato desiderato.
9. Nella barra multifunzione **Home** fare clic su **Modifica query**.
   
    ![Screenshot della barra multifunzione Home con l'opzione modifica query denominata.](./media/data-lake-store-power-bi/edit-queries.png "Modifica query")
10. Nell'editor di query fare clic su **Binario** nella colonna **Contenuto**.
    
    ![Screenshot dell'editor di query con la colonna di contenuto denominata out.](./media/data-lake-store-power-bi/convert-query1.png "Modifica query")
11. Viene visualizzata un'icona che rappresenta il file **Drivers.txt** caricato. Fare clic sul file con il pulsante destro del mouse e scegliere **CSV**.    
    
    ![Screenshot dell'editor di query con l'opzione CSV denominata.](./media/data-lake-store-power-bi/convert-query2.png "Modifica query")
12. L'output sarà come quello illustrato di seguito. I dati ora sono disponibili in un formato che può essere usato per creare visualizzazioni.
    
    ![Screenshot dell'editor di query con l'output visualizzato come previsto.](./media/data-lake-store-power-bi/convert-query3.png "Modifica query")
13. Dalla barra multifunzione **Home** fare clic su **Chiudi e applica**, quindi su **Chiudi e applica**.
    
    ![Screenshot della barra multifunzione Home con l'opzione Chiudi e applica denominata.](./media/data-lake-store-power-bi/load-edited-query.png "Modifica query")
14. Dopo l'aggiornamento della query, la scheda **Campi** contiene i nuovi campi disponibili per la visualizzazione.
    
    ![Campi aggiornati](./media/data-lake-store-power-bi/updated-query-fields.png "Campi aggiornati")
15. A questo punto è possibile creare un grafico a torta che rappresenta i conducenti di ogni città di un determinato paese o area geografica. Per eseguire questa operazione, effettuare le selezioni seguenti.
    
    1. Nella scheda Visualizzazioni fare clic sul simbolo del grafico a torta.
       
        ![Creare un grafico a torta](./media/data-lake-store-power-bi/create-pie-chart.png "Creazione di un grafico a torta")
    2. Verranno utilizzate **Colonna 4** (nome della città) e **Colonna 7** (nome del paese/area geografica). Trascinare le colonne dalla scheda **Campi** nella scheda **Visualizzazioni** come illustrato di seguito.
       
        ![Creare visualizzazioni](./media/data-lake-store-power-bi/create-visualizations.png "Creare visualizzazioni")
    3. Il grafico a torta ora dovrebbe essere simile a quello che segue.
       
        ![Grafico a torta](./media/data-lake-store-power-bi/pie-chart.png "Creare visualizzazioni")
16. Se si seleziona un determinato paese o area geografica dai filtri a livello di pagina, sarà possibile visualizzare il numero di conducenti di ogni città dell'area geografica o del paese selezionato. Ad esempio, nella scheda **Visualizzazioni** selezionare **Brazil** da **Filtri a livello di pagina**.
    
    ![Selezionare un paese/area geografica](./media/data-lake-store-power-bi/select-country.png "Selezionare un paese/area geografica")
17. Il grafico a torta viene automaticamente aggiornato in modo da visualizzare i conducenti delle città del Brasile.
    
    ![Conducenti in un paese/area geografica](./media/data-lake-store-power-bi/driver-per-country.png "Conducenti per paese/area geografica")
18. Dal menu **File** fare clic su **Salva** per salvare la visualizzazione come file di Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Pubblicare report nel servizio Power BI
Dopo aver creato le visualizzazioni in Power BI Desktop, è possibile condividerle con altri utenti pubblicandole nel servizio Power BI. Per istruzioni sulla procedura, vedere [Pubblicare da Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Vedere anche
* [Analizzare i dati in Data Lake Storage Gen1 con Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

