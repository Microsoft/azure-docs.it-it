---
title: Proteggere gli endpoint pubblici di Azure SQL Istanza gestita
description: Usare in modo sicuro gli endpoint pubblici in Azure SQL Istanza gestita
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332849"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Usare il Istanza gestita SQL di Azure in modo sicuro con gli endpoint pubblici
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Istanza gestita SQL di Azure è in grado di fornire la connettività degli utenti sugli [endpoint pubblici](../../virtual-network/virtual-network-service-endpoints-overview.md). Questo articolo illustra come rendere più sicura questa configurazione.

## <a name="scenarios"></a>Scenari

Azure SQL Istanza gestita fornisce un endpoint privato per consentire la connettività dall'interno della rete virtuale. L'opzione predefinita prevede l'isolamento massimo. Esistono tuttavia scenari in cui è necessario fornire una connessione all'endpoint pubblico:

- L'istanza gestita deve integrarsi con offerte di piattaforma distribuita come servizio (PaaS) multi-tenant.
- È necessaria una velocità effettiva maggiore dello scambio di dati rispetto a quella possibile quando si usa una VPN.
- I criteri aziendali proibiscono PaaS all'interno delle reti aziendali.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuire un'istanza gestita per l'accesso a endpoint pubblici

Sebbene non obbligatorio, il modello di distribuzione comune per un'istanza gestita con accesso a endpoint pubblico consiste nel creare l'istanza in una rete virtuale isolata dedicata. In questa configurazione, la rete virtuale viene usata solo per l'isolamento dei cluster virtuali. Non è importante se lo spazio degli indirizzi IP dell'istanza gestita si sovrappone allo spazio di indirizzi IP di una rete aziendale.

## <a name="secure-data-in-motion"></a>Proteggere i dati in movimento

Il traffico dati Istanza gestita SQL è sempre crittografato se il driver client supporta la crittografia. I dati inviati tra l'istanza gestita e altre macchine virtuali di Azure o i servizi di Azure non lasciano mai la spina dorsale di Azure. Se è presente una connessione tra l'istanza gestita e una rete locale, è consigliabile usare Azure ExpressRoute. ExpressRoute consente di evitare lo stato di trasferimento dei dati sulla rete Internet pubblica. Per la connettività privata dell'istanza gestita, è possibile usare solo il peering privato.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Blocca la connettività in ingresso e in uscita

Il diagramma seguente illustra le configurazioni di sicurezza consigliate:

![Configurazioni di sicurezza per il blocco della connettività in ingresso e in uscita](./media/public-endpoint-overview/managed-instance-vnet.png)

Un'istanza gestita ha un [indirizzo endpoint pubblico dedicato](management-endpoint-find-ip-address.md). Nel firewall in uscita lato client e nelle regole del gruppo di sicurezza di rete impostare questo indirizzo IP dell'endpoint pubblico per limitare la connettività in uscita.

Per garantire che il traffico verso l'istanza gestita provenga da origini attendibili, è consigliabile connettersi da origini con indirizzi IP noti. Usare un gruppo di sicurezza di rete per limitare l'accesso all'endpoint pubblico dell'istanza gestita sulla porta 3342.

Quando i client devono avviare una connessione da una rete locale, assicurarsi che l'indirizzo di origine venga convertito in un set noto di indirizzi IP. Se non è possibile eseguire questa operazione (ad esempio, una forza lavoro mobile è uno scenario tipico), si consiglia di usare [connessioni VPN da punto a sito e un endpoint privato](point-to-site-p2s-configure.md).

Se le connessioni vengono avviate da Azure, è consigliabile che il traffico provenga da un [indirizzo IP virtuale](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) assegnato ben noto (ad esempio, una macchina virtuale). Per semplificare la gestione degli indirizzi IP virtuali (VIP), è consigliabile usare i [prefissi degli indirizzi IP pubblici](../../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare l'endpoint pubblico per la gestione delle istanze: [configurare l'endpoint pubblico](public-endpoint-configure.md)
