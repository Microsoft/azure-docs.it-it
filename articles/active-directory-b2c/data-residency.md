---
title: Disponibilità delle aree e residenza dei dati
titleSuffix: Azure AD B2C
description: Disponibilità dell'area, residenza dei dati e informazioni sui tenant di Azure Active Directory B2C Preview.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: bb9c6dbf9984ec81fbd4b93a61552211928d0f0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388715"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: aree di disponibilità e residenza dei dati

Le aree di disponibilità e la residenza dei dati sono due concetti molto diversi che si applicano in modo diverso ad Azure AD B2C rispetto al resto di Azure. In questo articolo vengono illustrate le differenze tra questi due concetti e viene confrontato il modo in cui si applicano ad Azure rispetto a Azure AD B2C.

Azure AD B2C è **disponibile a livello generale in tutto il mondo** con l'opzione per la **residenza dei dati** in **Stati Uniti, Europa o Asia Pacifico**.

L'[area di disponibilità](#region-availability) si riferisce al paese in cui è possibile usare un servizio.

La [residenza dei dati](#data-residency) si riferisce alla zona in cui i dati utente vengono memorizzati.

## <a name="region-availability"></a>Aree di disponibilità

Azure AD B2C è disponibile in tutto il mondo tramite il cloud pubblico di Azure.

Questo comportamento è diverso dal modello seguito dalla maggior parte degli altri servizi di Azure, che in genere corrispondono alla *disponibilità* con la *residenza dei dati*. È possibile vedere alcuni esempi di questo tipo di approccio nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) e [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residenza dei dati

Azure AD B2C archivia i dati utente in Stati Uniti, in Europa o nell'area Asia Pacifico.

La residenza dei dati è determinata dal paese selezionato quando si [Crea un tenant di Azure ad B2C](tutorial-create-tenant.md):

![Screenshot di un modulo Crea tenant, selezione paese o area geografica.](./media/data-residency/data-residency-b2c-tenant.png)

I dati risiedono nella **Stati Uniti** per i paesi/aree geografiche seguenti:

> Stati Uniti, Canada, Costa Rica, Repubblica dominicana, El Salvador, Guatemala, Messico, Panama, Portorico e Trinidad e Tobago

I dati si trovano in **Europa** per i paesi/aree geografiche seguenti:

> Algeria, Austria, Azerbaigian, Bahrain, Bielorussia, Belgio, Bulgaria, Croazia, Cipro, Repubblica Ceca, Danimarca, Egitto, Estonia, Finlandia, Francia, Germania, Grecia, Ungheria, Islanda, Irlanda, Israele, Italia, Giordania Kazakistan, Kenya, Kuwait, Lettonia, Libano, Liechtenstein, Lituania, Lussemburgo, Macedonia settentrionale, Malta, Montenegro, Marocco, Paesi Bassi, Nigeria, Norvegia, Oman, Pakistan, Polonia, Portogallo, Qatar, Romania, Russia, Arabia Saudita, Serbia, Slovacchia, Slovenia, Sudafrica, Spagna, Svezia, Svizzera, Tunisia, Turchia, Ucraina, Emirati Arabi Uniti e Regno Unito.

I dati risiedono in **Asia Pacifico** per i paesi/aree geografiche seguenti:

> Afghanistan, Hong Kong SAR, India, Indonesia, Giappone, Corea, Malaysia, Filippine, Singapore, Sri Lanka, Taiwan e Thailandia.

I paesi/aree geografiche seguenti sono in fase di aggiunta all'elenco. Per ora, è comunque possibile usare Azure AD B2C scegliendo uno dei paesi/aree geografiche indicati precedentemente.

> Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Iraq, Nuova Zelanda, Paraguay, Perù, Uruguay e Venezuela.

## <a name="preview-tenant"></a>Tenant di anteprima

Se è stato creato un tenant B2C durante Azure AD periodo di anteprima B2C, è probabile che il **tipo di tenant** indichi **tenant di anteprima**.

In tal caso, è necessario usare il tenant solo a scopo di sviluppo e test. Non usare un tenant di anteprima per le applicazioni di produzione.

Non esiste **alcun percorso di migrazione** da un tenant di anteprima B2C a un tenant B2C a livello di produzione. È necessario creare un nuovo tenant B2C per le applicazioni di produzione.

Si verificano problemi noti quando si elimina un tenant B2C di anteprima e si crea un tenant B2C a livello di produzione con lo stesso nome di dominio. *È necessario creare un tenant B2C a livello di produzione con un nome di dominio diverso*.

![Screenshot di un tipo di tenant, come tenant di anteprima.](./media/data-residency/preview-b2c-tenant.png)