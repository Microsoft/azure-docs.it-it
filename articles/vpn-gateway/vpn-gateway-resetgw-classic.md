---
title: Reimpostare un gateway VPN di Azure per ristabilire il tunnel IPsec
description: Questo articolo descrive la reimpostazione di Gateway VPN di Azure per ristabilire i tunnel IPsec. L'articolo riguarda i gateway VPN nei modelli di distribuzione classica e Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244420"
---
# <a name="reset-a-vpn-gateway"></a>Reimpostare un gateway VPN

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Questo articolo consente di reimpostare il gateway VPN.

### <a name="what-happens-during-a-reset"></a>Cosa accade durante un ripristino

Un gateway VPN di Azure è costituito da due istanze di macchina virtuale in esecuzione in una configurazione di standby attivo. Quando si reimposta il gateway, quest'ultimo viene riavviato e gli vengono riapplicate le configurazioni cross-premise. Il gateway mantiene l'indirizzo IP pubblico già disponibile. Non sarà quindi necessario aggiornare la configurazione del router VPN con un nuovo indirizzo IP pubblico per il gateway VPN di Azure.

Dopo l'emissione del comando per ripristinare il gateway, l'istanza attualmente attiva del gateway VPN di Azure viene riavviata immediatamente. Si verificherà un breve intervallo durante il failover dall'istanza attiva (in fase di riavvio) all'istanza di standby. L'intervallo dovrebbe essere inferiore a un minuto.

Se la connessione non viene ripristinata dopo il primo riavvio, emettere di nuovo lo stesso comando per riavviare la seconda istanza della VM, ovvero il nuovo gateway attivo. Se vengono richiesti due riavvii uno dopo l'altro, il periodo necessario per il riavvio di entrambe le istanze della VM (attiva e di standby) sarà leggermente più lungo. Ciò causerà un gap più lungo sulla connettività VPN, fino a un massimo di 30-45 minuti affinché le VM completino il riavvio.

Dopo due riavvii, se si verificano ancora problemi di connettività cross-premise, aprire una richiesta di supporto dal portale di Azure.

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Prima di reimpostare il gateway, verificare gli elementi chiave elencati di seguito per ogni tunnel VPN da sito a sito (S2S) IPsec. Eventuali mancate corrispondenze negli elementi provocherà la disconnessione dei tunnel VPN S2S. La verifica e la correzione delle configurazioni per i gateway locali e VPN di Azure evitano riavvii non necessari e interruzioni per le altre connessioni funzionanti nei gateway.

Verificare gli elementi seguenti prima di reimpostare il gateway:

* Gli indirizzi IP virtuali per il gateway VPN di Azure e il gateway VPN locale devono essere configurati correttamente nei criteri VPN di Azure e locali.
* La chiave precondivisa deve essere uguale nei gateway VPN di Azure e locali.
* Se si applica una configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi hash e PFS (Perfect Forward Secrecy), assicurarsi che i gateway VPN di Azure e locali abbiano le stesse configurazioni.

## <a name="azure-portal"></a><a name="portal"></a>Portale di Azure

È possibile reimpostare un gateway VPN di Resource Manager tramite il portale di Azure. Se si desidera reimpostare un gateway classico, vedere la procedura di [PowerShell](#resetclassic).

### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

1. Aprire il [portale di Azure](https://portal.azure.com) e passare al gateway di rete virtuale di Resource Manager che si desidera reimpostare.
2. Nel pannello per il gateway di rete virtuale fare clic su "Reimposta".

   ![Pannello di reimpostazione gateway VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Nel pannello Reimposta, fare clic sul pulsante **Reimposta**.

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il cmdlet per la reimpostazione di un gateway è **Reset-AzVirtualNetworkGateway**. Prima di eseguire una reimpostazione, verificare di avere la versione più recente dei [cmdlet di PowerShell AZ](https://docs.microsoft.com/powershell/module/az.network). Nell'esempio seguente viene ripristinato un gateway di rete virtuale denominato VNet1GW nel gruppo di risorse TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Risultato:

Quando si riceve un valore restituito, si può presupporre che il ripristino del gateway abbia avuto esito positivo. Tuttavia, non c'è alcun elemento nel risultato restituito che indica in modo esplicito che il ripristino è riuscito. Se si desidera esaminare attentamente la cronologia per trovare il momento preciso in cui si è verificato il ripristino del gateway, è possibile visualizzare l'informazione nel [portale di Azure](https://portal.azure.com). Nel portale, passare a **"GatewayName" -> Integrità risorse**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Modello di distribuzione classica

Il cmdlet per la reimpostazione di un gateway è **Reset-AzureVNetGateway**. I cmdlet di Azure PowerShell per la gestione dei servizi devono essere installati localmente sul desktop. Non è possibile usare Azure Cloud Shell. Prima di eseguire un ripristino assicurarsi di avere la versione più recente dei [cmdlet di PowerShell per Gestione servizi](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Quando si usa questo comando, assicurarsi di usare il nome completo della rete virtuale. I reti virtuali classici creati con il portale hanno un nome lungo che è necessario per PowerShell. È possibile visualizzare il nome lungo usando "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml".

L'esempio seguente reimposta il gateway per una rete virtuale denominata "Group TestRG1 TestVNet1" (che mostra semplicemente "TestVNet1" nel portale):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Risultato:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>INTERFACCIA della riga di comando di Azure

Per reimpostare il gateway, usare il comando [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway). Nell'esempio seguente viene ripristinato un gateway di rete virtuale denominato VNet5GW nel gruppo di risorse TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Risultato:

Quando si riceve un valore restituito, si può presupporre che il ripristino del gateway abbia avuto esito positivo. Tuttavia, non c'è alcun elemento nel risultato restituito che indica in modo esplicito che il ripristino è riuscito. Se si desidera esaminare attentamente la cronologia per trovare il momento preciso in cui si è verificato il ripristino del gateway, è possibile visualizzare l'informazione nel [portale di Azure](https://portal.azure.com). Nel portale, passare a **"GatewayName" -> Integrità risorse**.
