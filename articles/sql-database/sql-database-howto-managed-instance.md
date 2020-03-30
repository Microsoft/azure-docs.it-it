---
title: Come configurare un'istanza gestitaHow to configure a managed instance
description: Informazioni su come configurare e gestire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256913"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Come usare un'istanza gestita nel database SQL di Azure

In questo articolo sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare l'istanza gestita.

## <a name="migration"></a>Migrazione

- [Eseguire la migrazione a un'istanza gestita](sql-database-managed-instance-migrate.md): ottenere informazioni sul processo di migrazione consigliato e sugli strumenti per la migrazione in istanza gestita.

- [Eseguire la migrazione del certificato TDE a un'istanza gestita](sql-database-managed-instance-migrate-tde-certificate.md): se il database di SQL Server è protetto tramite crittografia TDE (Transparent Data Encryption), è necessario eseguire la migrazione di certificati che un'istanza gestita può usare per decrittografare il backup da ripristinare in Azure.

## <a name="network-configuration"></a>Configurazione di rete

- [Determinare le dimensioni di una subnet di un'istanza gestita](sql-database-managed-instance-determine-size-vnet-subnet.md): la funzione Istanza gestita viene inserita in subnet dedicate che non possono essere ridimensionate quando si aggiungono le risorse all'interno. Si deve pertanto calcolare quale intervallo di indirizzi IP sarebbe necessario per la subnet a seconda del numero e dei tipi di istanze da distribuire nella subnet.
- [Creare una nuova rete virtuale e una nuova subnet per un'istanza gestita](sql-database-managed-instance-create-vnet-subnet.md): la rete virtuale e la subnet di Azure in cui si intende distribuire le istanze gestite devono essere configurate in base ai [requisiti di rete descritti qui](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Questa guida descrive il modo più semplice per creare la nuova rete virtuale e la nuova subnet configurate correttamente per le istanze gestite.
- [Configurare una rete virtuale e una subnet esistenti per un'istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md): se si intende configurare la rete virtuale e la subnet esistenti per distribuire le istanze gestite all'interno, qui è possibile trovare lo script che controlla i [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements) e che consente di configurare la subnet in base ai requisiti.
- [Configurare](sql-database-managed-instance-custom-dns.md) il DNS personalizzato: è necessario configurare il DNS personalizzato se si desidera accedere alle risorse esterne nei domini personalizzati dall'istanza gestita tramite un server collegato di profili di posta db.
- [Sincronizzare la configurazione di rete](sql-database-managed-instance-sync-network-configuration.md): può accadere che, sebbene [l'app in uso sia stata integrata con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md), non sia possibile stabilire una connessione a un'stanza gestita. È possibile provare ad aggiornare la configurazione di rete per il piano di servizio.
- [Indirizzo IP dell'endpoint](sql-database-managed-instance-find-management-endpoint-ip-address.md) di gestione Find: l'istanza gestita usa endpoint pubblici per scopi di gestione. È possibile determinare l'indirizzo IP dell'endpoint di gestione tramite lo script descritto qui.
- [Verificare la protezione firewall integrata:](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) l'istanza gestita è protetta con firewall incorporato che consente il traffico solo sulle porte necessarie. È possibile controllare e verificare le regole del firewall incorporato usando lo script descritto in questa guida.
- [Connettere le applicazioni:](sql-database-managed-instance-connect-app.md) l'istanza gestita viene inserita nella propria rete virtuale di Azure privata con indirizzo IP privato. Informazioni su modelli differenti per connettere le applicazioni all'istanza gestita.

## <a name="feature-configuration"></a>Configurazione delle funzionalità

- La [replica transazionale](replication-with-sql-database-managed-instance.md) consente di replicare i dati tra istanze gestite o da SQL Server in locale a un'istanza gestita e viceversa. Altre informazioni sull'uso e sulla configurazione della replica delle transazioni sono disponibili in questa guida.
- [Configurare il rilevamento delle minacce](sql-database-managed-instance-threat-detection.md): il [rilevamento delle minacce](sql-database-threat-detection-overview.md) è una funzionalità integrata del database SQL di Azure che rileva attacchi potenziali diversi, ad esempio un attacco SQL injection oppure un accesso da posizioni sospette. Questa guida descrive come abilitare e configurare il [rilevamento delle minacce](sql-database-threat-detection-overview.md) per un'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle [guide alle procedure per singoli database](sql-database-howto-single-database.md)