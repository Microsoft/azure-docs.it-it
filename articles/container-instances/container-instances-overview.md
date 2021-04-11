---
title: Contenitori serverless in Azure
description: Il servizio Istanze di Azure Container rappresenta il modo più semplice e rapido per eseguire contenitori isolati in Azure, senza dover gestire macchine virtuali né adottare un agente di orchestrazione di livello superiore.
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: c445687db7a154b6fc86e962d2c2340ad6297431
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799072"
---
# <a name="what-is-azure-container-instances"></a>Informazioni su Istanze di Azure Container

I contenitori si stanno affermando come soluzione preferita per la creazione di pacchetti, la distribuzione e la gestione di applicazioni cloud. Istanze di Azure Container rappresenta il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover gestire macchine virtuali né dover adottare un servizio di livello superiore.

Istanze di Azure Container è un'ottima soluzione per qualsiasi scenario e funziona anche in contenitori isolati, inclusi i processi di compilazione, l'automazione di attività e le applicazioni semplici. Per gli scenari in cui si rende necessaria l'orchestrazione completa dei contenitori, quali il rilevamento dei servizi tra più contenitori, la scalabilità automatica e gli aggiornamenti coordinati delle applicazioni, è consigliabile usare il [servizio Azure Kubernetes](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempi di avvio rapidi

I contenitori offrono notevoli vantaggi in termini di avvio rispetto alle macchine virtuali. Istanze di Azure Container consente di avviare contenitori in Azure in pochi secondi, senza dover gestire VM né doverne effettuare il provisioning.

È possibile inserire immagini di contenitori Linux o Windows da Docker Hub, un [registro contenitori di Azure](../container-registry/index.yml) privato o un altro registro Docker basato sul cloud. Per informazioni sui registri supportati da ACI, vedere le [domande frequenti](container-instances-faq.md) . Istanze di Azure Container memorizza nella cache diverse immagini comuni dei sistemi operativi di base, accelerando la distribuzione di immagini di applicazioni personalizzate.

## <a name="container-access"></a>Accesso ai contenitori

Istanze di Azure Container consente di esporre i gruppi di contenitori direttamente in Internet con un indirizzo IP e un nome di dominio completo (FQDN). Quando si crea un'istanza di contenitore, è possibile specificare un'etichetta personalizzata per il nome DNS, in modo che l'applicazione sia raggiungibile tramite *etichettapersonalizzata*.*areaazure*.contenitoreazure.io.

Istanze di Azure Container supporta anche l'esecuzione di un comando in un contenitore in esecuzione offrendo una shell interattiva per facilitare lo sviluppo e la risoluzione dei problemi delle applicazioni. L'accesso viene effettuato tramite HTTPS, usando TLS per proteggere le connessioni client.

> [!IMPORTANT]
> A partire dal 13 gennaio 2020, Istanze di Azure Container richiederà l'uso di TLS 1.2 in tutte le connessioni sicure da server e applicazioni. Il supporto per TLS 1.0 e 1.1 verrà ritirato.

## <a name="compliant-deployments"></a>Distribuzioni conformi

### <a name="hypervisor-level-security"></a>Sicurezza a livello di hypervisor

In passato i contenitori offrivano l'isolamento di dipendenze delle applicazioni e la governance delle risorse, ma non erano considerati sufficientemente protetti per l'uso di multi-tenant ostili. Istanze di Azure Container garantisce all'applicazione in un contenitore lo stesso livello di isolamento di cui usufruirebbe in una VM.

### <a name="customer-data"></a>Dati del cliente

Il servizio Istanze di Azure Container archivia i dati minimi del cliente necessari per assicurare che i gruppi di contenitori vengano eseguiti come previsto. L'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) dell'area geografica di Asia Pacifico geografica e del Brasile meridionale (San Paolo) del Brasile. Per tutte le altre aree i dati dei clienti vengono archiviati in [Geo](https://azure.microsoft.com/global-infrastructure/geographies/). Per altre informazioni, contattare il supporto di Azure.

## <a name="custom-sizes"></a>Dimensioni personalizzate

I contenitori sono in genere ottimizzati per l'esecuzione di un'applicazione singola, ma le esigenze specifiche di tale applicazione possono variare notevolmente. Istanze di Azure Container supporta un utilizzo ottimale consentendo di specificare esattamente memoria e core CPU. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.

Per i processi a elevato utilizzo di calcolo, ad esempio l'apprendimento automatico, Istanze di Azure Container può pianificare contenitori Linux per l'uso di [risorse GPU](container-instances-gpu.md) NVIDIA Tesla (anteprima).

## <a name="persistent-storage"></a>Archiviazione permanente

Per recuperare e rendere persistente lo stato con Istanze di Azure Container, è disponibile il [montaggio diretto di condivisioni di File di Azure](./container-instances-volume-azure-files.md), supportato da Archiviazione di Azure.

## <a name="linux-and-windows-containers"></a>Contenitori Linux e Windows

Istanze di Azure Container consente di pianificare contenitori sia Windows che Linux con la stessa API. È sufficiente specificare il tipo di sistema operativo quando si creano i [gruppi di contenitori](container-instances-container-groups.md).

Alcune funzionalità attualmente sono limitate ai contenitori Linux:

* Più contenitori per gruppo di contenitori
* Montaggio di volumi ([File di Azure](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [segreto](container-instances-volume-secret.md))
* [Metriche di utilizzo delle risorse](container-instances-monitor.md) con Monitoraggio di Azure
* [Distribuzione della rete virtuale](container-instances-vnet.md)
* [Risorse GPU](container-instances-gpu.md) (anteprima)

Per le distribuzioni di contenitori di Windows, usare le immagini basate sulle comuni [immagini di base di Windows](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Gruppi con pianificazione condivisa

Istanze di Azure Container supporta la pianificazione di [gruppi multi-contenitore](container-instances-container-groups.md) che condividono un computer host, una rete locale, un archivio e un ciclo di vita. Questo consente di combinare il contenitore dell'applicazione principale con altri contenitori di ruoli di supporto, come quelli collaterali di registrazione.

## <a name="virtual-network-deployment"></a>Distribuzione della rete virtuale

Istanze di Azure Container consente la [distribuzione di istanze di contenitori in una rete virtuale di Azure](container-instances-vnet.md). Quando vengono distribuite in una subnet all'interno della rete virtuale, le istanze di contenitori possono comunicare in modo sicuro con altre risorse della rete virtuale, incluse quelle locali (tramite [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Passaggi successivi

Provare a distribuire un contenitore in Azure con un comando singolo, seguendo le indicazioni della guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva di Istanze di Azure Container](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
