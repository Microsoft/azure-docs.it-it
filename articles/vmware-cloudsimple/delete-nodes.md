---
title: Eliminare i nodi per la soluzione VMware da CloudSimple-Azure
description: Informazioni su come eliminare i nodi da VMWare con la distribuzione di CloudSimple. I nodi CloudSimple sono a consumo. Eliminare i nodi non utilizzati da portale di Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142265"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Eliminare i nodi dalla soluzione VMware di Azure CloudSimple

I nodi CloudSimple vengono misurati dopo la creazione.  Per arrestare la misurazione dei nodi, è necessario eliminare i nodi.  Si eliminano i nodi non utilizzati da portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Un nodo può essere eliminato solo nelle condizioni seguenti:

* Un cloud privato creato con i nodi viene eliminato.  Per eliminare un cloud privato, vedere [eliminare una soluzione VMware di Azure da CloudSimple cloud privato](delete-private-cloud.md).
* Il nodo è stato rimosso dal cloud privato compattando il cloud privato.  Per compattare un cloud privato, vedere la pagina relativa alla [compattazione della soluzione VMware di Azure con CloudSimple private cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Elimina nodo CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare i **nodi CloudSimple**.

   ![Cerca nodi CloudSimple](media/create-cloudsimple-node-search.png)

3. Selezionare i **nodi CloudSimple**.

4. Selezionare i nodi che non appartengono a un cloud privato da eliminare.  Colonna **nome cloud privato** indica il nome del cloud privato a cui appartiene un nodo.  Se un nodo non è utilizzato da un cloud privato, il valore sarà vuoto. 

    ![Seleziona nodi CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Solo i nodi che non fanno parte del cloud privato possono essere eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [cloud privato](cloudsimple-private-cloud.md)
