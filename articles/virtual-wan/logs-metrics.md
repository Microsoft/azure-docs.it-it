---
title: Log e metriche
titleSuffix: Azure Virtual WAN
description: Informazioni sulle metriche e i log WAN virtuali di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: a5bbcc4c07826a5093da3f51c709f49ceb231753
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596071"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Metriche e log WAN virtuali di Azure

È possibile monitorare la rete WAN virtuale di Azure con monitoraggio di Azure. La rete WAN virtuale è un servizio di rete che riunisce numerose funzionalità di rete, sicurezza e routing per fornire una singola interfaccia operativa. I gateway VPN WAN virtuali, i gateway ExpressRoute e il firewall di Azure hanno la registrazione e le metriche disponibili tramite monitoraggio di Azure.

Questo articolo illustra le metriche e le diagnostica disponibili tramite il portale. Le metriche sono leggere e possono supportare scenari quasi in tempo reale, rendendoli utili per gli avvisi e il rilevamento rapido dei problemi.

## <a name="monitoring-secured-hub-azure-firewall"></a>Monitoraggio dell'hub protetto (firewall di Azure) 

È possibile monitorare l'hub protetto usando i log del firewall di Azure. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Se si è scelto di proteggere l'hub virtuale usando il firewall di Azure, i log e le metriche pertinenti sono disponibili qui: [log e metriche del firewall di Azure](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Metriche

Le metriche in monitoraggio di Azure sono valori numerici che descrivono alcuni aspetti di un sistema in un determinato momento. Le metriche vengono raccolte ogni minuto e sono utili per gli avvisi perché possono essere campionate spesso. Un avviso può essere generato rapidamente con una logica relativamente semplice.

### <a name="site-to-site-vpn-gateways"></a>Gateway VPN da sito a sito

Per i gateway VPN da sito a sito di Azure sono disponibili le metriche seguenti:

* **Larghezza di banda del gateway** : larghezza di banda di aggregazione media da sito a sito di un gateway in byte al secondo.
* **Larghezza di banda tunnel** : larghezza di banda media di un tunnel in byte al secondo.
* **Byte in uscita tunnel** : byte in uscita di un tunnel. 
* **Pacchetti in uscita tunnel** : numero di pacchetti in uscita di un tunnel. 
* Mancata corrispondenza del pacchetto **TS in uscita del tunnel** : numero di rilasci del pacchetto in uscita dal selettore di traffico di un tunnel. 
* **Byte in ingresso tunnel** -byte in ingresso di un tunnel. 
* **Pacchetto in ingresso tunnel** : numero di pacchetti in ingresso di un tunnel. 
* **Eliminazione del pacchetto di mancata corrispondenza** dei pacchetti di Servizi terminal in ingresso del tunnel-numero di rilasci di pacchetti in ingresso dal selettore del traffico di un tunnel. 

### <a name="point-to-site-vpn-gateways"></a>Gateway VPN da punto a sito

Per i gateway VPN da punto a sito di Azure sono disponibili le metriche seguenti:

* **Larghezza di banda P2S del gateway** : larghezza di banda di aggregazione da punto a sito media di un gateway in byte al secondo.
* **Conteggio delle connessioni P2S** : numero di connessioni da punto a sito di un gateway.

### <a name="azure-expressroute-gateways"></a>Gateway ExpressRoute di Azure

Per i gateway ExpressRoute di Azure sono disponibili le metriche seguenti:

* **BitsInPerSecond** : BITS in ingresso in Azure al secondo.
* **BitsOutPerSecond** -BITS in uscita da Azure al secondo.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Visualizzare le metriche del gateway

La procedura seguente consente di individuare e visualizzare le metriche:

1. Nel portale passare all'hub virtuale che contiene il gateway.

2. Selezionare **VPN (da sito a sito)** per individuare un gateway da sito a sito, **ExpressRoute** per individuare un gateway ExpressRoute o una **VPN utente (da punto a sito)** per individuare un gateway da punto a sito. Nella pagina è possibile visualizzare le informazioni sul gateway. Copiare queste informazioni. Verrà usato in un secondo momento per visualizzare la diagnostica con monitoraggio di Azure.

3. Selezionare **Metriche**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Screenshot mostra un riquadro V P N da sito a sito con visualizzazione in monitoraggio di Azure selezionato.":::

4. Nella pagina **metrica** è possibile visualizzare le metriche a cui si è interessati.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Screenshot che mostra la pagina &quot;metriche&quot; con le categorie evidenziate.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Log di diagnostica

### <a name="site-to-site-vpn-gateways"></a>Gateway VPN da sito a sito

Per i gateway VPN da sito a sito di Azure sono disponibili le seguenti operazioni di diagnostica:

* **Log di diagnostica del gateway** : diagnostica specifica del gateway, ad esempio integrità, configurazione, aggiornamenti dei servizi, nonché diagnostica aggiuntiva.
* **Log di diagnostica del tunnel** : si tratta di log correlati al tunnel IPSec, ad esempio eventi di connessione e disconnessione per un tunnel IPSec da sito a sito, SAS negoziata, motivi di disconnessione, nonché diagnostica aggiuntiva.
* Eseguire il **routing dei log di diagnostica** : si tratta di log correlati a eventi per route statiche, BGP, aggiornamenti di route e diagnostica aggiuntiva.
* **Log di diagnostica IKE** : diagnostica specifica IKE per le connessioni IPSec.

### <a name="point-to-site-vpn-gateways"></a>Gateway VPN da punto a sito

Per i gateway VPN da punto a sito di Azure sono disponibili le seguenti operazioni di diagnostica:

* **Log di diagnostica del gateway** : diagnostica specifica del gateway, ad esempio integrità, configurazione, aggiornamenti dei servizi e altri dati di diagnostica.
* **Log di diagnostica IKE** : diagnostica specifica IKE per le connessioni IPSec.
* **Log di diagnostica di P2S** : si tratta di eventi di configurazione P2S e client VPN utente (da punto a sito). Sono incluse la connessione/disconnessione del client, l'allocazione di indirizzi del client VPN, nonché altri elementi di diagnostica.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Visualizzare i log di diagnostica

La procedura seguente consente di individuare e visualizzare la diagnostica:

1. Nel portale passare alla risorsa WAN virtuale. Nella sezione **Panoramica** della pagina WAN virtuale del portale selezionare **Essentials** per espandere la visualizzazione e ottenere informazioni sul gruppo di risorse. Copiare le informazioni sul gruppo di risorse.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Screenshot che mostra la sezione &quot;Overview&quot; con una freccia che punta al pulsante &quot;copy&quot;.":::

2. Nella sezione monitoraggio passare al gruppo di risorse. Selezionare **impostazioni di diagnostica**, quindi immettere le informazioni sulle risorse. Si tratta delle informazioni sulle risorse copiate nel passaggio 2 dalla sezione [visualizzare le metriche del gateway](#metrics-steps) , più indietro in questo articolo.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Screenshot che mostra la sezione &quot;monitoraggio&quot; con una freccia che punta all'elenco a discesa &quot;Resource&quot;.":::

3. Nella pagina risultati selezionare **+ Aggiungi impostazione di diagnostica**, quindi selezionare un'opzione. È possibile scegliere di inviare a Log Analytics, trasmettere a un hub eventi o semplicemente archiviarli in un account di archiviazione.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="pagina metrica":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics query di esempio

I log si trovano nell' **area di lavoro log Analytics di Azure**. È possibile configurare una query in Log Analytics. L'esempio seguente contiene una query per ottenere la diagnostica della route da sito a sito.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Sostituire i valori seguenti, dopo **= =**, in base alle esigenze.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Log attività

Le voci del **log attività** vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure. È possibile usare i log attività di Azure (precedentemente noti come *log operativi* e *log di controllo*) per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come monitorare log e metriche del Firewall di Azure, vedere [Esercitazione: monitorare i log del Firewall di Azure](../firewall/firewall-diagnostics.md).
* Per altre informazioni sulle metriche in monitoraggio di Azure, vedere [metriche in monitoraggio di Azure](../azure-monitor/essentials/data-platform-metrics.md).
