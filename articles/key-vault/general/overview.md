---
title: Panoramica di Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ef31a11414fad1b9a3d06d00055c36c995adcce
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84216328"
---
# <a name="about-azure-key-vault"></a>Informazioni su Azure Key Vault

Azure Key Vault contribuisce a risolvere i problemi seguenti:

- **Gestione dei segreti**: Azure Key Vault può essere usato per archiviare in modo sicuro e controllare rigorosamente l'accesso a token, password, certificati, chiavi API e altri segreti.
- **Gestione delle chiavi**: Azure Key Vault può essere usato anche come soluzione di gestione delle chiavi. Con Azure Key Vault è semplice creare e controllare le chiavi di crittografia usate per crittografare i dati. 
- **Gestione dei certificati**: Azure Key Vault è anche un servizio che facilita il provisioning, la gestione e la distribuzione dei certificati Transport Layer Security/Secure Sockets Layer (TLS/SSL) pubblici e privati da usare con Azure e le risorse connesse interne. 
- **Archiviazione dei segreti supportati da moduli di protezione hardware**: le chiavi e i segreti possono essere protetti da software o da moduli di protezione hardware certificati FIPS 140-2 livello 2

## <a name="why-use-azure-key-vault"></a>Vantaggi di Azure Key Vault

### <a name="centralize-application-secrets"></a>Centralizzare i segreti delle applicazioni

Centralizzando l'archiviazione dei segreti delle applicazioni in Azure Key Vault è possibile controllare la distribuzione dei segreti. Key Vault riduce notevolmente le probabilità di divulgazione accidentale dei segreti. Quando si usa Key Vault, gli sviluppatori di applicazioni non devono più archiviare le informazioni di sicurezza nell'applicazione. Il fatto di non dover archiviare le informazioni di sicurezza nelle applicazioni elimina la necessità di inserirle nel codice. Ad esempio, per un'applicazione potrebbe essere necessario connettersi a un database. Invece di archiviare la stringa di connessione nel codice dell'app, è possibile archiviarla in modo sicuro in Key Vault.

Le applicazioni possono accedere in modo sicuro alle informazioni necessarie tramite URI. Questi URI permettono alle applicazioni di recuperare versioni specifiche di un segreto. Non è necessario scrivere codice personalizzato per proteggere le informazioni segrete archiviate in Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Archiviare segreti e chiavi con la massima sicurezza

Le chiavi e i segreti vengono protetti da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware. I moduli di protezione hardware usati sono convalidati per Federal Information Processing Standards (FIPS) 140-2 livello 2.

L'accesso di un chiamante (utente o applicazione) a un'istanza di Key Vault richiede opportune procedure di autenticazione e autorizzazione. L'autenticazione stabilisce l'identità del chiamante, mentre l'autorizzazione determina le operazioni che il chiamante è autorizzato a eseguire.

L'autenticazione avviene tramite Azure Active Directory. L'autorizzazione può essere eseguita tramite il controllo degli accessi in base al ruolo o i criteri di accesso di Key Vault. Il controllo degli accessi in base al ruolo viene usato per la gestione degli insiemi di credenziali e vengono usati criteri di accesso di Key Vault quando si tenta di accedere ai dati archiviati in un insieme di credenziali.

Le istanze di Azure Key Vault possono essere protette da moduli di protezione software o hardware. Per i casi in cui si richiede maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Microsoft usa i moduli di protezione hardware nCipher. È possibile usare gli strumenti nCipher per spostare una chiave dal modulo di protezione hardware ad Azure Key Vault.

Azure Key Vault è infine progettato in modo che Microsoft non possa vedere o estrarre i dati.

### <a name="monitor-access-and-use"></a>Monitorare l'accesso e l'uso

Dopo aver creato un paio di istanze di Key Vault, può essere utile monitorare modalità e tempi di accesso a chiavi e segreti. È possibile monitorare l'attività abilitando la registrazione per gli insiemi di credenziali. È possibile configurare Azure Key Vault per eseguire queste operazioni:

- Archiviare in un account di archiviazione.
- Streaming in un hub eventi.
- Inviare i log ai log di Monitoraggio di Azure.

L'utente ha il controllo dei registri e può proteggerli limitando l'accesso. È anche possibile eliminare i log non più necessari.

### <a name="simplified-administration-of-application-secrets"></a>Amministrazione semplificata dei segreti delle applicazioni

Quando si archiviano dati importanti, è necessario eseguire diversi passaggi. Le informazioni di sicurezza devono essere protette, devono seguire un ciclo di vita e devono avere disponibilità elevata. Azure Key Vault semplifica il processo per soddisfare i requisiti in questi modi:

- Eliminando la necessità di competenze interne in materia di moduli di protezione hardware.
- Aumentando le prestazioni con breve preavviso per soddisfare i picchi d'uso dell'organizzazione.
- Replicando i contenuti di un'area di Key Vault in un'area secondaria. La replica dei dati garantisce la disponibilità elevata ed elimina la necessità di interventi da parte dell'amministratore per l'attivazione del failover.
- Fornendo opzioni standard di amministrazione di Azure tramite il portale, l'interfaccia della riga di comando di Azure e PowerShell.
- Automatizzando determinate attività sui certificati acquistati da autorità di certificazione pubbliche, ad esempio la registrazione e il rinnovo.

Azure Key Vault consente anche di separare i segreti delle applicazioni. Le applicazioni possono accedere solo all'insieme di credenziali per il quale hanno l'autorizzazione ed essere limitate alla sola esecuzione di alcune operazioni specifiche. È possibile creare un'istanza di Azure Key Vault per ogni applicazione e limitare i segreti archiviati in un'istanza a un'applicazione e un team di sviluppatori specifici.

### <a name="integrate-with-other-azure-services"></a>Integrare il servizio con altri servizi di Azure

In qualità di archivio sicuro in Azure, Key Vault è stato usato per semplificare scenari come i seguenti:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Funzionalità [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) e [Transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) in SQL server e nel Database SQL di Azure
- [Servizio app di Azure]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Key Vault può integrarsi con account di archiviazione, hub eventi e Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni su [chiavi, segreti e certificati](about-keys-secrets-certificates.md)
- [Avvio rapido: Creare un'istanza di Azure Key Vault usando l'interfaccia della riga di comando](../secrets/quick-create-cli.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
