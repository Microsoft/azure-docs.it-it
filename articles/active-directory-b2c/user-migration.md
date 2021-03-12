---
title: Approcci alla migrazione degli utenti
titleSuffix: Azure AD B2C
description: Eseguire la migrazione degli account utente da un altro provider di identità a Azure AD B2C usando i metodi di migrazione pre-migrazione o senza problemi.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2d4a61f653c5bedb31223d2eb3d37b92a076821
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010168"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Eseguire la migrazione di utenti ad Azure AD B2C

La migrazione da un altro provider di identità a Azure Active Directory B2C (Azure AD B2C) potrebbe richiedere anche la migrazione degli account utente esistenti. Di seguito sono descritti due metodi di migrazione, *pre-migrazione* e *migrazione senza* problemi. Con entrambi gli approcci, è necessario scrivere un'applicazione o uno script che usi l' [API Microsoft Graph](microsoft-graph-operations.md) per creare gli account utente nel Azure ad B2C.

## <a name="pre-migration"></a>Pre-migrazione

Nel flusso di pre-migrazione, l'applicazione di migrazione esegue questi passaggi per ogni account utente:

1. Leggere l'account utente dal vecchio provider di identità, incluse le credenziali correnti (nome utente e password).
1. Creare un account corrispondente nella directory Azure AD B2C con le credenziali correnti.

Usare il flusso di pre-migrazione in una delle due situazioni seguenti:

- È possibile accedere alle credenziali in testo non crittografato dell'utente (nome utente e password).
- Le credenziali sono crittografate, ma è possibile decrittografarle.

Per informazioni sulla creazione di account utente a livello di codice, vedere [manage Azure ad B2C user accounts with Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Migrazione senza problemi

Usare il flusso di migrazione senza problemi se le password non crittografate nel vecchio provider di identità non sono accessibili. Ad esempio, nei casi seguenti:

- La password viene archiviata in un formato crittografato unidirezionale, ad esempio con una funzione hash.
- La password viene archiviata dal provider di identità legacy in modo che non sia possibile accedere a. Ad esempio, quando il provider di identità convalida le credenziali chiamando un servizio Web.

Il flusso di migrazione trasparente richiede ancora la pre-migrazione degli account utente, ma usa un [criterio personalizzato](custom-policy-get-started.md) per eseguire una query su un' [API REST](custom-policy-rest-api-intro.md) (creata) per impostare la password di ogni utente al primo accesso.

Il flusso di migrazione senza problemi comporta quindi due fasi: *pre-migrazione* e *set credentials*.

### <a name="phase-1-pre-migration"></a>Fase 1: pre-migrazione

1. L'applicazione di migrazione legge gli account utente dal vecchio provider di identità.
1. L'applicazione di migrazione crea gli account utente corrispondenti nella directory Azure AD B2C, ma *imposta le password casuali* generate.

### <a name="phase-2-set-credentials"></a>Fase 2: impostare le credenziali

Al termine della pre-migrazione degli account, i criteri personalizzati e l'API REST eseguono le operazioni seguenti quando un utente accede:

1. Leggere l'account utente Azure AD B2C corrispondente all'indirizzo di posta elettronica immesso.
1. Controllare se l'account è contrassegnato per la migrazione valutando un attributo di estensione booleano.
    - Se l'attributo di estensione restituisce `True` , chiamare l'API REST per convalidare la password rispetto al provider di identità legacy.
      - Se l'API REST determina che la password non è corretta, restituire un errore descrittivo all'utente.
      - Se l'API REST determina che la password è corretta, scrivere la password nell'account Azure AD B2C e modificare l'attributo di estensione booleano in `False` .
    - Se l'attributo di estensione booleano restituisce `False` , continuare il processo di accesso come di consueto.

Per visualizzare un criterio personalizzato di esempio e un'API REST, vedere l' [esempio di migrazione di utenti](https://aka.ms/b2c-account-seamless-migration) semplificati su GitHub.

![Diagramma del diagramma di flusso dell'approccio di migrazione trasparente alla migrazione degli utenti](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagramma: flusso di migrazione senza problemi*

## <a name="best-practices"></a>Procedure consigliate

### <a name="security"></a>Sicurezza

L'approccio di migrazione trasparente usa l'API REST personalizzata per convalidare le credenziali di un utente rispetto al provider di identità legacy.

**È necessario proteggere l'API REST da attacchi di forza bruta.** Un utente malintenzionato può inviare diverse password con la speranza di indovinare le credenziali di un utente. Per evitare questi attacchi, interrompere la conservazione delle richieste all'API REST quando il numero di tentativi di accesso supera una determinata soglia. Inoltre, è possibile proteggere le comunicazioni tra Azure AD B2C e l'API REST. Per informazioni su come proteggere le API RESTful per la produzione, vedere [Proteggere i servizi RESTful](secure-rest-api.md).

### <a name="user-attributes"></a>Attributi utente

Non è necessario eseguire la migrazione di tutte le informazioni del provider di identità legacy alla directory Azure AD B2C. Identificare il set appropriato di attributi utente da archiviare in Azure AD B2C prima della migrazione.

- **Archivia in** Azure ad B2C
  - Nome utente, password, indirizzi di posta elettronica, numeri di telefono, numeri di appartenenza/identificatori.
  - Indicatori di consenso per l'informativa sulla privacy e i contratti di licenza con l'utente finale
- **Non** archiviare in Azure ad B2C
  - Dati sensibili, come numeri di carta di credito, codici fiscali (SSN, Social Security Number), record medici o altri dati regolamentati da enti governativi o di conformità del settore.
  - Preferenze di marketing o comunicazione, comportamenti degli utenti e informazioni dettagliate.

### <a name="directory-clean-up"></a>Pulizia directory

Prima di iniziare il processo di migrazione, è possibile eliminare la directory.

- Identificare il set di attributi utente da archiviare in Azure AD B2C e migrare solo gli elementi necessari. Se necessario, è possibile creare [attributi personalizzati](user-flow-custom-attributes.md) per archiviare più dati relativi a un utente.
- Se si esegue la migrazione da un ambiente con più origini di autenticazione (ad esempio, ogni applicazione dispone di una propria directory utente), eseguire la migrazione a un account unificato in Azure AD B2C.
- Se più applicazioni hanno nomi utente diversi, è possibile archiviarli tutti in un account utente Azure AD B2C usando l'insieme di identità. Per quanto riguarda la password, consentire all'utente di sceglierne una e impostarla nella directory. Ad esempio, con la migrazione senza problemi, solo la password scelta deve essere archiviata nell'account Azure AD B2C.
- Rimuovere gli account utente inutilizzati prima della migrazione oppure non eseguire la migrazione degli account obsoleti.

### <a name="password-policy"></a>Criteri password

Se gli account di cui si sta eseguendo la migrazione hanno un livello di complessità della password più debole rispetto al livello di [attendibilità della password forte](../active-directory/authentication/concept-sspr-policy.md) applicato da Azure ad B2C, è possibile disabilitare il requisito per la password complessa. Per ulteriori informazioni, vedere [proprietà dei criteri password](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Passaggi successivi

Il repository [Azure-ad-B2C/migrazione utenti](https://github.com/azure-ad-b2c/user-migration) in github contiene un esempio di codice personalizzato per la migrazione e un esempio di codice dell'API REST:

[Esempio di codice dell'API REST per la migrazione utente semplice &](https://aka.ms/b2c-account-seamless-migration)
