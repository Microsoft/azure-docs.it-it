---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Questo argomento descrive il comportamento degli attributi degli attributi msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861766"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
Nel documento di riferimento seguente vengono descritti questi attributi utilizzati da Exchange e il modo corretto per modificare le regole di sincronizzazione predefinite.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Che cosa sono msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Sono disponibili due tipi di [esenzioni](/Exchange/policy-and-compliance/holds/holds) per un server Exchange: la conservazione dei contenziosi e il In-Place Hold. Quando è abilitata la funzionalità di contenzioso, tutti gli elementi della cassetta postale vengono posizionati in attesa.  Per mantenere solo gli elementi che soddisfano i criteri di una query di ricerca definita tramite lo strumento In-Place eDiscovery, viene utilizzato un In-Place.

Gli attributi MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies consentono AD locale e Azure AD di determinare quali utenti sono in attesa a seconda che utilizzino Exchange locale o Exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>flusso di sincronizzazione msExchUserHoldPolicies
Per impostazione predefinita, MsExchUserHoldPolcies vengono sincronizzati da Azure AD Connect direttamente all'attributo msExchUserHoldPolicies nel metaverse e quindi all'attributo msExchUserHoldPolicies in Azure AD

Le tabelle seguenti descrivono il flusso:

In ingresso da Active Directory locale:

|Attributo Active Directory|Nome attributo|Tipo di flusso|Attributo metaverse|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Active Directory locale|msExchUserHoldPolicies|Connessione diretta|msExchUserHoldPolicies|In da AD-utente Exchange|

In uscita per Azure AD:

|Attributo metaverse|Nome attributo|Tipo di flusso|Attributo di Azure AD|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Connessione diretta|msExchUserHoldPolicies|Da out ad AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>flusso di sincronizzazione cloudMsExchUserHoldPolicies
Per impostazione predefinita, cloudMsExchUserHoldPolicies vengono sincronizzati da Azure AD Connect direttamente all'attributo cloudMsExchUserHoldPolicies nel metaverse. Quindi, se msExchUserHoldPolicies non è null nel metaverse, l'attributo in viene propagato a Active Directory.

Le tabelle seguenti descrivono il flusso:

In ingresso da Azure AD:

|Attributo Active Directory|Nome attributo|Tipo di flusso|Attributo metaverse|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Active Directory locale|cloudMsExchUserHoldPolicies|Connessione diretta|cloudMsExchUserHoldPolicies|In da AAD-scambio utente|

In uscita per Active Directory locale:

|Attributo metaverse|Nome attributo|Tipo di flusso|Attributo di Azure AD|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NON NULL)|msExchUserHoldPolicies|Uscita da ad – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informazioni sul comportamento dell'attributo
MsExchangeUserHoldPolicies è un attributo dell'autorità singola.  È possibile impostare un attributo di autorità singolo per un oggetto (in questo caso, oggetto utente) nella directory locale o nella directory cloud.  L'inizio delle regole Authority impone che se l'attributo viene sincronizzato dall'ambiente locale, a Azure AD non sarà consentito aggiornare l'attributo.

Per consentire agli utenti di impostare un criterio di mantenimento per un oggetto utente nel cloud, viene usato l'attributo cloudMSExchangeUserHoldPolicies. Questo attributo viene usato perché Azure AD non può impostare msExchangeUserHoldPolicies direttamente in base alle regole descritte in precedenza.  Questo attributo viene quindi sincronizzato nuovamente con la directory locale se, msExchangeUserHoldPolicies non è null e sostituisce il valore corrente di msExchangeUserHoldPolicies.

In determinate circostanze, ad esempio, se entrambi sono stati modificati in locale e in Azure nello stesso momento, potrebbero verificarsi alcuni problemi.  

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
