---
title: Eliminare una soluzione VMware di Azure da CloudSimple cloud privato
description: Informazioni su come eliminare un cloud privato CloudSimple. Quando si elimina un cloud privato, verranno eliminati tutti i cluster.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896261"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Eliminare un cloud privato CloudSimple

CloudSimple offre la flessibilità necessaria per eliminare un cloud privato.  Un cloud privato è costituito da uno o più cluster vSphere. Ogni cluster può avere da 3 a 16 nodi. Quando si elimina un cloud privato, verranno eliminati tutti i cluster.

## <a name="before-you-begin"></a>Prima di iniziare

L'eliminazione di un cloud privato comporta l'eliminazione dell'intero cloud privato.  Tutti i componenti del cloud privato verranno eliminati.  Se si vuole mantenere i dati, assicurarsi di aver eseguito il backup dei dati nell'archiviazione locale o in archiviazione di Azure.

I componenti di un cloud privato includono:

* Nodi CloudSimple
* Macchine virtuali
* VLAN/subnet
* Tutti i dati utente archiviati nel cloud privato
* Tutti gli allegati delle regole del firewall in una VLAN/subnet

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Eliminare un cloud privato

1. [Accedere al portale di CloudSimple](access-cloudsimple-portal.md).

2. Aprire la pagina **risorse** .

3. Fare clic sul cloud privato che si vuole eliminare

4. Nella pagina Riepilogo fare clic su **Elimina**.

    ![Elimina cloud privato](media/delete-private-cloud.png)

5. Nella pagina Conferma immettere il nome del cloud privato e fare clic su **Elimina**. 

    ![Elimina cloud privato-conferma](media/delete-private-cloud-confirm.png)

Il cloud privato è contrassegnato per l'eliminazione.  Il processo di eliminazione viene avviato dopo tre ore ed Elimina il cloud privato.

> [!CAUTION]
> I nodi devono essere eliminati dopo l'eliminazione del cloud privato.  La misurazione dei nodi continuerà fino a quando i nodi non verranno eliminati dalla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* [Eliminare nodi](delete-nodes.md)
