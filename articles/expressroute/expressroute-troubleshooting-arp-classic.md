---
title: 'Azure ExpressRoute: tabelle ARP-risoluzione dei problemi: classica'
description: Questa pagina fornisce istruzioni per ottenere le tabelle ARP per un circuito ExpressRoute - modello di distribuzione classica.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394840"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Recupero di tabelle ARP nel modello di distribuzione classica
> [!div class="op_single_selector"]
> * [PowerShell - Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell-classico](expressroute-troubleshooting-arp-classic.md)
> 
> 

L’articolo illustra i passaggi per ottenere le tabelle ARP ( Address Resolution Protocol) per il circuito ExpressRoute di Azure.

> [!IMPORTANT]
> Questo documento è progettato per aiutare l'utente a rilevare e risolvere i problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Se le seguenti linee guida non permettono di risolvere il problema, aprire una richiesta di supporto con [Guida e supporto di Azure Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (Address Resolution Protocol) e tabelle ARP
ARP è un protocollo di livello 2 che viene definito in [RFC 826](https://tools.ietf.org/html/rfc826). Il protocollo ARP viene usato per mappare un indirizzo Ethernet (indirizzo MAC) su un indirizzo IP.

Una tabella ARP fornisce un mapping dell'indirizzo IPv4 e dell'indirizzo MAC per un particolare peering. La tabella ARP del peering di un circuito ExpressRoute fornisce le informazioni seguenti per ogni interfaccia (primaria e secondaria):

1. Mapping dell'indirizzo IP dell'interfaccia del router locale all'indirizzo MAC
2. Mapping dell'indirizzo IP dell'interfaccia del router ExpressRoute all'indirizzo MAC
3. L’età del mapping

Le tabelle ARP consentono di convalidare la configurazione di livello 2 e la risoluzione dei problemi di connettività di base di livello 2.

Di seguito è riportato un esempio di tabella ARP:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


La sezione seguente fornisce informazioni su come visualizzare le tabelle ARP visibili tramite i router perimetrali di ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Prerequisiti per l'utilizzo delle tabelle ARP
Prima di continuare, verificare che siano presenti gli elementi seguenti:

* Un circuito ExpressRoute valido configurato con almeno un peer. Il circuito deve essere completamente configurato dal provider di connettività. L'utente o il provider di connettività deve configurare almeno un peer (privato di Azure, pubblico di Azure e Microsoft) su questo circuito.
* Gli intervalli degli indirizzi IP usati per la configurazione del peer (privato di Azure, pubblico di Azure e Microsoft). Esaminare gli esempi di assegnazione di indirizzi IP nella [pagina requisiti di routing di ExpressRoute](expressroute-routing.md) per comprendere in che modo viene eseguito il mapping degli indirizzi IP alle interfacce sul lato e sul lato ExpressRoute. È possibile ottenere informazioni sulla configurazione del peering consultando la [pagina sulla configurazione del peering di ExpressRoute](expressroute-howto-routing-classic.md).
* Informazioni dal team di rete o provider di connettività sugli indirizzi MAC delle interfacce usate con questi indirizzi IP.
* Il più recente modulo PowerShell per Azure (versione 1.50 o successiva).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Le tabelle ARP per il circuito ExpressRoute
In questa sezione vengono fornite le istruzioni su come visualizzare le tabelle ARP per ciascun tipo di peering tramite PowerShell. Prima di continuare, il peering deve essere configurato dall’utente o dal provider di connettività. Ogni circuito ha due percorsi (primario e secondario). È possibile controllare la tabella ARP di ogni percorso in modo indipendente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelle ARP per il peering privato di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering privato di Azure

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Di seguito è illustrato un esempio di output per uno dei percorsi:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabelle ARP per il peering pubblico di Azure
Il cmdlet seguente fornisce le tabelle ARP per il peering pubblico di Azure

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Di seguito è illustrato un esempio di output per uno dei percorsi:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Di seguito è illustrato un esempio di output per uno dei percorsi:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabelle ARP per il peering di Microsoft
Il cmdlet seguente fornisce le tabelle ARP per il peering di Microsoft

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


Di seguito è illustrato un esempio di output per uno dei percorsi:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Come usare queste informazioni
La tabella ARP di un peer può essere usata per convalidare la connettività e la configurazione di livello 2 valide. Questa sezione offre una panoramica dell'aspetto delle tabelle ARP in scenari diversi.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>La tabella ARP quando un circuito è in stato operativo (previsto)
* La tabella ARP contiene una voce per il lato locale con gli indirizzi IP e MAC validi e una voce simile per il lato Microsoft.
* L'ultimo ottetto dell'indirizzo IP locale è sempre un numero dispari.
* L'ultimo ottetto dell'indirizzo IP Microsoft è sempre un numero pari.
* Lo stesso indirizzo MAC viene visualizzato sul lato Microsoft per tutti i 3 peer (principale/secondario).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>La tabella ARP quando il lato locale o del provider di connettività presenta problemi
 Viene visualizzata solo una voce nella tabella ARP. Mostra il mapping tra gli indirizzi MAC e IP usati sul lato Microsoft.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Se si verifica un problema simile, aprire una richiesta di supporto con il provider di connettività per risolverlo.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>La tabella ARP quando il lato Microsoft presenta problemi
* Se sono presenti problemi sul lato Microsoft, non verrà visualizzata la tabella ARP illustrata per il peering.
* Aprire una richiesta di supporto con [Guida e supporto di Azure Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Specificare che si è riscontrato un problema di connettività di livello 2.

## <a name="next-steps"></a>Passaggi successivi
* Convalidare le configurazioni di livello 3 per il circuito ExpressRoute
  * Ottenere un riepilogo del routing per determinare lo stato delle sessioni BGP.
  * Ottenere la tabella del routing per stabilire i prefissi pubblicati in ExpressRoute.
* Convalidare il trasferimento dei dati controllando i byte in ingresso e uscita.
* Aprire una richiesta di supporto con [Guida e supporto di Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se continuano a verificarsi problemi.

