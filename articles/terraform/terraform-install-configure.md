---
title: 'Avvio rapido: Installare e configurare Terraform per il provisioning delle risorse di Azure'
description: Questo argomento di avvio rapido illustra come installare e configurare Terraform per creare risorse di Azure
keywords: azure devops terraform installare configurare
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943510"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Guida introduttiva: Installare e configurare Terraform per il provisioning delle risorse di Azure
 
Terraform offre un modo semplice per definire, visualizzare in anteprima e distribuire l'infrastruttura cloud usando un [linguaggio di creazione modelli semplice](https://www.terraform.io/docs/configuration/syntax.html). Questo articolo descrive la procedura da seguire per usare Terraform per effettuare il provisioning di risorse in Azure.

Per altre informazioni su come usare Terraform con Azure, visitare l'[hub di Terraform](/azure/terraform).
> [!NOTE]
> Per il supporto specifico di Terraform, contattare direttamente Terraform usando uno dei relativi canali della community:
>
>    * La [sezione Terraform](https://discuss.hashicorp.com/c/terraform-core) del portale della community contiene domande, casi d'uso e modelli utili.
>
>    * Per domande relative ai provider, visitare la sezione sui [provider di Terraform](https://discuss.hashicorp.com/c/terraform-providers) del portale della community.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Terraform viene installato per impostazione predefinita in [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se si sceglie di installare Terraform in locale, completare il passaggio successivo; in caso contrario, passare a [Configurare l'accesso di Terraform in Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Installazione di Terraform

Per installare Terraform, [scaricare](https://www.terraform.io/downloads.html) il pacchetto appropriato per il sistema operativo in uso in una directory di installazione distinta. Il download contiene un singolo file eseguibile, per cui è anche necessario definire un percorso globale. Per istruzioni su come impostare il percorso in Mac e Linux, vedere [questa pagina Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Per istruzioni su come impostare il percorso in Windows, vedere [questa pagina Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Per verificare la configurazione del percorso, usare il comando `terraform`. Viene visualizzato un elenco delle opzioni Terraform disponibili, come nell'output di esempio di seguito:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Impostazione dell'accesso di Terraform in Azure

Per consentire a Terraform di eseguire il provisioning di risorse in Azure, creare un'[entità servizio di Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). L'entità servizio concede agli script Terraform la possibilità di effettuare il provisioning di risorse nella sottoscrizione di Azure.

Se si hanno più sottoscrizioni di Azure, eseguire prima una query sull'account con [az account list](/cli/azure/account#az-account-list) per ottenere un elenco di valori di ID sottoscrizione e ID tenant:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Per usare una sottoscrizione selezionata, impostare la sottoscrizione per questa sessione con [az account set](/cli/azure/account#az-account-set). Impostare la`SUBSCRIPTION_ID` variabile di ambiente che conterrà il valore del campo restituito`id` dalla sottoscrizione che si intende usare:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

A questo punto è possibile creare un'entità servizio per l'uso con Terraform. Usare [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) e impostare l'*ambito* alla sottoscrizione come indicato di seguito:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vengono restituiti i valori `appId`, `password`, `sp_name` e `tenant`. Prendere nota di `appId` e `password`.

## <a name="configure-terraform-environment-variables"></a>Configurare le variabili di ambiente di Terraform

Per configurare Terraform per l'uso dell'entità servizio di Azure AD, impostare le variabili di ambiente seguenti che saranno quindi usate dai [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure). È anche possibile impostare l'ambiente se si usa un cloud di Azure diverso da Azure pubblico.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Di seguito è riportato uno script della shell di esempio che è possibile usare per impostare tali variabili:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Eseguire uno script di esempio

Creare un file `test.tf` in una directory vuota e incollare al suo interno lo script seguente.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salvare il file e quindi inizializzare la distribuzione di Terraform. Questo passaggio scarica i moduli di Azure necessari per creare un gruppo di risorse di Azure.

```bash
terraform init
```

L'output è simile all'esempio seguente:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

È possibile visualizzare in anteprima le azioni da completare dallo script di Terraform con `terraform plan`. Quando si è pronti per creare il gruppo di risorse, applicare il piano Terraform come indicato di seguito:

```bash
terraform apply
```

L'output è simile all'esempio seguente:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, viene installato Terraform o usato Cloud Shell per configurare le credenziali di Azure e iniziare la creazione di risorse nella sottoscrizione di Azure. Per creare una distribuzione più completa di Terraform in Azure, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Creare una macchina virtuale di Azure con Terraform](terraform-create-complete-vm.md)