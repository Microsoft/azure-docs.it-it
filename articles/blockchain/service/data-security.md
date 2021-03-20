---
title: Sicurezza del servizio Azure blockchain
description: Concetti relativi all'accesso ai dati e alla sicurezza del servizio blockchain di Azure
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80879599"
---
# <a name="azure-blockchain-service-security"></a>Sicurezza del servizio Azure blockchain

Il servizio Azure blockchain usa diverse funzionalità di Azure per garantire la sicurezza e la disponibilità dei dati. I dati sono protetti tramite isolamento, crittografia e autenticazione.

## <a name="isolation"></a>Isolamento

Le risorse del servizio Azure blockchain sono isolate in una rete virtuale privata. Ogni nodo di transazione e convalida è una macchina virtuale (VM). Le macchine virtuali in una rete virtuale non possono comunicare direttamente con le VM in una rete virtuale diversa. L'isolamento garantisce che la comunicazione rimanga privata all'interno della rete virtuale. Per altre informazioni sull'isolamento della rete virtuale di Azure, vedere [isolamento nel cloud pubblico di Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagramma VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Crittografia

I dati utente vengono archiviati in archiviazione di Azure. I dati utente vengono crittografati in movimento e inattivi per la sicurezza e la riservatezza. Per ulteriori informazioni, vedere la [Guida alla sicurezza di archiviazione di Azure](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Authentication

È possibile inviare le transazioni ai nodi blockchain tramite un endpoint RPC. I client comunicano con un nodo di transazione usando un server proxy inverso che gestisce l'autenticazione utente e crittografa i dati su TLS.

![Diagramma di autenticazione](./media/data-security/authentication.png)

Sono disponibili tre modalità di autenticazione per l'accesso RPC.

### <a name="basic-authentication"></a>Autenticazione di base

L'autenticazione di base usa un'intestazione di autenticazione HTTP contenente il nome utente e la password. Nome utente è il nome del nodo blockchain. La password viene impostata durante il provisioning di un membro o di un nodo. La password può essere modificata usando il portale di Azure o l'interfaccia della riga di comando.

### <a name="access-keys"></a>Chiavi di accesso

Le chiavi di accesso usano una stringa generata in modo casuale incluso nell'URL dell'endpoint. Due tasti di accesso consentono di abilitare la rotazione della chiave. Le chiavi possono essere rigenerate dal portale di Azure e dall'interfaccia della riga di comando.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) utilizza un meccanismo di autenticazione basato sulle attestazioni in cui l'utente viene autenticato da Azure AD utilizzando Azure AD credenziali utente. Azure AD offre la gestione delle identità basata sul cloud e consente ai clienti di usare una singola identità in un'intera azienda e di accedere alle applicazioni nel cloud. Il servizio Azure blockchain si integra con Azure AD abilitando la Federazione degli ID, Single Sign-On e l'autenticazione a più fattori. È possibile assegnare utenti, gruppi e ruoli applicazione all'interno dell'organizzazione per l'accesso ai membri e ai nodi di blockchain.

Il proxy client Azure AD è disponibile in [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Il proxy client indirizza l'utente alla pagina di accesso Azure AD e ottiene una bearer token al completamento dell'autenticazione. Successivamente, l'utente connette un'applicazione client Ethereum, ad esempio Geth o tartufo, all'endpoint del proxy client. Infine, quando viene inviata una transazione, il proxy client inserisce il bearer token nell'intestazione HTTP e il proxy inverso convalida il token tramite il protocollo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Chiavi e account Ethereum

Quando si esegue il provisioning di un membro del servizio Azure blockchain, viene generato un account Ethereum e una coppia di chiavi pubblica e privata. La chiave privata viene usata per inviare transazioni a blockchain. L'account Ethereum è costituito dagli ultimi 20 byte dell'hash della chiave pubblica. L'account Ethereum è denominato anche portafogli.

La coppia di chiavi pubblica e privata viene archiviata come un file di chiave in formato JSON. La chiave privata viene crittografata con la password immessa al momento della creazione del servizio Ledger blockchain.

Le chiavi private vengono usate per firmare digitalmente le transazioni. In blockchain privato, un contratto intelligente firmato da una chiave privata rappresenta l'identità del firmatario. Per verificare la validità della firma, il ricevitore può confrontare la chiave pubblica del firmatario con l'indirizzo calcolato dalla firma.

Le chiavi della costellazione vengono usate per identificare in modo univoco un nodo quorum. Le chiavi della costellazione vengono generate al momento del provisioning dei nodi e vengono specificate nel parametro privateFor di una transazione privata nel quorum.

## <a name="next-steps"></a>Passaggi successivi

Vedere [come configurare l'accesso Azure Active Directory per il servizio Azure blockchain](configure-aad.md).
