---
title: Gestire gli indirizzi IP pubblici | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Gestire gli indirizzi IP pubblici.  Viene anche illustrato come un indirizzo IP pubblico è una risorsa con le proprie impostazioni configurabili.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934940"
---
# <a name="manage-public-ip-addresses"></a>Gestire gli indirizzi IP pubblici

Informazioni su un indirizzo IP pubblico e su come crearlo, modificarlo ed eliminarlo. Un indirizzo IP pubblico è una risorsa con impostazioni proprie configurabili. Assegnazione di un indirizzo IP pubblico a una risorsa di Azure che supporta gli indirizzi IP pubblici consente:
- Comunicazioni in ingresso da Internet alla risorsa, ad esempio macchine virtuali (VM) di Azure, i gateway applicazione Azure, Azure Load Balancer, i gateway VPN di Azure e altro. È comunque possibile comunicare con alcune risorse, quali le macchine virtuali da Internet se una macchina virtuale non dispone di un indirizzo IP pubblico assegnato, fino a quando la macchina virtuale fa parte di un pool back-end di bilanciamento del carico a cui viene assegnato un indirizzo IP pubblico. Per determinare se a una risorsa per uno specifico servizio in Azure può essere assegnato un indirizzo IP pubblico, o se è possibile comunicare con essa tramite l'indirizzo IP pubblico di una diversa risorsa di Azure, vedere la documentazione relativa al servizio.
- Connettività in uscita verso Internet tramite un indirizzo IP prevedibile. Una macchina virtuale può, ad esempio, comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: per impostazione predefinita il relativo indirizzo sarà l'indirizzo di rete convertito da Azure in un indirizzo pubblico non prevedibile. L'assegnazione di un indirizzo IP pubblico a una risorsa consente di conoscere l'indirizzo IP usato per la connessione in uscita. Anche se prevedibile, l'indirizzo può essere modificato, a seconda del metodo di assegnazione scelto. Per altre informazioni, vedere [Creare un indirizzo IP pubblico](#create-a-public-ip-address). Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con l'account Azure.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo Azure PowerShell 1.0.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.31 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

L'account a cui si accede o che si connette ad Azure deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate in [autorizzazioni](#permissions).

Per gli indirizzi IP pubblici è previsto un addebito nominale. Per visualizzare i prezzi, consultare la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per istruzioni su come creare indirizzi IP pubblici usando il portale, PowerShell o l'interfaccia della riga di comando, vedere le pagine seguenti:

 * [Creare indirizzi IP pubblici - Portale](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Creare indirizzi IP pubblici - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Creare indirizzi IP pubblici - Interfaccia della riga di comando di Azure](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Sebbene il portale offra la possibilità di creare due risorse indirizzo IP pubblico (un IPv4 e un IPv6), i comandi PowerShell e CLI creano una risorsa con un indirizzo per una versione IP o l'altra. Se si vogliono due risorse indirizzo IP pubblico, una per ogni versione IP, è necessario eseguire il comando due volte, specificando nomi e versioni IP diverse per le risorse indirizzo IP pubblico.

Per altri dettagli sugli attributi specifici di un indirizzo IP pubblico durante la creazione, vedere la tabella seguente.

   |Impostazione|Necessaria?|Dettagli|
   |---|---|---|
   |Versione indirizzo IP|Sì| Selezionare IPv4 o IPv6 o entrambi. Se si selezionano entrambe, i due indirizzi IP pubblici vengono creati con un indirizzo IPv4 e un indirizzo IPv6. Altre informazioni su [IPv6 in Azure reti virtuali](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Sì|Tutti gli indirizzi IP pubblici creati prima dell'introduzione degli SKU sono indirizzi IP pubblici con SKU di **base** . Dopo aver creato l'indirizzo IP pubblico, non è possibile modificare lo SKU. Una macchina virtuale autonoma, le macchine virtuali all'interno di un set di disponibilità o i set di scalabilità di macchine virtuali possono usare SKU Basic o Standard. Non è consentito combinare SKU tra macchine virtuali all'interno di set di disponibilità o set di scalabilità o macchine virtuali autonome. SKU **Basic**: se si sta creando un indirizzo IP pubblico in un'area che supporta zone di disponibilità, l'opzione **Zona di disponibilità** è impostata su *Nessuna* per impostazione predefinita. Gli indirizzi IP pubblici di base non supportano le zone di disponibilità. SKU **Standard**: un indirizzo IP pubblico con SKU Standard può essere associato a una macchina virtuale o a un front-end di bilanciamento del carico. Se si sta creando un indirizzo IP pubblico in un'area che supporta zone di disponibilità, l'opzione **Zona di disponibilità** è impostata su *Con ridondanza della zona* per impostazione predefinita. Per altre informazioni sulle zone di disponibilità, vedere l'impostazione **Zona di disponibilità**. Lo SKU Standard è necessario se si associa l'indirizzo a un bilanciamento del carico Standard. Per saperne di più sui servizi di bilanciamento del carico standard, vedere [Azure load balancer standard SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU Standard per il bilanciamento del carico di Azure). Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](./network-security-groups-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.|
   |Livello|Sì|Indica se l'indirizzo IP è associato a un'area (**regione) o** è "Anycast" da più aree (**globale**). *Si noti che un IP "livello globale" è una funzionalità di anteprima per gli IP standard e attualmente utilizzata solo per l'Load Balancer tra aree*.|
   |Nome|Sì|Il nome deve essere univoco all'interno del gruppo di risorse selezionato.|
   |Assegnazione indirizzi IP|Sì|**Dinamico**: gli indirizzi dinamici vengono assegnati solo dopo che un indirizzo IP pubblico è stato associato a una risorsa di Azure e che tale risorsa è stata avviata per la prima volta. Gli indirizzi dinamici possono cambiare se sono assegnati a una risorsa, come ad esempio una macchina virtuale, e la macchina virtuale viene riavviata dopo essere stata arrestata (deallocata). L'indirizzo rimane invariato se una macchina virtuale viene riavviata o arrestata, ma non deallocata. Gli indirizzi dinamici vengono rilasciati quando una risorsa indirizzo IP pubblico viene dissociata da una risorsa a cui era associata. **Statico**: gli indirizzi statici vengono assegnati al momento della creazione di un indirizzo IP pubblico. Gli indirizzi statici non vengono rilasciati fino all'eliminazione di una risorsa indirizzo IP pubblico. Se l'indirizzo non è associato a una risorsa, è possibile modificare il metodo di assegnazione dopo che viene creato l'indirizzo. Se l'indirizzo è associato a una risorsa, potrebbe non essere possibile modificare il metodo di assegnazione. Se si seleziona *IPv6* per la **versione IP**, il metodo di assegnazione deve essere *dinamico* per lo SKU Basic.  Gli indirizzi SKU standard sono *statici* sia per IPv4 che per IPv6. |
   |Timeout di inattività (minuti)|No|Il numero di minuti in cui la connessione TCP o HTTP resta aperta senza affidarsi ai client per l'invio di messaggi Keep-Alive. Se si seleziona IPv6 in **Versione indirizzo IP**, questo valore non può essere modificato. |
   |Etichetta del nome DNS|No|Deve essere univoca all'interno della località di Azure nella quale viene creato il nome (incluse tutte le sottoscrizioni e tutti i clienti). Azure registra automaticamente il nome e l'indirizzo IP nel proprio DNS, consentendo la connessione a una risorsa con tale nome. Per creare il nome DNS completo, Azure aggiunge una subnet predefinita come *location.cloudapp.azure.com* al nome specificato, dove location è la località selezionata. Se si sceglie di creare entrambe le versioni dell'indirizzo, lo stesso nome DNS viene assegnato sia all'indirizzo IPv4 che all'indirizzo IPv6. Il DNS predefinito di Azure contiene i record dei nomi AAAA sia per IPv4 che per IPv6 e risponde con entrambi i record quando viene eseguita una ricerca per il nome DNS. Il client sceglie con quale indirizzo comunicare, IPv4 o IPv6. Anziché (oppure oltre a) usare l'etichetta del nome DNS con il suffisso predefinito, è possibile usare il servizio DNS di Azure per configurare un nome DNS con un suffisso personalizzato che viene risolto nell'indirizzo IP pubblico. Per altre informazioni, vedere [Usare DNS di Azure con un indirizzo IP pubblico di Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nome (visibile solo se si seleziona la versione IP di **entrambi**)|Sì, se si seleziona la versione IP di **entrambi**|Il nome deve essere diverso da quello immesso come primo **nome** in questo elenco. Se si sceglie di creare sia un indirizzo IPv4 che un indirizzo IPv6, il portale crea due risorse indirizzo IP pubblico separate, a ognuna delle quali è assegnata una versione dell'indirizzo IP.|
   |Assegnazione di indirizzi IP (visibile solo se si seleziona la versione IP di **entrambi**)|Sì, se si seleziona la versione IP di **entrambi**|Stesse restrizioni dell'assegnazione di indirizzi IP precedente|
   |Subscription|Sì|Deve esistere nella stessa [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della risorsa a cui si associeranno gli IP pubblici.|
   |Resource group|Sì|Può esistere nello stesso gruppo di risorse o in un altro [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) della risorsa alla quale si associeranno gli indirizzi IP pubblici.|
   |Posizione|Sì|Deve esistere nella stessa [posizione](https://azure.microsoft.com/regions), nota anche come area, come risorsa a cui associare gli IP pubblici.|
   |Zona di disponibilità| No | Questa impostazione viene visualizzata solo se si seleziona una località supportata. Per un elenco di località supportate, vedere [Panoramica di Zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se è stato selezionato lo SKU **Basic**, viene selezionata automaticamente l'opzione *Nessuna*. Se si preferisce garantire una zona specifica, è possibile selezionarne una. Entrambe le opzioni non prevedono la ridondanza della zona. Se è stato selezionato lo SKU **Standard**: viene selezionata automaticamente l'opzione con ridondanza della zona e il percorso dei dati è resiliente agli errori a livello di zona. Se si preferisce garantire una zona specifica, non resiliente agli errori a livello di zona, è possibile selezionarne una.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Visualizzare, modificare le impostazioni o eliminare un indirizzo IP pubblico

   - **Visualizzazione/elenco**: per esaminare le impostazioni per un indirizzo IP pubblico, inclusi lo SKU, l'indirizzo, qualsiasi associazione applicabile (ad esempio NIC macchina virtuale, Load Balancer front-end).
   - **Modifica**: per modificare le impostazioni usando le informazioni nel passaggio 4 di [creare un indirizzo IP pubblico](#create-a-public-ip-address), ad esempio il timeout di inattività, l'etichetta del nome DNS o il metodo di assegnazione.  Per il processo completo di aggiornamento di uno SKU IP pubblico da Basic a standard, vedere [aggiornare gli indirizzi IP pubblici di Azure](./virtual-network-public-ip-address-upgrade.md).
   >[!WARNING]
   >Per modificare l'assegnazione di un indirizzo IP pubblico da statico a dinamico, è innanzitutto necessario annullare l'associazione dell'indirizzo da qualsiasi configurazione IP applicabile. vedere la sezione **Delete** .  Si noti anche che quando si modifica il metodo di assegnazione da statico a dinamico, si perde l'indirizzo IP assegnato all'indirizzo IP pubblico. Mentre i server DNS pubblici Azure mantengono un mapping tra gli indirizzi statici o dinamici e qualsiasi etichetta del nome DNS (se ne è stato definito uno), un indirizzo IP dinamico può essere modificato quando la macchina virtuale viene avviata dopo essere stata arrestata (deallocata). Per evitare la modifica dell'indirizzo, assegnare un indirizzo IP statico.
   
|Operazione|Portale di Azure|Azure PowerShell|Interfaccia della riga di comando di Azure|
|---|---|---|---|
|Visualizzazione | Nella sezione **Panoramica** di un indirizzo IP pubblico |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per recuperare un oggetto indirizzo IP pubblico e visualizzarne le impostazioni| [AZ Network Public-IP Show](/cli/azure/network/public-ip#az-network-public-ip-show) per visualizzare le impostazioni|
|Elenco | Nella categoria **indirizzi IP pubblici** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per recuperare uno o più oggetti indirizzo IP pubblico e visualizzarne le impostazioni|[AZ Network Public-IP list](/cli/azure/network/public-ip#az-network-public-ip-list) per elencare gli indirizzi IP pubblici|
|Modifica | Per un indirizzo IP che è stato annullato, selezionare **configurazione** per modificare il timeout di inattività, l'etichetta del nome DNS o l'assegnazione di modifica dell'indirizzo IP di base da statico a dinamico.  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) per aggiornare le impostazioni |[AZ Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) per aggiornare |

   - **Delete**: l'eliminazione di indirizzi IP pubblici richiede che l'oggetto IP pubblico non sia associato ad alcuna configurazione IP o scheda di interfaccia di rete della macchina virtuale. Per ulteriori informazioni, vedere la tabella seguente.

|Risorsa|Portale di Azure|Azure PowerShell|Interfaccia della riga di comando di Azure|
|---|---|---|---|
|[Macchina virtuale](./remove-public-ip-address-vm.md)|Selezionare **dissocia** per annullare l'associazione dell'indirizzo IP dalla configurazione NIC, quindi selezionare **Elimina**.|[Impostare-AzPublicIpAddress per annullare](/powershell/module/az.network/set-azpublicipaddress) l'associazione dell'indirizzo IP dalla configurazione NIC; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) da eliminare|[AZ Network Public-IP Update--Remove per annullare](/cli/azure/network/public-ip#az-network-public-ip-update) l'associazione dell'indirizzo IP dalla configurazione NIC; [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) da eliminare |
|Front-End Load Balancer | Passare a un indirizzo IP pubblico non usato e selezionare **associa** e scegliere il Load Balancer con la configurazione IP front-end pertinente per sostituirlo (l'IP precedente può essere eliminato con lo stesso metodo usato per la macchina virtuale)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) per associare la nuova configurazione IP front-end a public Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) da eliminare; può anche usare [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) per rimuovere la configurazione IP front-end se ne sono presenti più di una |[AZ Network lb frontend-IP Update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) per associare la nuova configurazione IP front-end a public Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) da eliminare; può anche usare [AZ Network lb frontend-IP Delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) per rimuovere la configurazione IP front-end se ne sono presenti più di una|
|Firewall|N/D| [Deallocate ()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) per deallocare il firewall e rimuovere tutte le configurazioni IP | [AZ network firewall IP-config Delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) per rimuovere IP (ma deve usare PowerShell per deallocare per primo)|

## <a name="virtual-machine-scale-sets"></a>Set di scalabilità di macchine virtuali

Quando si usa un set di scalabilità di macchine virtuali con indirizzi IP pubblici, non sono presenti oggetti IP pubblici distinti associati alle singole istanze di macchine virtuali. È tuttavia possibile utilizzare un oggetto prefisso IP pubblico [per generare gli IP dell'istanza](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Per elencare gli indirizzi IP pubblici in un set di scalabilità di macchine virtuali, è possibile usare PowerShell ([Get-AzPublicIpAddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) o l'interfaccia della riga di comando ([AZ vmss list-instance-public-IPS](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Per altre informazioni, vedere [Rete per i set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Assegnare un indirizzo IP pubblico

Informazioni su come assegnare un indirizzo IP pubblico alle risorse seguenti:

- Una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (durante la creazione) o a una [macchina virtuale esistente](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Load Balancer pubblico](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway applicazione](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connessione da sito a sito con un gateway VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività negli indirizzi IP pubblici, l'account deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                                             | Nome                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Leggere un indirizzo IP pubblico                                          |
| Microsoft.Network/publicIPAddresses/write                          | Creare o aggiornare un indirizzo IP pubblico                           |
| Microsoft.Network/publicIPAddresses/delete                         | Eliminare un indirizzo IP pubblico                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associare un indirizzo IP pubblico a una risorsa                    |

## <a name="next-steps"></a>Passaggi successivi

- Creare un indirizzo IP pubblico usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Resource Manager](template-samples.md)
- Creare e assegnare [definizioni di criteri di Azure](./policy-reference.md) per indirizzi IP pubblici
