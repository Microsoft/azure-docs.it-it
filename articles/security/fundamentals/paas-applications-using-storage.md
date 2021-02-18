---
title: Protezione delle applicazioni PaaS usando Archiviazione di Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate di sicurezza in Archiviazione di Azure per proteggere le applicazioni PaaS Web e per dispositivi mobili.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093989"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Procedure consigliate per la protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante Archiviazione di Azure
Questo articolo illustra un insieme di procedure consigliate di sicurezza in Archiviazione di Azure per la protezione delle applicazioni PaaS Web e per applicazioni mobili. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

Azure consente di distribuire e usare le risorse di archiviazione in modi che non è facile ottenere in locale. Con archiviazione di Azure è possibile raggiungere livelli elevati di scalabilità e disponibilità con un lavoro richiesto minimo. Non solo Archiviazione di Azure è la base per le macchine virtuali di Azure che eseguono Windows o Linux, ma può supportare anche applicazioni distribuite di grandi dimensioni.

Archiviazione di Azure offre i quattro servizi seguenti: archiviazione BLOB, archiviazione tabelle, archiviazione code e archiviazione file. Per altre informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md).

La [Guida alla sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md) è una fonte eccellente di informazioni dettagliate su Archiviazione di Azure e sulla sicurezza. Questo articolo sulle procedure consigliate fa riferimento a livello generale ad alcuni concetti illustrati nella Guida alla sicurezza con i relativi collegamenti, oltre ad altre fonti per altre informazioni.

In questo articolo vengono illustrate le seguenti procedure consigliate:

- Firme di accesso condiviso (SAS)
- Controllo degli accessi in base al ruolo di Azure
- Crittografia lato client per i dati di valore elevato
- Crittografia del servizio di archiviazione


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Usare la firma di accesso condiviso anziché una chiave dell'account di archiviazione
Il controllo di accesso è fondamentale. Per facilitare il controllo di accesso ad Archiviazione di Azure, Azure genera due chiavi dell'account di archiviazione a 512 bit quando si crea un account di archiviazione. Il livello di ridondanza delle chiavi consente di evitare le interruzioni del servizio durante la normale rotazione delle chiavi. 

Le chiavi di accesso alle risorse di archiviazione sono segreti ad alta priorità che devono essere accessibili solo ai responsabili del controllo di accesso alle risorse di archiviazione. Se persone sbagliate ottengono l'accesso a queste chiavi, disporranno del controllo completo delle risorse di archiviazione e potrebbero sostituire, eliminare o aggiungere file alle risorse di archiviazione. Sono inclusi il malware e altri tipi di contenuto che potrebbero compromettere l'organizzazione o i clienti.

È comunque necessario un modo per fornire l'accesso agli oggetti nella risorsa di archiviazione. Per fornire un accesso più granulare è possibile sfruttare la firma di accesso condiviso (SAS). La firma di accesso condiviso consente la condivisione di oggetti specifici nella risorsa di archiviazione per un intervallo di tempo predefinito e con autorizzazioni specifiche. Una firma di accesso condiviso consente di definire:

- L'intervallo di validità della firma di accesso condiviso, incluse l'ora di inizio e quella di scadenza.
- Le autorizzazioni concesse dalla firma di accesso condiviso. Ad esempio, una firma di accesso condiviso su un BLOB potrebbe concedere a un utente le autorizzazioni di lettura e di scrittura per questo BLOB, ma non eliminare le autorizzazioni.
- Un indirizzo IP o un intervallo di indirizzi IP facoltativo da cui Archiviazione di Azure accetta la firma di accesso condiviso. Ad esempio, è possibile specificare un intervallo di indirizzi IP appartenenti all'organizzazione. Fornisce un'altra misura di sicurezza per la firma di accesso condiviso.
- Il protocollo tramite cui Archiviazione di Azure accetta la firma di accesso condiviso. È possibile usare questo parametro facoltativo per limitare l'accesso ai client tramite HTTPS.

La firma di accesso condiviso consente di condividere il contenuto nel modo in cui si vuole condividerlo, senza fornire le chiavi dell'account di archiviazione. L'uso costante di una firma di accesso condiviso rappresenta un metodo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account di archiviazione.

Per altre informazioni sulle firme di accesso condiviso vedere [Uso delle firme di accesso condiviso](../../storage/common/storage-sas-overview.md). 

## <a name="use-azure-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo di Azure
Un altro modo per gestire l'accesso consiste nell'usare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md). Con il controllo degli accessi in base al ruolo di Azure, l'utente si concentra sulla concessione delle autorizzazioni esatte necessarie per i dipendenti, in base ai principi di sicurezza con necessità di conoscenza un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente ostacola l'efficienza del lavoro dei dipendenti. Il controllo degli accessi in base al ruolo di Azure consente di risolvere questo problema offrendo una gestione con granularità fine per Azure. Questo è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati.

È possibile usare i ruoli predefiniti di Azure in Azure per assegnare privilegi agli utenti. Si consiglia di usare Collaboratore Account di archiviazione per gli operatori cloud che devono gestire gli account di archiviazione e il ruolo Collaboratore Account di archiviazione classico per gestire gli account di archiviazione classici. Per gli operatori cloud che hanno necessità di gestire le VM ma non la rete virtuale o l'account di archiviazione a cui sono connesse, è possibile aggiungere il ruolo di Collaboratore Macchina virtuale.

Le organizzazioni che non applicano il controllo di accesso ai dati usando funzionalità come il controllo degli accessi in base al ruolo di Azure possono fornire più privilegi del necessario per gli utenti. Questo può comportare la compromissione dei dati perché ad alcuni utenti potrebbe essere concesso l'accesso a dati di cui non dovrebbero disporre.

Per ulteriori informazioni su RBAC di Azure, vedere:

- [Assegnare i ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md)
- [Guida alla sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Usare la crittografia lato client per i dati di valore elevato
La crittografia lato client consente di crittografare i dati in transito a livello di codice prima di caricarli in Archiviazione di Azure e decrittografarli a livello di codice quando vengono recuperati. Questo approccio consente la crittografia dei dati in transito, ma anche dei dati inattivi. La crittografia dei dati lato client è il metodo più sicuro, ma richiede modifiche all'applicazione a livello di codice e l'implementazione della gestione delle chiavi.

La crittografia lato client consente anche il controllo esclusivo delle chiavi di crittografia. È possibile generare e gestire chiavi di crittografia personalizzate. Usa una tecnica basata su busta in cui la libreria client di archiviazione di Azure genera una chiave di crittografia del contenuto (CEK) di cui viene eseguito il wrapping (crittografia) usando la chiave di crittografia della chiave (KEK). La chiave di crittografia della chiave è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata in [Azure Key Vault](../../key-vault/general/overview.md).

La crittografia lato client è incorporata nelle librerie client di archiviazione .NET e Java. Vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](../../storage/common/storage-client-side-encryption.md) per informazioni sulla crittografia dei dati all'interno di applicazioni client e la generazione e gestione di chiavi di crittografia personalizzate.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Attivare la crittografia del servizio di archiviazione di Azure per dati inattivi
Quando è abilitata la [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md) per archiviazione file, i dati vengono crittografati automaticamente usando l'algoritmo AES-256. Microsoft gestisce interamente la crittografia, la decrittografia e la gestione delle chiavi. Questa funzionalità è disponibile per i tipi di ridondanza archiviazione con ridondanza locale e con ridondanza geografica.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive una serie di procedure consigliate per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante Archiviazione di Microsoft Azure. Per ulteriori informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Protezione delle distribuzioni PaaS](paas-deployments.md)
- [Protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante i Servizi app di Azure](paas-applications-using-app-services.md)
- [Protezione di database PaaS in Azure](paas-applications-using-sql.md)