---
title: Che cos'è DNS di Azure?
description: Panoramica del servizio di hosting DNS in Microsoft Azure. Ospitare il dominio in Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: d59f166897674e9dcff8086b8f5c906605155ee3
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924275"
---
# <a name="what-is-azure-dns"></a>Che cos'è DNS di Azure?

DNS di Azure è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.

Non è possibile usare DNS di Azure per acquistare un nome di dominio. Per una tariffa annuale, è possibile acquistare un nome di dominio usando [Domini del servizio app](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) o un registrar di nomi di dominio di terze parti. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

DNS di Azure offre le funzionalità seguenti.

## <a name="reliability-and-performance"></a>Affidabilità e prestazioni

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. DNS di Azure usa le reti Anycast. Ogni query DNS riceve una risposta dal server DNS disponibile più vicino, per assicurare prestazioni e disponibilità elevate per il dominio.

## <a name="security"></a>Security

 DNS di Azure si basa su Azure Resource Manager, che offre funzionalità quali:

* [Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per controllare gli utenti autorizzati ad accedere ad azioni specifiche per l'organizzazione.

* [Log attività](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa o per trovare un errore durante la risoluzione dei problemi.

* [Blocco delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) per bloccare una sottoscrizione, un gruppo di risorse o una risorsa. I blocchi impediscono ad altri utenti dell'organizzazione di modificare o eliminare accidentalmente risorse di importanza fondamentale.

Per altre informazioni, vedere [Come proteggere le zone e i record DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

DNS di Azure attualmente non supporta DNSSEC. Nella maggior parte dei casi è possibile ridurre la necessità di usare DNSSEC usando coerentemente HTTPS/TLS nelle applicazioni. Se DNSSEC è un requisito critico per le zone DNS, è possibile ospitare tali zone con provider di hosting DNS di terze parti.

## <a name="ease-of-use"></a>Semplicità d'uso

 DNS di Azure consente di gestire i record DNS per i servizi di Azure, nonché di garantire il servizio DNS alle risorse esterne. DNS di Azure è integrato nel portale di Azure e usa le stesse credenziali, lo stesso contratto di supporto e gli stessi metodi di fatturazione di altri servizi di Azure. 

La fatturazione di DNS è basata sul numero di zone DNS ospitate in Azure e sul numero di query DNS ricevute. Per altre informazioni sui prezzi, vedere [Prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure. Le applicazioni che richiedono la gestione automatizzata di DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Reti virtuali personalizzabili con domini privati

DNS di Azure supporta anche i domini DNS privati. Questa funzionalità consente di usare nomi di dominio personalizzati nelle reti private virtuali, anziché i nomi forniti da Azure attualmente disponibili.

Per altre informazioni, vedere [Usare DNS di Azure per i domini privati](private-dns-overview.md).

## <a name="alias-records"></a>Record alias

DNS di Azure supporta i set di record alias. È possibile usare un set di record alias per fare riferimento a una risorsa di Azure, come ad esempio un indirizzo IP pubblico di Azure, un profilo di Gestione traffico di Azure o un endpoint della rete per la distribuzione di contenuti (rete CDN) di Azure. Se l'indirizzo IP della risorsa sottostante viene modificato, il set di record alias si aggiorna automaticamente durante la risoluzione DNS. Il set di record alias fa riferimento all'istanza del servizio e l'istanza del servizio è associata a un indirizzo IP.

Inoltre, ora è possibile indirizzare il dominio radice o di tipo naked a un profilo di Gestione traffico o un endpoint della rete CDN usando un record alias. ad esempio contoso.com.

Per altre informazioni, vedere la [panoramica dei record alias DNS di Azure](dns-alias.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui record e le zone DNS, vedere [Panoramica delle zone e dei record DNS](dns-zones-records.md).

* Per informazioni su come creare una zona in DNS di Azure, vedere [Creare una zona DNS](./dns-getstarted-create-dnszone-portal.md).

* Per domande frequenti sul servizio DNS di Azure, vedere [Domande frequenti su DNS di Azure](dns-faq.md).
