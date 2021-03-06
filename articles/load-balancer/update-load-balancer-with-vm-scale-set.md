---
title: Aggiornare o eliminare un servizio di bilanciamento del carico esistente usato dai set di scalabilità di macchine virtuali
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Questo articolo illustra come iniziare a usare i set di scalabilità Load Balancer Standard macchine virtuali di Azure.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480087"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aggiornare o eliminare un servizio di bilanciamento del carico usato dai set di scalabilità di macchine virtuali

Quando si lavora con i set di scalabilità di macchine virtuali e un'istanza di Azure Load Balancer, è possibile:

- Aggiungere, aggiornare ed eliminare regole.
- Aggiungere configurazioni.
- Eliminare il servizio di bilanciamento del carico.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Configurare un servizio di bilanciamento del carico per la scalabilità orizzontale dei set di scalabilità di macchine virtuali

Assicurarsi che l'istanza di Azure Load Balancer sia configurato un [pool NAT in](/cli/azure/network/lb/inbound-nat-pool) ingresso e che il set di scalabilità di macchine virtuali sia inserito nel pool back-end del servizio di bilanciamento del carico. Load Balancer crea automaticamente nuove regole NAT in ingresso nel pool NAT in ingresso quando vengono aggiunte nuove istanze di macchina virtuale al set di scalabilità di macchine virtuali.

Per verificare se il pool NAT in ingresso è configurato correttamente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra selezionare **Tutte le risorse**. Selezionare quindi **MyLoadBalancer nell'elenco** delle risorse.
1. In **Impostazioni** selezionare **Regole NAT in ingresso.** Nel riquadro destro, se viene visualizzato un elenco di regole create per ogni singola istanza nel set di scalabilità di macchine virtuali, è possibile aumentare le istanze in qualsiasi momento.

## <a name="add-inbound-nat-rules"></a>Aggiungere regole NAT in ingresso

Non è possibile aggiungere singole regole NAT in ingresso. È tuttavia possibile aggiungere un set di regole NAT in ingresso con un intervallo di porte front-end e una porta back-end definiti per tutte le istanze nel set di scalabilità di macchine virtuali.

Per aggiungere un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, creare prima un pool NAT in ingresso nel servizio di bilanciamento del carico. Fare quindi riferimento al pool NAT in ingresso dal profilo di rete del set di scalabilità di macchine virtuali. Viene illustrato un esempio completo dell'uso dell'interfaccia della riga di comando.

Il nuovo pool NAT in ingresso non deve avere un intervallo di porte front-end sovrapposto con i pool NAT in ingresso esistenti. Per visualizzare i pool NAT in ingresso esistenti impostati, usare questo comando dell'interfaccia [della riga di comando:](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Aggiornare le regole NAT in ingresso

Non è possibile aggiornare singole regole NAT in ingresso. È tuttavia possibile aggiornare un set di regole NAT in ingresso con un intervallo di porte front-end definito e una porta back-end per tutte le istanze nel set di scalabilità di macchine virtuali.

Per aggiornare un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, aggiornare il pool NAT in ingresso nel servizio di bilanciamento del carico.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Eliminare le regole NAT in ingresso

Non è possibile eliminare singole regole NAT in ingresso, ma è possibile eliminare l'intero set di regole NAT in ingresso eliminando il pool NAT in ingresso.

Per eliminare il pool NAT, rimuoverlo dal set di scalabilità. Di seguito è riportato un esempio completo dell'uso dell'interfaccia della riga di comando:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Aggiungere più configurazioni IP

Per aggiungere più configurazioni IP:

1. Nel menu a sinistra selezionare **Tutte le risorse**. Selezionare quindi **MyLoadBalancer nell'elenco** delle risorse.
1. In **Impostazioni** selezionare **Configurazione IP front-end**. Quindi selezionare **Aggiungi**.
1. Nella pagina **Aggiungi indirizzo IP front-end** immettere i valori e selezionare **OK.**
1. Se sono necessarie nuove regole di bilanciamento del carico, seguire i passaggi [5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) di questa esercitazione.
1. Creare un nuovo set di regole NAT in ingresso usando le configurazioni IP front-end appena create, se necessario. Un esempio è disponibile nella sezione precedente.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Più set di scalabilità di macchine virtuali dietro una singola Load Balancer

Creare un pool NAT in ingresso in Load Balancer, fare riferimento al pool NAT in ingresso nel profilo di rete di un set di scalabilità di macchine virtuali e infine aggiornare le istanze per l'applicazione delle modifiche. Ripetere i passaggi per tutti i set di scalabilità di macchine virtuali.

Assicurarsi di creare pool NAT in ingresso separati con intervalli di porte front-end non sovrapposti.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Eliminare la configurazione IP front-end usata dal set di scalabilità di macchine virtuali

Per eliminare la configurazione IP front-end in uso dal set di scalabilità:

 1. Eliminare prima di tutto il pool NAT in ingresso (il set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni su come eliminare le regole in ingresso sono disponibili nella sezione precedente.
 1. Eliminare la regola di bilanciamento del carico che fa riferimento alla configurazione IP front-end.
 1. Eliminare la configurazione IP front-end.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Eliminare un servizio di bilanciamento del carico usato da un set di scalabilità di macchine virtuali

Per eliminare la configurazione IP front-end in uso dal set di scalabilità:

 1. Eliminare prima di tutto il pool NAT in ingresso (il set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni su come eliminare le regole in ingresso sono disponibili nella sezione precedente.
 1. Eliminare la regola di bilanciamento del carico che fa riferimento al pool back-end che contiene il set di scalabilità di macchine virtuali.
 1. Rimuovere il `loadBalancerBackendAddressPool` riferimento dal profilo di rete del set di scalabilità di macchine virtuali.
 
 Di seguito è riportato un esempio completo dell'uso dell'interfaccia della riga di comando:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Infine, eliminare la risorsa del servizio di bilanciamento del carico.
 
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui set Azure Load Balancer e sui set di scalabilità di macchine virtuali, vedere altre informazioni sui concetti.

> [Azure Load Balancer set di scalabilità di macchine virtuali](load-balancer-standard-virtual-machine-scale-sets.md)
