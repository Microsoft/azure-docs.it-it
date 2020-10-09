---
title: Come risolvere i problemi Azure Data Catalog
description: Questo articolo descrive le problematiche più comuni per la risoluzione dei problemi relativi alle risorse Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68879546"
---
# <a name="troubleshooting-azure-data-catalog"></a>Risoluzione dei problemi di Azure Data Catalog

Questo articolo descrive le problematiche più comuni per la risoluzione dei problemi relativi alle risorse Azure Data Catalog. 

## <a name="functionality-limitations"></a>Limitazioni delle funzionalità

Quando si usa Azure Data Catalog, le funzionalità seguenti sono limitate:

- Gli account con **ruolo Guest** di tipo non sono supportati. Non è possibile aggiungere account Guest come utenti di Azure Data Catalog e gli utenti guest non possono usare il portale all'indirizzo [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- La creazione di risorse di Azure Data Catalog usando modelli Azure Resource Manager o comandi Azure PowerShell non è supportata.

- Non è possibile spostare la risorsa Azure Data Catalog tra i tenant di Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configurazione dei criteri di Azure Active Directory

In alcune situazioni è possibile accedere al portale di Azure Data Catalog, ma quando si prova ad accedere allo strumento di registrazione dell'origine dati viene visualizzato un messaggio di errore che impedisce l'accesso. Questo errore può verificarsi quando ci si trova nella rete aziendale o quando ci si connette dall'esterno della rete aziendale.

Lo strumento di registrazione usa l' *autenticazione basata su form* per convalidare l'accesso utente in Azure Active Directory. Per completare l'accesso, un amministratore di Azure Active Directory deve abilitare l'autenticazione basata su form nei *criteri di autenticazione globali*.

I criteri di autenticazione globali consentono di abilitare l'autenticazione separatamente per connessioni Intranet ed Extranet, come illustrato nell'immagine seguente. Se l'autenticazione basata su form non è abilitata per la rete da cui ci si connette, è possibile che si verifichino errori di accesso.

 ![Criteri di autenticazione globali di Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Per altre informazioni, vedere [Configurazione dei criteri di autenticazione](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'istanza di Azure Data Catalog](data-catalog-get-started.md)
