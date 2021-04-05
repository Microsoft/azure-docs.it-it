---
title: "Esercitazione: Usare un'identità gestita per accedere al database SQL di Azure - Windows - Azure AD"
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere al database SQL di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856513"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità assegnata dal sistema per una macchina virtuale Windows per accedere al database SQL di Azure. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
>
> * Concedere l'accesso della macchina virtuale al database SQL di Azure
> * Abilitare l'autenticazione di Azure AD
> * Creare un utente indipendente nel database che rappresenta l'identità assegnata dal sistema della macchina virtuale
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per eseguire query sul database SQL di Azure

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Abilitare

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Concedere l'accesso

Per concedere alla macchina virtuale l'accesso a un database nel database SQL di Azure, è possibile usare un [server SQL logico](../../azure-sql/database/logical-servers.md) esistente o crearne uno nuovo. Per creare un nuovo server e un database tramite il portale di Azure, seguire questa [Guida rapida di SQL di Azure](../../azure-sql/database/single-database-create-quickstart.md). Nella [documentazione di SQL di Azure](/azure/sql-database/) sono disponibili anche guide rapide che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

Per concedere alla macchina virtuale l'accesso a un database sono necessari due passaggi:

1. Abilitare l'autenticazione di Azure AD per il server.
2. Creare un **utente indipendente** nel database che rappresenta l'identità assegnata dal sistema della macchina virtuale.

### <a name="enable-azure-ad-authentication"></a>Abilitare l'autenticazione di Azure AD

**Per [configurare l'autenticazione di Azure AD](../../azure-sql/database/authentication-aad-configure.md):**

1. Nel portale di Azure selezionare **i server SQL** dal menu a sinistra.
2. Fare clic sul server SQL da abilitare per l'autenticazione di Azure AD.
3. Nella sezione **Impostazioni** del pannello fare clic su **Amministratore di Active Directory**.
4. Nella barra dei comandi fare clic su **Imposta amministratore**.
5. Selezionare un account utente di Azure AD da rendere amministratore del server e fare clic su **Seleziona**.
6. Nella barra dei comandi fare clic su **Salva**.

### <a name="create-contained-user"></a>Creare un utente indipendente

Questa sezione illustra come creare un utente indipendente nel database che rappresenta l'identità assegnata dal sistema della macchina virtuale. Per questo passaggio è necessario [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Prima di iniziare, potrebbe essere utile consultare anche gli articoli seguenti per avere informazioni sull'integrazione di Azure AD:

- [Autenticazione universale con il database SQL e Azure Synapse Analytics (supporto di SSMS per MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

Il database SQL richiede nomi visualizzati AAD univoci. Con questa impostazione, gli account AAD come utenti, gruppi ed entità servizio (applicazioni) e i nomi di macchine virtuali abilitati per l'identità gestita devono essere definiti in modo univoco in AAD in relazione ai nomi visualizzati. Il database SQL controlla il nome visualizzato AAD durante la creazione T-SQL di tali utenti e, se non è univoco, il comando ha esito negativo e richiede di specificare un nome visualizzato AAD univoco per un determinato account.

**Per creare un utente indipendente:**

1. Avviare SQL Server Management Studio.
2. Nella finestra di dialogo **Connetti al server** immettere il nome del server nel campo **Nome server**.
3. Nel campo **Autenticazione** selezionare **Active Directory - Universale con supporto MFA**.
4. Nel campo **Nome utente** immettere il nome dell'account di Azure AD impostato come amministratore del server, ad esempio helen@woodgroveonline.com
5. Fare clic su **Opzioni**.
6. Nel campo **Connetti a database** immettere il nome del database esterno al sistema che si desidera configurare.
7. Fare clic su **Connetti**. Completare il processo di accesso.
8. In **Esplora oggetti** espandere la cartella **Database**.
9. Fare clic con il pulsante destro del mouse su un database utente e scegliere **Nuova query**.
10. Nella finestra della query immettere la riga seguente e fare clic su **Esegui** nella barra degli strumenti:

    > [!NOTE]
    > `VMName` nel comando seguente è il nome della macchina virtuale su cui è stata abilitata l'identità assegnata dal sistema nella sezione Prerequisiti.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    Il comando dovrebbe completare l'operazione correttamente, creando l'utente indipendente per l'identità assegnata dal sistema della macchina virtuale.
11. Cancellare la finestra di query, immettere la riga seguente e fare clic su **Esegui** nella barra degli strumenti:

    > [!NOTE]
    > `VMName` nel comando seguente è il nome della macchina virtuale su cui è stata abilitata l'identità assegnata dal sistema nella sezione Prerequisiti.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di leggere tutto il database.

Il codice in esecuzione nella macchina virtuale può ora ottenere un token tramite l'identità gestita assegnata dal sistema e usarlo per l'autenticazione al server.

## <a name="access-data"></a>Accedere ai dati

Questa sezione illustra come ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale e usarlo per chiamare Azure SQL. Azure SQL supporta in modo nativo l'autenticazione di Azure AD, in modo da poter accettare direttamente i token di accesso ottenuti usando identità gestite per le risorse di Azure. Usare il metodo di creazione di una connessione a SQL del **token di accesso**. Questo fa parte dell'integrazione di SQL di Azure con Azure AD e non prevede l'inserimento di credenziali nella stringa di connessione.

Di seguito è riportato un esempio di codice .NET per l'apertura di una connessione a SQL tramite un token di accesso. Questo codice deve essere eseguito nella macchina virtuale per poter accedere all'endpoint dell'identità gestita assegnata dal sistema della macchina virtuale. Per usare il metodo del token di accesso, è necessario **.NET Framework 4.6** o versione successiva oppure **.NET Core 2.2** o versione successiva. Sostituire i valori di AZURE-SQL-SERVERNAME e DATABASE di conseguenza. Si noti che l'ID risorsa per Azure SQL è `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Un altro modo rapido per verificare le impostazioni end-to-end senza dover scrivere e distribuire un'app nella macchina virtuale consiste nell'usare PowerShell.

1. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**.
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows.
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota.
4. Usando `Invoke-WebRequest` di PowerShell, eseguire una richiesta all'endpoint locale dell'identità gestita per ottenere un token di accesso per Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Convertire la risposta da un oggetto JSON a un oggetto di PowerShell.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Estrarre il token di accesso dalla risposta.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Aprire una connessione al server. Ricordarsi di sostituire i valori di AZURE-SQL-SERVERNAME e DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Successivamente, creare e inviare una query al server. Ricordarsi di sostituire il valore per TABELLA.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Esaminare il valore di `$DataSet.Tables[0]` per visualizzare i risultati della query.

## <a name="disable"></a>Disabilitazione

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per accedere al database SQL di Azure. Per altre informazioni sul database SQL di Azure, vedere:

> [!div class="nextstepaction"]
> [Database SQL di Azure](../../azure-sql/database/sql-database-paas-overview.md)
