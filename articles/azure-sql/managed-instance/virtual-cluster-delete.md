---
title: Elimina una subnet dopo l'eliminazione di un Istanza gestita SQL
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99093683"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo fornisce linee guida su come eliminare manualmente una subnet dopo aver eliminato l'ultimo Istanza gestita SQL di Azure che si trova al suo interno.

Le istanze gestite di SQL vengono distribuite in [cluster virtuali](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Ogni cluster virtuale è associato a una subnet. Il cluster virtuale viene mantenuto in base alla progettazione per 12 ore dopo l'eliminazione dell'ultima istanza, per consentire di creare più rapidamente istanze gestite di SQL nella stessa subnet. Non è previsto alcun addebito per mantenere un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si vuole attendere 12 ore e si preferisce eliminare il cluster virtuale e la subnet prima, è possibile farlo manualmente. Eliminare manualmente il cluster virtuale usando il portale di Azure o l'API cluster virtuali.

> [!IMPORTANT]
> - Per il corretto completamento dell'eliminazione, il cluster virtuale non deve contenere istanze gestite di SQL. 
> - L'eliminazione di un cluster virtuale è un'operazione a esecuzione prolungata che dura circa 1,5 ore (vedere [operazioni di gestione di SQL istanza gestita](./sql-managed-instance-paas-overview.md#management-operations) per l'ora di eliminazione del cluster virtuale aggiornata). Il cluster virtuale sarà ancora visibile nel portale fino al completamento del processo.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Eliminare un cluster virtuale dalla portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale.

![Screenshot del portale di Azure, con la casella di ricerca evidenziata](./media/virtual-cluster-delete/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare questa risorsa e selezionare **Elimina**. Viene richiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del dashboard portale di Azure cluster virtuali, con l'opzione Elimina evidenziata](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Portale di Azure notifiche indicheranno che la richiesta di eliminazione del cluster virtuale è stata inviata correttamente. L'operazione di eliminazione durerà per circa 1,5 ore, durante le quali il cluster virtuale sarà ancora visibile nel portale. Al termine del processo, il cluster virtuale non sarà più visibile e la subnet associata verrà rilasciata per il riutilizzo.

> [!TIP]
> Se non sono presenti istanze gestite di SQL nel cluster virtuale e non è possibile eliminare il cluster virtuale, assicurarsi che non sia in corso una distribuzione dell'istanza in corso. Sono incluse le distribuzioni avviate e annullate ancora in corso. Questo perché queste operazioni utilizzeranno comunque il cluster virtuale, bloccando l'eliminazione. Esaminando la scheda **distribuzioni** del gruppo di risorse in cui l'istanza è stata distribuita indicherà le distribuzioni in corso. In questo caso, attendere il completamento della distribuzione, eliminare il Istanza gestita SQL e quindi eliminare il cluster virtuale.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Eliminare un cluster virtuale usando l'API

Per eliminare un cluster virtuale tramite l'API, usare i parametri URI specificati nel [metodo Delete dei cluster virtuali](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Informazioni sull' [architettura di connettività in SQL istanza gestita](connectivity-architecture-overview.md).
- Informazioni su come [modificare una rete virtuale esistente per SQL istanza gestita](vnet-existing-add-subnet.md).
- Per un'esercitazione che illustra come creare una rete virtuale, creare un Istanza gestita SQL di Azure e ripristinare un database da un backup del database, vedere [creare un istanza gestita SQL di Azure (portale)](instance-create-quickstart.md).
- Per problemi relativi al DNS, vedere [configurare un DNS personalizzato](custom-dns-configure.md).