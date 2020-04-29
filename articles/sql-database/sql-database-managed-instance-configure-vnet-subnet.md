---
title: Configurare una rete virtuale esistente per l'istanza gestita
description: Questo articolo descrive come configurare una rete virtuale e una subnet esistenti in cui distribuire Istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476950"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurare una rete virtuale esistente per Istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure deve essere distribuita in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure e in una subnet dedicate solo per le istanze gestite. È possibile usare la rete virtuale e la subnet esistenti se sono configurate in base ai [requisiti per la rete virtuale di Istanza gestita](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se uno degli scenari seguenti si applica al proprio caso, è possibile convalidare e modificare la rete usando lo script presentato in questo articolo:

- Si ha una nuova subnet che non è ancora configurata.
- Non si è certi che la subnet sia conforme ai [requisiti](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Si vuole verificare che la subnet sia ancora conforme ai [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements) dopo aver apportato modifiche.

> [!Note]
> È possibile creare un'istanza gestita solo nelle reti virtuali create tramite il modello di distribuzione di Azure Resource Manager. Le reti virtuali di Azure create tramite il modello di distribuzione classica non sono supportate. Calcolare le dimensioni della subnet seguendo le indicazioni dell'articolo [Determinare le dimensioni di una subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). Non è possibile ridimensionare la subnet dopo che vi sono state distribuite le risorse.
>
> Dopo la creazione di un'istanza gestita, lo stato di trasferimento dell'istanza gestita o VNet a un altro gruppo di risorse o a una sottoscrizione non è supportato.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Convalidare e modificare una rete virtuale esistente

Per creare un'istanza gestita all'interno di una subnet esistente, è consigliabile usare lo script di PowerShell seguente per preparare la subnet:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Lo script prepara la subnet in tre passaggi:

1. Validate: convalida la rete virtuale e la subnet selezionate per i requisiti di rete Istanza gestita.
2. Conferma: Mostra all'utente un set di modifiche che devono essere apportate per preparare la subnet per la distribuzione di Istanza gestita. Chiede inoltre all'utente di dare il proprio consenso.
3. Prepara: configura correttamente la rete virtuale e la subnet.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [che cos'è un istanza gestita?](sql-database-managed-instance.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per problemi relativi al DNS, vedere [configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
