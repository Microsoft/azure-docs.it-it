---
title: Crittografia dei dati inattivi di Translator
titleSuffix: Azure Cognitive Services
description: Microsoft consente di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per il traduttore e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ff7b9f86cebc3c2479105d2a52aa92a265f8a1b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524454"
---
# <a name="translator-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi di Translator

Translator esegue automaticamente la crittografia dei dati, caricati per creare modelli di traduzione personalizzati, quando vengono salvati in modo permanente nel cloud, in modo da soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare la crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. Se si usa un piano tariffario che supporta chiavi gestite dal cliente, è possibile visualizzare le impostazioni di crittografia per la risorsa nella sezione **crittografia** del [portale di Azure](https://portal.azure.com), come illustrato nella figura seguente.

![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/encryptionblade.png)

Per le sottoscrizioni che supportano solo le chiavi di crittografia gestite da Microsoft, non è presente una sezione di **crittografia** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È anche possibile gestire la sottoscrizione con chiavi personalizzate denominate chiavi gestite dal cliente (CMK). CMK offre una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati. Se CMK è configurato per la sottoscrizione, viene fornita la crittografia doppia, che offre un secondo livello di protezione, consentendo al contempo di controllare la chiave di crittografia tramite il Azure Key Vault.

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili per tutti i piani tariffari per il servizio di conversione. Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta di conversione Customer-Managed chiave](https://aka.ms/cogsvc-cmk) . l'operazione richiederà circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvata l'utilizzo di CMK con il servizio di conversione, sarà necessario creare una nuova risorsa di conversione. Una volta creata la risorsa di conversione, è possibile usare Azure Key Vault per configurare l'identità gestita.

Per abilitare le chiavi gestite dal cliente per il traduttore, attenersi alla procedura seguente:

1. Creare la nuova risorsa di traduzione regionale o servizi cognitivi regionali. Questa operazione non funzionerà con una risorsa globale.
2. È stata abilitata l'identità gestita nel portale di Azure e si aggiungono le informazioni sulla chiave gestita dal cliente.
3. Creare una nuova area di lavoro nel convertitore personalizzato e associare le informazioni di sottoscrizione.

### <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente

È necessario usare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/general/overview.md).

Una nuova risorsa di servizi cognitivi è sempre crittografata usando chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente nel momento in cui viene creata la risorsa. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile non appena viene creata la risorsa.

Per informazioni su come usare le chiavi gestite dal cliente con Azure Key Vault per la crittografia dei servizi cognitivi, vedere:

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia dei servizi cognitivi dall'portale di Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dal cliente consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso al Key Vault selezionato durante l'installazione della chiave gestita dal cliente. È possibile ottenere altre informazioni sulle [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzionalità che dipendono da questi dati smetteranno di funzionare. Anche tutti i modelli distribuiti verranno annullati. Tutti i dati caricati verranno eliminati dal convertitore personalizzato. Se le identità gestite sono riabilitate, il modello non verrà ridistribuito automaticamente.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi.

Con la crittografia dei servizi cognitivi sono supportate solo le chiavi RSA di dimensioni 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/general/about-keys-secrets-certificates.md).

> [!NOTE]
> Se l'intero insieme di credenziali delle chiavi viene eliminato, i dati non verranno più visualizzati e verrà annullata la distribuzione di tutti i modelli. Tutti i dati caricati verranno eliminati dal convertitore personalizzato. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [PowerShell per Azure Key Vault](/powershell/module/az.keyvault//) o [Interfaccia della riga di comando per Azure Key Vault](/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi e i modelli non verranno distribuiti, perché la chiave di crittografia non è accessibile dai servizi cognitivi. Tutti i dati caricati verranno eliminati anche dal convertitore personalizzato.


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure Key Vault](../../key-vault/general/overview.md)
