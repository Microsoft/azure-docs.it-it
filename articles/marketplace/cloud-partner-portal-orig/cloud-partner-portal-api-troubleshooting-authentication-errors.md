---
title: Risoluzione dei problemi relativi agli errori di autenticazione comuni Azure Marketplace
description: Offre assistenza relativamente agli errori di autenticazione frequenti quando si usano API del portale Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 1da9bbd1ed4bc4abea0699e56d8adc397086d6e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280423"
---
# <a name="troubleshooting-common-authentication-errors"></a>Risoluzione dei problemi: errori di autenticazione frequenti

Questo articolo offre assistenza relativamente agli errori di autenticazione frequenti quando si usano API del portale Cloud Partner.

## <a name="unauthorized-error"></a>Errore non autorizzato

Se si visualizzano costantemente errori `401 unauthorized`, verificare di disporre di un token di accesso valido.  Se non è ancora stato fatto, creare un'applicazione di base Azure Active Directory (Azure AD) e un'entità servizio come illustrato in [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Usare quindi l'applicazione o una semplice richiesta HTTP POST per verificare l'accesso.  Verranno inclusi l'ID tenant, l'ID applicazione, l'ID oggetto e la chiave privata per ottenere il token di accesso, come illustrato nell'immagine seguente:

![Risoluzione dei problemi: errore 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Errore non consentito

Se si visualizza un errore `403 forbidden`, assicurarsi che sia stata aggiunta l'entità servizio corretta all'account di pubblicazione nel portale Cloud Partner.
Seguire la procedura nella pagina [Prerequisiti](./cloud-partner-portal-api-prerequisites.md) per aggiungere l'entità servizio al portale.

Se è stata aggiunta l'entità servizio corretta, verificare tutte le altre informazioni. Prestare particolare attenzione all'ID oggetto immesso nel portale. Sono presenti due ID oggetto nella pagina di registrazione dell'app Azure Active Directory ed è necessario utilizzare l'ID oggetto locale. È possibile trovare il valore corretto visitando la pagina **Registrazioni app** per l'app e facendo clic sul nome dell'app nella sezione **Applicazione gestita nella directory locale**. Ciò consente di visualizzare le proprietà locali per l'app, in cui è possibile trovare l'ID oggetto corretto nella pagina **Proprietà**, come illustrato nella figura seguente. Inoltre, garantisce l'uso dell'ID editore corretto quando si aggiunge l'entità servizio e si effettua la chiamata API.

![Risoluzione dei problemi: errore 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
