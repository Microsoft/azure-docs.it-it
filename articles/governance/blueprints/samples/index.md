---
title: Indice degli esempi di progetto
description: Indice di esempi di conformità e standard per la distribuzione di ambienti, criteri ed elementi di base di Cloud Adoptions Framework con Azure Blueprints.
ms.date: 02/08/2020
ms.topic: sample
ms.openlocfilehash: c64bc02c1dea4e2c53d4ff195add2ac7fa57874f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370955"
---
# <a name="azure-blueprints-samples"></a>Esempi di Azure Blueprints

La tabella seguente include collegamenti a esempi per Azure Blueprints. Ogni esempio è di qualità produzione ed è pronto per la distribuzione immediata, rispettando i vari requisiti di conformità.

## <a name="standards-based-blueprint-samples"></a>Progetti di esempio basati su standard

| Esempio | Descrizione |
|---------|---------|
| [Australian Government ISM PROTECTED](./ism-protected/index.md) | Fornisce misure di tutela per la conformità ad Australian Government ISM PROTECTED. |
| [Azure Security Benchmark](./azure-security-benchmark.md) | Fornisce misure di tutela per la conformità ad [Azure Security Benchmark](../../../security/benchmarks/overview.md). |
| [Azure Security benchmark Foundation](./azure-security-benchmark-foundation/index.md) | Distribuisce e configura Azure Security benchmark Foundation. |
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Fornisce misure di tutela per la conformità a Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1-1-0.md)| Fornisce un set di criteri per garantire la conformità alle raccomandazioni di CIS Microsoft Azure Foundations Benchmark. |
| [DoD Impact Level 4](./dod-impact-level-4/index.md) | Fornisce un set di criteri per garantire la conformità a DoD Impact Level 4. |
| [DoD Impact Level 5](./dod-impact-level-5/index.md) | Fornisce un set di criteri per garantire la conformità a DoD Impact Level 5. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Fornisce un set di criteri per garantire la conformità a FedRAMP Moderate. |
| [FedRAMP High](./fedramp-h/index.md) | Fornisce un set di criteri per garantire la conformità a FedRAMP High. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | Fornisce un set di criteri per garantire la conformità a HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Fornisce Guardrails per la conformità con l'IRS 1075.|
| [ISO 27001](./iso-27001-2013.md) | Fornisce misure di tutela per la conformità a ISO 27001. |
| [Servizi condivisi ISO 27001](./iso27001-shared/index.md) | Offre un set di modelli di infrastrutture conformi e limiti di criteri utili per ottenere l'attestazione ISO 27001. |
| [Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001](./iso27001-ase-sql-workload/index.md) | Fornisce una maggiore infrastruttura per l'esempio di progetto [dei servizi condivisi ISO 27001](./iso27001-shared/index.md) . |
| [Supporti](./media/index.md) | Fornisce un set di criteri per garantire la conformità ai controlli MPAA per Media. |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | Fornisce Guardrails per la conformità con NIST SP 800-53 R4. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | Fornisce Guardrails per la conformità con NIST SP 800-171 R2. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Fornisce un set di criteri per agevolare la conformità allo standard PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Supporta la conformità a SWIFT CSP-CSCF V2020. |
| [Governance di UK OFFICIAL e UK NHS](./ukofficial/index.md) | Offre un set di modelli di infrastrutture conformi e misure di tutela basate su criteri utili per ottenere le attestazioni UK OFFICIAL e UK NHS. |
| [Definizione della base di Cloud Adoption Framework](./caf-foundation/index.md) | Offre un set di controlli per facilitare la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Zona di destinazione per la migrazione di Cloud Adoption Framework](./caf-migrate-landing-zone/index.md) | Offre un set di controlli per facilitare la configurazione per la migrazione del primo carico di lavoro e la gestione dell'ambiente cloud in linea con [Microsoft Cloud Adoption Framework (CAF) per Azure](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Strategia degli esempi

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="Diagramma della posizione degli esempi di progetto ai fini del confronto tra complessità dell'architettura e requisiti di conformità." border="false":::
   Illustra un sistema di coordinate in cui la complessità dell'architettura corrisponde all'asse X e i requisiti di conformità corrispondono all'asse Y.  Poiché i requisiti di conformità e complessità dell'architettura aumentano, adottare esempi di progetto standard dal portale designato nell'area E.  Per i clienti che iniziano a usare Azure usare il Framework di adozione cloud (C A F) basato su base e i progetti di zona di destinazione definiti dall'area A e B.  Lo spazio rimanente è attribuito ai progetti personalizzati creati dai clienti come partner per le aree C, D e F. :::image-end:::

I progetti per la definizione della base di Cloud Adoption Framework e la zona di destinazione per la migrazione di Cloud Adoption Framework presuppongono che il cliente prepari una sottoscrizione singola pulita in cui eseguire la migrazione di carichi di lavoro e asset locali ad Azure
(area A e B nella figura).  

Oltre all'opportunità di eseguire l'iterazione dei progetti di esempio e cercare criteri di personalizzazione applicati da un cliente, è anche possibile gestire progetti di settori specifici, come servizi finanziari e e-commerce (parte superiore dell'area B). Analogamente, è stata prevista la creazione di progetti che tengono conto di particolari esigenze architetturali, come più sottoscrizioni, disponibilità elevata, risorse tra aree e clienti che implementano controlli su sottoscrizioni e risorse esistenti (area C e D).

Sono disponibili progetti di esempio per la gestione di scenari cliente con requisiti di conformità elevati e architettura particolarmente complessa (area E nella figura). L'area F nella figura è quella che viene trattata da clienti e partner che applicano i progetti di esempio e li personalizzano per le proprie esigenze specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).
