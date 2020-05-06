---
title: Usare Azure Key Vault nei modelli
description: Informazioni su come usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione di modelli di Resource Manager
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 7fd84fc2e98578772c806f358cb8d6c400e0d994
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185014"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Azure Resource Manager

Informazioni su come recuperare i segreti da Azure Key Vault e passarli come parametri quando si distribuisce un modello di Azure Resource Manager (ARM). Il valore dei parametri non viene mai esposto perché si fa riferimento solo al relativo ID dell'insieme di credenziali delle chiavi. Per fare riferimento al segreto dell'insieme di credenziali delle chiavi, è possibile usare un ID statico o un ID dinamico. In questa esercitazione viene usato un ID statico. Con l'approccio basato sull'ID statico, si fa riferimento all'insieme di credenziali delle chiavi nel file dei parametri del modello, non nel file del modello. Per altre informazioni su entrambi gli approcci, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./key-vault-parameter.md).

Nell'esercitazione [Impostare l'ordine di distribuzione delle risorse](./template-tutorial-create-templates-with-dependent-resources.md) viene creata una macchina virtuale. È necessario specificare il nome utente e la password dell'amministratore della macchina virtuale. Invece di immettere la password, è possibile archiviarla preventivamente in Azure Key Vault e quindi personalizzare il modello per recuperare la password dall'insieme di credenziali delle chiavi durante la distribuzione.

![Diagramma che illustra l'integrazione di un modello di Resource Manager con un insieme di credenziali delle chiavi](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un insieme di credenziali delle chiavi
> * Aprire un modello di avvio rapido
> * Modificare il file dei parametri
> * Distribuire il modello
> * Convalidare la distribuzione
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Usare Visual Studio Code per creare i modelli di Azure Resource Manager](use-vs-code-to-create-template.md).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Ecco un esempio di generazione di una password:

    ```console
    openssl rand -base64 32
    ```
    Verificare che la password generata soddisfi i requisiti delle password per le macchine virtuali. Ogni servizio di Azure presenta requisiti specifici in termini di password. Per informazioni sui requisiti delle password per le macchine virtuali, vedere [Quali requisiti devono avere le password quando si crea una macchina virtuale?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparare un insieme di credenziali delle chiavi

In questa sezione si crea un insieme di credenziali delle chiavi e vi si aggiunge un segreto, per poter recuperare il segreto quando si distribuisce il modello. Esistono molti modi per creare un insieme di credenziali delle chiavi. In questa esercitazione si usa Azure PowerShell per distribuire un [modello di Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Questo modello consente di eseguire due operazioni:

* Creare un insieme di credenziali delle chiavi con la proprietà `enabledForTemplateDeployment` abilitata. Affinché il processo di distribuzione del modello possa accedere ai segreti definiti nell'insieme di credenziali delle chiavi, impostare questa proprietà su *true*.
* Aggiungere un segreto all'insieme di credenziali delle chiavi. Il segreto contiene la password dell'amministratore della macchina virtuale.

> [!NOTE]
> Se l'utente che distribuisce il modello di macchina virtuale non è il proprietario o un collaboratore dell'insieme di credenziali delle chiavi, è necessario che il proprietario o un collaboratore conceda all'utente l'accesso all'autorizzazione *Microsoft.KeyVault/vaults/deploy/action* per l'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./key-vault-parameter.md).

Per eseguire lo script di Azure PowerShell seguente, selezionare **Prova** per aprire Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sul riquadro della shell e quindi scegliere **Incolla**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Il nome del gruppo di risorse corrisponde al nome del progetto seguito da **rg**. Per semplificare la [pulizia delle risorse create in questa esercitazione](#clean-up-resources), usare lo stesso nome di progetto e di gruppo di risorse per la [distribuzione del modello seguente](#deploy-the-template).
> * Il nome predefinito del segreto è **vmAdminPassword**. Tale nome è hardcoded nel modello.
> * Affinché il modello possa recuperare il segreto, è necessario abilitare un criterio di accesso denominato **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli** per l'insieme di credenziali delle chiavi. Questo criterio viene abilitato nel modello. Per altre informazioni sui criteri di accesso, vedere [Distribuire insiemi di credenziali delle chiavi e segreti](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Il modello ha un valore di output denominato *keyVaultId*. Questo ID verrà usato insieme al nome del segreto per recuperare il valore del segreto più avanti nell'esercitazione. Il formato dell'ID della risorsa è il seguente:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando si copia e si incolla l'ID, questo potrebbe venire suddiviso in più righe. Unire le righe e tagliare gli spazi aggiuntivi.

Per convalidare la distribuzione, eseguire il comando di PowerShell seguente nello stesso riquadro della shell per recuperare il segreto come testo non crittografato. Il comando funziona solo nella stessa sessione della shell, perché usa la variabile *$keyVaultName* definita nello script di PowerShell precedente.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

A questo punto sono stati preparati un insieme di credenziali delle chiavi e un segreto. Le sezioni seguenti illustrano come personalizzare un modello esistente per recuperare il segreto durante la distribuzione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

I modelli di avvio rapido di Azure costituiscono un repository di modelli di Azure Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è il modello di [distribuzione di una macchina virtuale Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File** > **Apri file**.

1. Nella casella **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file. Lo scenario è identico a quello usato in [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./template-tutorial-create-templates-with-dependent-resources.md).
   Il modello definisce sei risorse:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Prima di personalizzare il modello è utile acquisirne una conoscenza di base.

1. Selezionare **File** > **Salva con nome** e quindi salvare una copia del file con il nome *azuredeploy.json* nel computer locale.

1. Ripetere i passaggi da 1 a 3 per aprire l'URL seguente e quindi salvare il file con il nome *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Modificare il file dei parametri

Con l'approccio basato sull'ID statico non è necessario apportare modifiche al file del modello. Il recupero del valore del segreto viene eseguito configurando il file dei parametri del modello.

1. In Visual Studio Code aprire *azuredeploy.parameters.json*, se non è già aperto.
1. Aggiornare il parametro `adminPassword` in:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Sostituire il valore di **id** con l'ID della risorsa dell'insieme di credenziali delle chiavi creato nella procedura precedente. Il valore hardcoded di secretName è **vmAdminPassword**.  Vedere [Preparare un insieme di credenziali delle chiavi](#prepare-a-key-vault).

    ![Integrare Key Vault e i modelli di Resource Manager - File dei parametri per la distribuzione di macchine virtuali](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aggiornare i valori seguenti:

    * **adminUsername**: nome dell'account amministratore della macchina virtuale.
    * **dnsLabelPrefix**: nome per il valore dnsLabelPrefix.

    Per esempi di nomi, vedere l'immagine precedente.

1. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Accedere ad [Azure Cloud Shell](https://shell.azure.com)

1. Scegliere l'ambiente preferito selezionando **PowerShell** o **Bash** (per l'interfaccia della riga di comando) nell'angolo in alto a sinistra.  Quando si cambia interfaccia, è necessario riavviare la shell.

    ![Caricare file in Cloud Shell nel portale di Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selezionare **Carica/Scarica file** e quindi **Carica**. Caricare entrambi i file *azuredeploy.json* e *azuredeploy.parameters.json* in Cloud Shell. Dopo aver caricato il file, è possibile usare i comandi **ls** e **cat** per verificare che il file sia stato caricato.

1. quindi eseguire lo script di PowerShell seguente per distribuire il modello.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Quando si distribuisce il modello, usare lo stesso gruppo di risorse usato nell'insieme di credenziali delle chiavi. Questo approccio semplifica la pulizia delle risorse, perché è necessario eliminare un solo gruppo di risorse anziché due.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Dopo aver distribuito la macchina virtuale, testare le credenziali di accesso usando la password archiviata nell'insieme di credenziali delle chiavi.

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** >  **\<*NomeGruppoRisorse*>**  > **simpleWinVM**.
1. Selezionare **connetti** nella parte superiore.
1. Selezionare **Scarica file RDP** e quindi seguire le istruzioni per accedere alla macchina virtuale usando la password archiviata nell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato recuperato un segreto da Azure Key Vault. Il segreto è stato quindi usato per la distribuzione del modello. Per informazioni su come usare estensioni di macchina virtuale per eseguire le attività post-distribuzione, vedere:

> [!div class="nextstepaction"]
> [Distribuire estensioni di macchina virtuale](./template-tutorial-deploy-vm-extensions.md)
