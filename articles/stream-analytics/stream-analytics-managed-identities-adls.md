---
title: Autenticare analisi di flusso di Azure per Azure Data Lake Storage Gen1
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di Analisi di flusso di Azure nell'output di Azure Data Lake Storage Gen1.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 551f0065f1547e94d93993a38795234f455b9eef
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044397"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autenticare analisi di flusso per Azure Data Lake Storage Gen1 usando identità gestite

Analisi di flusso di Azure supporta l'autenticazione con identità gestita con l'output di Azure Data Lake Storage (ADLS) Gen1. L'identità è un'applicazione gestita registrata in Azure Active Directory che rappresenta un determinato processo di Analisi di flusso e può essere usata per l'autenticazione in una risorsa di destinazione. Le identità gestite eliminano le limitazioni associate ai metodi di autenticazione basata sull'utente, ad esempio la necessità di ripetere l'autenticazione a causa di modifiche della password o le scadenze dei token utente ogni 90 giorni. Inoltre, le identità gestite semplificano l'automazione delle distribuzioni dei processi di Analisi di flusso che inviano l'output ad Azure Data Lake Storage Gen1.

Questo articolo illustra tre metodi per abilitare l'identità gestita per un processo di Analisi di flusso di Azure che invia l'output ad Azure Data Lake Storage Gen1 tramite il portale di Azure, la distribuzione dei modelli di Azure Resource Manager e gli strumenti di Analisi di flusso di Azure per Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portale di Azure

1. Per iniziare, creare un nuovo processo di Analisi di flusso o aprire un processo esistente nel portale di Azure. Dalla barra dei menu disponibile sul lato sinistro della schermata selezionare **identità gestita** situata in **Configura**.

   ![Configurare l'identità gestita di analisi di flusso](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selezionare **Usa identità gestita assegnata dal sistema** dalla finestra visualizzata a destra. Fare clic su **Salva** in un'entità servizio per l'identità del processo di analisi di flusso in Azure Active Directory. Il ciclo di vita dell'identità creata sarà gestito da Azure. Quando si elimina il processo di Analisi di flusso, l'identità associata (ovvero, l'entità servizio) viene eliminata automaticamente da Azure.

   Al momento del salvataggio della configurazione, l'ID oggetto (OID) dell'entità servizio viene indicato come ID dell'entità, come illustrato di seguito:

   ![ID entità servizio di Analisi di flusso](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   L'entità servizio ha lo stesso nome del processo di Analisi di flusso. Ad esempio, se il nome del processo è **MyASAJob**, anche il nome dell'entità servizio creata è **MyASAJob**.

3. Nella finestra Proprietà output del sink di output ADLS Gen1 fare clic sull'elenco a discesa modalità di autenticazione e selezionare * * identità gestita * *.

4. Compilare le proprietà rimanenti. Per altre informazioni sulla creazione di un output di ADLS, vedere [Create a Data lake Store output with stream analytics](../data-lake-store/data-lake-store-stream-analytics.md) (Creare un output di Data Lake Store con Analisi di flusso). Al termine, fare clic su **Salva**.

   ![Configurare Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Passare alla pagina Panoramica di ADLS Gen1 e fare clic su **Esplora dati**.

   ![Configurare Data Lake Storage - Panoramica](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Nel riquadro Esplora dati selezionare **Accesso** e fare clic su **Aggiungi** nel riquadro di accesso.

   ![Configurare l'accesso a Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Nella casella di testo nel riquadro **Seleziona utente o gruppo** digitare il nome dell'entità servizio. Tenere presente che il nome dell'entità servizio è anche il nome del processo di Analisi di flusso corrispondente. Quando si inizia a digitare il nome dell'entità, questa viene visualizzata sotto la casella di testo. Scegliere il nome dell'entità servizio desiderata e fare clic su **Seleziona**.

   ![Selezionare il nome di un'entità servizio](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Nel riquadro **Autorizzazioni** selezionare le autorizzazioni **Scrittura** ed **Esecuzione**, quindi assegnarle a **Questa cartella e tutti gli elementi figlio**. Quindi fare clic su **OK**.

   ![Selezionare le autorizzazioni di scrittura ed esecuzione](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. L'entità servizio viene elencata in **Autorizzazioni assegnate** nel riquadro **Accesso** come illustrato di seguito. È ora possibile tornare indietro e avviare il processo di Analisi di flusso.

   ![Elenco di accesso di Analisi di flusso nel portale](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Per altre informazioni sulle autorizzazioni del file system di Data Lake Storage Gen1, vedere [Controllo di accesso in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Strumenti di Analisi di flusso per Visual Studio

1. In JobConfig.json impostare **Usa identità assegnata dal sistema** su **True**.

   ![Configurazione del processo di Analisi di flusso per le identità gestite](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Nella finestra Proprietà output del sink di output ADLS Gen1 fare clic sull'elenco a discesa modalità di autenticazione e selezionare * * identità gestita * *.

   ![Output di ADLS per le identità gestite](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Compilare le proprietà rimanenti e fare clic su **Salva**.

4. Nell'editor di query fare clic su **Invia ad Azure**.

   Quando si invia il processo, gli strumenti eseguono due operazioni:

   * Creano automaticamente un'entità servizio per l'identità del processo di Analisi di flusso in Azure Active Directory. Il ciclo di vita dell'identità creata sarà gestito da Azure. Quando si elimina il processo di Analisi di flusso, l'identità associata (ovvero, l'entità servizio) viene eliminata automaticamente da Azure.

   * Impostano automaticamente le autorizzazioni di **scrittura** ed **esecuzione** per il percorso di prefisso di ADLS Gen1 usato nel processo e lo assegnano a questa cartella e a tutti gli elementi figlio.

5. È possibile generare modelli di Resource Manager con la proprietà seguente usando il [pacchetto NuGet Stream Analytics CI.CD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 o versioni successive in un computer di compilazione (all'esterno di Visual Studio). Per ottenere l'entità servizio e concedere l'accesso all'entità servizio tramite PowerShell, seguire i passaggi della distribuzione del modello di Resource Manager nella sezione successiva.

## <a name="resource-manager-template-deployment"></a>Distribuzione del modello di Resource Manager

1. È possibile creare una risorsa *Microsoft.StreamAnalytics/streamingjobs* con un'identità gestita, includendo la proprietà seguente nella sezione delle risorse del modello di Resource Manager:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Questa proprietà indica ad Azure Resource Manager di creare e gestire l'identità per il processo di Analisi di flusso di Azure.

   **Processo di esempio**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Risposta del processo di esempio**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Prendere nota dell'ID entità della risposta del processo per concedere l'accesso alla risorsa di ADLS richiesta.

   **ID tenant** è l'ID del tenant di Azure Active Directory in cui creare l'entità servizio. L'entità servizio viene creata nel tenant di Azure considerato attendibile dalla sottoscrizione.

   **tipo** indica il tipo di identità gestita, come descritto nei tipi di identità gestite. È supportato solo il tipo Assegnata dal sistema.

2. Fornire l'accesso all'entità servizio tramite PowerShell. Per concedere l'accesso all'entità servizio tramite PowerShell, eseguire il comando seguente:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** è l'ID oggetto dell'entità servizio ed è elencato nella schermata del portale dopo aver creato l'entità servizio. Se il processo è stato creato usando la distribuzione di un modello di Resource Manager, l'ID oggetto è elencato nella proprietà Identity della risposta del processo.

   **Esempio**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Per altre informazioni sul comando PowerShell precedente, vedere la documentazione di [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="limitations"></a>Limitazioni
Questa funzionalità non supporta quanto segue:

1. **Accesso multi-tenant**: l'entità servizio creata per un determinato processo di analisi di flusso si trova nel tenant Azure Active Directory in cui è stato creato il processo e non può essere usata per una risorsa che risiede in un tenant di Azure Active Directory diverso. È quindi possibile usare l'identità del servizio gestito solo per le risorse ADLS di generazione 1 che si trovano all'interno dello stesso tenant di Azure Active Directory del processo di analisi di flusso di Azure. 

2. **[Identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md)**: non supportata. Ciò significa che l'utente non è in grado di immettere la propria entità servizio per l'uso da parte del processo di analisi di flusso. L'entità servizio viene generata da analisi di flusso di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Create a Data lake Store output with stream analytics](../data-lake-store/data-lake-store-stream-analytics.md) (Creare un output di Data Lake Store con Analisi di flusso)
* [Eseguire test locali delle query di Analisi di flusso con Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md) 
