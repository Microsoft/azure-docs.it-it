---
title: "Avvio rapido: Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure"
description: Iniziare a usare le distribuzioni imparando a creare rapidamente un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5ed6ff00e2ec9a47cec0290fa88ef3b554287607
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518159"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Avvio rapido: Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'uso delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. Un servizio Azure Load Balancer distribuisce quindi il traffico alle istanze di macchina virtuale nel set di scalabilità. In questa guida introduttiva viene creato un set di scalabilità di macchine virtuali e viene distribuita un'applicazione di esempio con l'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.29 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per poter creare un set di scalabilità, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss). L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che è impostato per l'aggiornamento automatico man mano che vengono applicate modifiche e genera le chiavi SSH se non esistono in *~/.ssh/id_rsa*. Queste chiavi SSH vengono usate per l'accesso alle istanze di macchina virtuale. Per usare invece un set di chiavi SSH esistente, usare il parametro `--ssh-key-value` e specificare il percorso delle chiavi.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="deploy-sample-application"></a>Distribuire l'applicazione di esempio
Per testare il set di scalabilità, installare un'applicazione Web di base. Usare l'estensione script personalizzati di Azure per scaricare ed eseguire uno script che installa un'applicazione nelle istanze di macchina virtuale. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/extensions/custom-script-linux.md).

Usare l'estensione script personalizzati per installare un server NGINX di base. Applicare l'estensione script personalizzati che installa NGINX con [az vmss extension set](/cli/azure/vmss/extension) nel modo seguente:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Consentire il traffico verso l'applicazione
Quando è stato creato il set di scalabilità, è stato distribuito automaticamente un servizio Azure Load Balancer. Il servizio di bilanciamento del carico distribuisce il traffico alle istanze di macchina virtuale nel set di scalabilità. Per consentire al traffico di raggiungere l'applicazione Web di esempio, creare una regola del servizio di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per vedere il set di scalabilità in azione, accedere all'applicazione Web di esempio in un Web browser. Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web predefinita in NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group) per rimuoverli, come segue. Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato creato un set di scalabilità di base ed è stata usata l'estensione script personalizzati per installare un server Web NGINX di base nelle istanze di macchina virtuale. Per altre informazioni, passare all'esercitazione su come creare e gestire i set di scalabilità di macchine virtuali di Azure.

> [!div class="nextstepaction"]
> [Creare e gestire i set di scalabilità di macchine virtuali di Azure](tutorial-create-and-manage-cli.md)
