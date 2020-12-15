---
title: Usa server DNS personalizzato
titleSuffix: Azure Machine Learning
description: Come configurare un server DNS personalizzato per l'uso con un'area di lavoro Azure Machine Learning e un endpoint privato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 07ff656c5eacbbcdc16c6c7cf098478ca6baf745
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509292"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Come usare l'area di lavoro con un server DNS personalizzato

Quando si usa un'area di lavoro di Azure Machine Learning con un endpoint privato, esistono [diversi modi per gestire la risoluzione dei nomi DNS](../private-link/private-endpoint-dns.md). Per impostazione predefinita, Azure gestisce automaticamente la risoluzione dei nomi per l'area di lavoro e l'endpoint privato. Se invece si _Usa un server DNS personalizzato_ _, è necessario creare manualmente le voci DNS per l'area di lavoro.

> [!IMPORTANT]
> Questo articolo illustra solo come trovare il nome di dominio completo (FQDN) e gli indirizzi IP per queste voci, ma non fornisce informazioni sulla configurazione dei record DNS per questi elementi. Per informazioni su come aggiungere record, consultare la documentazione relativa al software DNS.

## <a name="prerequisites"></a>Prerequisiti

- Una rete virtuale di Azure che usa [il proprio server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Un'area di lavoro Azure Machine Learning con un endpoint privato. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Familiarità con l'uso dell' [isolamento rete durante il training & inferenza](./how-to-network-security-overview.md).

- Familiarità con la [configurazione della zona DNS dell'endpoint privato di Azure](../private-link/private-endpoint-dns.md)

- Facoltativamente, l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Trovare gli indirizzi IP

L'elenco seguente contiene i nomi di dominio completi (FQDN) usati dall'area di lavoro e dall'endpoint privato:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Se si crea un'istanza di calcolo, è necessario aggiungere anche una voce per `<instance-name>.<region>.instances.azureml.ms` con l'indirizzo IP privato dell'endpoint privato dell'area di lavoro.

    > [!NOTE]
    > È possibile accedere alle istanze di calcolo solo dall'interno della rete virtuale.

Per trovare gli indirizzi IP interni per i nomi di dominio completi in VNet, usare uno dei metodi seguenti:

> [!NOTE]
> I nomi di dominio completi e gli indirizzi IP saranno diversi in base alla configurazione. Il valore GUID nel nome di dominio, ad esempio, sarà specifico per l'area di lavoro.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Nella [portale di Azure](https://portal.azure.com)selezionare l' __area di lavoro__ Azure Machine Learning.
1. Nella sezione __Impostazioni__ selezionare connessioni a __endpoint privati__.
1. Selezionare il collegamento nella colonna __endpoint privato__ visualizzato.
1. Un elenco dei nomi di dominio completi (FQDN) e degli indirizzi IP per l'endpoint privato dell'area di lavoro si trova nella parte inferiore della pagina.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Elenco di FQDN nel portale":::

---

Le informazioni restituite da tutti i metodi sono le stesse. elenco di FQDN e indirizzo IP privato per le risorse.

| Nome di dominio completo | Indirizzo IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Alcuni FQDN non vengono visualizzati nell'elenco dall'endpoint privato, ma sono richiesti dall'area di lavoro. Questi FQDN sono elencati nella tabella seguente ed è necessario aggiungerli anche al server DNS:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Se si dispone di un'istanza di calcolo, usare `<instance-name>.<region>.instances.azureml.ms` , dove `<instance-name>` è il nome dell'istanza di calcolo. Usare l'indirizzo IP privato dell'endpoint privato dell'area di lavoro. Si noti che l'istanza di calcolo è accessibile solo dall'interno della rete virtuale.
>
> Per tutti questi indirizzi IP, usare lo stesso indirizzo delle `*.api.azureml.ms` voci restituite nei passaggi precedenti.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Azure Machine Learning con una rete virtuale, vedere la [Panoramica della rete virtuale](how-to-network-security-overview.md).
