---
title: Importare file BACPAC SQL con i modelli
description: Informazioni su come usare estensioni per il database SQL di Azure per importare file BACPAC SQL con modelli di Azure Resource Manager (modelli di ARM).
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1bd9f7408baf40791c31626ea9e87a73c65b999c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963998"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Esercitazione: Importare file BACPAC SQL con i modelli di Azure Resource Manager

Informazioni su come usare estensioni per il database SQL di Azure per importare file [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) con modelli di Azure Resource Manager (modelli di ARM). Gli artefatti della distribuzione sono tutti i file, oltre ai file modello principali, necessari per completare una distribuzione. Il file BACPAC è uno di questi elementi.

In questa esercitazione verrà creato un modello per distribuire un [server SQL logico](../../azure-sql/database/logical-servers.md) e un database singolo e importare un file BACPAC. Per informazioni su come distribuire le estensioni macchina virtuale di Azure tramite modelli di Azure Resource Manager, vedere [Esercitazione: Distribuire estensioni di macchina virtuale con i modelli di Azure Resource Manager](./template-tutorial-deploy-vm-extensions.md).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
>
> * Preparare un file BACPAC.
> * Aprire un modello di avvio rapido.
> * Modificare il modello.
> * Distribuire il modello.
> * Verificare la distribuzione.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Avvio rapido: Creare modelli di ARM con Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore del server. Di seguito è riportato un esempio che è possibile usare per generare una password:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Azure Resource Manager](./template-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-bacpac-file"></a>Preparare un file BACPAC

Un file BACPAC viene condiviso in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Per creare un file BACPAC, vedere [Esportare un database dal database SQL di Azure in un file BACPAC](../../azure-sql/database/database-export.md). Se si sceglie di pubblicare il file in una posizione personalizzata, è necessario aggiornare il modello più avanti in questa esercitazione.

Il file BACPAC deve essere archiviato in un account di archiviazione di Azure prima di poter essere importato con un modello di Azure Resource Manager. Lo script di PowerShell seguente prepara il file BACPAC con questi passaggi:

* Scaricare il file BACPAC.
* Creare un account di archiviazione di Azure.
* Creare un contenitore BLOB nell'account di archiviazione.
* Caricare il file BACPAC nel contenitore.
* Visualizzare la chiave dell'account di archiviazione e l'URL del BLOB.

1. Selezionare **Prova** per aprire Azure Cloud Shell. Incollare lo script di PowerShell seguente nella finestra della shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Prendere nota della chiave dell'account di archiviazione e dell'URL del file BACPAC. Questi valori saranno necessari per distribuire il modello.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Il modello usato in questa esercitazione è archiviato in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. In Visual Studio Code selezionare **File** > **Apri file**.
1. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file.

    Nel modello sono definite due risorse:

   * `Microsoft.Sql/servers`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.sql/servers/databases).

        Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
1. Selezionare **File** > **Salva con nome** per salvare una copia del file con il nome *azuredeploy.json* nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

1. Aggiungere altri due parametri alla fine della sezione `parameters` per impostare la chiave dell'account di archiviazione e l'URL BACPAC.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Aggiungere una virgola dopo la parentesi graffa di chiusura (`}`) della proprietà `adminPassword`. Per formattare il file JSON da Visual Studio Code, selezionare MAIUSC+ALT+F.

    Per ottenere questi due valori, vedere [Preparare un file BACPAC](#prepare-a-bacpac-file).

1. Aggiungere altre due risorse al modello.

    * Per consentire all'estensione Database SQL di importare file BACPAC, è necessario consentire il traffico proveniente dai servizi di Azure. Aggiungere la definizione della regola del firewall seguente nella definizione del server:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Il modello è simile al seguente:

        ![Modello con definizione della regola del firewall](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Aggiungere una risorsa estensione di database SQL alla definizione del database con il codice JSON seguente:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Il modello è simile al seguente:

        ![Modello con estensione Database SQL](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Per informazioni sulla definizione della risorsa, vedere le [informazioni di riferimento sulle estensioni di database SQL](/azure/templates/microsoft.sql/servers/databases/extensions). Di seguito sono illustrati alcuni elementi importanti.

        * `dependsOn`: la risorsa di estensione deve essere creata dopo che è stato creato il database.
        * `storageKeyType`: specificare il tipo di chiave di archiviazione da usare. Il valore può essere `StorageAccessKey` o `SharedAccessKey`. In questa esercitazione usare `StorageAccessKey`.
        * `storageKey`: specificare la chiave dell'account di archiviazione in cui è archiviato il file BACPAC. Se il tipo di chiave di archiviazione è `SharedAccessKey`, deve essere preceduta da "?".
        * `storageUri`: specificare l'URL del file BACPAC archiviato in un account di archiviazione.
        * `administratorLoginPassword`: password dell'amministratore di SQL. Usare una password generata. Vedere [Prerequisiti](#prerequisites).

Il modello completato è simile a:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Distribuire il modello

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vedere la sezione [Distribuire il modello](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) per la procedura di distribuzione. Usare invece lo script di distribuzione di PowerShell seguente:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

È consigliabile usare lo stesso nome di progetto usato durante la preparazione del file BACPAC, affinché tutte le risorse vengano archiviate nello stesso gruppo di risorse. In questo modo, è più facile gestire le attività delle risorse, ad esempio la pulizia delle risorse. Se si usa lo stesso nome di progetto, è possibile rimuovere il comando `New-AzResourceGroup` dallo script oppure rispondere sì (y) o no (n) quando viene chiesto se si vuole aggiornare il gruppo di risorse esistente.

Usare una password generata. Vedere [Prerequisiti](#prerequisites).

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per accedere al server dal computer client, è necessario aggiungere una regola del firewall aggiuntiva. Per altre informazioni, vedere [Creare e gestire le regole del firewall IP](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

Nel portale di Azure selezionare il database nel gruppo di risorse appena distribuito. Selezionare **Editor di query (anteprima)** e quindi immettere le credenziali di amministratore. Verranno visualizzate le due tabelle importate nel database.

![Editor di query (anteprima)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
1. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
1. Selezionare il nome del gruppo di risorse. Nel gruppo di risorse verrà visualizzato un totale di sei risorse.
1. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati distribuiti un server e un database ed è stato importato un file BACPAC. Per informazioni su come risolvere i problemi di distribuzione del modello, vedere:

> [!div class="nextstepaction"]
> [Risolvere i problemi delle distribuzioni di modelli di Azure Resource Manager](./template-tutorial-troubleshoot.md)
