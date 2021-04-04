---
title: Funzionalità dei dati dei clienti in Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Informazioni sui dati dei clienti, che Azure Key Vault riceve durante la creazione o l'aggiornamento di insiemi di credenziali, chiavi, segreti, certificati e account di archiviazione gestiti.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8806ff41edabcd3c0875d3c02360dc9a275e3878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460832"
---
# <a name="azure-key-vault-customer-data-features"></a>Funzionalità dei dati dei clienti in Azure Key Vault

Azure Key Vault riceve i dati dei clienti durante la creazione o l'aggiornamento di insiemi di credenziali, pool di HSM gestiti, chiavi, segreti, certificati e account di archiviazione gestiti. I dati dei clienti sono visibili direttamente nel portale di Azure e tramite l'API REST e possono essere modificati o eliminati aggiornando o eliminando l'oggetto contenente tali dati.

I log di accesso al sistema vengono generati quando un utente o un'applicazione accede a Key Vault. I log di accesso dettagliati sono disponibili per i clienti che usano Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificazione dei dati dei clienti

Le informazioni seguenti identificano i dati dei clienti all'interno di Azure Key Vault:

- I criteri di accesso per Azure Key Vault contengono ID oggetto che rappresentano utenti, gruppi o applicazioni.
- I soggetti dei certificati possono includere indirizzi di posta elettronica o altri identificatori di utenti o organizzazioni.
- I contatti dei certificati possono contenere indirizzi di posta elettronica, nomi o numeri di telefono degli utenti.
- Le autorità di certificazione possono contenere indirizzi di posta elettronica, nomi, numeri di telefono, credenziali di account e dettagli delle organizzazioni.
- È possibile applicare tag arbitrari agli oggetti in Azure Key Vault. Questi oggetti includono insiemi di credenziali, chiavi, segreti, certificati e account di archiviazione. I tag usati possono contenere dati personali.
- I log di accesso di Azure Key Vault contengono ID oggetto, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md) e indirizzi IP per ogni chiamata API REST.
- I log di diagnostica di Azure Key Vault possono contenere ID oggetto e indirizzi IP per le chiamate API REST.

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti

Le stesse API REST, esperienze del portale e SDK usati per creare insiemi di credenziali, chiavi, segreti, certificati e account di archiviazione gestiti sono inoltre in grado di aggiornare ed eliminare questi oggetti.

L'eliminazione temporanea consente di recuperare i dati eliminati per 90 giorni dopo l'eliminazione. Quando si usa l'eliminazione temporanea, è possibile che i dati vengano eliminati definitivamente prima della scadenza del periodo di conservazione di 90 giorni eseguendo un'operazione di ripulitura. Se l'insieme di credenziali o la sottoscrizione è stata configurata per bloccare le operazioni di ripulitura, non è possibile eliminare definitivamente i dati fino a quando non è scaduto il periodo di conservazione pianificato.

## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti

Le stesse API REST, esperienze del portale e SDK usati per creare insiemi di credenziali, chiavi, segreti, certificati e account di archiviazione gestiti consentono inoltre di visualizzare ed esportare questi oggetti.

La registrazione dell'accesso di Azure Key Vault è una funzionalità facoltativa che può essere attivata per generare log per ogni chiamata API REST. Questi log verranno trasferiti in un account di archiviazione nella sottoscrizione in cui si applicano i criteri di conservazione conformi ai requisiti dell'organizzazione.

I log di diagnostica di Azure Key Vault che contengono dati personali possono essere recuperati effettuando una richiesta di esportazione nel portale Privacy dell'utente. Questa richiesta deve essere effettuata dall'amministratore del tenant.

## <a name="next-steps"></a>Passaggi successivi

- [Registrazione dell'insieme di credenziali delle chiavi di Azure](logging.md)

- [Panoramica dell'eliminazione temporanea di Azure Key Vault](./key-vault-recovery.md)

- [Azure Key Vault key operations](/rest/api/keyvault/key-operations) (Operazioni relative alle chiavi di Azure Key Vault)

- [Azure Key Vault secret operations](/rest/api/keyvault/secret-operations) (Operazioni relative ai segreti di Azure Key Vault)

- [Azure Key Vault certificates and policies](/rest/api/keyvault/certificates-and-policies) (Certificati e criteri di Azure Key Vault)

- [Azure Key Vault storage account operations](/rest/api/keyvault/storage-account-key-operations) (Operazioni relative agli account di archiviazione di Azure Key Vault)