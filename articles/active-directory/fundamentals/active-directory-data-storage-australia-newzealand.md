---
title: Archiviazione dati del cliente per clienti australiani e neozelandesi-Azure AD
description: Informazioni su dove Azure Active Directory archivia i dati relativi ai clienti per i clienti australiani e neozelandesi.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836972"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Archiviazione dati del cliente per clienti australiani e neozelandesi in Azure Active Directory 

Azure Active Directory (Azure AD) archivia i dati dei clienti in una posizione geografica in base al paese specificato al momento dell'iscrizione a un servizio online Microsoft. I Microsoft Online Services includono Microsoft 365 e Azure. 

Per informazioni sulla posizione in cui si trovano i dati Azure AD e altri servizi Microsoft, vedere la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) del Centro protezione Microsoft.

Dal 26 febbraio 2020, Microsoft ha iniziato a archiviare i dati dei clienti di Azure AD per i nuovi tenant con un indirizzo di fatturazione australiano o neozelandese all'interno dei data center australiani. Tra il 1 ° maggio 2020 e il 31 marzo 2021, Microsoft eseguirà la migrazione dei tenant esistenti che dispongono di un indirizzo di fatturazione australiano o neozelandese per i Data Center australiani senza richiedere alcuna azione da parte del cliente. Il processo di migrazione non implica alcun tempo di inattività per i clienti e non influirà sulle funzionalità di un tenant durante la migrazione.

Inoltre, alcune funzionalità di Azure AD non supportano ancora l'archiviazione dei dati dei clienti in Australia. Per informazioni sulle funzionalità specifiche, visitare la [mappa dei dati Azure ad](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9). Ad esempio, Microsoft Azure AD Multi-Factor Authentication archivia i dati dei clienti negli Stati Uniti e li elabora a livello globale. Vedere [la residenza dei dati e i dati dei clienti per Azure AD multi-factor authentication](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> I prodotti, i servizi e le applicazioni di terze parti Microsoft che si integrano con Azure AD possono accedere ai dati dei clienti. Valutare ogni prodotto, servizio e applicazione utilizzato per determinare il modo in cui i dati dei clienti vengono elaborati dal prodotto, dal servizio e dall'applicazione specifici e dal fatto che soddisfino i requisiti di archiviazione dei dati dell'azienda. Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.