---
title: Esercitazione`:` Usare un'identità gestita per accedere ad Azure Data Lake Store - Windows - Azure AD
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093820"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a un'istanza di Azure Data Lake Store. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Concedere alla VM l'accesso a un'istanza di Azure Data Lake Store
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per accedere a un'istanza di Azure Data Lake Store

## <a name="prerequisites"></a>Prerequisiti

- Conoscenza delle identità gestite. Se non si ha familiarità con la funzionalità delle identità gestite per le risorse di Azure, vedere questa [panoramica](overview.md). 
- Un account Azure. [Iscriversi per riceverne uno gratuito](https://azure.microsoft.com/free/).
- Autorizzazioni di "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse) per eseguire le procedure di creazione delle risorse e gestione dei ruoli. Se è necessaria assistenza per l'assegnazione di ruolo, vedere [assegnare i ruoli di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).
- È anche necessaria una macchina virtuale Windows con identità gestite assegnate dal sistema abilitate.
  - Se è necessario creare una macchina virtuale per questa esercitazione, è possibile seguire l'articolo [Creare una macchina virtuale con l'identità assegnata dal sistema abilitata](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)




## <a name="enable"></a>Abilitare

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Concedere l'accesso

È ora possibile concedere alla VM l'accesso a file e cartelle in un'istanza di Azure Data Lake Store.  Per questo passaggio, è possibile usare un'istanza di Data Lake Store esistente o crearne una nuova.  Per creare una nuova istanza di Data Lake Store usando il portale di Azure, seguire questa [guida introduttiva ad Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Nella [documentazione di Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md) sono disponibili anche guide introduttive che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

Nell'istanza di Data Lake Store creare una nuova cartella e concedere all'identità assegnata dal sistema della macchina virtuale l'autorizzazione per leggere, scrivere ed eseguire file in quella cartella:

1. Nel portale di Azure fare clic su **Data Lake Store** nel riquadro di spostamento sinistro.
2. Fare clic sull'istanza di Data Lake Store che si vuole usare per questa esercitazione.
3. Fare clic su **Esplora dati** sulla barra dei comandi.
4. Viene selezionata la cartella radice dell'istanza di Data Lake Store.  Fare clic su **Accedi** sulla barra dei comandi.
5. Fare clic su **Aggiungi**.  Nel campo **Seleziona** immettere il nome della VM, ad esempio **DevTestVM**.  Fare clic per selezionare la VM nei risultati della ricerca, quindi fare clic su **Seleziona**.
6. Fare clic su **Selezionare le autorizzazioni**.  Selezionare **Lettura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **An access permission only** (Solo autorizzazione di accesso).  Fare clic su **OK**.  L'autorizzazione verrà aggiunta correttamente.
7. Chiudere il pannello **Accesso**.
8. Per questa esercitazione creare una nuova cartella.  Fare clic su **Nuova cartella** sulla barra dei comandi e assegnare un nome alla nuova cartella, ad esempio **TestFolder**.  Fare clic su **OK**.
9. Fare clic sulla cartella creata e quindi su **Accedi** sulla barra dei comandi.
10. Come nel passaggio 5, fare clic su **Aggiungi**, nel campo **Seleziona** immettere il nome della VM, selezionarla e fare clic su **Seleziona**.
11. Come nel passaggio 6, fare clic su **Selezionare le autorizzazioni**, selezionare **Lettura**, **Scrittura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.  Fare clic su **OK**.  L'autorizzazione verrà aggiunta correttamente.

L'identità gestita assegnata dal sistema della macchina virtuale può ora eseguire tutte le operazioni sui file nella cartella creata.  Per altre informazioni sulla gestione dell'accesso a Data Lake Store, vedere [Controllo di accesso in Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Accedere ai dati

Azure Data Lake Store supporta in modo nativo l'autenticazione di Azure AD, in modo da poter accettare direttamente i token di accesso ottenuti usando identità gestite per le risorse di Azure.  Per eseguire l'autenticazione al file system di Data Lake Store, inviare un token di accesso rilasciato da Azure AD all'endpoint del file system di Data Lake Store, in un'intestazione dell'autorizzazione con il formato "Bearer <ACCESS_TOKEN_VALUE>".  Per altre informazioni sul supporto di Data Lake Store per l'autenticazione di Azure AD, vedere [Autenticazione con Data Lake Store usando Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Gli SDK client del file system di Data Lake Store non supportano ancora le identità gestite per le risorse di Azure.  Questa esercitazione verrà aggiornata quando verrà aggiunto il supporto all'SDK.

In questa esercitazione si esegue l'autenticazione all'API REST del file system di Data Lake Store usando PowerShell per creare richieste REST. Per usare l'identità gestita assegnata dal sistema della macchina virtuale per l'autenticazione, è necessario eseguire le richieste dalla macchina virtuale.

1. Nel portale passare a **Macchine virtuali**, selezionare la VM Windows e in **Panoramica** fare clic su **Connetti**.
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4. Usando `Invoke-WebRequest` di PowerShell, eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Data Lake Store.  L'identificatore della risorsa per Data Lake Store è `https://datalake.azure.net/`.  La barra finale è importante perché Data Lake stabilisce una corrispondenza esatta con l'identificatore della risorsa.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Convertire la risposta da un oggetto JSON a un oggetto di PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Estrarre il token di accesso dalla risposta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando `Invoke-WebRequest' di PowerShell, creare una richiesta all'endpoint REST di Data Lake Store per elencare le cartelle nella cartella radice.  È un modo semplice per controllare che tutto sia configurato correttamente.  È importante che la stringa "Bearer" nell'intestazione dell'autorizzazione abbia una lettera maiuscola "B".  È possibile trovare il nome dell'istanza di Data Lake Store nella sezione **Panoramica** del pannello Data Lake Store nel portale di Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. È ora possibile provare a caricare un file nell'istanza di Data Lake Store.  Creare prima di tutto un file da caricare.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Usando `Invoke-WebRequest` di PowerShell, creare una richiesta all'endpoint REST dell'istanza di Data Lake Store per caricare il file nella cartella creata prima.  Questa richiesta prevede due passaggi.  Nel primo passaggio si crea una richiesta e si ottiene un reindirizzamento alla posizione in cui deve essere caricato il file.  Nel secondo passaggio si carica effettivamente il file.  Ricordare di impostare il nome della cartella e del file in modo appropriato se sono stati usati valori diversi da quelli di questa esercitazione. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Se si osserva il valore di `$HdfsRedirectResponse`, dovrà essere simile alla risposta seguente:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Completare il caricamento inviando una richiesta all'endpoint di reindirizzamento:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Usando altre API del file system di Data Lake Store, è possibile accodare ai file, scaricare i file ed eseguire altre operazioni.


## <a name="disable"></a>Disabilitazione

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a un'istanza di Azure Data Lake Store. Per altre informazioni su Azure Data Lake Store, vedere:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)