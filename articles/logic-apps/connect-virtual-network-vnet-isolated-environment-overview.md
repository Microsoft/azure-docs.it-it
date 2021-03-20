---
title: Accedere alle reti virtuali di Azure
description: Panoramica su come gli ambienti di Integration Services (ISEs) consentono alle app per la logica di accedere alle reti virtuali di Azure (reti virtuali)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 4d83609eea57c2350881360ef757b1a291627c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374729"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

A volte, le app per la logica devono accedere alle risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, che sono all'interno o connesse a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per configurare questo accesso, è possibile [creare un *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE è un'istanza del servizio app per la logica che usa risorse dedicate e viene eseguita separatamente dal servizio "globale" di app per la logica multi-tenant. I dati in un ISE rimangono nella [stessa area in cui si crea e si distribuisce ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

Ad esempio, alcune reti virtuali di Azure usano endpoint privati, che è possibile configurare tramite il [collegamento privato di Azure](../private-link/private-link-overview.md), per fornire l'accesso ai servizi PaaS di Azure, ad esempio archiviazione di azure, Azure Cosmos DB o database SQL di Azure, servizi partner o servizi clienti ospitati in Azure. Se le app per la logica necessitano dell'accesso alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di ISE.

Quando si crea un ISE, Azure lo *inserisce* o lo distribuisce nella rete virtuale di Azure. È quindi possibile usare questo ISE come posizione per le app per la logica e gli account di integrazione che richiedono l'accesso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Questa panoramica offre altre informazioni sui [motivi per cui si vuole usare ISE](#benefits), le [differenze tra il servizio app per la logica dedicato e multi-tenant](#difference)e come è possibile accedere direttamente alle risorse che si trovano all'interno o alla connessione alla rete virtuale di Azure.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Perché usare ISE?

L'esecuzione di app per la logica in un'istanza dedicata separata consente di ridurre il potenziale impatto degli altri tenant di Azure sulle prestazioni delle app nel cosiddetto [effetto "noisy neighbor"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre inoltre questi vantaggi:

* Accesso diretto alle risorse che si trovano all'interno o alla connessione alla rete virtuale

  Le app per la logica create ed eseguite in ISE possono usare [connettori appositamente progettati che vengono eseguiti in ISE](../connectors/apis-list.md#ise-connectors). Se è presente un connettore ISE per un sistema locale o un'origine dati, è possibile connettersi direttamente senza dover usare il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Per ulteriori informazioni, vedere [dedicato rispetto a multi-tenant](#difference) e [accesso ai sistemi locali](#on-premises) più avanti in questo argomento.

* Accesso continuo a risorse esterne o non connesse alla rete virtuale

  Le app per la logica create ed eseguite in un ISE possono comunque usare i connettori eseguiti nel servizio app per la logica multi-tenant quando non è disponibile un connettore specifico di ISE. Per ulteriori informazioni, vedere [dedicato rispetto a multi-tenant](#difference).

* Disponibilità di indirizzi IP statici separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio multi-tenant. È anche possibile configurare un unico indirizzo IP in uscita pubblico, statico e prevedibile per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall in questi sistemi di destinazione per ogni ISE.

* Aumento dei limiti per durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richieste e risposte HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [Limiti e configurazione per App per la logica di Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedicato rispetto a multi-tenant

Quando si creano ed eseguono app per la logica in ISE, si ottengono le stesse esperienze utente e funzionalità simili a quelle del servizio app per la logica multi-tenant. È possibile usare tutti gli stessi trigger, azioni e connettori gestiti predefiniti disponibili nel servizio app per la logica multi-tenant. Alcuni connettori gestiti offrono versioni di ISE aggiuntive. La differenza tra i connettori ISE e i connettori non ISE esiste nei punti in cui vengono eseguiti e le etichette presenti nella finestra di progettazione delle app per la logica quando si lavora all'interno di ISE.

![Connettori con e senza etichette in ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Trigger e azioni predefiniti, ad esempio HTTP, visualizzano l'etichetta **principale** ed eseguono nello stesso ISE dell'app per la logica.

* I connettori gestiti che visualizzano l'etichetta **ISE** sono appositamente progettati per ISEs ed *eseguiti sempre nello stesso ISE dell'app per la logica*. Ad esempio, di seguito sono riportati alcuni [connettori che offrono le versioni di ISE](../connectors/apis-list.md#ise-connectors):<p>

  * Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
  * Bus di servizio di Azure, code di Azure, Hub eventi di Azure
  * Automazione di Azure, Azure Key Vault, griglia di eventi di Azure e log di monitoraggio di Azure
  * FTP, SFTP-SSH, file System e SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure sinapsi Analytics, Azure Cosmos DB
  * AS2, X12 ed EDIFACT

  Con eccezioni rare, se è disponibile un connettore ISE per un sistema locale o un'origine dati, è possibile connettersi direttamente senza usare il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Per ulteriori informazioni, vedere l'articolo relativo all' [accesso ai sistemi locali](#on-premises) più avanti in questo argomento.

* I connettori gestiti che non visualizzano l'etichetta **ISE** continuano a funzionare per le app per la logica all'interno di ISE. Questi connettori *vengono sempre eseguiti nel servizio app per la logica multi-tenant*, non in ISE.

* I connettori personalizzati creati al di *fuori di ISE*, che richiedono o meno il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md), continuano a funzionare per le app per la logica all'interno di ISE. Tuttavia, i connettori personalizzati creati *all'interno di ISE* non funzioneranno con il gateway dati locale. Per altre informazioni, vedere [accesso ai sistemi locali](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Accesso ai sistemi locali

Le app per la logica eseguite all'interno di un ISE possono accedere direttamente a sistemi locali e origini dati che si trovano all'interno o connessi a una rete virtuale di Azure usando questi elementi:<p>

* Il trigger o l'azione HTTP, che Visualizza l'etichetta **principale**

* Connettore **ISE** , se disponibile, per un sistema locale o un'origine dati

  Se è disponibile un connettore ISE, è possibile accedere direttamente al sistema o all'origine dati senza il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Tuttavia, se è necessario accedere a SQL Server da un ISE e si usa l'autenticazione di Windows, è necessario usare la versione non ISE del connettore e il gateway dati locale. La versione di ISE del connettore non supporta l'autenticazione di Windows. Per altre informazioni, vedere [connettori ISE](../connectors/apis-list.md#ise-connectors) e [connettersi da un ambiente del servizio di integrazione](../connectors/apis-list.md#integration-service-environment).

* Un connettore personalizzato

  * I connettori personalizzati creati al di *fuori di ISE*, che richiedono o meno il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md), continuano a funzionare per le app per la logica all'interno di ISE.

  * I connettori personalizzati creati *all'interno di ISE* non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente a sistemi locali e origini dati che si trovano all'interno o connessi alla rete virtuale che ospita ISE. Quindi, le app per la logica che si trovano all'interno di un ISE non necessitano in genere del gateway dati per accedere a tali risorse.

Per accedere a sistemi locali e origini dati che non dispongono di connettori ISE, si trovano all'esterno della rete virtuale o non sono connessi alla rete virtuale, è comunque necessario usare il gateway dati locale. Le app per la logica all'interno di ISE possono continuare a usare i connettori che non hanno l'etichetta **Core** o **ISE** . Questi connettori vengono eseguiti nel servizio app per la logica multi-tenant, anziché in ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU ISE

Quando si crea ISE, è possibile selezionare lo SKU per sviluppatori o lo SKU Premium. Questa opzione SKU è disponibile solo alla creazione di ISE e non può essere modificata in un secondo momento. Ecco le differenze tra gli SKU seguenti:

* **Developer**

  Fornisce un'ISE con costi ridotti che è possibile usare per l'esplorazione, gli esperimenti, lo sviluppo e i test, ma non per i test delle prestazioni o di produzione. Lo SKU Developer include trigger e azioni predefiniti, connettori standard, connettori aziendali e un singolo account di integrazione del [livello gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) per un [Prezzo mensile fisso](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Questo SKU non prevede alcun contratto di servizio, funzionalità di scalabilità verticale o ridondanza durante il riciclo, il che significa che è possibile che si verifichino ritardi o tempi di inattività. Gli aggiornamenti back-end potrebbero interrompere il servizio in modo intermittente.

  Per informazioni sulla capacità e sui limiti, vedere [limiti di ISE in app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Per informazioni sul funzionamento della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica.

* **Premium**

  Fornisce un ISE che è possibile usare per i test di produzione e delle prestazioni. Lo SKU Premium include supporto del contratto di servizio, trigger e azioni predefiniti, connettori standard, connettori aziendali, un unico account di integrazione del [livello standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , funzionalità di scalabilità verticale e ridondanza durante il riciclo per un [Prezzo mensile fisso](https://azure.microsoft.com/pricing/details/logic-apps).

  Per informazioni sulla capacità e sui limiti, vedere [limiti di ISE in app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Per informazioni sul funzionamento della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Accesso endpoint ISE

Quando si crea ISE, è possibile scegliere di usare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale. Questi endpoint influiscono anche sul modo in cui è possibile accedere agli input e agli output dalla cronologia delle esecuzioni delle app per la logica.

> [!IMPORTANT]
> È possibile selezionare l'endpoint di accesso solo durante la creazione di ISE e non è possibile modificare questa opzione in un secondo momento.

* **Interno**: gli endpoint privati consentono le chiamate alle app per la logica in ISE, in cui è possibile visualizzare e accedere agli input e agli output dalla cronologia delle esecuzioni delle app per *la logica solo dall'interno della rete virtuale*.

  > [!IMPORTANT]
  > Se è necessario usare questi trigger basati su webhook, usare endpoint esterni, *non* endpoint interni, quando si crea ISE:
  > 
  > * Azure DevOps
  > * Griglia di eventi di Azure
  > * Common Data Service
  > * Office 365
  > * SAP (versione ISE)
  > 
  > Inoltre, assicurarsi di disporre della connettività di rete tra gli endpoint privati e il computer da cui si desidera accedere alla cronologia di esecuzione. In caso contrario, quando si prova a visualizzare la cronologia di esecuzione dell'app per la logica, viene visualizzato un errore che indica "errore imprevisto. Non è stato possibile recuperare ".
  >
  > ![Errore di azione di archiviazione di Azure risultante dall'impossibilità di inviare il traffico attraverso il firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Ad esempio, il computer client può esistere all'interno della rete virtuale di ISE o in una rete virtuale connessa alla rete virtuale di ISE tramite peering o una rete privata virtuale. 

* **External**: gli endpoint pubblici consentono le chiamate alle app per la logica in ISE, in cui è possibile visualizzare e accedere agli input e agli output dalla cronologia delle esecuzioni delle app per *la logica dall'esterno della rete virtuale*. Se si usano i gruppi di sicurezza di rete (gruppi), assicurarsi che siano configurati con regole in ingresso per consentire l'accesso agli input e agli output della cronologia di esecuzione. Per altre informazioni, vedere [abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Per determinare se ISE usa un endpoint di accesso interno o esterno, nel menu di ISE, in **Impostazioni**, selezionare **Proprietà** e trovare la proprietà **endpoint di accesso** :

![Trovare l'endpoint di accesso ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modello di prezzi

Le app per la logica, i trigger incorporati, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario fisso diverso dal piano tariffario in base al consumo. Per altre informazioni, vedere [modello di determinazione prezzi per app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione con ISE, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). Per informazioni sui limiti, vedere [limiti dell'account di integrazione](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
