---
title: 'Flessibilità delle dimensioni della macchina virtuale: istanze di VM riservate di Azure'
description: Informazioni sulle serie di dimensioni a cui si applica uno sconto di prenotazione tramite un'istanza di macchina virtuale riservata.
author: yashesvi
ms.service: virtual-machines
ms.subservice: reserved-instances
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/01/2021
ms.author: yashar
ms.openlocfilehash: 9270b7fb135eddd78a7aeb30cf88af4d704e176e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030707"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate

Quando si acquista un'istanza di macchina virtuale riservata, è possibile scegliere di ottimizzare la flessibilità delle dimensioni dell'istanza o la priorità della capacità. Per altre informazioni sull'impostazione o sulla modifica dell'impostazione di ottimizzazione per le istanze di VM riservate, vedere [modificare l'impostazione di ottimizzazione per le istanze di VM riservate](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Con un'istanza di macchina virtuale riservata ottimizzata per la flessibilità delle dimensioni dell'istanza, la prenotazione acquistata può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di flessibilità delle dimensioni dell'istanza. Se ad esempio si acquista una prenotazione per le dimensioni di una macchina virtuale elencate nella serie DSv2, ad esempio Standard_DS3_v2, lo sconto di prenotazione può essere applicato alle altre dimensioni elencate nello stesso gruppo di flessibilità delle dimensioni dell'istanza:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Lo sconto per le prenotazioni non si applica tuttavia alle dimensioni delle VM elencate in gruppi di flessibilità delle dimensioni dell'istanza diversi, ad esempio gli SKU nella memoria alta della serie DSv2: Standard_DS11_v2, Standard_DS12_v2 e così via.

All'interno del gruppo di flessibilità delle dimensioni dell'istanza, il numero di macchine virtuali a cui si applica lo sconto della prenotazione dipende dalle dimensioni della macchina virtuale selezionate quando si acquista una prenotazione. Dipende anche dalle dimensioni delle macchine virtuali in esecuzione. La colonna ratio confronta il footprint relativo per ogni dimensione di macchina virtuale in tale gruppo di flessibilità delle dimensioni dell'istanza. Usare il valore di rapporto per calcolare come viene applicato lo sconto per la prenotazione alle macchine virtuali in esecuzione.

## <a name="examples"></a>Esempio

Gli esempi seguenti usano le dimensioni e i rapporti nella tabella della serie DSv2.

Si acquista un'istanza di macchina virtuale riservata con le dimensioni Standard_DS4_v2, con il rapporto (o footprint relativo) pari a 8 rispetto alle altre dimensioni nella serie.

- Scenario 1: eseguire otto macchine virtuali con dimensioni Standard_DS1_v2 con un rapporto pari a 1. Lo sconto per la prenotazione si applica a tutte e otto le macchine virtuali.
- Scenario 2: eseguire due macchine virtuali con dimensioni Standard_DS2_v2 con un rapporto pari a 2 ognuna. Eseguire anche una macchina virtuale con dimensioni Standard_DS3_v2 con un rapporto pari a 4. Il footprint complessivo è 2+2+4=8. Pertanto, lo sconto per la prenotazione si applica a tutte e tre le macchine virtuali.
- Scenario 3: eseguire una macchina virtuale Standard_DS5_v2 con un rapporto di 16. Lo sconto per la prenotazione si applica a metà del costo di calcolo di tale macchina virtuale.

Le sezioni seguenti mostrano quali dimensioni sono incluse nello stesso gruppo di serie di dimensioni quando si acquista un'istanza di macchina virtuale riservata ottimizzata per la flessibilità di dimensioni dell'istanza.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Rapporto di flessibilità delle dimensioni dell'istanza per le macchine virtuali 

Il volume CSV seguente include i gruppi di flessibilità delle dimensioni dell'istanza, ArmSkuName e i rapporti.  

[Rapporti di flessibilità delle dimensioni dell'istanza](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Azure mantiene il collegamento e lo schema aggiornati in modo che sia possibile usare il file a livello di codice.

## <a name="view-vm-size-recommendations"></a>Visualizza le indicazioni sulle dimensioni delle VM

Azure Mostra le indicazioni sulle dimensioni delle VM nell'esperienza di acquisto. Per visualizzare le indicazioni sulle dimensioni minime, selezionare **Raggruppa per dimensioni minime**.

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="Screenshot che mostra le quantità consigliate." lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [che cosa sono le prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md).
