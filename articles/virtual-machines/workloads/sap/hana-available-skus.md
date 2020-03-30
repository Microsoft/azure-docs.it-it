---
title: SKU per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: SKU per SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617254"
---
# <a name="available-skus-for-hli"></a>SKU disponibili per HLI

Sap HANA in Azure (istanze di grandi dimensioni) servizio basato su revisione 3 timbri è disponibile in diverse configurazioni nelle aree di Azure di:SAP HANA on Azure (Large Instances) service based on Revision 3 stamps is available in several configurations in the Azure regions of:

- Stati Uniti occidentali
- Stati Uniti orientali
- Australia orientale
- Australia sud-orientale
- Europa occidentale
- Europa settentrionale
- Giappone orientale
- Giappone occidentale

Sap HANA in Azure (istanze di grandi dimensioni) servizio basato su revisione 4 timbri è disponibile in diverse configurazioni nelle aree di Azure di:SAP HANA on Azure (Large Instances) service based on Revision 4 stamps is available in several configurations in the Azure regions of:

- Stati Uniti occidentali 2
- Stati Uniti orientali
- Stati Uniti centro-meridionali
- Europa occidentale
- Europa settentrionale



Esempio di elenco [SKU certificati SAP HANA per HANA istanze Large](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure):

| Soluzione SAP | CPU | Memoria | Archiviazione | Disponibilità |
| --- | --- | --- | --- | --- |
| Ottimizzata per OLAP: SAP BW, BW/4HANA<br /> o SAP HANA per carico di lavoro OLAP generico | SAP HANA in Azure S72<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  768 GB |  3 TB | Non più disponibile |
| --- | SAP HANA in Azure S144<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  1,5 TB |  6 TB | Non più disponibile |
| --- | SAP HANA in Azure S192<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU |  2 TB |  8 TB | Non più disponibile |
| --- | SAP HANA in Azure S224<br /> – 4 x processore Intel® Xeon® Platinum 8276 (noto anche come lago Cascade)<br /> 112 core CPU e 224 thread della CPU |  3 TB |  6,3 TB | Disponibile nei francobolli Revision3 e Revision4  |
| --- | SAP HANA in Azure S384<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  4 TB |  16 TB | Disponibile in revisione4 francobolli |
| Ottimizzata per OLTP: SAP Business Suite<br /> in SAP HANA o S/4HANA (OLTP),<br /> OLTP generico | SAP HANA in Azure S72m<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  1,5 TB |  6 TB | Non più disponibile |
|---| SAP HANA in Azure S144m<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  3 TB |  12 TB | Non più disponibile |
|---| SAP HANA in Azure S192m<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU  |  4 TB |  16 TB | Non più disponibile |
| --- | SAP HANA in Azure S224m<br /> – 4 x processore Intel® Xeon® Platinum 8276 (noto anche come lago Cascade)<br /> 112 core CPU e 224 thread della CPU |  6 TB |  10,5 TB | Disponibile nei francobolli Revision3 e Revision4  |
|---| SAP HANA in Azure S384m<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  6 TB |  18 TB | Disponibile in revisione4 francobolli|
|---| SAP HANA in Azure S384xm<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  8 TB |  22 TB |  Disponibile in revisione4 francobolli |
|---| SAP HANA in Azure S576m<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  12 TB |  28 TB | Disponibile in revisione4 francobolli|
|---| SAP HANA in Azure S768m<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  16 TB |  36 TB | Disponibile in revisione4 francobolli|
|---| SAP HANA in Azure S960m<br /> – 20 x processore Intel® Xeon® E7-8890 v4<br /> 480 core CPU e 960 thread CPU |  20 TB |  46 TB | Disponibile in revisione4 francobolli|


In SAP HANA TDIv5, SAP consente il dimensionamento specifico del cliente e i progetti specifici del cliente, che potrebbero portare a configurazioni del server, che non sono elencate come certificate in:

- [SAP HANA Certified Appliances](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html) (Appliance certificate per SAP HANA)
- [Piattaforme IaaS certificate SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

In molti casi, queste configurazioni server specifiche del cliente contengono più memoria rispetto alle unità server certificate con SAP. Lavorando con SAP i clienti hanno la possibilità di ottenere il supporto SAP e di certificare le proprie configurazioni server con dimensionamento personalizzato. In Azure sono disponibili gli SKU standard HANA Large Instance seguenti e nel listino prezzi Microsoft per tali progetti di ridimensionamento specifici del cliente TDIv5.In Azure, the following HANA Large Instance standard SKUs are available and in the Microsoft price list for such TDIv5 customer-specific sizing projects.

| SKU|CPU | Memoria | Archiviazione | Disponibilità |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA in Azure S96<br /> – 2 x processore Intel® Xeon® E7-8890 v4<br /> 48 core CPU e 96 thread CPU |  768 GB |  3 TB | Disponibile nei francobolli Revision3 e Revision4|


| SKU originale che può essere <br /> esteso in memoria | CPU | Memoria | Archiviazione | Disponibilità |
| --- | --- | --- | --- | --- |
| S192m può essere esteso a | SAP HANA in Azure S192xm<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU |  6 TB |  16 TB | Non più disponibile |
| S384xm può essere esteso a | SAP HANA in Azure S384xxm<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  12 TB |  28 TB | Disponibile in revisione4 francobolli |
| S576m può essere esteso a | SAP HANA in Azure S576xm<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  18,0 TB |  41 TB | Disponibile in revisione4 francobolli|
| S768m può essere esteso a | SAP HANA in Azure S768xm<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  24,0 TB |  56 TB | Disponibile in revisione4 francobolli |

- Core CPU: somma di core CPU senza hyperthreading nella somma dei processori dell'unità server.
- Thread CPU: somma di thread di calcolo forniti da core CPU con hyperthreading nella somma dei processori dell'unità server. Per impostazione predefinita, la maggior parte delle unità sono configurate per l'uso di Hyper-Threading Technology.
- Conformemente ai consigli del fornitore, S768m, S768xm e S960m non sono configurate per l'uso di Hyper-Threading quando eseguono SAP HANA.


Le configurazioni specifiche scelte dipendono dal carico di lavoro, dalle risorse della CPU e dalla memoria desiderata. È possibile che il carico di lavoro OLTP sfrutti gli SKU ottimizzati per il carico di lavoro OLAP. 

La base hardware delle offerte gode della certificazione TDI di SAP Hana, ad eccezione dei progetti oggetto di dimensionamento specifico da parte dei clienti. Gli SKU sono suddivisi in due diverse classi di hardware:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m che sono indicati come "classe di tipo I" degli SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm e S960m, che sono indicati come "classe di tipo II" degli SKU.
- Se sei interessato ad altri SKU S224 che offrono da 4,5 TB a 9 TB con Optane, contatta il team del tuo account Microsoft per ottenere ulteriori informazioni. 


Un intero modulo per istanze Large di HANA non viene allocato esclusivamente per l'uso di un singolo cliente. Ciò vale anche per i rack delle risorse di calcolo e archiviazione connesse tramite un'infrastruttura di rete distribuita in Azure. Analogamente ad Azure, l'infrastruttura di istanze Large di HANA distribuisce diversi &quot;tenant&quot; dei clienti, isolati l'uno dall'altro, nei tre livelli seguenti:

- **Rete**: Isolamento tramite reti virtuali all'interno del timbro HANA Large Instance(Large Instance).
- **Archiviazione:** isolamento tramite macchine virtuali di archiviazione con volumi di archiviazione assegnati e isolare volumi di archiviazione tra tenant.
- **Calcolo:** assegnazione dedicata delle unità server a un singolo tenant. Nessun partizionamento hardware o software delle unità server. Nessuna condivisione di una singola unità server o host tra i tenant. 

Le distribuzioni di unità di istanze Large di HANA tra tenant diversi non risultano visibili l'una all'altra. Le unità di istanze Large di HANA distribuite in tenant diversi non possono comunicare direttamente tra loro a livello del modulo per istanze Large di HANA. Questa comunicazione è possibile solo per le unità di istanze Large di HANA all'interno di un solo tenant.

A un tenant distribuito nel modulo per istanze Large viene assegnata una sola sottoscrizione di Azure ai fini della fatturazione. A livello di rete, tuttavia, il tenant è accessibile dalle reti virtuali di altre sottoscrizioni di Azure nell'ambito della stessa iscrizione ad Azure. Se si esegue la distribuzione con un'altra sottoscrizione di Azure nella stessa area di Azure, si può anche scegliere di richiedere un tenant per istanze Large di HANA separato.

Vi sono differenze significative tra l'esecuzione di SAP HANA nelle istanze Large di HANA e quella nelle VM distribuite in Azure:

- Non esiste alcun livello di virtualizzazione per SAP HANA in Azure (istanze di grandi dimensioni). Si ottengono le prestazioni dell'hardware bare metal sottostante.
- A differenza di Azure, il server di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato a un cliente specifico. Un'unità server, o host, non può essere in alcun modo partizionata a livello di hardware o software. Di conseguenza, un'unità di istanze Large di HANA viene usata come se fosse assegnata per intero a un tenant e con il tenant assegnato al singolo cliente. Un riavvio o un arresto del server non comporta automaticamente la distribuzione del sistema operativo e di SAP HANA in un altro server. Per gli SKU della classe di tipo I, l'unica eccezione è data dal caso in cui vengono riscontrati problemi in un server ed è necessario eseguire la ridistribuzione in un altro server.
- A differenza di Azure, in cui i tipi di processore host vengono selezionati per il miglior rapporto prezzo/prestazioni, per SAP HANA in Azure (istanze Large) vengono scelti i tipi di processore con le prestazioni più elevate della linea di processori Intel E7v3 ed E7v4.

**Passaggi successivi**
- Vedere [ridimensionamento HLI ](hana-sizing.md)
