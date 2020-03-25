---
title: Modalità di applicazione degli sconti per le prenotazioni in Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come applicare gli sconti per le prenotazioni in Azure SQL Data Warehouse per risparmiare denaro.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b08f11799c1471af22138fefcd57ed1eb951a1a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199382"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Modalità di applicazione degli sconti per le prenotazioni in Azure SQL Data Warehouse

Dopo aver acquistato capacità riservata di Azure SQL Data Warehouse, lo sconto per la prenotazione viene applicato automaticamente ai data warehouse presenti in tale area. Lo sconto per la prenotazione si applica ai dati di utilizzo generati dal contatore di DWU a elevato utilizzo di calcolo di SQL Data Warehouse. I costi relativi alle risorse di archiviazione e di rete vengono addebitati in base alle tariffe con pagamento in base al consumo.

## <a name="reservation-discount-application"></a>Applicazione dello sconto per la prenotazione

Lo sconto per la capacità riservata di SQL Data Warehouse viene applicato su base oraria ai data warehouse in esecuzione. Se non è presente un warehouse distribuito per un'ora, la capacità riservata risulta sprecata per tale ora e non viene mantenuta.

Dopo l'acquisto la prenotazione acquistata viene confrontata con i dati di utilizzo di SQL Data Warehouse generati dai warehouse in esecuzione in qualsiasi momento. Se si arrestano alcuni warehouse, gli sconti per le prenotazioni vengono applicati automaticamente a qualsiasi altro warehouse corrispondente.

Per i warehouse che non vengono eseguiti per un'intera ora, la prenotazione viene applicata automaticamente ad altre istanze corrispondenti in tale ora.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano la modalità di applicazione dello sconto per la capacità riservata di SQL Data Warehouse a seconda delle distribuzioni.

- **Esempio 1**: si acquistano 5 unità di capacità riservata da 100 DWU a elevato utilizzo di calcolo. Si esegue un'istanza di SQL Data Warehouse DW1500c per un'ora. In questo caso vengono generati dati di utilizzo per 15 unità di utilizzo da 100 DWU a elevato utilizzo di calcolo. Lo sconto per la prenotazione si applica alle 5 unità usate. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 10 unità di utilizzo da 100 DWU a elevato utilizzo di calcolo usate. In altri termini, è possibile la copertura parziale per più prenotazioni.

- **Esempio 2:** si acquistano 5 unità di capacità riservata da 100 DWU a elevato utilizzo di calcolo. Si eseguono due istanze di SQL Data Warehouse DW100c per un'ora. In questo caso vengono generati due eventi di utilizzo per 1 unità di utilizzo da 100 DWU a elevato utilizzo di calcolo. Gli sconti per la capacità riservata vengono applicati a entrambi gli eventi di utilizzo. Le rimanenti 3 unità di capacità riservata da 100 DWU a elevato utilizzo di calcolo vengono sprecate e non vengono mantenute per eventuali usi futuri. In altri termini, una singola prenotazione può corrispondere a più istanze di SQL Data Warehouse.

- **Esempio 3**: Si acquista 1 unità di capacità riservata da 100 DWU a elevato utilizzo di calcolo. Si eseguono due istanze di SQL Data Warehouse DW100c. Ognuna viene eseguita per 30 minuti. In questo caso gli sconti per la capacità riservata vengono applicati a entrambi gli eventi di utilizzo. Nessun tipo di utilizzo viene addebitato in base alle tariffe con pagamento in base al consumo.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

- In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
- [Visualizzare le transazioni di prenotazione](view-reservations.md)
- [Ottenere le transazioni di prenotazione e i dati di utilizzo tramite l'API](reservation-apis.md)
- [Gestire le prenotazioni](manage-reserved-vm-instance.md)
