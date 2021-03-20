---
title: Autenticazione SAML con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'autenticazione SAML con Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168645"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Autenticazione SAML con Azure Active Directory

Security Assertion Markup Language (SAML) è uno standard aperto per lo scambio di dati di autenticazione e autorizzazione tra un provider di identità e un provider di servizi. SAML è un linguaggio di markup basato su XML per le asserzioni di sicurezza, ovvero istruzioni che i provider di servizi utilizzano per prendere decisioni relative al controllo di accesso. 

La specifica SAML definisce tre ruoli:

* Entità, in genere un utente
* Provider di identità (IdP)
* Provider di servizi (SP)


## <a name="use-when"></a>Casi di utilizzo

Per un'applicazione SAML aziendale è necessario fornire un'esperienza di Single Sign-On (SSO).

Sebbene uno dei casi d'uso più importanti per gli indirizzi SAML sia SSO, soprattutto estendendo SSO tra domini di sicurezza, esistono anche altri casi d'uso (detti profili). 

![diagramma dell'architettura per SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: richiede un servizio dall'applicazione.

* **Web browser**: il componente con cui l'utente interagisce.

* **App Web**: applicazione aziendale che supporta SAML e USA Azure ad come IDP.

* **Token**: un'asserzione SAML (nota anche come token SAML) che contiene set di attestazioni effettuate dal provider di identità per il principio (utente). Contiene informazioni di autenticazione, attributi e istruzioni per la decisione di autorizzazione.

* **Azure ad**: Enterprise Cloud IDP che fornisce SSO e l'autenticazione a più fattori per le app SAML. Il servizio sincronizza, gestisce e gestisce le informazioni di identità per gli utenti e fornisce servizi di autenticazione per le applicazioni. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementare l'autenticazione SAML con Azure AD

* [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Configurazione Single Sign-On basata su SAML per applicazioni non della raccolta](../manage-apps/add-application-portal.md) 

* [Modalità di utilizzo del protocollo SAML da parte di Azure AD](../develop/active-directory-saml-protocol-reference.md)