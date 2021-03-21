---
title: Che cos'è Azure Key Vault? | Microsoft Docs
description: Informazioni su come Azure Key Vault protegge le chiavi crittografiche e i segreti usati dalle applicazioni e dai servizi cloud.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cc00a4f1c1551932b4a30a8ef9b27cb1d4082667
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071597"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault concetti di base

Azure Key Vault è un servizio cloud per archiviare e accedere in modo sicuro ai segreti. Un segreto è qualsiasi elemento a cui si vuole controllare rigorosamente l'accesso, ad esempio chiavi API, password, certificati o chiavi crittografiche. Key Vault servizio supporta due tipi di contenitori: insiemi di credenziali e pool HSM gestiti. Gli insiemi di credenziali supportano l'archiviazione di chiavi, segreti e certificati basati sul modulo di protezione hardware. I pool HSM gestiti supportano solo le chiavi supportate da HSM. Per informazioni complete, vedere [Panoramica dell'API REST di Azure Key Vault](about-keys-secrets-certificates.md) .

Ecco altri termini importanti:

- **Tenant**: un tenant è l'organizzazione che possiede e gestisce una specifica istanza dei servizi cloud Microsoft. Viene spesso usato per fare riferimento al set di servizi di Azure e Microsoft 365 per un'organizzazione.

- **Proprietario dell'insieme di credenziali**: il proprietario può creare un insieme di credenziali delle chiavi e ottenerne un accesso e controllo completi. Il proprietario dell'insieme di credenziali può anche configurare il controllo per registrare chi accede a segreti e chiavi. Gli amministratori possono controllare il ciclo di vita delle chiavi. Possono passare a una nuova versione della chiave, eseguirne il backup e svolgere attività correlate.

- **Consumer dell'insieme di credenziali**: quando il proprietario dell'insieme di credenziali gli concede l'accesso, il consumer può eseguire azioni sulle risorse nell'insieme di credenziali delle chiavi. Le azioni disponibili dipendono dalle autorizzazioni concesse.

- **Amministratori HSM gestiti**: gli utenti a cui è assegnato il ruolo di amministratore hanno il controllo completo su un pool di moduli di protezione hardware gestito. Possono creare più assegnazioni di ruolo per delegare l'accesso controllato ad altri utenti.

- **Amministratore o utente crittografico HSM gestito**: ruoli predefiniti che in genere sono assegnati a utenti o entità servizio che eseguiranno operazioni di crittografia usando le chiavi del modulo di protezione hardware gestito. L'utente crittografico può creare nuove chiavi, ma non può eliminare chiavi.

- **Crittografia del servizio di crittografia HSM gestita**: ruolo predefinito che viene generalmente assegnato a un'identità del servizio gestito degli account di servizio, ad esempio un account di archiviazione, per la crittografia dei dati inattivi con la chiave gestita dal cliente.

- **Risorsa**: una risorsa è un elemento gestibile disponibile tramite Azure. Esempi comuni sono la macchina virtuale, l'account di archiviazione, l'app Web, il database e la rete virtuale. Sono disponibili molte altre.

- **Gruppo di risorse**: un gruppo di risorse è un contenitore con risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

- **Entità** di sicurezza: un'entità di sicurezza di Azure è un'identità di sicurezza usata da app, servizi e strumenti di automazione creati dall'utente per accedere a risorse di Azure specifiche. È possibile considerarlo come una "identità utente" (nome utente e password o certificato) con un ruolo specifico e autorizzazioni strettamente controllate. Un'entità di sicurezza deve solo eseguire operazioni specifiche, a differenza di un'identità utente generale. Migliora la sicurezza se si concede solo il livello di autorizzazione minimo necessario per eseguire le attività di gestione. Un'entità di sicurezza usata con un'applicazione o un servizio viene chiamata in particolare un' **entità servizio**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD è il servizio Active Directory per un tenant. Ogni directory dispone di uno o più domini. A una directory possono essere associate molte sottoscrizioni, ma un solo tenant.

- **ID tenant di Azure**: un ID tenant è un modo univoco per identificare un'istanza di Azure AD all'interno di una sottoscrizione di Azure.

- **Identità gestite**: Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma il codice deve eseguire l'autenticazione a Key Vault per recuperarli. L'uso di un'identità gestita consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione in Key Vault o in qualsiasi servizio che supporti l'autenticazione di Azure AD, senza dover inserire le credenziali nel codice. Per altre informazioni, vedere la figura seguente e la [Panoramica delle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authentication"></a>Authentication
Per eseguire qualsiasi operazione con Key Vault, è prima necessario eseguire l'autenticazione. Esistono tre modi per eseguire l'autenticazione a Key Vault:

- [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md): quando si distribuisce un'app in una macchina virtuale in Azure, è possibile assegnare un'identità alla macchina virtuale che ha accesso a Key Vault. È anche possibile assegnare identità ad [altre risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Il vantaggio di questo approccio è che l'app o il servizio non gestisce la rotazione del primo segreto. Azure ruota automaticamente l'identità. Questo approccio è consigliato come procedura consigliata. 
- **Entità servizio e certificato**: è possibile usare un'entità servizio e un certificato associato con accesso a Key Vault. Questo approccio non è consigliato perché il proprietario o lo sviluppatore dell'applicazione deve ruotare il certificato.
- **Entità servizio e segreto**: Sebbene sia possibile usare un'entità servizio e un segreto per l'autenticazione a Key Vault, non è consigliabile. È difficile ruotare automaticamente il segreto di bootstrap usato per l'autenticazione Key Vault.


## <a name="key-vault-roles"></a>Ruoli dell'insieme di credenziali delle chiavi

La seguente tabella permette di capire meglio come l'insieme di credenziali chiave aiuti a soddisfare le esigenze degli sviluppatori e degli amministratori della sicurezza.

| Ruolo | Presentazione del problema | Soluzione offerta dall'insieme di credenziali chiave di Azure |
| --- | --- | --- |
| Sviluppatore di un'applicazione Azure |"Desidero scrivere un'applicazione per Azure che usa chiavi per la firma e la crittografia. Tuttavia, desidero che queste chiavi siano esterne all'applicazione, in modo che la soluzione sia adatta a un'applicazione geograficamente distribuita. <br/><br/>Voglio che queste chiavi e questi segreti siano protetti, senza dover scrivere manualmente il codice, Desidero anche che le chiavi e i segreti siano facili da usare dalle mie applicazioni, con prestazioni ottimali. " |√ Le chiavi vengono archiviate in un insieme di credenziali e richiamate dall'URI quando è necessario.<br/><br/> √ Le chiavi vengono protette da Azure con algoritmi standard del settore, lunghezze delle chiavi e moduli di protezione hardware.<br/><br/>  √ Le chiavi vengono elaborate in moduli di protezione hardware che risiedono negli stessi data center di Azure in cui si trovano le applicazioni. In questo modo si ottiene una migliore affidabilità e una latenza ridotta rispetto a chiavi che si trovano in una posizione diversa, ad esempio in locale. |
| Sviluppatore di software come un servizio (SaaS) |"Non desidero avere la responsabilità o la responsabilità potenziale per le chiavi del tenant e i segreti dei miei clienti. <br/><br/>Desidero che i clienti possano dedicarsi e gestire le proprie chiavi, in modo da potersi concentrare su ciò che preferisco, che fornisce le funzionalità di base del software. " |√ I clienti possono importare le loro chiavi in Azure e gestirle. Quando un'applicazione SaaS deve eseguire operazioni di crittografia usando le chiavi dei clienti, Key Vault esegue queste operazioni per conto dell'applicazione. L'applicazione non Visualizza le chiavi dei clienti. |
| Responsabile della sicurezza |"Desidero essere consapevole che le nostre applicazioni sono conformi a FIPS 140-2 Level 2 o FIPS 140-2 Level 3 HSM per la gestione delle chiavi sicure. <br/><br/>Voglio assicurarmi che la mia organizzazione abbia il controllo del ciclo di vita delle chiavi e possa monitorare l'utilizzo delle chiavi. <br/><br/>Anche se usiamo più servizi e risorse di Azure, voglio gestire le chiavi da un'unica posizione in Azure. " |√ Scegliere **gli** insiemi di credenziali per FIPS 140-2 livello 2 convalidato HSM.<br/>√ Scegliere i **pool di HSM gestiti** per FIPS 140-2 livello 3 convalidato HSM.<br/><br/>√ L'insieme di credenziali delle chiavi è progettato in modo che Microsoft non possa vedere o estrarre le chiavi.<br/>√ L'utilizzo delle chiavi viene registrato quasi in tempo reale.<br/><br/>√ L'insieme di credenziali offre un'unica interfaccia, indipendentemente dal numero di insiemi di credenziali disponibili in Azure, dalle aree supportate e dalle applicazioni che li usano. |

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Sebbene Key Vault vantaggi per gli sviluppatori e gli amministratori della sicurezza, può essere implementato e gestito dall'amministratore di un'organizzazione che gestisce altri servizi di Azure. Questo amministratore può ad esempio accedere con una sottoscrizione di Azure, creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e quindi essere responsabile delle attività operative come le seguenti:

- Creare o importare una chiave o un segreto
- Revocare o eliminare una chiave o un segreto
- Autorizzare gli utenti o le applicazioni per l'accesso all'insieme di credenziali delle chiavi, per consentire la gestione o l'uso delle chiavi e dei segreti corrispondenti
- Configurare l'utilizzo delle chiavi (ad esempio, la firma o la crittografia)
- Monitorare l'utilizzo delle chiavi

Questo amministratore offre agli sviluppatori gli URI da chiamare dalle applicazioni. Questo amministratore fornisce inoltre le informazioni di registrazione dell'utilizzo chiave all'amministratore della sicurezza. 

![Panoramica del funzionamento di Azure Key Vault][1]

Gli sviluppatori possono gestire le chiavi anche direttamente, usando le API. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [proteggere l'](secure-your-key-vault.md)insieme di credenziali.
- Informazioni su come [proteggere i pool di HSM gestiti](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).