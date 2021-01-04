---
title: Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault
description: Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 78b42a8ad3685d07b61c4faca384c7ee8f5a5f94
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616389"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Risoluzione dei problemi relativi ai criteri di accesso di Azure Key Vault

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Non è possibile elencare o ottenere segreti/chiavi/certificati. Viene visualizzato un messaggio analogo a "Si è verificato un errore". Errore.
Se si verificano problemi con l'elenco, il recupero, la creazione o l'accesso a un segreto, assicurarsi che siano definiti criteri di accesso per eseguire tale operazione: [Criteri di accesso di Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>In che modo è possibile identificare le tempistiche e le modalità di accesso agli insiemi di credenziali delle chiavi?

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. È possibile eseguire il monitoraggio abilitando la registrazione per Azure Key Vault. Per una guida dettagliata per abilitare la registrazione, [leggere altre informazioni](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Come è possibile monitorare la disponibilità, i periodi di latenza del servizio o altre metriche delle prestazioni per l'insieme di credenziali?

Quando si inizia a ridimensionare il servizio, il numero di richieste inviate all'insieme di credenziali delle chiavi aumenta. Con questa domanda può aumentare anche la latenza delle richieste e, in casi estremi, le richieste possono venire limitate, con un impatto sulle prestazioni del servizio. È possibile monitorare le metriche delle prestazioni dell'insieme di credenziali delle chiavi e ricevere avvisi per soglie specifiche. Per una guida dettagliata per configurare il monitoraggio, [leggere altre informazioni](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Non si riesce a modificare i criteri di accesso. Che cosa bisogna fare per abilitarla?
Per modificare i criteri di accesso, l'utente deve avere autorizzazioni AAD sufficienti. In questo caso l'utente deve avere il ruolo di collaboratore di livello superiore.

### <a name="i-am-seeing-unkwown-policy-error-what-does-that-mean"></a>Viene visualizzato l'errore 'Criteri sconosciuti'. Che cosa significa?
I criteri di accesso vengono visualizzati nella sezione Sconosciuti in due casi:
* Un utente precedente ha effettuato l'accesso, ma per qualche motivo l'utente non esiste.
* Se i criteri di accesso vengono aggiunti tramite PowerShell e vengono aggiunti per l'ID oggetto dell'applicazione anziché per l'entità servizio

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>In che modo è possibile assegnare il controllo di accesso per ogni oggetto dell'insieme di credenziali delle chiavi? 

La disponibilità della funzionalità di controllo di accesso per ogni segreto, chiave o certificato verrà segnalata qui. [Altre informazioni](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>In che modo è possibile fornire l'autenticazione dell'insieme di credenziali delle chiavi usando criteri di controllo di accesso?

Il modo più semplice per autenticare un'applicazione basata sul cloud in Key Vault consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Eseguire l'autenticazione con Azure Key Vault](authentication.md).
Se si crea un'applicazione locale, si sviluppa in locale o se non è possibile usare un'identità gestita per altri motivi, si può provare a registrare manualmente un'entità servizio e fornire l'accesso all'insieme di credenziali delle chiavi usando un criterio di controllo di accesso. Vedere [Assegnare un criterio di controllo di accesso](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Come è possibile concedere al gruppo di AD l'accesso all'insieme di credenziali delle chiavi?

Concedere al gruppo di AD le autorizzazioni per l'insieme di credenziali delle chiavi usando il comando `az keyvault set-policy` dell'interfaccia della riga di comando di Azure o il cmdlet Set-AzKeyVaultAccessPolicy di Azure PowerShell. Vedere [Assegnare un criterio di accesso - Interfaccia della riga di comando](assign-access-policy-cli.md) e [Assegnare un criterio di accesso - PowerShell](assign-access-policy-powershell.md).

L'applicazione necessita anche che sia assegnato almeno un ruolo di gestione delle identità e degli accessi (IAM) all'insieme di credenziali delle chiavi. In caso contrario, non potrà eseguire l'accesso e l'accesso alla sottoscrizione non riuscirà a causa di diritti insufficienti. I gruppi di Azure AD con le identità gestite possono richiedere fino a otto ore per aggiornare i token e diventare effettivi.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>In che modo è possibile ridistribuire Key Vault con un modello di Azure Resource Manager senza eliminare i criteri di accesso esistenti?

Attualmente, la ridistribuzione di Key Vault comporta l'eliminazione degli eventuali criteri di accesso in Key Vault, che vengono sostituiti con quelli nel modello di Azure Resource Manager. Non è disponibile alcuna opzione incrementale per i criteri di accesso di Key Vault. Per mantenere i criteri di accesso in Key Vault, è necessario leggere i criteri di accesso esistenti in Key Vault e usarli per popolare il modello di Azure Resource Manager per evitare interruzioni dell'accesso.

Un'altra opzione che può essere utile per questo scenario consiste nell'usare il controllo degli accessi in base al ruolo di Azure e i ruoli come alternativa ai criteri di accesso. Con il controllo degli accessi in base al ruolo di Azure è possibile ridistribuire l'insieme di credenziali delle chiavi senza specificare di nuovo il criterio. Altre informazioni su questa soluzione sono disponibili [qui](./rbac-guide.md).

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Procedure consigliate per la risoluzione dei problemi per i tipi di errore seguenti

* HTTP 401: Richiesta non autenticata - [Procedura di risoluzione dei problemi](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Autorizzazioni insufficienti - [Procedura di risoluzione dei problemi](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Troppe richieste - [Procedura di risoluzione dei problemi](rest-error-codes.md#http-429-too-many-requests)
* Controllare se è stata eliminata l'autorizzazione di accesso all’insieme di credenziali delle chiavi: Vedere [Assegnare un criterio di accesso - Interfaccia della riga di comando](assign-access-policy-cli.md), [Assegnare un criterio di accesso - PowerShell](assign-access-policy-powershell.md) o [Assegnare un criterio di accesso - Portale](assign-access-policy-portal.md).
* Se si riscontra un problema con l'autenticazione nell'insieme di credenziali delle chiavi nel codice, usare l'[SDK di autenticazione](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quali sono le procedure consigliate da implementare in caso di limitazione dell'insieme di credenziali delle chiavi?
Seguire le procedure consigliate illustrate [qui](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come risolvere gli errori di autenticazione di Key Vault: [Guida alla risoluzione dei problemi di Key Vault](rest-error-codes.md).
