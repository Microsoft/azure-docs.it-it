---
title: Configurare gli endpoint del servizio di rete virtuale per il bus di servizio di Azure
description: Questo articolo fornisce informazioni su come aggiungere un endpoint di servizio Microsoft. ServiceBus a una rete virtuale.
ms.topic: article
ms.date: 03/29/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 9b5aba6c5ad4e1e6d0c90b99ebcdf441bb35cc39
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960453"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Consentire l'accesso allo spazio dei nomi del bus di servizio di Azure da reti virtuali specifiche
L'integrazione di endpoint del servizio bus di servizio con [rete virtuale (VNet)][vnet-sep] consente di accedere in modo sicuro alle funzionalità di messaggistica da carichi di lavoro come le macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Una volta configurato per essere associato ad almeno un endpoint del servizio subnet della rete virtuale, il rispettivo spazio dei nomi del bus di servizio non accetterà più il traffico da qualsiasi luogo, ma reti virtuali autorizzate e, facoltativamente, indirizzi IP Internet specifici. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi del bus di servizio a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica.

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi del bus di servizio corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici.

## <a name="important-points"></a>Punti importanti
- Le reti virtuali sono supportate solo negli spazi dei nomi del bus di servizio di [livello Premium](service-bus-premium-messaging.md). Quando si usano gli endpoint del servizio VNet con il bus di servizio, è consigliabile non abilitare questi endpoint nelle applicazioni che combinano gli spazi dei nomi del bus di servizio di livello standard e Premium. Poiché il livello standard non supporta reti virtuali. L'endpoint è limitato solo agli spazi dei nomi del livello Premium.
- L'implementazione dell'integrazione delle reti virtuali può impedire l'interazione da parte di altri servizi Azure con il bus di servizio. Come eccezione, è possibile consentire l'accesso alle risorse del bus di servizio da determinati **Servizi attendibili** anche quando gli endpoint di servizio di rete sono abilitati. Per un elenco di servizi attendibili, vedere [Servizi attendibili](#trusted-microsoft-services).

    I servizi Microsoft seguenti devono essere in una rete virtuale
    - Servizio app di Azure
    - Funzioni di Azure
- Specificare almeno **una regola IP o una regola della rete virtuale** per lo spazio dei nomi per consentire il traffico solo dagli indirizzi IP o dalla subnet specificata di una rete virtuale. Se non sono presenti regole IP e reti virtuali, è possibile accedere allo spazio dei nomi tramite la rete Internet pubblica (usando la chiave di accesso).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Per le soluzioni con meccanismi di sicurezza rigidi e suddivisi in vari compartimenti e per le quali le subnet della rete virtuale offrono la segmentazione tra i servizi nei diversi compartimenti, sono in genere comunque necessari percorsi di comunicazione tra i servizi che risiedono in tali compartimenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica offrono percorsi di comunicazione completamente isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti coinvolte. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza del bus di servizio possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Associazione del bus di servizio alle reti virtuali

Le *regole di rete virtuale* rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server del bus di servizio di Azure accetta le comunicazioni inviate da una subnet specifica della rete virtuale.

L'associazione di uno spazio dei nomi del bus di servizio a una rete virtuale è un processo in due passaggi. Per prima cosa è necessario creare un **endpoint di servizio di rete virtuale** in una subnet di rete virtuale e abilitarlo per **Microsoft. ServiceBus** come illustrato nella [Panoramica dell'endpoint di servizio][vnet-sep]. Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi del bus di servizio all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione tra lo spazio dei nomi del bus di servizio e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi del bus di servizio. Il bus di servizio non stabilisce mai direttamente connessioni in uscita e non deve ottenere l'accesso, quindi non ottiene mai l'accesso alla subnet abilitando questa regola.

> [!NOTE]
> Tenere presente che un endpoint del servizio di rete fornisce le applicazioni in esecuzione nella rete virtuale che accedono allo spazio dei nomi del bus di servizio. La rete virtuale controlla la raggiungibilità dell'endpoint, ma non le operazioni che possono essere eseguite sulle entità del bus di servizio (code, argomenti o sottoscrizioni). Utilizzare Azure Active Directory (Azure AD) per autorizzare le operazioni che possono essere eseguite dalle applicazioni sullo spazio dei nomi e sulle relative entità. Per altre informazioni, vedere [autenticare e autorizzare un'applicazione con Azure ad per accedere alle entità del bus di servizio](authenticate-application.md).


## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra come usare portale di Azure per aggiungere un endpoint del servizio rete virtuale. Per limitare l'accesso, è necessario integrare l'endpoint del servizio rete virtuale per questo spazio dei nomi di hub eventi.

1. Passare allo  **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare opzione di **rete** in **Impostazioni**.  

    > [!NOTE]
    > Viene visualizzata la scheda **rete** solo per gli spazi dei nomi **Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Pagina rete-impostazione predefinita" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se si seleziona l'opzione **tutte le reti** , lo spazio dei nomi del bus di servizio accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0. 

    ![Opzione Firewall - Tutte le reti selezionata](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Per limitare l'accesso a specifiche reti virtuali, selezionare l'opzione **reti selezionate** se non è già selezionata.
1. Nella sezione **rete virtuale** della pagina selezionare **+ Aggiungi rete virtuale esistente**. 

    ![aggiungi rete virtuale esistente](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > Se si seleziona l'opzione **reti selezionate** e non si aggiunge almeno una regola del firewall IP o una rete virtuale in questa pagina, è possibile accedere allo spazio dei nomi tramite Internet pubblico (usando la chiave di accesso).
3. Selezionare la rete virtuale dall'elenco di reti virtuali e quindi selezionare la **subnet**. Prima di aggiungere la rete virtuale all'elenco, è necessario abilitare l'endpoint del servizio. Se l'endpoint del servizio non è abilitato, il portale richiederà di abilitarlo.
   
   ![Selezionare una subnet](./media/service-endpoints/select-subnet.png)

4. Dopo che l'endpoint del servizio per la subnet è stato abilitato per **Microsoft. ServiceBus**, dovrebbe essere visualizzato il messaggio seguente. Selezionare **Aggiungi** nella parte inferiore della pagina per aggiungere la rete. 

    ![seleziona subnet e abilita endpoint](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se non è possibile abilitare l'endpoint del servizio, è possibile ignorare l'endpoint del servizio di rete virtuale mancante usando il modello di Gestione risorse. Questa funzionalità non è disponibile sul portale.
6. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. Attendere alcuni minuti prima che la conferma venga visualizzata nelle notifiche del portale. Il pulsante **Salva** deve essere disabilitato. 

    ![Salva rete](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Per istruzioni su come consentire l'accesso da indirizzi o intervalli IP specifici, vedere [consentire l'accesso da indirizzi o intervalli IP specifici](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
Il modello di Gestione risorse di esempio seguente aggiunge una regola della rete virtuale a uno spazio dei nomi del bus di servizio esistente. Per la regola di rete, specifica l'ID di una subnet in una rete virtuale. 

L'ID è un percorso di Gestione risorse completo per la subnet della rete virtuale. Ad esempio, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` per la subnet predefinita di una rete virtuale.

Quando si aggiungono le regole della rete virtuale o dei firewall, impostare il valore di `defaultAction` su `Deny` .

Template:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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
> Se non sono presenti regole IP e reti virtuali, tutto il traffico passa allo spazio dei nomi anche se si imposta `defaultAction` su `deny` .  È possibile accedere allo spazio dei nomi tramite la rete Internet pubblica (usando la chiave di accesso). Specificare almeno una regola IP o una regola della rete virtuale per lo spazio dei nomi per consentire il traffico solo dagli indirizzi IP o dalla subnet specificata di una rete virtuale.  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle reti virtuali, vedere i collegamenti seguenti:

- [Endpoint servizio di rete virtuale di Azure][vnet-sep]
- [Azure Service Bus IP filtering (Filtro IP del bus di servizio di Azure)][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
