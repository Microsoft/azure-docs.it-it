---
title: 'Azure ExpressRoute: configurare ExpressRoute Direct: interfaccia della riga di comando'
description: Questo articolo consente di configurare ExpressRoute Direct usando l'interfaccia della riga di comando di Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 05/20/2019
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4cbacf997413c4eba1e8a142925cc6cde58fb0ec
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488007"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Configurare ExpressRoute Direct usando l'interfaccia della riga di comando di Azure

ExpressRoute Direct offre la possibilità di connettersi direttamente alla rete globale di Microsoft in località di peering distribuite in modo strategico in tutto il mondo. Per altre informazioni, vedere [Informazioni su ExpressRoute Direct Connect](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Creare la risorsa

1. Accedere ad Azure e selezionare la sottoscrizione che contiene ExpressRoute. La risorsa di ExpressRoute Direct e i circuiti ExpressRoute devono essere nella stessa sottoscrizione. Nell'interfaccia della riga di comando di Azure eseguire i comandi seguenti:

   ```azurecli
   az login
   ```

   Controllare le sottoscrizioni per l'account: 

   ```azurecli
   az account list 
   ```

   Selezionare la sottoscrizione per la quale si vuole creare un circuito ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Registrare nuovamente la sottoscrizione a Microsoft. Network per accedere alle API expressrouteportslocation e expressrouteport

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Elencare tutte le località in cui è supportato ExpressRoute Direct:
    
   ```azurecli
   az network express-route port location list
   ```

   **Output di esempio**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Determinare se una delle località elencate nel passaggio precedente ha larghezza di banda disponibile:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Output di esempio**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Creare una risorsa di ExpressRoute Direct basata sulla località scelta nei passaggi precedenti.

   ExpressRoute Direct supporta l'incapsulamento QinQ e Dot1Q. Se si seleziona QinQ, a ogni circuito ExpressRoute viene assegnato dinamicamente un S-Tag e il circuito è univoco per tutta la risorsa di ExpressRoute Direct. Ogni C-Tag nel circuito deve essere univoco nel circuito, ma non in tutta la risorsa di ExpressRoute Direct.  

   Se si seleziona l'incapsulamento Dot1Q, è necessario gestire l'univocità del C-Tag (VLAN) nell'intera risorsa di ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct può essere un solo tipo di incapsulamento. Non è possibile modificare il tipo di incapsulamento dopo aver creato la risorsa di ExpressRoute Direct.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > L'attributo **encapsulation** può anche essere impostato su **Dot1Q**. 
   >

   **Output di esempio**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Modificare AdminState per i collegamenti

Usare questo processo per eseguire un test di livello 1. Assicurarsi che ogni Cross Connection sia correttamente trasferita a ciascun router per le porte primaria e secondaria.

1. Impostare i collegamenti su s**Enabled**. Ripetere questo passaggio per impostare ogni collegamento su **Enabled**.

   Links[0] è la porta primaria e Links[1] è la porta secondaria.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Output di esempio**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Usare la stessa procedura con `AdminState = "Disabled"` per rifiutare le porte.

## <a name="create-a-circuit"></a><a name="circuit"></a>Creare un circuito

Per impostazione predefinita, è possibile creare dieci circuiti nella sottoscrizione che contiene la risorsa di ExpressRoute Direct. Il supporto tecnico Microsoft può aumentare il limite predefinito. Si è responsabili del monitoraggio della larghezza di banda con provisioning e di quella utilizzata. La larghezza di banda con provisioning è la somma della larghezza di banda di tutti i circuiti della risorsa di ExpressRoute Direct. La larghezza di banda utilizzata è l'utilizzo fisico delle interfacce fisiche sottostanti.

È possibile usare altre larghezze di banda del circuito in ExpressRoute Direct solo per supportare gli scenari descritti in questo articolo. Le larghezze di banda sono 40 Gbps e 100 Gbps.

**SkuTier** può essere locale, standard o Premium.

**SkuFamily** deve essere MeteredData solo se illimitato non è supportato in ExpressRoute Direct.
Creare un circuito nella risorsa di ExpressRoute Direct:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Altre larghezze di banda includono 5 Gbps, 10 Gbps e 40 Gbps.

  **Output di esempio**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute Direct, vedere la [panoramica](expressroute-erdirect-about.md).
