---
title: Analisi del traffico di Azure | Microsoft Docs
description: Informazioni su come analizzare i log dei flussi dei gruppi di sicurezza di rete di Azure con Analisi del traffico.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 87dacd28223fd88866edb90266aa76fd3cdf86cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095368"
---
# <a name="traffic-analytics"></a>Analisi del traffico

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nelle reti cloud. Analisi del traffico analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Con Analisi del traffico è possibile:

- Visualizzare l'attività della rete nelle sottoscrizioni di Azure e identificare le aree sensibili.
- Identificare le minacce alla sicurezza e proteggere la rete con informazioni quali porte aperte, applicazioni che tentano l'accesso a Internet e macchine virtuali (VM) che si connettono a reti non autorizzate.
- Conoscere i modelli di flusso di traffico nelle aree di Azure e in Internet per ottimizzare le prestazioni e la capacità della distribuzione della rete.
- Trovare le configurazioni di rete errate che comportano connessioni non riuscite nella rete.

> [!NOTE]
> Analisi del traffico supporta ora la raccolta dei dati dei log dei flussi NSG a una frequenza maggiore di 10 minuti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Perché usare Analisi del traffico?

È fondamentale monitorare, gestire e conoscere la rete per fare in modo che la sicurezza, la conformità e le prestazioni non vengano compromesse. Per proteggere e ottimizzare l'ambiente, è indispensabile conoscerlo. Spesso è necessario conoscere lo stato corrente della rete, chi si connette a cosa, da dove si connettono, quali porte sono aperte a Internet, il comportamento previsto della rete, il comportamento irregolare della rete e i picchi improvvisi di traffico.

Le reti cloud sono diverse dalle reti aziendali locali, in cui sono disponibili router e commutatori idonei per Netflow o per un protocollo equivalente, che consentono di raccogliere il traffico di rete IP in ingresso o in uscita in un'interfaccia di rete. Analizzando i dati sul flusso di traffico, è possibile compilare un'analisi del flusso e del volume del traffico di rete.

Le reti virtuali di Azure hanno i log dei flussi dei gruppi di sicurezza di rete, che forniscono informazioni sul traffico IP in ingresso e in uscita tramite un gruppo di sicurezza di rete associato a singole interfacce di rete, VM o subnet. Analizzando i log dei flussi dei gruppi di sicurezza di rete non elaborati e inserendo l'intelligence per sicurezza, topologia e geografia, Analisi del traffico può fornire informazioni sul flusso del traffico nell'ambiente. Analisi del traffico fornisce informazioni, tra cui gli host che comunicano di più, i protocolli applicativi che comunicano di più, le coppie di host che conversano di più, il traffico consentito/bloccato, il traffico in ingresso/uscita, le porte Internet aperte, le regole di blocco più frequenti, la distribuzione del traffico per data center di Azure, rete virtuale, subnet o reti non autorizzate.

## <a name="key-components"></a>Componenti chiave

- **Gruppo di sicurezza di rete**: contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a Rete virtuale di Microsoft Azure. I gruppi di sicurezza di rete possono essere associati a subnet, singole VM (distribuzione classica) o singole interfacce di rete collegate a VM (Resource Manager). Per altre informazioni, vedere [Panoramica dei gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log dei flussi del gruppo di sicurezza di rete (NSG)** : Consente di visualizzare le informazioni sul traffico IP in ingresso e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita per ogni regola, la scheda di rete a cui si applica il flusso, informazioni a cinque tuple relative al flusso (indirizzo IP di origine/destinazione, porta di origine/destinazione e protocollo) e se il traffico è consentito o meno. Per altre informazioni sui log dei flussi dei gruppi di sicurezza di rete, vedere [Log dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: servizio di Azure che raccoglie i dati di monitoraggio e li archivia in un repository centrale. Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API di Azure. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione. Le applicazioni di monitoraggio, ad esempio Monitoraggio prestazioni rete e Analisi del traffico, sono basate sui log di Monitoraggio di Azure. Per altre informazioni, vedere [Log di Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **area di lavoro Log Analytics**: istanza dei log di Monitoraggio di Azure, in cui vengono archiviati i dati relativi a un account Azure. Per altre informazioni sulle aree di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete in Azure. Con Network Watcher è possibile attivare e disattivare i log dei flussi dei gruppi di sicurezza di rete. Per altre informazioni, vedere [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Come funziona Analisi del traffico

Analisi del traffico esamina i log dei flussi dei gruppi di sicurezza di rete e acquisisce log ridotti aggregando i flussi comuni tra lo stesso indirizzo IP di origine, indirizzo IP di destinazione, porta di destinazione e protocollo, Ad esempio Host 1 (indirizzo IP: 10.10.10.10) che comunica con Host 2 (indirizzo IP: 10.10.20.10), 100 volte in un periodo di 1 ora usando la porta (ad esempio, 80) e il protocollo (ad esempio, http). Il log ridotto ha una voce, indicante che Host 1 e Host 2 hanno comunicato 100 volte nell'arco di 1 ora usando la porta *80* e il protocollo *HTTP*, invece di 100 voci. I log ridotti vengono migliorati con informazioni su geografia, sicurezza e topologia e quindi archiviati in un'area di lavoro Log Analytics. L'immagine seguente illustra il flusso di dati:

![Flusso di dati per l'elaborazione dei log dei flussi dei gruppi di sicurezza di rete](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Aree supportate: NSG 

È possibile usare l'analisi del traffico per i gruppi di sicurezza di rete in una qualsiasi delle aree supportate seguenti:
:::row:::
   :::column span="":::
      Australia centrale  
      Australia orientale  
      Australia sud-orientale  
      Brasile meridionale  
      Canada centrale  
      Canada orientale  
      India centrale  
      Stati Uniti centrali  
      Cina orientale 2  
      Cina settentrionale 2  
      Asia orientale       
   :::column-end:::
   :::column span="":::
      Stati Uniti orientali  
      Stati Uniti orientali 2  
      Stati Uniti orientali 2 EUAP  
      Francia centrale  
      Germania centro-occidentale Giappone orientale  
      Giappone occidentale  
      Corea centrale  
      Corea meridionale  
      Stati Uniti centro-settentrionali  
      Europa settentrionale    
   :::column-end:::
   :::column span="":::
      Sudafrica settentrionale  
      Stati Uniti centro-meridionali  
      India meridionale  
      Asia sud-orientale  
      Svizzera settentrionale  
      Svizzera occidentale  
      Emirati Arabi Uniti settentrionali  
      Regno Unito meridionale  
      Regno Unito occidentale   
      USGov Arizona  
      USGov Texas
   :::column-end:::
   :::column span="":::
      USGov Virginia  
      USNat est  
      USNat ovest  
      USSec est  
      USSec ovest  
      Stati Uniti centro-occidentali  
      Europa occidentale  
      Stati Uniti occidentali  
      Stati Uniti occidentali 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Aree supportate: Aree di lavoro Log Analytics

L'area di lavoro Log Analytics deve esistere nelle aree indicate di seguito:
:::row:::
   :::column span="":::
      Australia centrale  
      Australia orientale  
      Australia sud-orientale  
      Brasile meridionale  
      Canada centrale  
      India centrale  
      Stati Uniti centrali  
      Cina orientale 2  
      Asia orientale  
      Stati Uniti orientali  
   :::column-end:::
   :::column span="":::
      Stati Uniti orientali 2  
      Stati Uniti orientali 2 EUAP  
      Francia centrale  
      Germania centro-occidentale  
      Giappone orientale  
      Corea centrale  
      Stati Uniti centro-settentrionali  
      Europa settentrionale  
      Sudafrica settentrionale  
      Stati Uniti centro-meridionali  
   :::column-end:::
   :::column span="":::
      Asia sud-orientale  
      Svizzera settentrionale  
      Svizzera occidentale  
      Emirati Arabi Uniti centrali  
      Emirati Arabi Uniti settentrionali  
      Regno Unito meridionale  
      Regno Unito occidentale   
      USGov Arizona  
      USGov Virginia  
      USNat est   
   :::column-end:::
   :::column span="":::
      USNat ovest   
      USSec est  
      USSec ovest  
      Stati Uniti centro-occidentali  
      Europa occidentale  
      Stati Uniti occidentali  
      Stati Uniti occidentali 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Prerequisiti

### <a name="user-access-requirements"></a>Requisiti di accesso utente

L'account deve essere un membro di uno dei seguenti [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Modello di distribuzione   | Ruolo                   |
|---------          |---------               |
|Gestione risorse   | Proprietario                  |
|                   | Collaboratore            |
|                   | Reader                 |
|                   | Collaboratore di rete    |

Se l'account non è assegnato a uno dei ruoli predefiniti, deve essere assegnato a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) al quale vengono assegnate le seguenti azioni, a livello di sottoscrizione:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"
- "Microsoft.Network/expressRouteCircuits/read"

Per informazioni su come controllare le autorizzazioni di accesso utente, vedere [Domande frequenti su analisi traffico](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Abilitare Network Watcher

Per analizzare il traffico, è necessario avere un'istanza di Network Watcher esistente o [abilitare un'istanza di Network Watcher](network-watcher-create.md) in ogni area in cui sono presenti gruppi di sicurezza di rete per cui si vuole analizzare il traffico. Analisi del traffico può essere abilitata per i gruppi di sicurezza di rete ospitati in una delle [aree supportate](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Selezionare un gruppo di sicurezza di rete

Prima di abilitare la registrazione dei flussi per i gruppi di sicurezza di rete, è necessario avere un gruppo di sicurezza di rete per cui registrare i flussi. Se non è disponibile un gruppo di sicurezza di rete, vedere [Creare un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md#create-a-network-security-group) per crearne uno.

Nel portale di Azure aprire **Network Watcher** e quindi selezionare **Log del flusso del NSG**. Selezionare il gruppo di sicurezza di rete per cui si vuole abilitare un log del flusso del gruppo di sicurezza di rete, come illustrato nell'immagine seguente:

![Selezione di gruppi di sicurezza di rete che richiedono l'abilitazione del log del flusso del gruppo di sicurezza di rete](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se si prova ad abilitare Analisi del traffico per un gruppo di sicurezza di rete ospitato in un'area diversa dalle [aree supportate](#supported-regions-nsg), viene visualizzato un errore "Non trovato".

## <a name="enable-flow-log-settings"></a>Abilitare le impostazioni dei log dei flussi

Prima di abilitare le impostazioni dei log dei flussi, è necessario completare le attività seguenti:

Registrare il provider Azure Insights, se non è già registrato, per la sottoscrizione:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Se non è già disponibile un account di archiviazione di Azure in cui archiviare i log dei flussi dei gruppi di sicurezza di rete, è necessario crearne uno. È possibile creare un account di archiviazione con il comando che segue. Prima di eseguire il comando, sostituire `<replace-with-your-unique-storage-account-name>` con un nome univoco in tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri, usando solo numeri e lettere minuscole. È anche possibile modificare il nome del gruppo di risorse, se necessario.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selezionare le opzioni seguenti, come illustrato nell'immagine:

1. Per *Stato* selezionare **Sì**
2. Selezionare *Versione 2* per **Versione dei log dei flussi**. La versione 2 contiene le statistiche di sessione dei flussi (byte e pacchetti).
3. Selezionare un account di archiviazione esistente nel quale archiviare i log dei flussi. Assicurarsi che nella risorsa di archiviazione lo "spazio dei nomi gerarchico abilitato per Data Lake Storage Gen2" non sia impostato su true.
4. Impostare **Conservazione** sul numero di giorni per cui si vogliono archiviare i dati. Per archiviare i dati per sempre, impostare il valore su *0*. Si devono sostenere i costi di archiviazione di Azure per l'account di archiviazione. 
5. Selezionare *Sì* per **Stato di Analisi del traffico**.
6. Selezionare l'intervallo di elaborazione. In base alla scelta effettuata, i log dei flussi verranno raccolti dall'account di archiviazione ed elaborati da Analisi del traffico. È possibile scegliere di eseguire un intervallo di elaborazione ogni ora oppure ogni 10 minuti. 
7. Selezionare un'area di lavoro di Log Analytics (OMS) esistente oppure selezionare **Crea una nuova area di lavoro** per crearne una nuova. Un'area di lavoro Log Analytics viene usata da Analisi del traffico per archiviare i dati aggregati e indicizzati che vengono quindi usati per generare l'analisi. Se si seleziona un'area di lavoro esistente, deve esistere in una delle [aree supportate](#supported-regions-log-analytics-workspaces) ed essere stata aggiornata al nuovo linguaggio di query. Se non si vuole aggiornare un'area di lavoro esistente o non si ha un'area di lavoro in un'area supportata, crearne una nuova. Per altre informazioni sui linguaggi di query, vedere [Aggiornamento di Azure Log Analytics alla nuova ricerca log](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Non è necessario che l'area di lavoro Log Analytics che ospita la soluzione Analisi del traffico e i gruppi di sicurezza di rete si trovino nella stessa area. È ad esempio possibile avere Analisi del traffico in un'area di lavoro nell'area Europa occidentale e i gruppi di sicurezza di rete in Stati Uniti orientali e Stati Uniti occidentali. È possibile configurare più gruppi di sicurezza di rete nella stessa area di lavoro.

8. Selezionare **Salva**.

    ![Selezione dell'account di archiviazione, dell'area di lavoro Log Analytics e dell'abilitazione di Analisi del traffico](./media/traffic-analytics/ta-customprocessinginterval.png)

Ripetere i passaggi precedenti per qualsiasi altro gruppo di sicurezza di rete per il quale si vuole abilitare Analisi del traffico. I dati dai log dei flussi vengono inviati all'area di lavoro, quindi assicurarsi che le leggi locali e le normative in vigore nel paese o nell'area di appartenenza consentano l'archiviazione dei dati nell'area in cui è presente l'area di lavoro. Se sono stati impostati intervalli di elaborazione diversi per gruppi di sicurezza di rete differenti, i dati verranno raccolti a intervalli diversi. Ad esempio: è possibile scegliere di abilitare un intervallo di elaborazione di 10 minuti per reti virtuali di importanza critica e di 1 ora per reti virtuali meno rilevanti.

È inoltre possibile configurare Analisi del traffico usando il cmdlet di PowerShell [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) in Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Visualizzare Analisi del traffico

Per visualizzare Analisi del traffico, cercare **Network Watcher** nella barra di ricerca del portale. Dopo aver eseguito l'accesso a Network Watcher, per esplorare Analisi del traffico e le relative funzionalità, selezionare **Analisi del traffico** nel menu a sinistra. 

![Accesso al dashboard Analisi del traffico](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

La prima visualizzazione del dashboard potrebbe richiedere fino a 30 minuti perché Analisi del traffico deve prima aggregare dati sufficienti per ottenere informazioni significative, prima di poter generare i report.

## <a name="usage-scenarios"></a>Scenari di utilizzo

Di seguito sono elencate alcune informazioni utili da visualizzare dopo la configurazione completa di Analisi del traffico:

### <a name="find-traffic-hotspots"></a>Trovare le aree sensibili del traffico

**Cercare**

- Quali host, subnet e reti virtuali inviano o ricevono la maggior parte del traffico, attraversano il traffico dannoso massimo e il blocco di flussi significativi?
    - Controllare grafico comparativo per host, subnet rete virtuale. Conoscere gli host, i subnet e le reti virtuali che inviano o ricevono il maggior traffico consente di identificare gli host che elaborano il maggior traffico e se la distribuzione del traffico viene eseguita correttamente.
    - È possibile valutare se il volume di traffico è appropriato per un host. Il volume di traffico è un comportamento normale o è opportuno eseguire un'ulteriore analisi?
- Qual è la quantità di traffico in ingresso/uscita?
    -   È previsto che l'host riceva più traffico in ingresso che in uscita o viceversa?
- Statistiche del traffico bloccato.
    - Perché un host blocca un volume di traffico non dannoso significativo? Questo comportamento richiede un'ulteriore analisi e probabilmente un'ottimizzazione della configurazione
- Statistiche del traffico dannoso consentito/bloccato
  - Perché un host riceve traffico dannoso e perché i flussi da origini dannose sono consentiti? Questo comportamento richiede un'ulteriore analisi e probabilmente un'ottimizzazione della configurazione.

    Selezionare **Visualizza tutto**, in **Host**, come illustrato nell'immagine seguente:

    ![Dashboard che presenta l'host con i dettagli sul maggior traffico](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- L'immagine seguente illustra la tendenza temporale per i cinque host che conversano di più e i dettagli relativi ai flussi (flussi consentiti - in ingresso/uscita e negati - in ingresso/uscita) per un host:

    ![Tendenza per i cinque host che conversano di più](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Cercare**

- Quali sono le coppie di host che conversano di più?
    - Comportamento previsto, ad esempio comunicazione front-end oppure back-end, o comportamento irregolare, ad esempio traffico Internet back-end.
- Statistiche del traffico consentito/bloccato
    - Perché un host consente o blocca un volume di traffico significativo
- Protocollo applicativo di uso più frequente tra le coppie di host che conversano di più:
    - Queste applicazioni sono consentite in questa rete?
    - Le applicazioni sono configurate correttamente? Usano il protocollo appropriato per la comunicazione? Selezionare **Visualizza tutto**  in **Conversazioni frequenti**, come illustrato nell'immagine seguente:

        ![Dashboard che presenta le conversazioni più frequenti](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- L'immagine seguente illustra la tendenza temporale per le prime cinque conversazioni e i dettagli relativi ai flussi, ad esempio flussi consentiti e negati in ingresso e in uscita per una coppia di conversazioni:

    ![Dettagli e tendenza delle cinque conversazioni più attive](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Cercare**

- Quale protocollo applicativo è più usato nell'ambiente e quali coppie di host in conversazione usano di più il protocollo applicativo?
    - Queste applicazioni sono consentite in questa rete?
    - Le applicazioni sono configurate correttamente? Usano il protocollo appropriato per la comunicazione? Il comportamento previsto usa porte comuni, ad esempio 80 e 443. Per la comunicazione standard, se vengono visualizzate porte insolite, potrebbe essere necessario modificare la configurazione. Selezionare **Visualizza tutto**  in **Porta dell'applicazione**, come illustrato nell'immagine seguente:

        ![Dashboard che presenta i primi protocolli applicativi](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Le immagini seguenti illustrano la tendenza temporale per i primi cinque protocolli L7 e i dettagli relativi ai flussi (ad esempio, flussi consentiti e negati) per un protocollo L7:

    ![Dettagli e tendenza dei primi cinque protocolli di livello 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Dettagli dei flussi per il protocollo applicativo in ricerca log](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Cercare**

- Tendenze di utilizzo della capacità di un gateway VPN nell'ambiente.
    - Ogni SKU VPN consente una certa quantità di larghezza di banda. I gateway VPN sono sottoutilizzati?
    - I gateway stanno per raggiungere la capacità massima? È consigliabile eseguire l'aggiornamento allo SKU superiore successivo?
- Quali sono gli host che conversano di più, tramite quale gateway VPN, su quale porta?
    - Questo modello è normale? Selezionare **Visualizza tutto**, in **Gateway VPN**, come illustrato nell'immagine seguente:

        ![Dashboard che presenta le connessioni VPN più attive](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- L'immagine seguente illustra la tendenza temporale per l'utilizzo della capacità di un gateway VPN di Azure e i dettagli relativi ai flussi (ad esempio, flussi consentiti e porte):

    ![Tendenza dell'utilizzo del gateway VPN e dettagli dei flussi](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizzare la distribuzione del traffico per area geografica

**Cercare**

- Distribuzione del traffico per data center, ad esempio origini più frequenti di traffico verso un data center, principali reti non autorizzate in conversazione con il data center e protocolli applicativi che conversano di più.
  - Se si osserva un carico maggiore in un data center, è possibile pianificare una distribuzione efficiente del traffico.
  - Se reti non autorizzate sono in conversazione nel data center, correggere le regole dei gruppi di sicurezza di rete per bloccarle.

    Selezionare **Visualizza mappa**, in **Ambiente**, come illustrato nell'immagine seguente:

    ![Dashboard che presenta la distribuzione del traffico](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- La mappa geografica mostra la barra multifunzione in alto per selezionare parametri come i data center (distribuiti/nessuna distribuzione/attivi/inattivi/Analisi del traffico abilitata/Analisi del traffico non abilitata) e i paesi o le aree che forniscono traffico non dannoso/dannoso alla distribuzione attiva:

    ![Visualizzazione della mappa geografica che presenta la distribuzione attiva](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- La mappa geografica illustra la distribuzione del traffico a un data center da paesi o aree e continenti che comunicano con il data center tramite linee di colore blu (traffico non dannoso) e rosso (traffico dannoso):

    ![Visualizzazione della mappa geografica che presenta la distribuzione del traffico a paesi o aree e continenti](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Dettagli dei flussi per la distribuzione del traffico nella ricerca log](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizzare la distribuzione del traffico per rete virtuale

**Cercare**

- Distribuzione del traffico per rete virtuale, topologia, origini più frequenti di traffico verso la rete virtuale, principali reti non autorizzate in conversazione con la rete virtuale e protocolli applicativi che conversano di più.
  - Determinare quali coppie di reti virtuali stanno conversando. Se la conversazione non è prevista, è possibile risolvere il problema.
  - Se reti non autorizzate conversano con una rete virtuale, è possibile correggere le regole dei gruppi di sicurezza di rete per bloccare le reti non autorizzate.
 
    Selezionare **Visualizza reti virtuali**, in **Ambiente**, come illustrato nell'immagine seguente:

    ![Dashboard che presenta la distribuzione delle reti virtuali](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- La topologia delle reti virtuali mostra la barra multifunzione in alto per selezionare parametri come le connessioni esterne, i flussi attivi e i flussi dannosi di una rete virtuale (connessioni tra reti virtuali/attive/inattive).
- È possibile filtrare la topologia di rete virtuale basata su sottoscrizioni, aree di lavoro, gruppi di risorse e intervallo di tempo. Filtri aggiuntivi che consentono di comprendere il flusso sono: Tipo di flusso (tra reti virtuali, IntraVNET e così via), la direzione del flusso (In ingresso, In uscita), lo stato del flusso (Consentito, Bloccato), le reti virtuali (Assegnate e Connesse), il tipo di connessione (Peering o Gateway: connessione da punto a sito e S2S) e gruppo di sicurezza di rete (NSG). Usare questi filtri per concentrarsi sulle reti virtuali che si desidera esaminare in dettaglio.
- La topologia delle reti virtuali illustra la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi (consentiti/bloccati/in ingresso/in uscita/non dannosi/dannosi), il protocollo applicativo e i gruppi di sicurezza di rete, ad esempio:

    ![Topologia delle reti virtuali che presenta i dettagli della distribuzione e dei flussi di traffico](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia di rete virtuale che presenta i filtri di livello superiore e altri filtri](./media/traffic-analytics/virtual-network-filters.png)

    ![Dettagli dei flussi per la distribuzione del traffico di rete virtuale nella ricerca log](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Cercare**

- Distribuzione del traffico per subnet, topologia, origini più frequenti di traffico verso la subnet, principali reti non autorizzate in conversazione con la subnet e protocolli applicativi che conversano di più.
    - Determinare quali coppie di subnet stanno conversando. Se si osservano conversazioni impreviste, è possibile correggere la configurazione.
    - Se reti non autorizzate sono in conversazione con una subnet, è possibile risolvere il problema configurando regole dei gruppi di sicurezza di rete per bloccare le reti non autorizzate.
- La topologia delle subnet mostra la barra multifunzione in alto per selezionare parametri come la subnet attiva/inattiva, le connessioni esterne, i flussi attivi e i flussi dannosi della subnet.
- La topologia delle subnet illustra la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi (consentiti/bloccati/in ingresso/in uscita/non dannosi/dannosi), il protocollo applicativo e i gruppi di sicurezza di rete, ad esempio:

    ![Topologia delle subnet che presenta la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Cercare**

Distribuzione del traffico per Gateway applicazione e bilanciamento del carico, topologia, origini più frequenti di traffico, principali reti non autorizzate in conversazione con Gateway applicazione e bilanciamento del carico e protocolli applicativi che conversano di più. 
    
 - Sapere quale subnet è in conversazione con quale gateway applicazione o bilanciamento del carico. Se si osservano conversazioni impreviste, è possibile correggere la configurazione.
 - Se reti non autorizzate sono in conversazione con un gateway applicazione o un bilanciamento del carico, è possibile risolvere il problema configurando regole dei gruppi di sicurezza di rete per bloccare le reti non autorizzate. 

    ![Screenshot mostra una topologia di subnet con distribuzione del traffico a una subnet del gateway applicazione per quanto riguarda i flussi.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visualizzare le porte e le macchine virtuali che ricevono il traffico da Internet

**Cercare**

- Quali porte aperte sono in conversazione via Internet?
  - Se risultano aperte porte impreviste, è possibile correggere la configurazione:

    ![Dashboard che presenta le porte che ricevono e inviano il traffico a Internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Dettagli degli host e delle porte di destinazione di Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Cercare**

È presente traffico dannoso nell'ambiente? Da dove ha origine? Qual è la destinazione?

![Dettaglio dei flussi di traffico dannoso in ricerca log](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualizzare le tendenze nei riscontri della regola del gruppo di sicurezza di rete/NSG

**Cercare**

- Le regole del gruppo di sicurezza/NSG prevedono il maggior numero di occorrenze nel grafico comparato con la distribuzione di flussi?
- Quali sono le coppie di conversazione di origine e di destinazione più frequenti per le regole del gruppo di sicurezza di rete/NSG?

    ![Dashboard che presenta le statistiche sui riscontri del gruppo di sicurezza di rete](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Le immagini seguenti illustrano la tendenza temporale per i riscontri delle regole del gruppo di sicurezza di rete e i dettagli dei flussi di origine-destinazione per un gruppo di sicurezza di rete:

  - Rilevare rapidamente quali regole NSGs e NSG attraversano flussi dannosi e quali sono gli indirizzi IP dannosi principali che accedono all'ambiente cloud
  - Identificare quali regole di sicurezza/NSG consentono/bloccano il traffico di rete significativo
  - Selezionare i filtri principali per un controllo granulare di un gruppo di regole di sicurezza di rete o NSG

    ![Presentazione della tendenza temporale per i riscontri delle regole del gruppo di sicurezza di rete e delle regole del gruppo di sicurezza di rete più frequenti](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Dettagli delle statistiche sulle regole del gruppo di sicurezza di rete più frequenti in ricerca log](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

Per le risposte alle domande frequenti, vedere [Traffic Analytics FAQ](traffic-analytics-faq.md) (Domande frequenti su Analisi del traffico).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come abilitare i log dei flussi, vedere l'argomento relativo all'[abilitazione della registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-portal.md).
- Per comprendere i dettagli relativi allo schema e all'elaborazione di Analisi del traffico, vedere [Schema di Analisi del traffico](traffic-analytics-schema.md).
