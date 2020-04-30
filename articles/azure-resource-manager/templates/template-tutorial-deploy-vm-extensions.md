---
title: Distribuire estensioni di VM con un modello
description: Informazioni su come distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 06d948b44064f029e00a2ef089077e9b55246545
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82184963"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Esercitazione: Distribuire estensioni di macchina virtuale con i modelli di Azure Resource Manager

Questo articolo illustra come usare le [estensioni di macchina virtuale di Azure](../../virtual-machines/extensions/features-windows.md) per eseguire attività di configurazione e automazione post-distribuzione nelle VM di Azure. Sono disponibili molte estensioni diverse delle macchine virtuali da usare con macchine virtuali di Azure. In questa esercitazione si distribuisce un'estensione di script personalizzati da un modello di Azure Resource Manager per eseguire uno script PowerShell in una macchina virtuale Windows.  Lo script installa un server Web nella VM.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare uno script di PowerShell
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Visual Studio Code con l'estensione Strumenti di Resource Manager. Vedere [Usare Visual Studio Code per creare i modelli di Azure Resource Manager](use-vs-code-to-create-template.md).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Azure Resource Manager](./template-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-powershell-script"></a>Preparare uno script di PowerShell

È possibile usare lo script inline di PowerShell o un file di script.  Questa esercitazione illustra come usare un file di script. Uno script di PowerShell con il contenuto seguente è condiviso da [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se si sceglie di pubblicare il file in una posizione personalizzata, aggiornare l'elemento `fileUri` del modello più avanti in questa esercitazione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

I modelli di avvio rapido di Azure costituiscono un repository di modelli di Azure Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File** > **Apri file**.
1. Nella casella **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selezionare **Apri** per aprire il file.
    Il modello definisce cinque risorse:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.

1. Salvare una copia del file nel computer locale con il nome *azuredeploy.json* selezionando **File** > **Salva con nome**.

## <a name="edit-the-template"></a>Modificare il modello

Aggiungere una risorsa estensione di macchina virtuale al modello esistente con il contenuto seguente:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Per altre informazioni su questa definizione di risorsa, vedere il [materiale di riferimento dell'estensione](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Di seguito sono illustrati alcuni elementi importanti.

* **name**: dato che la risorsa estensione è una risorsa figlio dell'oggetto macchina virtuale, il nome deve includere il nome della macchina virtuale come prefisso. Vedere [Impostare il nome e il tipo per le risorse figlio](child-resource-name-type.md).
* **dependsOn**: creare la risorsa estensione dopo aver creato la macchina virtuale.
* **fileUris**: posizioni in cui risiedono i file di script. Se si sceglie di non usare la posizione indicata, è necessario aggiornare i valori.
* **commandToExecute**: questo comando avvia lo script.

Per usare lo script inline, rimuovere **fileURIs** e aggiornare **commandToExecute** in:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Questo script inline aggiorna anche il contenuto di iisstart.html.

È anche necessario aprire la porta HTTP in modo da poter accedere al server Web.

1. Trovare **securityRules** nel modello.
1. Aggiungere la regola seguente accanto a **default-allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Distribuire il modello

Per la procedura di distribuzione, vedere la sezione "Distribuire il modello" di [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). È consigliabile usare una password generata per l'account amministratore della macchina virtuale. Vedere la sezione [Prerequisiti](#prerequisites) di questo articolo.

In Cloud Shell usare il comando seguente per recuperare l'indirizzo IP pubblico della nuova macchina virtuale:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Incollare l'indirizzo IP in un Web browser. Si aprirà la pagina iniziale predefinita di Internet Information Services (IIS):

![Pagina iniziale di Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel riquadro a sinistra.
2. Immettere il nome del gruppo di risorse nella casella **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
    Nel gruppo di risorse vengono visualizzate sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state distribuite una macchina virtuale e un'estensione di macchina virtuale. L'estensione ha installato il server Web IIS nella macchina virtuale. Per informazioni su come usare l'estensione del database SQL di Azure per importare un file BACPAC, vedere:

> [!div class="nextstepaction"]
> [Distribuire estensioni SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
