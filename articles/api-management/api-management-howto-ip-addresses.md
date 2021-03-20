---
title: Indirizzi IP del servizio gestione API di Azure | Microsoft Docs
description: Informazioni su come recuperare gli indirizzi IP di un servizio gestione API di Azure e quando cambiano.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80047735"
---
# <a name="ip-addresses-of-azure-api-management"></a>Indirizzi IP di gestione API di Azure

Questo articolo descrive come recuperare gli indirizzi IP del servizio gestione API di Azure. Gli indirizzi IP possono essere pubblici o privati se il servizio si trova in una rete virtuale.

È possibile usare gli indirizzi IP per creare regole del firewall, filtrare il traffico in ingresso verso i servizi back-end o limitare il traffico in uscita.

## <a name="ip-addresses-of-api-management-service"></a>Indirizzi IP del servizio gestione API

Ogni istanza del servizio gestione API nel livello Developer, Basic, standard o Premium ha indirizzi IP pubblici, che sono esclusivi solo per tale istanza del servizio (non sono condivisi con altre risorse). 

È possibile recuperare gli indirizzi IP dal dashboard di panoramica della risorsa nel portale di Azure.

![Indirizzo IP di gestione API](media/api-management-howto-ip-addresses/public-ip.png)

È anche possibile recuperarli a livello di codice con la chiamata API seguente:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Gli indirizzi IP pubblici faranno parte della risposta:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Nelle [distribuzioni](api-management-howto-deploy-multi-region.md)in più aree ogni distribuzione a livello di area ha un indirizzo IP pubblico.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Indirizzi IP del servizio gestione API in VNet

Se il servizio gestione API si trova all'interno di una rete virtuale, avrà due tipi di indirizzi IP: Public e private.

Gli indirizzi IP pubblici vengono usati per la comunicazione interna sulla porta `3443` , per la gestione della configurazione (ad esempio, tramite Azure Resource Manager). Nella configurazione External VNet vengono usati anche per il traffico API di Runtime. Quando una richiesta viene inviata da gestione API a un back-end pubblico (con connessione Internet), un indirizzo IP pubblico sarà visibile come origine della richiesta.

Gli indirizzi IP virtuali privati (VIP), disponibili **solo** in [modalità VNet interna](api-management-using-with-internal-vnet.md), vengono usati per la connessione dall'interno della rete agli endpoint di gestione API, ovvero gateway, portale per sviluppatori e il piano di gestione per l'accesso diretto alle API. È possibile usarli per configurare i record DNS all'interno della rete.

Gli indirizzi di entrambi i tipi vengono visualizzati nel portale di Azure e nella risposta della chiamata API:

![Gestione API nell'indirizzo IP VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

Gestione API usa un indirizzo IP pubblico per le connessioni all'esterno di VNet e un indirizzo IP privato per le connessioni all'interno di VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Indirizzi IP del servizio gestione API del livello di consumo

Se il servizio gestione API è un servizio a livello di consumo, non ha un indirizzo IP dedicato. Il servizio del livello a consumo viene eseguito in un'infrastruttura condivisa e senza un indirizzo IP deterministico. 

Per finalità di restrizione del traffico, è possibile usare l'intervallo di indirizzi IP dei Data Center di Azure. Per la procedura precisa, vedere [l'articolo della documentazione di funzioni di Azure](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Modifiche agli indirizzi IP

Nei livelli Developer, Basic, standard e Premium di gestione API, gli indirizzi IP pubblici (VIP) sono statici per la durata di un servizio, con le eccezioni seguenti:

* Il servizio viene eliminato e quindi ricreato.
* La sottoscrizione al servizio viene [sospesa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) o [avvisata](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states), ad esempio per mancato pagamento, e quindi ripristinata.
* La rete virtuale di Azure viene aggiunta o rimossa dal servizio.
* Il servizio gestione API è stato spostato tra la modalità di distribuzione VNet esterna e interna.

Nelle [distribuzioni](api-management-howto-deploy-multi-region.md)in più aree, l'indirizzo IP regionale viene modificato se un'area viene sgomberata e quindi ripristinata.
