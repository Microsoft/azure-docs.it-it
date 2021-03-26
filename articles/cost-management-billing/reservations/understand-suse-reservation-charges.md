---
title: Sconto del piano software - Azure | Microsoft Docs
description: Informazioni su come vengono applicati gli sconti del piano software al software delle macchine virtuali.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: banders
ms.openlocfilehash: 8bf53715b7f19c44d9114150e617f903cd05a51e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566316"
---
# <a name="azure-software-plan-discount"></a>Sconto del piano software di Azure

I piani software di Azure per SUSE e RedHat sono prenotazioni applicabili alle VM distribuite. Lo sconto del piano software viene applicato all'utilizzo del software delle VM distribuite che corrispondono alla prenotazione.

Quando si arresta una VM, lo sconto viene applicato automaticamente a un'altra VM corrispondente, se disponibile. Un piano software copre il costo di esecuzione del software in una VM. Gli altri costi, ad esempio per il calcolo, l'elaborazione e la rete, vengono addebitati separatamente.

Per acquistare il piano appropriato, è necessario avere i dati sull'utilizzo delle VM e sul numero di vCPU al loro interno. Usare le sezioni seguenti per identificare il piano da acquistare in base ai dati di utilizzo.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Esaminare l'utilizzo delle VM RedHat prima dell'acquisto

Ottenere il nome del prodotto dai dati di utilizzo e acquistare il piano RedHat con lo stesso tipo e le stesse dimensioni.

Se ad esempio l'utilizzo è per il prodotto con **licenza Red Hat Enterprise Linux - VM con 1-4 vCPU**, è necessario acquistare **Red Hat Enterprise Linux** per **VM con 1-4 vCPU**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Esaminare l'utilizzo delle VM SUSE prima dell'acquisto

Ottenere il nome del prodotto dai dati di utilizzo e acquistare il piano SUSE con lo stesso tipo e le stesse dimensioni.

Se ad esempio l'utilizzo è per il prodotto **SUSE Linux Enterprise Server Priority - VM con 2-4 vCPU**, è necessario acquistare **SUSE Linux Enterprise Server Priority** per **VM con 2-4 vCPU**.

![Esempio di selezione del prodotto da acquistare](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Lo sconto si applica alle diverse dimensioni di VM per i piani SUSE

Come le istanze di macchina virtuale riservate, gli acquisti di piani SUSE offrono flessibilità in termini di dimensioni dell'istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una VM con un numero di vCPU diverso. Lo sconto si applica a diverse dimensioni VM all'interno del piano software.

L'importo dello sconto dipende dal rapporto riportato nelle tabelle seguenti. Il rapporto mette a confronto il footprint relativo per ogni contatore di quel gruppo. Il rapporto varia a seconda delle vCPU delle VM. Usare il valore del rapporto per calcolare a quante istanze di macchina virtuale si applica lo sconto relativo al piano SUSE Linux.

Ad esempio, se si acquista un piano per SUSE Linux Enterprise Server per HPC - Priority per una VM con 3 o 4 vCPU, il rapporto per tale prenotazione è 2. Lo sconto copre il costo del software SUSE per:

- 2 VM distribuite con 1 o 2 vCPU,
- 1 VM distribuita con 3 o 4 vCPU,
- oppure 0,77 o circa il 77% di una VM con 5 o più vCPU.

Il rapporto per 5 o più vCPU è 2,6. Quindi una prenotazione per SUSE con una VM con 5 o più vCPU copre una sola parte del costo del software, che equivale a circa il 77%.

Le tabelle seguenti mostrano i piani software per i quali è possibile acquistare una prenotazione, i relativi contatori di utilizzo e i rapporti per ciascuno.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server per HPC - Priority

|VM SUSE | ID contatore| Rapporto| Dimensione VM di esempio|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server per HPC-Priority 1-2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server per HPC-Priority 3-4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server per HPC-Priority 5 + vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server per HPC - Standard

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server per HPC-Standard 1-2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server per HPC-Standard 3-4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SUSE Linux Enterprise Server per HPC-Standard 5 + vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SUSE Linux Enterprise Server per SAP standard

In precedenza, SUSE Linux Enterprise Server per SAP standard era denominato SUSE Linux Enterprise Server per SAP-Priority.

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server per SAP standard 1-2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server per SAP standard 3-4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server per SAP Standard 5 + vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server - Standard

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- |--- |
|SUSE Linux Enterprise Server-Standard 1-2 Core vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SUSE Linux Enterprise Server-Standard 3-4 core vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SUSE Linux Enterprise Server-Standard 5 + vCPU |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
- [Pagare in anticipo per i piani software SUSE con le prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)