---
title: 'Progettazione di una soluzione ibrida di gestione delle identità: requisiti di gestione dei contenuti in Azure | Microsoft Docs'
description: Fornisce importanti informazioni su come determinare i requisiti di gestione dei contenuti dell'azienda. In genere, quando un utente usa un dispositivo personale, si avvale anche di più credenziali diverse, in base all'applicazione a cui deve accedere. È importante quindi distinguere i contenuti creati usando credenziali personali rispetto a quelli realizzati ricorrendo a credenziali aziendali. La soluzione di identità adottata deve essere in grado di interagire con i servizi cloud, in modo da fornire un'esperienza trasparente per l'utente finale, garantirne la privacy e migliorare la protezione dalla perdita di dati.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408533"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinare i requisiti di gestione dei contenuti per una soluzione di identità ibrida
Comprendere i requisiti aziendali relativi alla gestione dei contenuti può incidere direttamente sulla decisione in merito alla soluzione di identità ibrida da usare. Con la proliferazione delle tipologie di dispositivi e la possibilità per gli utenti di usare dispositivi personali anche nelle attività lavorative (approccio[BYOD](/mem/intune/fundamentals/byod-technology-decisions)), è sempre più forte l'esigenza per le aziende di proteggere i dati senza compromettere la privacy degli utenti. In genere, quando un utente usa un dispositivo personale, si avvale anche di più credenziali diverse, in base all'applicazione a cui deve accedere. È importante quindi distinguere i contenuti creati usando credenziali personali rispetto a quelli realizzati ricorrendo a credenziali aziendali. La soluzione di identità adottata deve essere in grado di interagire con i servizi cloud, in modo da fornire un'esperienza trasparente per l'utente finale, garantirne la privacy e migliorare la protezione dalla perdita di dati. 

La soluzione di identità deve essere inoltre sottoposta a vari controlli tecnici per permettere la gestione dei contenuti, come illustrato nella figura seguente:

![controlli di sicurezza](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Controlli di protezione che interessano il sistema di gestione delle identità**

In generale, i requisiti di gestione dei contenuti interessano il sistema di gestione delle identità nelle aree seguenti:

* Privacy: identificare l'utente proprietario di una risorsa e applicare i controlli appropriati per garantirne l'integrità.
* Classificazione dei dati: identificare l'utente o il gruppo e il livello di accesso a un oggetto in base a una classificazione prestabilita. 
* Protezione contro la perdita di dati: per poter convalidare l'identità degli utenti, il sistema di gestione delle identità deve poter interagire con i controlli di protezione adottati per evitare perdite di dati. Questo aspetto è importante anche per eventuali esigenze di auditing.

> [!NOTE]
> Leggere l'articolo sulla [classificazione dei dati per la conformità al cloud](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) per maggiori informazioni sulle linee guida e le procedure consigliate per la classificazione dei dati.
> 
> 

Quando si pianifica una soluzione ibrida di gestione delle identità, assicurarsi che venga fornita una risposta alle domande seguenti, in base ai requisiti aziendali:

* Sono presenti in azienda controlli di sicurezza per garantire la riservatezza dei dati?
  * In caso affermativo, sarà possibile integrarli con la soluzione di identità ibrida che si intende adottare?
* È presente in azienda un sistema di classificazione dei dati?
  * In caso affermativo, sarà possibile integrarlo con la soluzione di identità ibrida che si intende adottare?
* È presente in azienda un sistema di protezione contro la perdita di dati? 
  * In caso affermativo, sarà possibile integrarlo con la soluzione di identità ibrida che si intende adottare?
* L'azienda desidera controllare l'accesso alle risorse?
  * In caso affermativo, che tipo di risorse?
  * Quale livello di informazioni è necessario?
  * Dove deve trovarsi il log di controllo, in locale o nel cloud?
* L'azienda ha necessità di crittografare i messaggi e-mail che contengono dati riservati, come numeri di previdenza sociale, numeri di carta di credito e così via?
* L'azienda desidera crittografare tutti i documenti e i contenuti condivisi con partner commerciali esterni?
* L'azienda intende applicare criteri specifici su determinati tipi di e-mail ("non rispondere a tutti", "non inoltrare" e così via)?

> [!NOTE]
> Accertarsi di prendere nota di ogni risposta e comprendere la logica che ne sta alla base. [definizione della strategia di protezione dei dati](plan-hybrid-identity-design-considerations-data-protection-strategy.md) esamina le opzioni disponibili con i relativi vantaggi e svantaggi.  Una volta fornite le risposte a queste domande, sarà possibile selezionare l'opzione più adatta in base alle esigenze aziendali.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
[Determinare i requisiti di controllo di accesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](plan-hybrid-identity-design-considerations-overview.md)