---
title: includere il file
description: includere file
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 033ec0f4011cae657fc464849aac5c5d9a850907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83343321"
---
Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente.  Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità.  I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

L'utilizzo di dimensioni con piano Isolato garantisce che la macchina virtuale sia l'unica in esecuzione nella specifica istanza del server.  Le offerte di macchine virtuali con piano Isolato correnti includono:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2<sup>*</sup>
* Standard_D15_v2<sup>*</sup>
* Standard_F72s_v2

<sup>*</sup>La garanzia di isolamento sarà ritirata entro il 15 maggio 2020

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Ritiro dell'isolamento D15_v2/DS15_v2 il 15 maggio 2020
**Aggiornamento il 10 febbraio 2020: la sequenza temporale di ritiro "isolamento" è stata estesa al 15 maggio 2020 "**

Host dedicato di Azure è ora disponibile a livello generale, che consente di eseguire le macchine virtuali Linux e Windows dell'organizzazione su server fisici a tenant singolo. Si prevede di sostituire completamente le VM di Azure isolate con l'host dedicato di Azure. Dopo il **15 maggio 2020** le macchine virtuali di Azure D15_v2/DS15_v2 non saranno più isolate dall'hardware.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica?
Dopo il 15 maggio 2020, non sarà più disponibile una garanzia di isolamento per le macchine virtuali di Azure D15_v2/DS15_v2. 

## <a name="what-actions-should-i-take"></a>Quali azioni è necessario eseguire?
Se l'isolamento hardware non è necessario per l'utente, non è necessario eseguire alcuna azione. 

Se è necessario l'isolamento, prima del 15 maggio 2020, è necessario eseguire una delle operazioni seguenti:

• [Eseguire la migrazione](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) del carico di lavoro all'host dedicato di Azure.

• [Richiedere l'accesso](https://aka.ms/D15iRequestAccess) a una D15i_v2 e DS15i_v2 VM di Azure per ottenere le stesse prestazioni del prezzo. Questa opzione è disponibile solo per gli scenari con pagamento in base al consumo e per istanze riservate di un anno.    

• [Eseguire la migrazione](https://azure.microsoft.com/blog/resize-virtual-machines/) del carico di lavoro a un'altra macchina virtuale isolata di Azure. 

Per informazioni dettagliate, vedere di seguito:

## <a name="timeline"></a>Sequenza temporale
| Data | Action | 
| --- | --- |
| 18 novembre 2019 | Disponibilità di D/DS15i_v2 (PAYG, 1 anno RI) |
| 14 maggio 2020  | Ultimo giorno per l'acquisto di D/DS15i_v2 1 anno RI | 
| 15 maggio 2020   | Garanzia di isolamento D/DS15_v2 rimossa | 
| 15 maggio 2021  | Ritira D/DS15i_v2 (tutti i clienti ad eccezione di chi ha acquistato il RI 3 anni di D/DS15_v2 prima del 18 novembre 2019)| 
| 17 novembre 2022  | Ritira D/DS15i_v2 quando viene eseguito il RIs di 3 anni (per i clienti che hanno acquistato il RI di 3 anni di D/DS15_v2 prima del 18 novembre 2019) | 

## <a name="faq"></a>Domande frequenti
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>D: le dimensioni D/DS15_v2 verranno ritirate?
**R**: No, la funzionalità di "isolamento" verrà ritirata. Se non è necessario l'isolamento, non è necessario eseguire alcuna azione.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>D: le dimensioni D/DS15i_v2 verranno ritirate?
**R**: Sì, le dimensioni sono disponibili solo fino al 15 maggio, 2021. Per i clienti che hanno acquistato RIs di 3 anni su D/DS15_v2 prima del 18 novembre, 2019 avrà accesso a D/DS15i_v2 fino al 17 novembre 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>D: perché non vengono visualizzati i nuovi formati D/DS15i_v2 nel portale?
**R**: se si è un cliente d/DS15_v2 corrente e si vogliono usare le nuove dimensioni d/DS15i_v2, compilare questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>D: perché non viene visualizzato alcun contingente per le nuove dimensioni D/DS15i_v2?
**R**: se si è un cliente d/DS15_v2 corrente e si vogliono usare le nuove dimensioni d/DS15i_v2, compilare questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>D: quando le altre dimensioni isolate verranno ritirate?
**R**: si forniranno promemoria 12 mesi prima della rimozione ufficiale delle dimensioni.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>D: si verifica un tempo di inattività quando la macchina virtuale si trova in un hardware non isolato?
**R**: se non è necessario l'isolamento, non è necessario eseguire alcuna azione e non si noterà alcun tempo di inattività.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>D: esistono modifiche ai costi per il passaggio a una macchina virtuale non isolata?
**R**: No 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>D: è già stata acquistata un'istanza riservata di 1 o 3 anni per D15_v2 o Ds15_v2. Come verrà applicato lo sconto all'utilizzo della macchina virtuale?
**R**: il servizio RIS acquistato prima del 18 novembre 2019 estenderà automaticamente la copertura alla nuova serie di VM isolate. 

| RI |  Flessibilità delle dimensioni dell'istanza | Idoneità ai vantaggi |   
| --- | --- | --- |
|   D15_v2  |   Off     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   On  |   La serie D15_v2 e la D15i_v2 riceveranno tutti i vantaggi del RI. |    
|   D14_v2  |   On  |   La serie D15_v2 e la D15i_v2 riceveranno tutti i vantaggi del RI. |    
 
Analogamente per la serie Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>D: Desidero acquistare istanze riservate aggiuntive per dv2. Qual è l'opzione migliore?
**R**: tutti i servizi di installazione remota acquistati dopo il 18 novembre 2019 hanno il comportamento seguente. 

| RI |  Flessibilità delle dimensioni dell'istanza | Idoneità ai vantaggi |   
| --- | --- | --- |
| D15_v2 |  Off |   Solo D15_v2  
| D15_v2 |  On |    La serie di D15_v2 riceverà il vantaggio di RI. La nuova D15i_v2 non sarà idonea per il vantaggio RI da questo tipo di RI. | 
| D15i_v2 |     Off | Solo D15i_v2 |  
| D15i_v2 |     On  | Solo D15i_v2 | 
 
Non è possibile usare la flessibilità delle dimensioni dell'istanza per applicare altre dimensioni, ad esempio D2_v2, D4_v2 o D15_v2. Analogamente, per la serie Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>D: è possibile acquistare un nuovo RI di 3 anni per D15i_v2 e DS15i_v2?
**R**: Sfortunatamente no, per un nuovo acquisto è disponibile solo un anno.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>D: è possibile spostare l'istanza di riserva D15_v2/DS15_v2 esistente in un'istanza riservata di dimensioni isolate?
**R**: questa azione non è necessaria perché il vantaggio verrà applicato alle dimensioni isolate e non isolate. Tuttavia, Azure supporterà la modifica delle istanze riservate D15_v2/DS15_v2 esistenti in D15i_v2/DS15i_v2. Per tutte le altre istanze riservate dv2/Dsv2, usare l'istanza riservata esistente o acquistare nuove istanze riservate per le dimensioni isolate.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>D: sono un cliente di Azure Service Fabric che si basa sui livelli di durabilità Silver o Gold. Questa modifica ha effetto?
**R**: No. Le garanzie fornite dai livelli di [durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) di Service Fabric continueranno a funzionare anche dopo questa modifica. Se è necessario un isolamento hardware fisico per altri motivi, potrebbe essere comunque necessario eseguire una delle azioni descritte in precedenza. 
