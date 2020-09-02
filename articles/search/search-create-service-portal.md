---
title: Creare un servizio di ricerca nel portale
titleSuffix: Azure Cognitive Search
description: Questo argomento di avvio rapido sul portale illustra come creare una risorsa Ricerca cognitiva di Azure nel portale di Azure. Scegliere gruppi di risorse, aree e SKU o piano tariffario.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 6813db0b7416695fb9433a701114f2684dd0e122
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927120"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Avvio rapido: Creare un servizio Ricerca cognitiva di Azure nel portale

Ricerca cognitiva di Azure è una risorsa autonoma usata per aggiungere un'esperienza di ricerca alle app personalizzate. Ricerca cognitiva di Azure si integra facilmente con altri servizi di Azure, con le app dei server di rete o con il software in esecuzione in altre piattaforme cloud.

In questo articolo verrà illustrato come creare una risorsa nel [portale di Azure](https://portal.azure.com/).

[![GIF animata](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Se si preferisce PowerShell, usare il [modello di servizio](https://azure.microsoft.com/resources/templates/101-azure-search-create/) di Azure Resource Manager. Per informazioni su come iniziare, vedere [Gestire il servizio Ricerca cognitiva di Azure con PowerShell](search-manage-powershell.md).

## <a name="before-you-start"></a>Prima di iniziare

Le proprietà seguenti sono fisse per la durata del servizio e per modificarne una qualsiasi di esse è richiesto un nuovo servizio. Dal momento che sono fisse, valutare le implicazioni di utilizzo seguenti quando si inseriscono le singole proprietà:

* Il nome del servizio diventa parte dell'endpoint dell'URL ([vedere i suggerimenti](#name-the-service) per i nomi di servizio utili).
* Il livello di servizio [influisce sulla fatturazione](search-sku-tier.md) e imposta un limite massimo per la capacità. Alcune funzionalità non sono disponibili nel livello gratuito.
* L'area del servizio può determinare la disponibilità di determinati scenari. Se servono [funzionalità con livelli di sicurezza elevati](search-security-overview.md) o [arricchimento tramite intelligenza artificiale](cognitive-search-concept-intro.md), sarà necessario posizionare Ricerca cognitiva di Azure nella stessa area degli altri servizi o in aree che offrono la funzionalità in questione. 

## <a name="subscribe-free-or-paid"></a>Sottoscrizione gratuita o a pagamento

[Aprire un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e usare i crediti per provare i servizi di Azure a pagamento. Dopo avere consumato i crediti, mantenere l'account e continuare a usare i servizi di Azure gratuiti, ad esempio Siti Web. Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.

In alternativa, [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

## <a name="find-azure-cognitive-search"></a>Trovare Ricerca cognitiva di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Fare clic sul segno più ("+ Crea risorsa") nell'angolo superiore sinistro.

1. Usare la barra di ricerca per trovare "Ricerca cognitiva di Azure" o passare alla risorsa selezionando **Web** > **Ricerca cognitiva di Azure**.

![Creare una risorsa nel portale](./media/search-create-service-portal/find-search3.png "Creare una risorsa nel portale")

## <a name="choose-a-subscription"></a>Scegliere una sottoscrizione.

Se sono disponibili più sottoscrizioni, sceglierne una per il servizio di ricerca. Se si sta implementando la [crittografia doppia](search-security-overview.md#double-encryption) o altre funzionalità che dipendono da identità di servizio gestite, scegliere la stessa sottoscrizione usata per Azure Key Vault o altri servizi per cui vengono usate le identità gestite.

## <a name="set-a-resource-group"></a>Impostare un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per la soluzione Azure. È obbligatorio per il servizio di ricerca. È utile anche per la gestione a livello globale delle risorse, inclusi i costi. Un gruppo di risorse può essere costituito da un servizio o da più servizi usati in combinazione. Se, ad esempio, si usa Ricerca cognitiva di Azure per l'indicizzazione di un database di Azure Cosmos DB, è possibile inserire i due servizi nello stesso gruppo di risorse per agevolarne la gestione. 

Se non si combinano le risorse in un singolo gruppo o se i gruppi di risorse esistenti sono riempiti con risorse usate in soluzioni non correlate, creare un nuovo gruppo di risorse solo per la risorsa Ricerca cognitiva di Azure. 

![Creare un nuovo gruppo di risorse](./media/search-create-service-portal/new-resource-group.png "Creare un nuovo gruppo di risorse")

Nel tempo, è possibile tenere traccia dei costi correnti e previsti a livello globale oppure visualizzare i costi per le singole risorse. Lo screenshot seguente mostra il tipo di informazioni sui costi che è possibile prevedere quando si combinano più risorse in un unico gruppo.

![Gestire i costi a livello di gruppo di risorse](./media/search-create-service-portal/resource-group-cost-management.png "Gestire i costi a livello di gruppo di risorse")

> [!TIP]
> I gruppi di risorse semplificano la pulizia, in quanto eliminando un gruppo verranno eliminati tutti i servizi al suo interno. Per i progetti prototipo che usano più servizi, l'inserimento di tutti gli elementi nello stesso gruppo di risorse ne semplifica l'eliminazione al termine del progetto.

## <a name="name-the-service"></a>Assegnare un nome al servizio

In Dettagli istanza fornire un nome per il servizio nel campo **URL**. Questo nome fa parte dell'endpoint dell'URL in cui vengono eseguite le chiamate API: `https://your-service-name.search.windows.net`. Se ad esempio si vuole che l'endpoint sia `https://myservice.search.windows.net`, immettere `myservice`.

Requisiti per i nomi di servizio:

* Deve essere univoco all'interno dello spazio dei nomi search.windows.net
* Deve avere una lunghezza compresa tra 2 e 60 caratteri
* È necessario usare lettere minuscole, cifre o trattini ("-")
* Non usare trattini ("-") nei primi 2 caratteri o come ultimo carattere
* Non è possibile usare trattini consecutivi ("--") indipendentemente dalla posizione

> [!TIP]
> Se si prevede di usare più servizi, è consigliabile includere l'area o la località nel nome del servizio come convenzione di denominazione. I servizi all'interno della stessa area possono scambiare dati gratuitamente, quindi se Ricerca cognitiva di Azure si trova nell'area Stati Uniti occidentali e in quest'area sono presenti altri servizi, un nome come `mysearchservice-westus` può consentire di evitare di dover visualizzare la pagina delle proprietà per decidere come combinare o collegare le risorse.

## <a name="choose-a-location"></a>Scegliere una posizione

Ricerca cognitiva di Azure è disponibile nella maggior parte delle aree. L'elenco delle aree supportate è disponibile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

> [!Note]
> Le aree India centrale e Emirati Arabi Uniti settentrionali non sono attualmente disponibili per i nuovi servizi. Per i servizi già presenti in tali aree, è possibile aumentare le risorse senza alcuna restrizione e il servizio è completamente supportato. Le restrizioni sono temporanee e limitate solo ai nuovi servizi. Quando le restrizione non saranno più applicabili, questa nota verrà rimossa.
>
> La crittografia doppia è disponibile solo in determinate aree. Per altre informazioni, vedere [Crittografia doppia](search-security-overview.md#double-encryption).

### <a name="requirements"></a>Requisiti

 Se si usano arricchimenti tramite intelligenza artificiale, creare il servizio di ricerca nella stessa area di Servizi cognitivi. *Per l'arricchimento tramite intelligenza artificiale, Ricerca cognitiva di Azure e Servizi cognitivi devono trovarsi nella stessa area*.

 I clienti con requisiti di continuità aziendale e ripristino di emergenza dovranno creare i servizi in [coppie di aree](../best-practices-availability-paired-regions.md#azure-regional-pairs). Se ad esempio si opera in America del Nord, è possibile scegliere Stati Uniti orientali e Stati Uniti occidentali oppure Stati Uniti centro-settentrionali e Stati Uniti centro-meridionali per ogni servizio.

### <a name="recommendations"></a>Consigli

Se si usano più servizi di Azure, scegliere un'area che ospita anche i dati o il servizio dell'applicazione. In questo modo si riducono al minimo o si evitano gli addebiti per la larghezza di banda dei dati in uscita (non sono previsti addebiti per i dati in uscita se i servizi si trovano nella stessa area).

## <a name="choose-a-pricing-tier-sku"></a>Scegliere un piano tariffario (SKU)

[Ricerca cognitiva di Azure attualmente è disponibile con vari piani tariffari](https://azure.microsoft.com/pricing/details/search/): Gratuito, Basic o Standard. Ogni piano tariffario prevede una specifica [capacità e limiti](search-limits-quotas-capacity.md). Per indicazioni, vedere [Scegliere uno SKU o un piano tariffario per Ricerca di Azure](search-sku-tier.md) .

Per carichi di lavoro di produzione viene in genere scelto il piano Basic o Standard, ma la maggior parte dei clienti inizia con il servizio gratuito. Le differenze principali tra i livelli sono la velocità e le dimensioni della partizione, nonché i limiti per il numero di oggetti che è possibile creare.

Tenere presente che non è possibile modificare il piano tariffario dopo aver creato il servizio. Se si vuole passare a un piano tariffario superiore o inferiore, è necessario creare nuovamente il servizio.

## <a name="create-your-service"></a>Creare il servizio

Dopo aver fornito gli input necessari, procedere con la creazione del servizio. 

![Rivedere e creare il servizio](./media/search-create-service-portal/new-service3.png "Rivedere e creare il servizio")

Il servizio viene distribuito entro pochi minuti. È possibile monitorare lo stato di avanzamento tramite le notifiche di Azure. Aggiungere il servizio al dashboard per accedervi facilmente in futuro.

![Monitorare e aggiungere il servizio](./media/search-create-service-portal/monitor-notifications.png "Monitorare e aggiungere il servizio")

## <a name="get-a-key-and-url-endpoint"></a>Ottenere una chiave e un endpoint dell'URL

A meno che non si usi il portale, per accedere al nuovo servizio a livello di codice è necessario specificare l'endpoint dell'URL e una chiave API di autenticazione.

1. Nella pagina **Panoramica** individuare e copiare l'endpoint dell'URL visualizzato a destra.

2. Nella pagina **Chiavi** copiare una delle chiavi di amministrazione (equivalenti). Le chiavi API di amministrazione sono necessarie per creare, aggiornare ed eliminare oggetti nel servizio. Al contrario, le chiavi di query forniscono l'accesso in lettura al contenuto dell'indice.

   ![Pagina Panoramica del servizio con endpoint dell'URL](./media/search-create-service-portal/get-url-key.png "Endpoint dell'URL e altri dettagli del servizio")

Per le attività basate sul portale, l'endpoint e la chiave non sono necessari. Il portale è già collegato alla risorsa di Ricerca cognitiva di Azure con diritti amministrativi. Per una procedura dettagliata per il portale, iniziare con [Avvio rapido: Creare un indice di Ricerca cognitiva di Azure nel portale](search-get-started-portal.md).

## <a name="scale-your-service"></a>Ridimensionare il servizio

Al termine del provisioning del servizio, è possibile ridimensionare il servizio per adattarlo alle proprie esigenze. Se è stato scelto il piano tariffario Standard per il servizio Ricerca cognitiva di Azure, è possibile ridimensionare il servizio in due dimensioni, ovvero partizioni e repliche. Se fosse stato scelto il piano Basic, sarebbe stato possibile aggiungere solo le repliche. Se è stato effettuato il provisioning del servizio Gratuito, la scalabilità non è disponibile.

Le ***partizioni*** consentono di archiviare e di eseguire ricerche in un numero maggiore di documenti nel servizio.

***Repliche*** consentire al servizio gestire un carico superiore di query di ricerca.

L'aggiunta di risorse fa aumentare la fattura mensile. Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) consente di capire le ramificazioni della fattura dovute all'aggiunta di risorse. Tenere presente che è possibile modificare le risorse in base al carico. Ad esempio, è possibile aumentare le risorse per creare un indice iniziale completo e ridurle successivamente a un livello più appropriato per l'indicizzazione incrementale.

> [!Important]
> Un servizio deve disporre di [2 repliche per ogni contratto di servizio di sola lettura e 3 repliche per ogni contratto di servizio di lettura/scrittura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Passare alla pagina del servizio di ricerca nel portale di Azure.
2. Nel riquadro di navigazione a sinistra selezionare **Impostazioni** > **Scalabilità**.
3. Usare la barra di scorrimento per aggiungere risorse di entrambi i tipi.

![Aggiungere capacità](./media/search-create-service-portal/settings-scale.png "Aggiungere capacità tramite repliche e partizioni")

> [!Note]
> A livelli superiori la velocità e lo spazio di archiviazione per partizione aumentano. Per altre informazioni, vedere l'articolo su [capacità e limiti](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Aggiunta di un secondo servizio

La maggior parte dei clienti usa un solo servizio su cui esegue il provisioning a un livello che offre il [giusto equilibrio di risorse](search-sku-tier.md). Un servizio può ospitare più indici, soggetto ai [limiti massimi del livello selezionato](search-capacity-planning.md), con ciascun indice isolato dall'altro. In Ricerca cognitiva di Azure le richieste possono essere indirizzate solo a un indice, riducendo al minimo le probabilità di recupero di dati accidentale o intenzionale da altri indici nello stesso servizio.

Sebbene la maggior parte dei clienti usi un solo servizio, la ridondanza del servizio potrebbe essere necessaria se i requisiti operativi includono i seguenti elementi:

+ [Continuità aziendale e ripristino di emergenza](../best-practices-availability-paired-regions.md). Ricerca cognitiva di Azure non offre il failover immediato in caso di interruzione.

+ Le [architetture multi-tenant](search-modeling-multitenant-saas-applications.md) talvolta richiedono due o più servizi.

+ Le applicazioni distribuite a livello globale potrebbero richiedere servizi di ricerca in ogni area geografica per ridurre al minimo la latenza.

> [!NOTE]
> In Ricerca cognitiva di Azure non è possibile isolare le operazioni di indicizzazione e query, quindi non devono essere creati più servizi per i carichi di lavoro isolati. Per un indice viene sempre eseguita una query sul servizio in cui è stato creato (non è possibile creare un indice in un servizio e copiarlo in un altro).

Non è necessario un secondo servizio per la disponibilità elevata. La disponibilità elevata per le query si raggiunge quando si usano 2 o più repliche nello stesso servizio. Gli aggiornamenti di replica sono sequenziali, il che significa che almeno uno è operativo quando viene implementato un aggiornamento del servizio. Per altre informazioni sul tempo di attività, vedere i [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passaggi successivi

Dopo il provisioning di un servizio, è possibile continuare nel portale con la creazione del primo indice.

> [!div class="nextstepaction"]
> [Avvio rapido: Creare un indice di Ricerca cognitiva di Azure nel portale](search-get-started-portal.md)