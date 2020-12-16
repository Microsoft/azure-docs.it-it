---
title: PowerShell-cluster HDInsight con Data Lake Storage Gen1-Azure
description: Usare Azure PowerShell per creare e usare cluster HDInsight di Azure con Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606777"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Creare cluster HDInsight con Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita usando PowerShell

> [!div class="op_single_selector"]
> * [Usare il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usare PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usare PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usare Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Informazioni su come usare Azure PowerShell per configurare cluster Azure HDInsight con Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita. Per istruzioni su come creare un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva, vedere [Creare un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva](data-lake-store-hdinsight-hadoop-use-powershell.md).

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Storage Gen1:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Storage Gen1 come risorsa di archiviazione predefinita è disponibile per le versioni 3.5 e 3.6 di HDInsight.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Storage Gen1 come risorsa di archiviazione predefinita *non è disponibile* per i cluster HDInsight Premium.

Per configurare HDInsight per l'uso con Data Lake Storage Gen1 tramite PowerShell, seguire le istruzioni fornite nelle cinque sezioni seguenti.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di iniziare l'esercitazione, verificare di soddisfare i requisiti seguenti:

* **Sottoscrizione di Azure**: passare a [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versione successiva**: vedere l'articolo su [come installare e configurare PowerShell](/powershell/azure/).
* **Windows Software Development Kit (SDK)**: per installare Windows SDK, passare a [Download e strumenti per Windows 10](https://dev.windows.com/downloads). L'SDK viene usato per creare un certificato di sicurezza.
* **Entità servizio Azure Active Directory**: questa esercitazione descrive come creare un'entità servizio in Azure Active Directory (Azure AD). Tuttavia, per creare un'entità servizio è necessario essere un amministratore di Azure AD. Se si è un amministratore, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    >[!NOTE]
    >È possibile creare un'entità servizio solo se si è un amministratore di Azure AD. Prima di poter creare un cluster HDInsight con Data Lake Storage Gen1, un amministratore di Azure AD deve creare un'entità servizio. L'entità servizio deve essere creata con un certificato, come descritto in [Creare un'entità servizio con certificato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Creare un account Azure Data Lake Storage Gen1

Per creare un account Data Lake Storage Gen1, procedere come segue:

1. Sul desktop aprire una finestra di PowerShell e quindi immettere i frammenti di codice seguenti. Quando viene richiesto di effettuare l'accesso, accedere come amministratore o proprietario della sottoscrizione. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Se si registra il provider di risorse Data Lake Storage Gen1 e si riceve un errore simile a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , la sottoscrizione potrebbe non essere approvata per data Lake storage Gen1. Per abilitare la sottoscrizione di Azure per Data Lake Storage Gen1, seguire le istruzioni in [Introduzione all'uso di Azure Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-get-started-portal.md).
    >

2. Un account Data Lake Storage Gen1 è associato a un gruppo di risorse di Azure. Iniziare creando un gruppo di risorse.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Verrà visualizzato un output simile al seguente:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Creare un account Data Lake Storage Gen1. Il nome dell'account specificato deve contenere solo lettere minuscole e numeri.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Verrà visualizzato un output simile al seguente:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

4. Per usare Data Lake Storage Gen1 come risorsa di archiviazione predefinita, è necessario specificare un percorso radice in cui vengono copiati i file specifici del cluster durante la creazione del cluster. Per creare un percorso radice, che nel frammento di codice è **/cluster/hdiadlcluster**, usare i cmdlet seguenti:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurare l'autenticazione per l'accesso basato sui ruoli a Data Lake Storage Gen1
Ogni sottoscrizione di Azure è associata a un'entità di Azure AD. Gli utenti e i servizi che accedono alle risorse della sottoscrizione usando il portale di Azure o l'API di Azure Resource Manager devono prima autenticarsi con Azure AD. L'accesso viene concesso alle sottoscrizioni e ai servizi di Azure tramite l'assegnazione del ruolo appropriato in una risorsa di Azure. Per i servizi, un'entità servizio identifica il servizio in Azure AD.

Questa sezione illustra come concedere a un servizio dell'applicazione, ad esempio HDInsight, l'accesso a una risorsa di Azure, ovvero l'account Data Lake Storage Gen1 creato in precedenza. A tale scopo, è necessario creare un'entità servizio per l'applicazione e assegnare ruoli a tale entità tramite PowerShell.

Per configurare l'autenticazione di Active Directory per Data Lake Storage Gen1, eseguire le attività nelle due sezioni seguenti.

### <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Assicurarsi di avere installato [Windows SDK](https://dev.windows.com/en-us/downloads) prima di continuare con i passaggi descritti in questa sezione. È necessario aver creato anche una directory, ad esempio *C:\mycertdir*, in cui creare il certificato.

1. Dalla finestra di PowerShell passare al percorso in cui è installato Windows SDK, in genere *C:\Programmi (x86)\Windows Kits\10\bin\x86*, e usare l'utilità [MakeCert][makecert] per creare un certificato autofirmato e una chiave privata. Usare il seguente comando:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Verrà richiesto di immettere la password della chiave privata. Una volta completata l'esecuzione del comando, nella directory del certificato specificata saranno presenti **CertFile.cer** e **mykey.pvk**.
2. Usare l'utilità [Pvk2Pfx][pvk2pfx] per convertire i file con estensione PVK e CER creati da MakeCert in un file con estensione PFX. Eseguire il comando seguente:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Quando viene chiesto, immettere la password della chiave privata specificata in precedenza. Il valore specificato per il parametro **-po** è la password associata al file PFX. Dopo che il comando è stato completato correttamente, dovrebbe essere visualizzato anche un file **CertFile. pfx** nella directory del certificato specificato.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Creare un'applicazione Azure AD e un'entità servizio
In questa sezione si crea un'entità servizio per un'applicazione Azure AD, si assegna un ruolo all'entità servizio e si esegue l'autenticazione come entità servizio fornendo un certificato. Per creare un'applicazione in Azure AD, eseguire i comandi seguenti:

1. Incollare i cmdlet seguenti nella finestra della console di PowerShell. Assicurarsi che il valore specificato per la proprietà **-DisplayName** sia univoco. I valori per **-HomePage** e **-IdentiferUris** sono valori segnaposto e non vengono verificati.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Creare un'entità servizio usando l'ID applicazione.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

    $objectId = $servicePrincipal.Id
    ```

3. Concedere all'entità servizio l'accesso alla radice di Data Lake Storage Gen1 e a tutte le cartelle nel percorso radice specificato in precedenza. Usare i cmdlet seguenti:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Creare un cluster HDInsight Linux con Data Lake Storage Gen1 come risorsa di archiviazione predefinita

In questa sezione viene creato un cluster HDInsight Hadoop Linux con Data Lake Storage Gen1 come risorsa di archiviazione predefinita. Per questa versione, il cluster HDInsight e Data Lake Storage Gen1 devono trovarsi nella stessa posizione.

1. Recuperare l'ID tenant della sottoscrizione e archiviarlo per usarlo in seguito.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Creare il cluster HDInsight usando i cmdlet seguenti:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Dopo il completamento del cmdlet, dovrebbe viene visualizzato un output con i dettagli del cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Eseguire i processi di test nel cluster HDInsight per usare Data Lake Storage Gen1
Dopo aver configurato un cluster HDInsight, è possibile eseguire i processi di test nel cluster per verificare che possa accedere a Data Lake Storage Gen1. A tale scopo, eseguire un processo hive di esempio per creare una tabella che usa i dati di esempio già disponibili in Data Lake Storage Gen1 in *\<cluster root> /example/Data/Sample.log*.

In questa sezione si stabilisce una connessione SSH (Secure Shell) al cluster HDInsight Linux creato e quindi si esegue una query Hive di esempio.

* Se si usa un client Windows per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Dopo aver stabilito la connessione, avviare l'interfaccia della riga di comando di Hive usando il comando seguente:

    ```powershell
    hive
    ```

2. Usare l'interfaccia della riga di comando per immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

L'output della query dovrebbe essere visualizzato nella console SSH.

>[!NOTE]
>Il percorso ai dati di esempio nel comando CREATE TABLE precedente è `adl:///example/data/`, dove `adl:///` è la radice del cluster. In base all'esempio della radice del cluster specificato in questa esercitazione, il comando è `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. È possibile usare l'alternativa più breve o indicare il percorso completo della radice del cluster.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Accedere a Data Lake Storage Gen1 tramite comandi HDFS
Dopo aver configurato il cluster HDInsight per l'uso di Data Lake Storage Gen1, è possibile usare i comandi della shell HDFS (Hadoop Distributed File System) per accedere all'archivio.

In questa sezione si stabilisce una connessione SSH al cluster HDInsight Linux creato e quindi si eseguono i comandi HDFS.

* Se si usa un client Windows per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se si usa un client Linux per stabilire una connessione SSH al cluster, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo avere stabilito la connessione,visualizzare l'elenco dei file in Data Lake Storage Gen1 usando il comando del file system HDFS seguente.

```azurepowershell
hdfs dfs -ls adl:///
```

È anche possibile usare il comando `hdfs dfs -put` per caricare alcuni file in Data Lake Storage Gen1 e quindi usare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

## <a name="see-also"></a>Vedere anche
* [Usare Data Lake Storage Gen1 con i cluster Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portale di Azure: Creare un cluster HDInsight per usare Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx