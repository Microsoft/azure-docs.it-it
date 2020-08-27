---
title: Esercitazione`:` Usare un'identità gestita per accedere ad Azure Data Lake Store - Linux - Azure AD
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fe442741ae0b8fa817c9ea177ff244a413720e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "75888516"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un’identità gestita assegnata dal sistema per una macchina virtuale Linux per l’accesso ad Azure Data Lake Store. Si apprenderà come: 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Concedere alla VM l'accesso ad Azure Data Lake Store.
> * Ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale per accedere ad Azure Data Lake Store.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Concedere l'accesso

Questa sezione illustra come concedere alla VM l'accesso a file e cartelle in Azure Data Lake Store. Per questo passaggio, è possibile usare un'istanza di Data Lake Store esistente o crearne una nuova. Per creare un'istanza di Data Lake Store usando il portale di Azure, seguire la [guida introduttiva ad Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Nella [documentazione per Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) sono disponibili anche guide introduttive che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

In Data Lake Store creare una nuova cartella e concedere all'identità gestita assegnata dal sistema della macchina virtuale Linux l'autorizzazione per leggere, scrivere ed eseguire file in quella cartella:

1. Nel portale di Azure selezionare **Data Lake Store** nel riquadro sinistro.
2. Selezionare l'istanza di Data Lake Store che si vuole usare.
3. Selezionare **Esplora dati** sulla barra dei comandi.
4. Verrà selezionata la cartella radice dell'istanza di Data Lake Store. Selezionare **Accesso** sulla barra dei comandi.
5. Selezionare **Aggiungi**.  Nella casella **Seleziona** immettere il nome della VM, ad esempio **DevTestVM**. Selezionare la VM nei risultati della ricerca e quindi fare clic su **Seleziona**.
6. Fare clic su **Selezionare le autorizzazioni**.  Selezionare **Lettura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **An access permission only** (Solo autorizzazione di accesso). Selezionare **OK**.  L'autorizzazione verrà aggiunta correttamente.
7. Chiudere il riquadro **Accesso**.
8. Per questa esercitazione creare una nuova cartella. Selezionare **Nuova cartella** sulla barra dei comandi e assegnare un nome alla nuova cartella, ad esempio **TestFolder**.  Selezionare **OK**.
9. Selezionare la cartella creata e quindi **Accesso** sulla barra dei comandi.
10. Come nel passaggio 5, selezionare **Aggiungi**. Nella casella **Seleziona** immettere il nome della VM. Selezionare la VM nei risultati della ricerca e quindi fare clic su **Seleziona**.
11. Come nel passaggio 6, selezionare **Selezionare le autorizzazioni**. Selezionare **Lettura**, **Scrittura** ed **Esecuzione**, aggiungere a **Questa cartella** e aggiungere come **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**. Selezionare **OK**.  L'autorizzazione verrà aggiunta correttamente.

Le identità gestite per le risorse di Azure possono ora eseguire tutte le operazioni sui file nella cartella creata. Per altre informazioni sulla gestione dell'accesso a Data Lake Store, vedere [Controllo di accesso in Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token"></a>Ottenere un token di accesso 

Questa sezione illustra come ottenere un token di accesso e chiamare il file system di Data Lake Store. Azure Data Lake Store supporta in modo nativo l'autenticazione di Azure AD, in modo da poter accettare direttamente i token di accesso ottenuti tramite le identità gestite per le risorse di Azure. Per eseguire l'autenticazione al file system di Data Lake Store, si invia un token di accesso rilasciato da Azure AD all'endpoint del file system di Data Lake Store. Il token di accesso è incluso in un'intestazione dell'autorizzazione nel formato "Bearer \<ACCESS_TOKEN_VALUE\>".  Per altre informazioni sul supporto di Data Lake Store per l'autenticazione di Azure AD, vedere [Autenticazione con Data Lake Store usando Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

In questa esercitazione si esegue l'autenticazione all'API REST per il file system di Data Lake Store usando cURL per inviare richieste REST.

> [!NOTE]
> Gli SDK client del file system di Data Lake Store non supportano ancora le identità gestite per le risorse di Azure.

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per informazioni sulla configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale passare alla VM Linux. In **Panoramica** selezionare **Connetti**.  
2. Connettersi alla VM usando il client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per inviare una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per il file system di Data Lake Store. L'identificatore della risorsa per Data Lake Store è `https://datalake.azure.net/`.  È importante includere la barra finale nell'identificatore della risorsa.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   Una risposta con esito positivo restituisce il token di accesso che viene usato per l'autenticazione a Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Usando cURL, inviare una richiesta all'endpoint REST del file system di Data Lake Store per ottenere un elenco delle cartelle nella cartella radice. È un modo semplice per controllare che tutto sia configurato correttamente. Copiare il valore del token di accesso dal passaggio precedente. È importante che la stringa "Bearer" nell'intestazione dell'autorizzazione contenga una "B" maiuscola. È possibile trovare il nome dell'istanza di Data Lake Store nella sezione **Panoramica** del riquadro **Data Lake Store** nel portale di Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Una risposta con esito positivo ha un aspetto simile al seguente:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. È ora possibile provare a caricare un file nell'istanza di Data Lake Store. Creare prima di tutto un file da caricare.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Usando cURL, inviare una richiesta all'endpoint REST del file system di Data Lake Store per caricare il file nella cartella creata in precedenza. Il caricamento implica un reindirizzamento e cURL segue automaticamente il reindirizzamento. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Una risposta con esito positivo ha un aspetto simile al seguente:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Usando altre API per il file system di Data Lake Store, è possibile eseguire aggiunte ai file, download di file e altre operazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere a un'istanza di Azure Data Lake Store. Per altre informazioni su Azure Data Lake Store, vedere:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
