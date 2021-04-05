---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 8ca054b3a3d5147b7d98a021ce1e26d02d5581b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011566"
---
### <a name="record-names"></a>Nomi dei record

Nel servizio DNS di Azure i record vengono specificati usando nomi relativi. Un nome di dominio completo (FQDN) include il nome della zona, *mentre un nome* *relativo* non lo è. Ad esempio, il nome del record relativo `www` nella zona `contoso.com` restituisce il nome completo del record `www.contoso.com` .

Un record *vertice* è un record DNS alla radice (o *vertice*) di una zona DNS. Ad esempio, nella zona DNS `contoso.com` un record Apex ha anche il nome completo `contoso.com` (a volte viene definito dominio *nudo* ).  Per convenzione, per rappresentare record vertice viene usato il nome relativo "\@".

### <a name="record-types"></a>Tipi di record

Ogni record DNS ha un nome e un tipo. I record sono organizzati in tipi diversi in base ai dati che contengono. Il tipo più comune è il record "A", che esegue il mapping di un nome a un indirizzo IPv4. Un altro tipo comune è il record "MX", che esegue il mapping di un nome a un server di posta.

DNS di Azure supporta tutti i tipi di record DNS comuni: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Si noti che i [record SPF vengono rappresentati usando record TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Set di record

In alcuni casi è necessario creare più record DNS con un determinato nome e tipo. Si supponga, ad esempio, che il sito Web "www.contoso.com" sia ospitato in due diversi indirizzi IP. Questo sito Web richiede due diversi record A, uno per ogni indirizzo IP. Ecco un esempio di un set di record:

```dns
www.contoso.com.        3600    IN    A    134.170.185.46
www.contoso.com.        3600    IN    A    134.170.188.221
```

DNS di Azure gestisce tutti i record DNS usando *set di record*. Un set di record, chiamato anche set di record di *risorse* è la raccolta di record DNS che hanno lo stesso nome e sono dello stesso tipo in una zona. La maggior parte dei set di record contiene un singolo record. Non sono tuttavia rari esempi come quello precedente, in cui un set di record contiene più di un record.

Si supponga, ad esempio, di aver già creato un record "www" nella zona "contoso.com", che punta all'indirizzo IP "134.170.185.46" (primo record sopra).  Per creare il secondo record, è necessario aggiungere il record al set di record esistente invece di creare un altro set di record.

I tipi di record SOA e CNAME sono eccezioni. Gli standard DNS non permettono più record con lo stesso nome per questi tipi, quindi questi set di record possono contenere un solo record.