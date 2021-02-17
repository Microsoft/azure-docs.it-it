---
title: Impostazioni DNS del firewall di Azure
description: È possibile configurare il firewall di Azure con le impostazioni del server DNS e del proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546245"
---
# <a name="azure-firewall-dns-settings"></a>Impostazioni DNS del firewall di Azure

È possibile configurare un server DNS personalizzato e abilitare il proxy DNS per il firewall di Azure. Configurare queste impostazioni durante la distribuzione del firewall o configurarle in un secondo momento dalla pagina **impostazioni DNS** .

## <a name="dns-servers"></a>Server DNS

Un server DNS gestisce e risolve i nomi di dominio in indirizzi IP. Per impostazione predefinita, il firewall di Azure usa DNS di Azure per la risoluzione dei nomi. L'impostazione del **server DNS** consente di configurare i server DNS per la risoluzione dei nomi del firewall di Azure. È possibile configurare un singolo server o più server.

> [!NOTE]
> Per le istanze del firewall di Azure gestite tramite gestione firewall di Azure, le impostazioni DNS sono configurate nei criteri del firewall di Azure associati.

### <a name="configure-custom-dns-servers---azure-portal"></a>Configurare server DNS personalizzati-portale di Azure

1. In **Impostazioni** del firewall di Azure selezionare **impostazioni DNS**.
2. In **server DNS** è possibile digitare o aggiungere i server DNS esistenti precedentemente specificati nella rete virtuale.
3. Selezionare **Salva**.

Il firewall ora indirizza il traffico DNS ai server DNS specificati per la risoluzione dei nomi.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Screenshot che mostra le impostazioni per i server D N S.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Configurare server DNS personalizzati-interfaccia della riga di comando di Azure

L'esempio seguente aggiorna il firewall di Azure con i server DNS personalizzati usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Il comando `az network firewall` richiede l'installazione dell'estensione dell'interfaccia della riga `azure-firewall` di comando di Azure. È possibile installarlo utilizzando il comando `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Configurare server DNS personalizzati-Azure PowerShell

L'esempio seguente aggiorna il firewall di Azure con i server DNS personalizzati usando Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy DNS

È possibile configurare il firewall di Azure in modo che funga da proxy DNS. Un proxy DNS è un intermediario per le richieste DNS dalle macchine virtuali client a un server DNS. Se si configura un server DNS personalizzato, abilitare il proxy DNS per evitare una mancata corrispondenza della risoluzione DNS e abilitare il filtro FQDN (nome di dominio completo) nelle regole di rete.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="D N S configurazione proxy utilizzando un server D N personalizzato.":::


Se non si Abilita il proxy DNS, le richieste DNS dal client possono passare a un server DNS in un momento diverso o restituire una risposta diversa rispetto a quella del firewall. Il proxy DNS inserisce il firewall di Azure nel percorso delle richieste del client per evitare l'incoerenza.

Quando il firewall di Azure è un proxy DNS, sono possibili due tipi di funzioni di memorizzazione nella cache:

- **Cache positiva**: la risoluzione DNS è riuscita. Il firewall usa la durata (TTL, time to Live) del pacchetto o dell'oggetto. 

- **Cache negativa**: la risoluzione DNS non restituisce alcuna risposta o nessuna soluzione. Il firewall memorizza nella cache queste informazioni per un'ora.

Il proxy DNS archivia tutti gli indirizzi IP risolti da FQDN nelle regole di rete. Come procedura consigliata, usare i nomi di dominio completi che vengono risolti in un unico indirizzo IP.  

### <a name="dns-proxy-configuration"></a>Configurazione del proxy DNS

Per la configurazione del proxy DNS sono necessari tre passaggi:
1. Abilitare il proxy DNS nelle impostazioni DNS del firewall di Azure.
2. Facoltativamente, configurare il server DNS personalizzato o usare il valore predefinito specificato.
3. Configurare l'indirizzo IP privato del firewall di Azure come indirizzo DNS personalizzato nelle impostazioni del server DNS della rete virtuale. Questa impostazione garantisce che il traffico DNS venga indirizzato al firewall di Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Configurare il proxy DNS-portale di Azure

Per configurare il proxy DNS, è necessario configurare l'impostazione dei server DNS della rete virtuale per l'uso dell'indirizzo IP privato del firewall. Abilitare quindi il proxy DNS nelle **impostazioni DNS** del firewall di Azure.

##### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale 

1. Selezionare la rete virtuale in cui il traffico DNS verrà instradato tramite l'istanza del firewall di Azure.
2. In **Impostazioni** selezionare **Server DNS**.
3. In **Server DNS** selezionare **Personalizzato**.
4. Immettere l'indirizzo IP privato del firewall.
5. Selezionare **Salva**.
6. Riavviare le VM connesse alla rete virtuale in modo che siano assegnate le nuove impostazioni del server DNS. Le macchine virtuali continuano a usare le impostazioni DNS correnti fino a quando non vengono riavviate.

##### <a name="enable-dns-proxy"></a>Abilita proxy DNS

1. Selezionare l'istanza del firewall di Azure.
2. In **Impostazioni** selezionare **impostazioni DNS**.
3. Per impostazione predefinita, il **proxy DNS** è disabilitato. Quando questa impostazione è abilitata, il firewall è in ascolto sulla porta 53 e invia le richieste DNS ai server DNS configurati.
4. Esaminare la configurazione dei **server DNS** per assicurarsi che le impostazioni siano appropriate per l'ambiente in uso.
5. Selezionare **Salva**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Screenshot che mostra le impostazioni per il proxy D N S.":::

#### <a name="configure-dns-proxy---azure-cli"></a>Configurare il proxy DNS-interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare le impostazioni del proxy DNS nel firewall di Azure. È anche possibile usarlo per aggiornare le reti virtuali per l'uso del firewall di Azure come server DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale

Nell'esempio seguente la rete virtuale viene configurata per l'uso del firewall di Azure come server DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Abilita proxy DNS

Nell'esempio seguente viene abilitata la funzionalità proxy DNS nel firewall di Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Configurare il proxy DNS-Azure PowerShell

È possibile usare Azure PowerShell per configurare le impostazioni del proxy DNS nel firewall di Azure. È anche possibile usarlo per aggiornare le reti virtuali per l'uso del firewall di Azure come server DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale

Nell'esempio seguente la rete virtuale viene configurata per l'uso del firewall di Azure come server DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Abilita proxy DNS

Nell'esempio seguente viene abilitata la funzionalità proxy DNS nel firewall di Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Passaggi successivi

[Filtro FQDN nelle regole di rete](fqdn-filtering-network-rules.md)
