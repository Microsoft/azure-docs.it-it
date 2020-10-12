---
title: Configurare zone di ricerca inversa per un controllo del banner SMTP
titlesuffix: Azure Virtual Network
description: Descrive come configurare zone di ricerca inversa per un controllo del banner SMTP in Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135056"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurare zone di ricerca inversa per un controllo del banner SMTP

Questo articolo descrive come usare una zona inversa in DNS di Azure e creare un record DNS inverso (PTR) per il controllo del banner SMTP.

## <a name="symptom"></a>Sintomo

Se si ospita un server SMTP in Microsoft Azure, quando si invia o si riceve un messaggio da server di posta remoti è possibile ricevere il messaggio di errore seguente:

**554: No PTR Record** (554: Nessun record PTR)

## <a name="solution"></a>Soluzione

Per gli indirizzi IP virtuali in Azure, i record inversi vengono creati in zone del dominio di proprietà di Microsoft, non in zone del dominio personalizzate.

Per configurare record PTR in zone di proprietà di Microsoft, usare la proprietà -ReverseFqdn nella risorsa PublicIpAddress. Per altre informazioni, vedere [Configurare il DNS inverso per i servizi ospitati in Azure](../dns/dns-reverse-dns-for-azure-services.md).

Quando si configurano i record PTR, assicurarsi che l'indirizzo IP e il nome di dominio completo (FQDN) inverso siano di proprietà della sottoscrizione. Se si tenta di impostare un nome di dominio completo inverso non appartenente alla sottoscrizione, viene visualizzato il messaggio di errore seguente:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Se si modifica manualmente il banner SMTP in modo che corrisponda al nome di dominio completo inverso predefinito, il server di posta remoto può comunque generare un errore, perché prevede che l'host del banner SMTP corrisponda al record MX per il dominio.
