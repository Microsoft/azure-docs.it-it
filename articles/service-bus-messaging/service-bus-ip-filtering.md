---
title: Configurare le regole del firewall IP per il bus di servizio di Azure
description: Uso delle regole del firewall per consentire le connessioni da indirizzi IP specifici a bus di servizio di Azure.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 747e15e912033c5bc6a1f64ab389f1ab03f40c0b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962323"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Consentire l'accesso allo spazio dei nomi del bus di servizio di Azure da intervalli o indirizzi IP specifici
Per impostazione predefinita, gli spazi dei nomi del bus di servizio sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui il bus di servizio deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa il bus di servizio con [Azure ExpressRoute][express-route], è possibile creare una **regola del firewall** per consentire traffico solo da indirizzi IP o indirizzi IP dell'infrastruttura locale di un gateway NAT aziendale. 

## <a name="ip-firewall-rules"></a>Regole del firewall IP
Le regole del firewall IP vengono applicate a livello dello spazio dei nomi del bus di servizio. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di bus di servizio di Microsoft Azure viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

## <a name="important-points"></a>Punti importanti
- I firewall e le reti virtuali sono supportate solo nel **livello Premium** del bus di servizio. Se non è possibile eseguire l'aggiornamento al **livello Premier**, si consiglia di proteggere il token di firma di accesso condiviso (SAS) e condividerlo solo con gli utenti autorizzati. Per altre informazioni sull'autenticazione SAS, vedere [Autenticazione e autorizzazione](service-bus-authentication-and-authorization.md#shared-access-signature).
- Specificare almeno una regola del **firewall IP o una regola della rete virtuale** per lo spazio dei nomi per consentire il traffico solo dagli indirizzi IP o dalla subnet specificata di una rete virtuale. Se non sono presenti regole IP e reti virtuali, è possibile accedere allo spazio dei nomi tramite la rete Internet pubblica (usando la chiave di accesso).  
- L'implementazione delle regole del firewall può impedire ad altri servizi di Azure di interagire con il bus di servizio. Come eccezione, è possibile consentire l'accesso alle risorse del bus di servizio da determinati **Servizi attendibili** anche quando è abilitato il filtro IP. Per un elenco di servizi attendibili, vedere [Servizi attendibili](#trusted-microsoft-services). 

    I servizi Microsoft seguenti devono essere in una rete virtuale
    - Servizio app di Azure
    - Funzioni di Azure

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare il portale di Azure per creare regole del firewall per gli indirizzi IP per uno spazio dei nomi del bus di servizio. 

1. Passare allo  **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare opzione di **rete** in **Impostazioni**.  

    > [!NOTE]
    > Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Pagina rete-impostazione predefinita" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se si seleziona l'opzione **tutte le reti** , lo spazio dei nomi del bus di servizio accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Screenshot della pagina di portale di Azure rete. L'opzione per consentire l'accesso da tutte le reti è selezionata nella scheda firewall e reti virtuali.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Per consentire l'accesso solo da un indirizzo IP specificato, selezionare l'opzione **reti selezionate** , se non è già selezionata. Nella sezione **Firewall** seguire questa procedura:
    1. Selezionare l'opzione **Aggiungere l'indirizzo IP client** per concedere all'IP del client corrente l'accesso allo spazio dei nomi. 
    2. Per **Intervallo di indirizzi** immettere un indirizzo IPv4 specifico o un intervallo di indirizzi IPv4 in notazione CIDR. 
    3. Specificare se si vuole **consentire ai servizi Microsoft attendibili di ignorare il firewall**. 

        >[!WARNING]
        > Se si seleziona l'opzione **reti selezionate** e non si aggiunge almeno una regola del firewall IP o una rete virtuale in questa pagina, è possibile accedere allo spazio dei nomi tramite Internet pubblico (usando la chiave di accesso).    

        ![Screenshot della pagina di portale di Azure rete. L'opzione per consentire l'accesso dalle reti selezionate è selezionata e la sezione del firewall è evidenziata.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere qualche minuto la visualizzazione della conferma tra le notifiche del portale.

    > [!NOTE]
    > Per limitare l'accesso a reti virtuali specifiche, vedere [consentire l'accesso da reti specifiche](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Questa sezione include un modello di Azure Resource Manager di esempio che aggiunge una rete virtuale e una regola del firewall a uno spazio dei nomi del bus di servizio esistente.

**ipMask** è un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. Ad esempio, nella notazione CIDR, 70.37.104.0/24 rappresenta i 256 indirizzi IPv4, da 70.37.104.0 a 70.37.104.255, con 24 che indica il numero di bit di prefisso significativi per l'intervallo.

Quando si aggiungono le regole della rete virtuale o dei firewall, impostare il valore di `defaultAction` su `Deny` .


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Per distribuire il modello, seguire le istruzioni per [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Se non sono presenti regole IP e reti virtuali, tutto il traffico passa allo spazio dei nomi anche se si imposta `defaultAction` su `deny` . È possibile accedere allo spazio dei nomi tramite la rete Internet pubblica (usando la chiave di accesso). Specificare almeno una regola IP o una regola della rete virtuale per lo spazio dei nomi per consentire il traffico solo dagli indirizzi IP o dalla subnet specificata di una rete virtuale.  


## <a name="next-steps"></a>Passaggi successivi

Per limitare l'accesso al bus di servizio dalle reti virtuali di Azure, vedere il collegamento seguente:

- [Usare gli endpoint servizio di rete virtuale con il bus di servizio di Azure][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services