---
title: Aumentare automaticamente le unità elaborate - Hub eventi di Azure | Microsoft Docs
description: Abilitare Aumento automatico in uno spazio dei nomi per aumentare le unità elaborate.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537242"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Aumentare automaticamente le unità elaborate di Hub eventi di Azure
Hub eventi di Azure è una piattaforma di streaming dei dati altamente scalabile. L'uso di Hub eventi, quindi, spesso aumenta dopo che si è iniziato a usare il servizio. Questo tipo di utilizzo richiede l'aumento delle [unità](event-hubs-scalability.md#throughput-units) elaborate predeterminate per scalare Hub eventi e gestire velocità di trasferimento maggiori. La funzionalità **Aumento automatico** di Hub eventi aumenta automaticamente le prestazioni aumentando il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

* Le velocità di ingresso dei dati superano le unità elaborate impostate.
* Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

Il servizio Hub eventi aumenta la velocità effettiva quando il carico supera la soglia minima, senza che le richieste abbiano esito negativo con errori ServerBusy.

## <a name="how-auto-inflate-works"></a>Funzionamento di Aumento automatico

Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-scalability.md#throughput-units). Una singola unità elaborata consente 1 MB al secondo in ingresso e il doppio in uscita. Gli hub eventi standard possono essere configurati con un numero di unità elaborate compreso tra 1 e 20. Aumento automatico consente di iniziare gradualmente con il minimo di unità elaborate richiesto a scelta. La funzionalità aumenta quindi automaticamente le unità elaborate fino al limite massimo necessario, a seconda dell'aumento del traffico. Aumento automatico offre i seguenti vantaggi:

- Un meccanismo di scala efficiente per iniziare con poche unità elaborate e aumentarle al bisogno.
- Aumenta automaticamente le unità elaborate fino al limite superiore specificato senza problemi di limitazioni.
- Offre maggiore controllo sull'aumento di prestazioni perché consente di specificare quando e come gestire l'aumento.

## <a name="enable-auto-inflate-on-a-namespace"></a>Abilitare Aumento automatico in uno spazio dei nomi

È possibile abilitare o disabilitare il gonfiaggio automatico in uno spazio dei nomi di hub eventi di livello standard usando uno dei metodi seguenti:

- [Portale di Azure](https://portal.azure.com).
- [Modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Gli spazi dei nomi di hub eventi di livello Basic non supportano il gonfiaggio automatico.

### <a name="enable-auto-inflate-through-the-portal"></a>Abilitare Aumento automatico tramite il portale


#### <a name="enable-at-the-time-of-creation"></a>Abilitare al momento della creazione 
È possibile abilitare la funzionalità di aumento automatico quando si **crea uno spazio dei nomi di hub eventi**:
 
![Attivare l'aumento automatico nel momento della creazione di hub eventi](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Dopo avere abilitato questa opzione, è possibile iniziare con un numero ridotto di unità elaborate e aumentarle in funzione delle esigenze di utilizzo. Il limite superiore per l'aumento non influisce immediatamente sui prezzi, che dipendono dal numero di unità elaborate usate all'ora.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Abilitare l'aumento automatico per un hub eventi esistente
È anche possibile abilitare la funzionalità di aumento automatico e modificarne le impostazioni usando le istruzioni seguenti: 
 
1. Nella pagina **Spazio dei nomi degli Hub eventi di Azure**, selezionare **Disabilitato** in **Aumento automatico delle unità elaborate**.  

    ![Selezionare le unità elaborate nella pagina di spazio dei nomi di Hub eventi di Azure](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Nella pagina delle **Impostazioni di scalabilità**, selezionare la casella di controllo per **Abilitare** (se non è stata abilitata la funzionalità di scalabilità automatica).

    ![Selezionare Abilita](./media/event-hubs-auto-inflate/scale-settings.png)
3. Immettere il numero **massimo** di unità elaborate o usare la barra di scorrimento per impostare il valore. 
4. (facoltativo) Aggiornare il numero **minimo** di unità elaborate nella parte superiore della pagina. 


> [!NOTE]
> Quando si applica la configurazione per l'aumento automatico delle unità elaborate, il servizio Hub eventi crea i log di diagnostica che forniscono informazioni su quando e perché la velocità effettiva è aumentata. Per abilitare la registrazione diagnostica per un hub eventi, selezionare **Impostazioni di diagnostica** nel menu a sinistra della pagina Hub eventi nel portale di Azure. Per altre informazioni, vedere [Log di diagnostica di Hub eventi](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Abilitare Aumento automatico usando un modello di Azure Resource Manager

È possibile abilitare Aumento automatico durante la distribuzione di un modello di Azure Resource Manager. Ad esempio, impostare la proprietà `isAutoInflateEnabled` su **true**, quindi impostare `maximumThroughputUnits` su 10. Ad esempio:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Per il modello completo, vedere il modello [Create Event Hubs namespace and enable inflate (Creare uno spazio dei nomi Hub eventi e abilitare l'aumento)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) in GitHub.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](./event-hubs-about.md)
