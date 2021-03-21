---
title: Procedure consigliate per la configurazione di app Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate durante l'uso di app Azure configurazione. Gli argomenti trattati includono raggruppamenti chiave, composizioni chiave-valore, bootstrap configurazione app e altro ancora.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371550"
---
# <a name="azure-app-configuration-best-practices"></a>Procedure consigliate per la configurazione di app Azure

Questo articolo illustra i modelli comuni e le procedure consigliate quando si usa la configurazione di app Azure.

## <a name="key-groupings"></a>Raggruppamenti di chiavi

La configurazione dell'app offre due opzioni per organizzare le chiavi:

* Prefissi di chiave
* Etichette

È possibile usare una o entrambe le opzioni per raggruppare le chiavi.

I *prefissi di chiave* sono le parti iniziali delle chiavi. È possibile raggruppare logicamente un set di chiavi usando lo stesso prefisso nei rispettivi nomi. I prefissi possono contenere più componenti connessi da un delimitatore, ad esempio, in modo `/` simile a un percorso URL, per formare uno spazio dei nomi. Queste gerarchie sono utili quando si archiviano chiavi per molte applicazioni, Servizi componenti e ambienti in un archivio di configurazione dell'app.

Un aspetto importante da tenere presente è che le chiavi sono i riferimenti del codice dell'applicazione per recuperare i valori delle impostazioni corrispondenti. Le chiavi non devono essere modificate, altrimenti sarà necessario modificare il codice ogni volta che si verifica.

Le *etichette* sono un attributo delle chiavi. Sono usati per creare varianti di una chiave. Ad esempio, è possibile assegnare etichette a più versioni di una chiave. Una versione potrebbe essere un'iterazione, un ambiente o altre informazioni contestuali. L'applicazione può richiedere un set completamente diverso di valori di chiave specificando un'altra etichetta. Di conseguenza, tutti i riferimenti alla chiave rimangono invariati nel codice.

## <a name="key-value-compositions"></a>Composizioni chiave-valore

La configurazione dell'app considera tutte le chiavi archiviate come entità indipendenti. La configurazione dell'app non tenta di dedurre alcuna relazione tra le chiavi o di ereditare i valori delle chiavi in base alla gerarchia. È possibile aggregare più insiemi di chiavi, tuttavia, usando etichette abbinate al corretto stack di configurazione nel codice dell'applicazione.

Di seguito è descritto un esempio. Si supponga di avere un'impostazione denominata **Asset1**, il cui valore può variare in base all'ambiente di sviluppo. Si crea una chiave denominata "Asset1" con un'etichetta vuota e un'etichetta denominata "Development". Nella prima etichetta si inserisce il valore predefinito per **Asset1** e si inserisce un valore specifico per "Development" nel secondo.

Nel codice è necessario innanzitutto recuperare i valori della chiave senza alcuna etichetta e quindi recuperare la stessa serie di valori di chiave una seconda volta con l'etichetta "Development". Quando si recuperano i valori la seconda volta, i valori precedenti delle chiavi vengono sovrascritti. Il sistema di configurazione di .NET Core consente di "stack" più set di dati di configurazione uno sull'altro. Se una chiave è presente in più di un set, viene usato l'ultimo set che lo contiene. Con un Framework di programmazione moderno, ad esempio .NET Core, questa funzionalità di stacking è disponibile gratuitamente se si usa un provider di configurazione nativo per accedere alla configurazione dell'app. Il frammento di codice seguente mostra come è possibile implementare lo stack in un'applicazione .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Usare le etichette per abilitare configurazioni diverse per ambienti diversi](./howto-labels-aspnet-core.md) fornisce un esempio completo.

## <a name="app-configuration-bootstrap"></a>Bootstrap configurazione app

Per accedere a un archivio di configurazione dell'app, è possibile usare la relativa stringa di connessione, disponibile nel portale di Azure. Poiché le stringhe di connessione contengono informazioni sulle credenziali, sono considerate segreti. Questi segreti devono essere archiviati in Azure Key Vault e il codice deve eseguire l'autenticazione a Key Vault per recuperarli.

Un'opzione migliore consiste nell'usare la funzionalità identità gestite in Azure Active Directory. Con le identità gestite è necessario solo l'URL dell'endpoint di configurazione dell'app per avviare l'accesso all'archivio di configurazione dell'app. È possibile incorporare l'URL nel codice dell'applicazione (ad esempio, nella *appsettings.jssu* file). Per informazioni dettagliate, vedere l' [integrazione con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Accesso all'app o alla funzione per la configurazione dell'app

È possibile fornire l'accesso alla configurazione dell'app per le app Web o le funzioni usando uno dei metodi seguenti:

* Tramite il portale di Azure, immettere la stringa di connessione all'archivio di configurazione dell'app nelle impostazioni dell'applicazione del servizio app.
* Archiviare la stringa di connessione nell'archivio di configurazione dell'app in Key Vault e [farvi riferimento dal servizio app](../app-service/app-service-key-vault-references.md).
* Usare le identità gestite di Azure per accedere all'archivio di configurazione dell'app. Per altre informazioni, vedere [integrazione con le identità gestite di Azure](howto-integrate-azure-managed-service-identity.md).
* Eseguire il push della configurazione dalla configurazione dell'app al servizio app. La configurazione dell'app fornisce una funzione di esportazione (in portale di Azure e l'interfaccia della riga di comando di Azure) che invia i dati direttamente nel servizio app. Con questo metodo non è necessario modificare il codice dell'applicazione.

## <a name="reduce-requests-made-to-app-configuration"></a>Ridurre le richieste effettuate alla configurazione dell'app

Richieste eccessive alla configurazione dell'app possono comportare una limitazione o addebiti in eccedenza. Per ridurre il numero di richieste effettuate:

* Aumentare il timeout di aggiornamento, soprattutto se i valori di configurazione non cambiano di frequente. Specificare un nuovo timeout di aggiornamento usando il [ `SetCacheExpiration` Metodo](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Osservare una singola *chiave Sentinel*, anziché osservare le singole chiavi. Aggiornare tutte le configurazioni solo se la chiave Sentinel viene modificata. Per un esempio, vedere [usare la configurazione dinamica in un'app ASP.NET Core](enable-dynamic-configuration-aspnet-core.md) .

* Usare griglia di eventi di Azure per ricevere notifiche in caso di modifiche alla configurazione, anziché eseguire costantemente il polling delle modifiche. Per altre informazioni, vedere [Route app Azure Configuration Events to a Web endpoint](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importazione dei dati di configurazione nella configurazione dell'app

La configurazione dell'app offre la possibilità di [importare](./howto-import-export-data.md) in blocco le impostazioni di configurazione dai file di configurazione correnti usando il portale di Azure o l'interfaccia della riga di comando. È anche possibile usare le stesse opzioni per esportare i valori dalla configurazione dell'app, ad esempio tra archivi correlati. Se si vuole configurare una sincronizzazione continuativa con il repository GitHub, è possibile usare l' [azione GitHub](./concept-github-action.md) per poter continuare a usare le procedure di controllo del codice sorgente esistenti sfruttando al contempo i vantaggi della configurazione dell'app.

## <a name="multi-region-deployment-in-app-configuration"></a>Distribuzione in più aree nella configurazione dell'app

La configurazione dell'app è servizio a livello di area. Per le applicazioni con diverse configurazioni per area, l'archiviazione di queste configurazioni in un'istanza può creare un singolo punto di errore. La distribuzione di una sola istanza di configurazione dell'app per area geografica in più aree può essere un'opzione migliore. Può essere utile per il ripristino di emergenza a livello di area, le prestazioni e il silo di sicurezza. La configurazione in base all'area migliora anche la latenza e USA quote di limitazione separate, poiché la limitazione è per istanza. Per applicare la mitigazione del ripristino di emergenza, è possibile usare [più archivi di configurazione](./concept-disaster-recovery.md). 

## <a name="client-applications-in-app-configuration"></a>Applicazioni client nella configurazione dell'app 

Richieste eccessive alla configurazione dell'app possono comportare una limitazione o addebiti in eccedenza. Le applicazioni sfruttano i vantaggi della memorizzazione nella cache e dell'aggiornamento intelligente attualmente disponibile per ottimizzare il numero di richieste inviate. È possibile eseguire il mirroring di questo processo in applicazioni client a volume elevato evitando connessioni dirette all'archivio di configurazione. Al contrario, le applicazioni client si connettono a un servizio personalizzato e questo servizio comunica con l'archivio di configurazione. Questa soluzione proxy può garantire che le applicazioni client non affrontino il limite di limitazione nell'archivio di configurazione. Per ulteriori informazioni sulla limitazione, vedere [le domande frequenti](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration).  

## <a name="next-steps"></a>Passaggi successivi

* [Chiavi e valori](./concept-key-value.md)