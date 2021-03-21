---
title: Uso della rete CDN di Azure con firma di accesso condiviso | Documentazione Microsoft
description: La rete CDN di Azure supporta l'uso della firma di accesso condiviso, ovvero SAS, per garantire l'accesso limitato ai contenitori di archiviazione privati.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725323"
---
# <a name="using-azure-cdn-with-sas"></a>Uso della rete CDN di Azure con firma di accesso condiviso

Quando si configura un account di archiviazione per la Rete di distribuzione dei contenuti di Microsoft Azure da usare per il contenuto della cache, per impostazione predefinita qualsiasi utente conosca l'URL per i contenitori di archiviazione può accedere ai file che sono stati caricati. Per proteggere i file nell'account di archiviazione, è possibile impostare l'accesso dei contenitori di archiviazione da pubblico a privato. In questa caso, tuttavia, nessuno potrà accedere ai file. 

Se si desidera concedere l'accesso limitato ai contenitori di archiviazione privati, è possibile usare la funzionalità di firma di accesso condiviso, ovvero SAS, dell'account di archiviazione di Azure. Una firma di accesso condiviso è un URI che concede diritti di accesso limitati alle risorse di Archiviazione di Azure senza esporre la chiave dell'account. È possibile fornire una firma di accesso condiviso ai client non considerati attendibili a cui non si vuole fornire la chiave dell'account di archiviazione ma a ai quali si desidera delegare l'accesso a determinate risorse dell'account di archiviazione. La distribuzione di un URI di firma di accesso condiviso a tali client, consente di concedere loro l'accesso a una risorsa per un periodo di tempo specificato.
 
La firma di accesso condiviso consente di definire vari parametri di accesso a un BLOB, ad esempio l'ora di inizio e di scadenza, le autorizzazioni (di lettura/scrittura) e gli intervalli IP. Questo articolo descrive come usare la firma di accesso condiviso con la rete CDN di Azure. Per altre informazioni sulla firma di accesso condiviso, incluse le procedure per crearla e le opzioni dei parametri, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Configurazione della rete CDN di Azure per l'uso della firma di accesso condiviso di archiviazione
Di seguito sono riportate le tre opzioni consigliate per l'uso della firma di accesso condiviso con la rete CDN di Azure. Per tutte le opzioni, si presuppone che sia stata già creata una firma di accesso condiviso (vedere Prerequisiti). 
 
### <a name="prerequisites"></a>Prerequisiti
Per iniziare, creare un account di archiviazione e quindi generare una firma di accesso condiviso per l'asset. È possibile generare due tipi di firme di accesso condiviso: una firma di accesso condiviso del servizio o una firma di accesso condiviso dell'account. Per altre informazioni, vedere [Tipi di firme di accesso condiviso](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

Dopo aver generato un token di firma di accesso condiviso, è possibile accedere al file di archiviazione BLOB aggiungendo `?sv=<SAS token>` all'URL. Questo URL ha il formato seguente: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Ad esempio:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Per altre informazioni sull'impostazione dei parametri, vedere [Considerazioni sui parametri della firma di accesso condiviso](#sas-parameter-considerations) e [Parametri della firma di accesso condiviso](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![Impostazioni della firma di accesso condiviso con la rete CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opzione 1: uso della firma di accesso condiviso con pass-through all'archiviazione BLOB dalla rete CDN di Azure

È l'opzione più semplice e prevede l'uso di un solo token di firma di accesso condiviso, che viene passato dalla rete CDN di Azure al server di origine.
 
1. Selezionare un endpoint, fare clic su **Regole di memorizzazione nella cache**, quindi selezionare **Memorizza nella cache tutti gli URL univoci** dall'elenco **Query string caching** (Memorizzazione nella cache della stringa di query).

    ![Regole di memorizzazione nella cache della rete CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Dopo aver configurato la firma di accesso condiviso nell'account di archiviazione, è necessario usare il token di firma di accesso condiviso con gli URL del server di origine e dell'endpoint della rete CDN per accedere al file. 
   
   L'URL dell'endpoint della rete CDN risultante ha il formato seguente: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Ad esempio:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Specificare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo intestazioni `Cache-Control` al server di origine. Poiché la rete CDN di Azure considera il token di firma di accesso condiviso come una stringa di query semplice, la procedura consigliata prevede la configurazione della durata della cache con scadenza alla stessa ora di scadenza della firma di acceso condiviso o a un'ora antecedente. In caso contrario, se un file viene memorizzato nella cache per un periodo più lungo di quello in cui la firma di accesso condiviso è attiva, una volta scaduta la firma di accesso condiviso, sarà possibile accedere a tale file dal server di origine della rete CDN di Azure. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di ripulitura sul file per cancellarlo dalla cache. Per informazioni sull'impostazione della durata della cache sulla rete CDN di Azure, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole di memorizzazione nella cache](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opzione 2: token di firma di accesso condiviso della rete CDN nascosto con regola di riscrittura
 
Questa opzione è disponibile solo per i profili di **rete CDN Premium di Azure fornita da Verizon**. Con questa opzione, è possibile proteggere l'archivio BLOB nel server di origine. È consigliabile usarla se non sono necessarie restrizioni di accesso specifiche per il file, ma si desidera impedire agli utenti di accedere all'origine di archiviazione in maniera diretta per migliorare i tempi di offload della rete CDN di Azure. Il token di firma di accesso condiviso, che non è noto all'utente, è necessario per chiunque acceda ai file nel contenitore specificato del server di origine. Il token di firma di accesso condiviso non è invece necessario nell'endpoint della rete CDN a causa della regola di riscrittura URL.
 
1. Usare il [motore regole](./cdn-verizon-premium-rules-engine.md) per creare una regola di riscrittura URL. Potrebbero essere necessarie fino a 4 ore per la propagazione delle nuove regole.

   ![Pulsante Gestisci della rete CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Pulsante Motore regole della rete CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   La regola di riscrittura dell'URL di esempio seguente usa un modello di espressione regolare con un gruppo di acquisizione e un endpoint denominato *sasstoragedemo*:
   
   Origine:   
   `(container1/.*)`


   Destinazione:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regola di riscrittura URL della rete CDN - sinistra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![Regola di riscrittura URL della rete CDN - destra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Dopo l'attivazione della nuova regola, chiunque può accedere ai file nel contenitore specificato nell'endpoint della rete CDN indipendentemente dal fatto che venga usato o meno un token di firma di accesso condiviso nell'URL. Il formato è il seguente: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Ad esempio:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Specificare la durata della cache tramite le regole di memorizzazione nella cache o aggiungendo intestazioni `Cache-Control` al server di origine. Poiché la rete CDN di Azure considera il token di firma di accesso condiviso come una stringa di query semplice, la procedura consigliata prevede la configurazione della durata della cache con scadenza alla stessa ora di scadenza della firma di acceso condiviso o a un'ora antecedente. In caso contrario, se un file viene memorizzato nella cache per un periodo di tempo più lungo di quello in cui la firma di accesso condiviso è attiva, il file potrebbe essere accessibile dall'endpoint della rete CDN di Azure al termine della scadenza della firma di accesso condiviso. In questo caso, se si desidera rendere inaccessibile il file memorizzato nella cache, è necessario eseguire un'operazione di ripulitura sul file per cancellarlo dalla cache. Per informazioni sull'impostazione della durata della cache sulla rete CDN di Azure, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole di memorizzazione nella cache](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opzione 3: uso dell'autenticazione tramite token di sicurezza della rete CDN con una regola di riscrittura

Per usare l'autenticazione tramite token di sicurezza della rete CDN di Azure, è necessario disporre di un profilo di **rete CDN Premium di Azure fornita da Verizon**. Questa opzione è la più sicura e personalizzabile. L'accesso del client si basa sui parametri di sicurezza impostati nel token di sicurezza. Dopo che è stato creato e configurato, il token di sicurezza sarà necessario in tutti gli URL di endpoint della rete CDN. Il token di firma di accesso condiviso non è invece necessario nell'endpoint della rete CDN a causa della regola di riscrittura URL. Se il token di firma di accesso condiviso diventa in seguito non valido, la rete CDN di Azure non potrà più riconvalidare i contenuti del server di origine.

1. [Creare un token di sicurezza della rete CDN di Azure](./cdn-token-auth.md#setting-up-token-authentication) e attivarlo tramite il motore regole per l'endpoint della rete CDN e il percorso in cui gli utenti possono accedere al file.

   Un URL di endpoint con token di sicurezza ha il formato seguente:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Ad esempio:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Le opzioni dei parametri per l'autenticazione tramite token di sicurezza sono diverse rispetto a quelle per un token di firma di accesso condiviso. Se si sceglie di usare una scadenza quando si crea un token di sicurezza, è consigliabile impostarla sullo stesso valore di scadenza del token di firma di accesso condiviso, in modo da garantire che sia stimabile. 
 
2. Usare il [motore regole](./cdn-verizon-premium-rules-engine.md) per creare una regola di riscrittura dell'URL per abilitare l'accesso tramite token di firma di accesso condiviso a tutti i BLOB presenti nel contenitore. Potrebbero essere necessarie fino a 4 ore per la propagazione delle nuove regole.

   La regola di riscrittura dell'URL di esempio seguente usa un modello di espressione regolare con un gruppo di acquisizione e un endpoint denominato *sasstoragedemo*:
   
   Origine:   
   `(container1/.*)`
   
   Destinazione:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regola di riscrittura URL della rete CDN - sinistra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![Regola di riscrittura URL della rete CDN - destra](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Se si rinnova la firma di accesso condiviso, assicurarsi di aggiornare la regola di riscrittura URL con il nuovo token di firma di accesso condiviso. 

## <a name="sas-parameter-considerations"></a>Considerazioni sui parametri della firma di accesso condiviso

Poiché i parametri della firma di accesso condiviso non sono visibili alla rete CDN di Azure, quest'ultima non può modificare il proprio comportamento di distribuzione in base ad essi. Le restrizioni dei parametri definite si applicano solo alle richieste inviate dalla rete CDN di Azure al server di origine e non alle richieste inviate dal client alla rete CDN di Azure. È importante tenere presente questa distinzione quando si impostano i parametri della firma di accesso condiviso. Se queste funzionalità avanzate sono necessarie e si usa l'[opzione 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), impostare le restrizioni appropriate nel token di sicurezza della rete CDN di Azure.

| Nome del parametro della firma di accesso condiviso | Descrizione |
| --- | --- |
| Inizia | Ora in cui la rete CDN di Azure può iniziare ad accedere al file BLOB. A causa dello sfasamento del clock (quando un segnale di clock perviene in momenti diversi per i diversi componenti), scegliere un'ora con 15 minuti di anticipo se si vuole che l'asset sia immediatamente disponibile. |
| Fine | Ora dopo la quale la rete CDN di Azure non può più accedere al file BLOB. I file precedentemente memorizzati nella cache sulla rete CDN di Azure sono ancora accessibili. Per controllare l'ora di scadenza dei file, impostare l'ora di scadenza appropriata nel token di sicurezza della rete CDN di Azure o ripulire l'asset. |
| Indirizzi IP consentiti | Facoltativa. Se si usa la **rete CDN di Azure fornita da Verizon**, è possibile impostare questo parametro sugli intervalli definiti in [Rete CDN di Azure da intervalli di indirizzi IP per server perimetrali Verizon](./cdn-pop-list-api.md). Se si usa la **rete CDN di Azure fornita da Akamai**, non è possibile impostare il parametro degli intervalli IP poiché gli indirizzi IP non sono statici.|
| Protocolli consentiti | Uno o più protocolli consentiti per una richiesta effettuata con la firma di acceso condiviso dell'account. È consigliabile usare l'impostazione HTTPS.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla firma di accesso condiviso, vedere gli articoli seguenti:
- [Uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md)
- [Firme di accesso condiviso, parte 2: Creare e usare una firma di accesso condiviso con l'archiviazione BLOB](../storage/common/storage-sas-overview.md)

Per altre informazioni sulla configurazione dell'autenticazione basata su token, vedere [Protezione di asset della rete per la distribuzione di contenuti (CDN) di Azure con l'autenticazione basata su token](./cdn-token-auth.md).