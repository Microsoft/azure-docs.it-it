---
title: Disponibilità di SAP HANA tra aree di Azure | Microsoft Docs
description: Panoramica delle considerazioni relative alla disponibilità durante l'esecuzione di SAP HANA in macchine virtuali di Azure in più aree di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "70078859"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilità di SAP HANA tra aree di Azure

Questo articolo descrive gli scenari relativi alla disponibilità di SAP HANA in diverse aree di Azure. A causa della distanza tra le aree di Azure, la configurazione della disponibilità di SAP HANA in più aree di Azure richiede considerazioni speciali.

## <a name="why-deploy-across-multiple-azure-regions"></a>Perché eseguire la distribuzione in più aree di Azure

Le aree di Azure sono spesso separate da grandi distanze. In base all'area geopolitica, la distanza tra le aree di Azure potrebbe essere di centinaia di chilometri o addirittura di alcune migliaia di chilometri, ad esempio negli Stati Uniti. A causa della distanza, è possibile che il traffico di rete tra asset distribuiti in due aree diverse di Azure presenti una latenza significativa a livello di round trip di rete. La latenza è sufficientemente elevata da escludere lo scambio sincrono di dati tra due istanze di SAP HANA in carichi di lavoro SAP tipici. 

D'altro canto, le organizzazioni hanno spesso un requisito di distanza tra la posizione del data center primario e quella di un data center secondario. Un requisito di distanza consente di fornire disponibilità se si verifica un disastro naturale in una località geografica più estesa, ad esempio gli uragani che hanno colpito i Caraibi e la Florida nei mesi di settembre e ottobre 2017. L'organizzazione potrebbe avere almeno un requisito di distanza minima. Per la maggior parte dei clienti di Azure, una definizione della distanza minima richiede la progettazione per la disponibilità nelle [aree di Azure](https://azure.microsoft.com/regions/). Poiché la distanza tra due aree di Azure è troppo elevata per l'uso della modalità di replica sincrona di HANA, è possibile che i requisiti di RTO e RPO impongano la distribuzione di configurazioni di disponibilità in un'area e quindi l'aggiunta di altre distribuzioni in una seconda area.

Un altro aspetto da considerare in questo scenario sono il failover e il reindirizzamento del client. Si presuppone che il failover tra le istanze di SAP HANA in due aree di Azure diverse sia sempre un failover manuale. Poiché la modalità di replica della replica di sistema di SAP HANA è impostata su asincrona, i dati di cui è stato eseguito il commit nell'istanza primaria di HANA potrebbero non essere ancora disponibili nell'istanza secondaria di HANA. Non è quindi possibile eseguire il failover automatico per le configurazioni in cui la replica è asincrona. Anche con il failover controllato manualmente, ad esempio in un esercizio relativo al failover, è necessario assicurarsi che tutti i dati di cui è stato eseguito il commit sul lato primario siano disponibili nell'istanza secondaria prima di passare manualmente all'altra area di Azure.
 
Rete virtuale di Azure usa un intervallo di indirizzi IP diverso. Gli indirizzi IP vengono distribuiti nella seconda area di Azure. In tal caso, è necessario modificare la configurazione del client SAP HANA o preferibilmente creare i passaggi per modificare la risoluzione dei nomi, in modo che i client vengano quindi reindirizzati al nuovo indirizzo IP del server del sito secondario. Per altre informazioni, vedere l'articolo [Client Connection Recovery after Takeover](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) (Ripristino della connessione client dopo l'acquisizione della proprietà) di SAP.   

## <a name="simple-availability-between-two-azure-regions"></a>Semplice disponibilità tra due aree di Azure

È possibile scegliere di non definire alcuna configurazione di disponibilità in una singola area, ma è ugualmente necessario che il carico di lavoro sia disponibile in caso di emergenza. Casi tipici per questi scenari sono in genere rappresentati da sistemi non di produzione. Anche se l'inattività del sistema per mezza giornata o addirittura per una giornata intera è sostenibile, non è possibile consentire che il sistema sia non disponibile per 48 ore e oltre. Per ridurre i costi dell'installazione, eseguire un altro sistema ancora meno importante nella macchina virtuale. L'altro sistema funge da destinazione. È anche possibile specificare dimensioni minori per la macchina virtuale nell'area secondaria e scegliere di non precaricare i dati. Poiché il failover è manuale e prevede un numero molto più elevato di passaggi per il failover dello stack completo dell'applicazione, il tempo aggiuntivo necessario per arrestare la macchina virtuale, ridimensionarla e quindi riavviarla è accettabile.

In uno scenario in cui la destinazione del ripristino di emergenza è condivisa con un sistema di controllo qualità in una macchina virtuale, è necessario tenere conto di queste considerazioni:

- Per questo scenario sono disponibili due [modalità operative](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) con delta_datashipping e logreplay
- Le due modalità hanno requisiti di memoria diversi senza precaricamento dei dati
- Rispetto a logreplay, delta_datashipping può richiedere una quantità di memoria decisamente inferiore senza l'opzione di precaricamento. Vedere il capitolo 4.3 del documento SAP [How To Perform System Replication for SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) (Come eseguire la replica di sistema per SAP HANA)
- Il requisito di memoria della modalità operativa logreplay senza precaricamento non è deterministico e dipende dalle strutture columnstore caricate. In casi estremi può essere necessario il 50% della memoria dell'istanza primaria. La memoria per la modalità operativa logreplay è indipendente dal fatto che si sia scelto di avere il set precaricato di dati o meno.


![Diagramma di due macchine virtuali in due aree](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> In questa configurazione non è possibile specificare un RPO=0, perché la modalità di replica di sistema di HANA è asincrona. Se è necessario specificare un RPO=0, questa configurazione non è consigliabile.

Una piccola modifica che si può apportare alla configurazione potrebbe consistere nel configurare il precaricamento dei dati. Considerati tuttavia la natura manuale del failover e il fatto che i livelli dell'applicazione devono passare anche alla seconda area, il precaricamento dei dati potrebbe non essere consigliabile. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinare la disponibilità in un'area e tra diverse aree 

Una combinazione di disponibilità entro un'area e tra diverse aree può essere determinata dai fattori seguenti:

- Requisito di RPO=0 entro un'area di Azure.
- Impossibilità di accettare che le operazioni globali dell'organizzazione siano interessate da una grave catastrofe naturale che colpisce un'area più ampia, come ad esempio alcuni uragani che hanno colpito i Caraibi negli anni scorsi.
- Normative che richiedono distanze tra i siti primario e secondario evidentemente superiori rispetto a quanto offerto dalle zone di disponibilità di Azure.

In questi casi è possibile configurare quella che SAP definisce una [configurazione di replica di sistema multilivello di SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) usando la replica di sistema HANA. L'architettura avrà un aspetto analogo al seguente:

![Diagramma di tre macchine virtuali in due aree](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP ha introdotto la [replica di sistema con più destinazioni](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) a partire da HANA 2.0 SPS3. La replica di sistema con più destinazioni offre alcuni vantaggi negli scenari di aggiornamento. Ad esempio, il sito di ripristino di emergenza (area 2) non è interessato quando il sito secondario a disponibilità elevata è inattivo per manutenzione o aggiornamenti. Altre informazioni sulla replica di sistema con più destinazioni HANA sono disponibili [qui](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Una possibile architettura con replica con più destinazioni può essere simile alla seguente:

![Diagramma di tre macchine virtuali su due aree con più destinazioni](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Se l'organizzazione prevede requisiti di conformità per disponibilità elevata nella seconda area di Azure (ripristino di emergenza), l'architettura sarà simile alla seguente:

![Diagramma di tre macchine virtuali su due aree con più destinazioni](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Con la modalità operativa logreplay, questa configurazione offre un RPO=0, con un basso RTO, all'interno dell'area primaria. La configurazione fornisce anche un RPO accettabile se si verifica uno spostamento nella seconda area. I tempi di RTO nella seconda area dipendono dal fatto che si precarichino o meno i dati. Molti clienti usano la macchina virtuale nell'area secondaria per eseguire un sistema di test. In tale caso d'uso i dati non possono essere precaricati.

> [!IMPORTANT]
> Le modalità operative tra i diversi livelli devono essere omogenee. **Non** è possibile usare la modalità operativa logreply tra il livello 1 e il livello 2 e delta_datashipping per fornire il livello 3. È consentito scegliere una sola delle due modalità operative che deve essere coerente per tutti i livelli. Poiché la modalità delta_datashipping non è adatta per ottenere un RPO=0, l'unica modalità operativa ragionevole per questa configurazione multilivello rimane logreplay. Per informazioni dettagliate sulle modalità operative e alcune restrizioni, vedere l'articolo di SAP [Operation Modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Modalità operative per la replica di sistema di SAP HANA). 

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni dettagliate sull'impostazione di queste configurazioni in Azure, vedere:

- [Configurare la replica di sistema SAP HANA nelle macchine virtuali di Azure](sap-hana-high-availability.md)
- [High availability for SAP HANA by using system replication (Disponibilità elevata per SAP HANA tramite la replica di sistema)](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
