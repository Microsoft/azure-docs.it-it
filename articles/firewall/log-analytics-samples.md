---
title: Log di monitoraggio di Azure per il firewall di Azure
description: I log di monitoraggio di Azure possono essere usati per analizzare il firewall di Azure. Un file di esempio è incorporato in Progettazione viste in monitoraggio di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 86538f6d0467eb15e549179166ca957902a2d0c3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659557"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Log di monitoraggio di Azure per il firewall di Azure

Gli esempi seguenti di log di monitoraggio di Azure possono essere usati per analizzare i log del firewall di Azure. Il file di esempio è incorporato in Progettazione viste in monitoraggio di Azure. l'articolo progettazione [viste in monitoraggio di Azure](../azure-monitor/platform/view-designer.md) contiene altre informazioni sul concetto di progettazione della vista.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Visualizzazione log di monitoraggio di Azure

Ecco come è possibile configurare un esempio di visualizzazione dei log di monitoraggio di Azure. È possibile scaricare la visualizzazione di esempio dal repository [azure-docs-json-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview). Il modo più semplice è fare clic con il pulsante destro sul collegamento ipertestuale in questa pagina e scegliere *Salva con nome* e specificare un nome, ad esempio **AzureFirewall.omsview**. 

Eseguire la procedura seguente per aggiungere la visualizzazione all'area di lavoro Log Analytics:

1. Aprire l'area di lavoro Log Analytics nel portale di Azure.
2. Aprire **Progettazione viste** sotto **Generale**.
3. Fare clic su **Importa**.
4. Sfogliare e selezionare il file **AzureFirewall.omsview** scaricato in precedenza.
5. Fare clic su **Salva**.

Ecco come è illustrato l'aspetto della visualizzazione dei dati del log delle regole di applicazione:

![Dati del log delle regole di applicazione](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

E per i dati di log delle regole di rete:

![Dati di log delle regole di rete]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Il firewall di Azure registra i dati sotto AzureDiagnostics con Category come **AzureFirewallApplicationRule** o **AzureFirewallNetworkRule**. I dati che contengono i dettagli vengono archiviati nel campo msg_s. Usando l'operatore [analizza](/azure/kusto/query/parseoperator) è possibile estrarre le varie proprietà interessante dal campo msg_s. Le query seguenti estraggono le informazioni per entrambe le categorie.

## <a name="application-rules-log-data-query"></a>Query sui dati di log delle regole di applicazione

La query seguente consente di analizzare i dati di log delle regole di applicazione. Nelle diverse righe di commento sono previste alcune indicazioni su come è stata compilata la query:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

La stessa query in un formato più ridotto:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Query sui dati di log delle regole di rete

La query seguente consente di analizzare i dati del log della regola di rete. Nelle diverse righe di commento sono previste alcune indicazioni su come è stata compilata la query:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

La stessa query in un formato più ridotto:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Query sui dati del log di intelligence per le minacce

La query seguente analizza i dati del registro delle regole di intelligence per le minacce:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Esempi di registro

Negli esempi di log seguenti vengono illustrati i dati inclusi in una voce di log.

:::image type="content" source="media/log-analytics-samples/log1.png" alt-text="Screenshot di una voce di log. Sono visibili più valori, ad esempio un timestamp, un protocollo, un numero di porta, un'azione, una raccolta regole e una regola." border="false":::

:::image type="content" source="media/log-analytics-samples/log2.png" alt-text="Screenshot di una voce di log. Sono visibili più valori, ad esempio un timestamp, un protocollo, gli indirizzi I P di origine e di destinazione e un'azione." border="false":::

:::image type="content" source="media/log-analytics-samples/log3.png" alt-text="Screenshot di una voce di log. Sono visibili più valori, ad esempio un timestamp, un protocollo, l'origine e la destinazione I P indirizzi e porte e un messaggio." border="false":::
## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e la diagnostica di Firewall di Azure, vedere [Esercitazione: Monitorare i log e le metriche di Firewall di Azure](./firewall-diagnostics.md).