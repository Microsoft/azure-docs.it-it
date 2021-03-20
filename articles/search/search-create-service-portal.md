---
title: Creare un servizio di ricerca nel portale
titleSuffix: Azure Cognitive Search
description: Questo argomento di avvio rapido sul portale illustra come creare una risorsa Ricerca cognitiva di Azure nel portale di Azure. Scegliere gruppi di risorse, aree e SKU o piano tariffario.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: e183d81355d4db81e677f34b02330ddb9b631957
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651987"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Avvio rapido: Creare un servizio Ricerca cognitiva di Azure nel portale

[Azure ricerca cognitiva](search-what-is-azure-search.md) è una risorsa di Azure usata per l'aggiunta di un'esperienza di ricerca full-text alle app personalizzate. Puoi integrarlo facilmente con altri servizi di Azure che forniscono dati o elaborazione aggiuntiva, con app nei server di rete o con il software in esecuzione su altre piattaforme cloud.

È possibile creare il servizio di ricerca usando il [portale di Azure](https://portal.azure.com/), che viene trattato in questo articolo. È anche possibile usare [Azure PowerShell](search-manage-powershell.md), l'interfaccia della riga di comando di [Azure](/cli/azure/search), l' [API REST di gestione](/rest/api/searchmanagement/)o un [modello di servizio Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![GIF animata](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Prima di iniziare

Le proprietà seguenti sono fisse per la durata del servizio e per modificarne una qualsiasi di esse è richiesto un nuovo servizio. Dal momento che sono fisse, valutare le implicazioni di utilizzo seguenti quando si inseriscono le singole proprietà:

+ Il nome del servizio diventa parte dell'endpoint dell'URL ([vedere i suggerimenti](#name-the-service) per i nomi di servizio utili).
+ Il [livello di servizio](search-sku-tier.md) influisca sulla fatturazione e imposta un limite crescente per la capacità. Alcune funzionalità non sono disponibili nel livello gratuito.
+ L'area del servizio può determinare la disponibilità di determinati scenari. Se sono necessarie [funzionalità di sicurezza elevata](search-security-overview.md) o l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md), sarà necessario creare ricerca cognitiva di Azure nella stessa area di altri servizi o in aree che forniscono la funzionalità in questione. 

## <a name="subscribe-free-or-paid"></a>Sottoscrizione gratuita o a pagamento

[Aprire un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e usare i crediti per provare i servizi di Azure a pagamento. Dopo avere consumato i crediti, mantenere l'account e continuare a usare i servizi di Azure gratuiti, ad esempio Siti Web. Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.

In alternativa, [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

## <a name="find-azure-cognitive-search"></a>Trovare Ricerca cognitiva di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Fare clic sul segno più (**"+ Crea risorsa"**) nell'angolo superiore sinistro.

1. Usare la barra di ricerca per trovare "Ricerca cognitiva di Azure" o passare alla risorsa selezionando **Web** > **Ricerca cognitiva di Azure**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Creare una risorsa nel portale" border="false":::

## <a name="choose-a-subscription"></a>Scegliere una sottoscrizione.

Se sono disponibili più sottoscrizioni, sceglierne una per il servizio di ricerca. Se si sta implementando la [crittografia doppia](search-security-overview.md#double-encryption) o altre funzionalità che dipendono da identità di servizio gestite, scegliere la stessa sottoscrizione usata per Azure Key Vault o altri servizi per cui vengono usate le identità gestite.

## <a name="set-a-resource-group"></a>Impostare un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per la soluzione Azure. È obbligatorio per il servizio di ricerca. È utile anche per la gestione a livello globale delle risorse, inclusi i costi. Un gruppo di risorse può essere costituito da un servizio o da più servizi usati in combinazione. Se, ad esempio, si usa Ricerca cognitiva di Azure per l'indicizzazione di un database di Azure Cosmos DB, è possibile inserire i due servizi nello stesso gruppo di risorse per agevolarne la gestione. 

Se non si combinano le risorse in un singolo gruppo o se i gruppi di risorse esistenti sono riempiti con risorse usate in soluzioni non correlate, creare un nuovo gruppo di risorse solo per la risorsa Ricerca cognitiva di Azure. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Creare un nuovo gruppo di risorse" border="false":::

Nel tempo, è possibile tenere traccia dei costi correnti e previsti a livello globale oppure visualizzare i costi per le singole risorse. Lo screenshot seguente mostra il tipo di informazioni sui costi che è possibile prevedere quando si combinano più risorse in un unico gruppo.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Gestire i costi a livello di gruppo di risorse" border="false":::

> [!TIP]
> I gruppi di risorse semplificano la pulizia, in quanto eliminando un gruppo verranno eliminati tutti i servizi al suo interno. Per i progetti prototipo che usano più servizi, l'inserimento di tutti gli elementi nello stesso gruppo di risorse ne semplifica l'eliminazione al termine del progetto.

## <a name="name-the-service"></a>Assegnare un nome al servizio

In Dettagli istanza fornire un nome per il servizio nel campo **URL**. Questo nome fa parte dell'endpoint dell'URL in cui vengono eseguite le chiamate API: `https://your-service-name.search.windows.net`. Se ad esempio si vuole che l'endpoint sia `https://myservice.search.windows.net`, immettere `myservice`.

Requisiti per i nomi di servizio:

+ Deve essere univoco all'interno dello spazio dei nomi search.windows.net
+ Deve avere una lunghezza compresa tra 2 e 60 caratteri
+ È necessario usare lettere minuscole, cifre o trattini ("-")
+ Non usare trattini ("-") nei primi 2 caratteri o come ultimo carattere
+ Non è possibile usare trattini consecutivi ("--") indipendentemente dalla posizione

> [!TIP]
> Se si prevede di usare più servizi, è consigliabile includere l'area o la località nel nome del servizio come convenzione di denominazione. I servizi all'interno della stessa area possono scambiare dati gratuitamente, quindi se Ricerca cognitiva di Azure si trova nell'area Stati Uniti occidentali e in quest'area sono presenti altri servizi, un nome come `mysearchservice-westus` può consentire di evitare di dover visualizzare la pagina delle proprietà per decidere come combinare o collegare le risorse.

## <a name="choose-a-location"></a>Scegliere una posizione

Azure ricerca cognitiva è disponibile nella maggior parte delle aree geografiche, come documentato nei [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=search). In genere, se si usano più servizi di Azure, scegliere un'area che ospita anche i dati o il servizio dell'applicazione. In questo modo si riducono al minimo o si evitano gli addebiti per la larghezza di banda dei dati in uscita (non sono previsti addebiti per i dati in uscita se i servizi si trovano nella stessa area).

+ Per l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) è necessario che i servizi cognitivi si trovino nella stessa area fisica del ricerca cognitiva di Azure. Esistono solo alcune aree che non forniscono entrambe. La pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=search) indica una doppia disponibilità visualizzando due segni di spunta in pila. Una combinazione non disponibile presenta un segno di spunta mancante:

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="Disponibilità a livello di area" border="true":::

+ I requisiti per la continuità aziendale e il ripristino di emergenza (BCDR) devono essere soddisfatti creando più servizi di ricerca nelle [coppie internazionali](../best-practices-availability-paired-regions.md#azure-regional-pairs). Se ad esempio si opera in America del Nord, è possibile scegliere Stati Uniti orientali e Stati Uniti occidentali o Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, per ogni servizio di ricerca.

Alcune funzionalità hanno una disponibilità limitata basata sulle aree. Le restrizioni sono descritte nella documentazione delle funzionalità:

+ [Crittografia doppia](search-security-overview.md#double-encryption)

+ ["Zone di disponibilità" per la scalabilità delle prestazioni](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Scegliere un piano tariffario

Ricerca cognitiva di Azure attualmente è disponibile con [vari piani tariffari](https://azure.microsoft.com/pricing/details/search/): Gratuito, Basic, Standard o con ottimizzazione per l'archiviazione. Ogni piano tariffario prevede una specifica [capacità e limiti](search-limits-quotas-capacity.md). Per indicazioni, vedere [Scegliere un piano tariffario](search-sku-tier.md).

Per carichi di lavoro di produzione viene in genere scelto il piano Basic o Standard, ma la maggior parte dei clienti inizia con il servizio gratuito. Le differenze principali tra i livelli sono la velocità e le dimensioni della partizione, nonché i limiti per il numero di oggetti che è possibile creare.

Tenere presente che non è possibile modificare il piano tariffario dopo aver creato il servizio. Se si vuole passare a un piano tariffario superiore o inferiore, è necessario creare nuovamente il servizio.

## <a name="create-your-service"></a>Creare il servizio

Dopo aver fornito gli input necessari, procedere con la creazione del servizio. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Rivedere e creare il servizio" border="false":::

Il servizio viene distribuito entro pochi minuti. È possibile monitorare lo stato di avanzamento tramite le notifiche di Azure. Aggiungere il servizio al dashboard per accedervi facilmente in futuro.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitorare e aggiungere il servizio" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Ottenere una chiave e un endpoint dell'URL

A meno che non si usi il portale, per accedere al nuovo servizio a livello di codice è necessario specificare l'endpoint dell'URL e una chiave API di autenticazione.

1. Nella pagina **Panoramica** individuare e copiare l'endpoint dell'URL visualizzato a destra.

1. Nella pagina **Chiavi** copiare una delle chiavi di amministrazione (equivalenti). Le chiavi API di amministrazione sono necessarie per creare, aggiornare ed eliminare oggetti nel servizio. Al contrario, le chiavi di query forniscono l'accesso in lettura al contenuto dell'indice.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Pagina Panoramica del servizio con endpoint dell'URL" border="false":::

Per le attività basate sul portale, l'endpoint e la chiave non sono necessari. Il portale è già collegato alla risorsa di Ricerca cognitiva di Azure con diritti amministrativi. Per una procedura dettagliata per il portale, iniziare con [Avvio rapido: Creare un indice di Ricerca cognitiva di Azure nel portale](search-get-started-portal.md).

## <a name="scale-your-service"></a>Ridimensionare il servizio

Al termine del provisioning del servizio, è possibile ridimensionare il servizio per adattarlo alle proprie esigenze. Se è stato scelto il piano tariffario Standard per il servizio Ricerca cognitiva di Azure, è possibile ridimensionare il servizio in due dimensioni, ovvero partizioni e repliche. Se fosse stato scelto il piano Basic, sarebbe stato possibile aggiungere solo le repliche. Se è stato effettuato il provisioning del servizio Gratuito, la scalabilità non è disponibile.

Le ***partizioni*** consentono di archiviare e di eseguire ricerche in un numero maggiore di documenti nel servizio.

***Repliche*** consentire al servizio gestire un carico superiore di query di ricerca.

L'aggiunta di risorse fa aumentare la fattura mensile. Il [calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/) consente di capire le ramificazioni della fattura dovute all'aggiunta di risorse. Tenere presente che è possibile modificare le risorse in base al carico. Ad esempio, è possibile aumentare le risorse per creare un indice iniziale completo e ridurle successivamente a un livello più appropriato per l'indicizzazione incrementale.

> [!Important]
> Un servizio deve disporre di [2 repliche per ogni contratto di servizio di sola lettura e 3 repliche per ogni contratto di servizio di lettura/scrittura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Passare alla pagina del servizio di ricerca nel portale di Azure.
1. Nel riquadro di navigazione a sinistra selezionare **Impostazioni** > **Scalabilità**.
1. Usare la barra di scorrimento per aggiungere risorse di entrambi i tipi.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Aggiungere capacità tramite repliche e partizioni" border="false":::

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

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)