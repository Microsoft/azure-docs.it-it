---
title: Domande frequenti su DNS privato di Azure
description: In questo articolo vengono fornite le domande frequenti su Azure DNS privato
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 9d183f2da7b916b1547fa1f81aa877b1b5488b41
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308436"
---
# <a name="azure-private-dns-faq"></a>Domande frequenti su DNS privato di Azure

Di seguito sono riportate le domande frequenti sul DNS privato di Azure.

## <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?

I domini privati sono supportati con la funzionalità zone DNS privato di Azure. DNS privato zone sono risolvibili solo all'interno di reti virtuali specificate. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

Per informazioni sulle altre opzioni DNS interne in Azure, vedere [risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure DNS privato zone funziona tra le aree di Azure?

Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, Tutte le reti virtuali devono essere collegate alla zona DNS privata.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>La connettività a Internet dalle reti virtuali è necessaria per le zone private?

No. Le zone private interagiscono con le reti virtuali Vengono usati per gestire i domini per le macchine virtuali o altre risorse all'interno e nelle reti virtuali. La connettività a Internet non è necessaria per la risoluzione dei nomi.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione?

Sì. È possibile collegare una zona DNS privata a migliaia di reti virtuali. Per altre informazioni, vedere [limiti di DNS di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Una rete virtuale appartenente a una sottoscrizione diversa può essere collegata a una zona privata?

Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere concessa a diversi ruoli di Azure. Ad esempio, il ruolo di Azure collaboratore rete classico dispone delle autorizzazioni di scrittura per le reti virtuali e il ruolo di collaboratore zone DNS privato dispone di autorizzazioni di scrittura per le zone DNS private. Per altre informazioni sui ruoli di Azure, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>I record DNS della macchina virtuale registrati automaticamente in una zona privata verranno eliminati automaticamente quando si elimina la macchina virtuale?

Sì. Se si elimina una macchina virtuale all'interno di una rete virtuale collegata con la registrazione automatica abilitata, i record registrati vengono eliminati automaticamente.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Ho riconfigurato il sistema operativo nella macchina virtuale per avere un nuovo nome host o un indirizzo IP statico. Perché non si può notare che le modifiche sono riflesse nella zona privata?

I record della zona privata vengono popolati dal servizio DHCP di Azure. i messaggi di registrazione client vengono ignorati. Se è stato disabilitato il supporto client DHCP nella macchina virtuale configurando un indirizzo IP statico, le modifiche apportate al nome host o all'indirizzo IP statico nella macchina virtuale non vengono riflesse nella zona.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Ho configurato un suffisso DNS preferito nella mia macchina virtuale Windows. Perché i record sono ancora registrati nella zona collegata alla rete virtuale?

Il servizio DHCP di Azure ignora qualsiasi suffisso DNS quando registra la zona DNS privata. Ad esempio, se la macchina virtuale è configurata `contoso.com` come suffisso DNS primario, ma la rete virtuale è collegata alla `fabrikam.com` zona DNS privata, la registrazione della macchina virtuale viene visualizzata nella `fabrikam.com` zona DNS privata.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>È possibile eliminare manualmente un record della macchina virtuale registrato automaticamente in una zona privata da una rete virtuale collegata?

Sì. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Cosa accade quando si prova a creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente registrata automaticamente in una rete virtuale collegata?

Si tenta di creare manualmente un nuovo record DNS in una zona privata con lo stesso nome host di una macchina virtuale esistente registrata automaticamente in una rete virtuale collegata. il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Cosa accade quando si scollega una rete virtuale collegata da una zona privata? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?

Sì. Per scollegare una rete virtuale collegata da una zona privata, è necessario aggiornare la zona DNS per rimuovere il collegamento alla rete virtuale associata. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Cosa accade quando si elimina una rete virtuale collegata collegata a una zona privata? È necessario aggiornare manualmente la zona privata per scollegare la rete virtuale come rete virtuale collegata dalla zona?

No. Quando si elimina una rete virtuale collegata senza scollegarla da una zona privata, l'operazione di eliminazione ha esito positivo e i collegamenti alla zona DNS vengono cancellati automaticamente.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>La risoluzione DNS tramite il nome di dominio completo predefinito (internal.cloudapp.net) continuerà a funzionare anche se una zona privata (ad esempio, private.contoso.com) è collegata a una rete virtuale?

Sì. Le zone private non sostituiscono la zona internal.cloudapp.net predefinita fornita da Azure. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata?

No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata.
Per istruzioni su come modificare questo suffisso, vedere [usare DNS dinamico per registrare i nomi host nel proprio server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quali sono i limiti di utilizzo per le zone private di DNS di Azure?

Per informazioni dettagliate sui limiti di utilizzo per le zone private di DNS di Azure, vedere [limiti di DNS di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) .

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Perché le zone DNS private esistenti non vengono visualizzate nella nuova esperienza del portale?

Se la zona DNS privata esistente è stata creata usando l'API di anteprima, è necessario eseguire la migrazione di queste zone al nuovo modello di risorsa. DNS privato zone create con l'API di anteprima non vengono visualizzate nella nuova esperienza del portale. Per istruzioni su come eseguire la migrazione al nuovo modello di risorse, vedere di seguito.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Ricerca per categorie eseguire la migrazione delle zone DNS private esistenti al nuovo modello?

Si consiglia vivamente di eseguire la migrazione al nuovo modello di risorsa appena possibile. Il modello di risorse legacy sarà supportato, tuttavia, le funzionalità aggiuntive non verranno sviluppate in base a questo modello. In futuro, si intende deprecarlo a favore del nuovo modello di risorse. Per istruzioni su come eseguire la migrazione delle zone DNS private esistenti al nuovo modello di risorse, vedere[Guida alla migrazione per le zone private di DNS di Azure](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più su Azure DNS privato](private-dns-overview.md)
