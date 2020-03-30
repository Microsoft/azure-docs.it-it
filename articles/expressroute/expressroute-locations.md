---
title: 'Località e provider di connettività: Azure ExpressRoute | Documentazione Microsoft'
description: Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinamento per provider di connettività.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 41e12a93f333936468cbdfbe7aa6bfa6e0b7e8ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278750"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partner e località di peering per ExpressRoute

> [!div class="op_single_selector"]
> * [Posizioni per provider](expressroute-locations.md)
> * [Provider per località](expressroute-locations-providers.md)


Le tabelle in questo articolo forniscono informazioni sulla copertura geografica e le posizioni di ExpressRoute, sui provider di connettività ExpressRoute e sui provider di connettività ExpressRoute System Integrator (SIs).

> [!Note]
> Le aree di Azure e le posizioni ExpressRoute hanno due concetti distinti e diversi, la comprensione della differenza tra i due è fondamentale per esplorare la connettività di rete ibrida di Azure.Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Aree di Azure
Le aree di Azure sono data center globali in cui si trovano le risorse di elaborazione, rete e archiviazione di Azure.Azure regions are global datacenters where Azure compute, networking and storage resources are located. Quando si crea una risorsa di Azure, un cliente deve selezionare una posizione della risorsa. La posizione della risorsa determina in quale data center di Azure (o zona di disponibilità) viene creata la risorsa.

## <a name="expressroute-locations"></a>Posizioni ExpressRouteExpressRoute locations
Le posizioni ExpressRoute (talvolta definite posizioni di peering o meet-me-location) sono strutture di co-location in cui si trovano i dispositivi Microsoft Enterprise Edge (MSEE). Le posizioni ExpressRoute sono il punto di ingresso alla rete Microsoft e sono distribuite a livello globale, offrendo ai clienti l'opportunità di connettersi alla rete Microsoft in tutto il mondo. Questi percorsi sono dove i partner ExpressRoute e i clienti diretti ExpressRoute emettono connessioni incrociate alla rete microsoft. In generale, non è necessario che il percorso ExpressRoute corrisponda all'area di Azure.In general, the ExpressRoute location does not need to match the Azure region. Ad esempio, un cliente può creare un circuito ExpressRoute con il percorso della risorsa *Stati Uniti orientali,* nella posizione di Seattle Peering.For example, a customer can create an ExpressRoute circuit with the resource location East US , in the *Seattle* Peering location.

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Aree di Azure in posizioni ExpressRoute all'interno di un'area geopolitica.
La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

| **Area geopolitica** | **Aree di AzureAzure regions** | **Posizioni ExpressRouteExpressRoute locations** |
| --- | --- | --- |
| **Australia Government** |Australia centrale, Australia centrale 2 |Canberra, Canberra2 |
| **Europa** | Francia Centrale, Francia Meridionale, Germania Nord, Germania Centro Ovest, Europa Settentrionale, Norvegia Est, Norvegia Ovest, Svizzera Nord, Svizzera Ovest, Regno Unito Ovest, Regno Unito Sud, Europa occidentale |Amsterdam, Amsterdam2, Copenaghen, Dublino, Francoforte, Ginevra, Londra, Londra2, Marsiglia, Milano, Monaco, Newport (Galles), Oslo, Parigi, Stavanger, Stoccolma |
| **America del Nord** |Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Asia** | Asia orientale, Asia sudorientale | Bangkok, Hong Kong, Hong Kong2, Giacarta, Kuala Lumpur, Singapore2, Taipei |
| **India** | India occidentale, India centrale, India Meridionale |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Giappone** | Giappone occidentale, Giappone orientale |Osaka, Tokyo, Tokyo2 |
| **Oceania** | Australia sud-orientale, Australia orientale |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Corea del Sud** | Corea centrale, Corea meridionale |Busan, Seoul|
| **Emirati Arabi Uniti** | Emirati Arabi Uniti Centrale, Emirati Arabi Uniti Nord | Dubai, Dubai2 |
| **Sud Africa** | Sud Africa Occidentale, Sud Africa Nord |Città del Capo, Johannesburg |
| **America del Sud** | Brasile meridionale |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Aree e confini geopolitici per cloud nazionali
Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

| **Area geopolitica** | **Aree di AzureAzure regions** | **Posizioni ExpressRouteExpressRoute locations** |
| --- | --- | --- |
| **Cloud del US Gov** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD (area centrale), US DoD (area orientale)  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Cina orientale** |Cina orientale, Cina orientale 2 |Shanghai, Shanghai2 |
| **Cina settentrionale** |Cina settentrionale, Cina settentrionale 2 |Beijing (Pechino), Beijing2 |
| **Germania** |Germania centrale, Germania orientale |Berlino, Francoforte |

La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Provider di connettività ExpressRoute

La tabella seguente mostra le località per provider di servizi. Per visualizzare i provider disponibili in base alla località, vedere la tabella dei [provider di servizi per località](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Azure commerciale globale

| **Provider di servizi** | **Microsoft Azure** | **Office 365**  | **Percorsi** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Supportato |Supportato |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Supportato | Supportato | Chennai2, Mumbai2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Supportato | Supportato | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Hong Kong SAR, San Paolo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Supportato |Supportato |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[A TOKYO](https://www.attokyo.com/service/cloudsconnection/forazure.html)** | Supportato | Supportato | Tokyo2 |
| **[BBIX (informazioni in stato di BBIX](https://www.bbix.net/en/service/ix/)** | Supportato | Supportato | Tokyo |
| **[BCX (informazioni in due)](https://www.bcx.co.za/solutions)** |Supportato |Supportato |Città del Capo|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Supportato |Supportato |Montreal, Toronto, Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Supportato |Supportato |Amsterdam, Hong Kong SAR, Johannesburg, Londra, Newport (Galles), San Paolo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Supportato |Supportato |Miami |
| **CDC** | Supportato | Supportato | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Supportato |Supportato |Amsterdam2, Chicago, Francoforte, Hong Kong, Las Vegas, Londra2, New York, Parigi, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Supportato |Supportato |Hong Kong |
| **Cina Mobile International** |Supportato |Supportato | Singapore |
| **China Telecom Global** |Supportato |Supportato |RAS di Hong Kong |
| **Cina Unicom Globale** |Supportato |Supportato | Singapore2 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Supportato |Supportato |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Supportato |Supportato |Amsterdam, Amsterdam2, Chicago, Dublino, Francoforte, Londra, Londra2, Newport, New York, Osaka, Parigi, Silicon Valley, Silicon Valley2, Singapore2, Tokyo, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Supportato |Supportato |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Supportato |Supportato |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington D.C., Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Supportato |Supportato |Amsterdam2, Francoforte, Marsiglia|
| **[Devoli](https://devoli.com/expressroute)** | Supportato |Supportato | Auckland, Melbourne, Sydney |
| **du datamena** |Supportato |Supportato | Dubai2 |
| **eir** |Supportato |Supportato |Dublino|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Supportato |Supportato |Singapore, Singapore2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Supportato |Supportato |Amsterdam, Atlanta, Chicago, Dallas, Dublino, Francoforte, Ginevra, Hong Kong SAR, Londra, Londra2, Los Angeles, Melbourne, Miami, New York, Osaka, Parigi, San Paolo, Seattle, Silicon Valley, Singapore, Stoccolma, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat Emirati Arabi Uniti** |Supportato |Supportato |Dubai|
| **[euReti](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Supportato |Supportato |Amsterdam, Amsterdam2, Dublino, Londra |
| **FarEasTo** |Supportato |Supportato |Taipei|
| **GÉANT** |Supportato |Supportato |Amsterdam, Francoforte, Marsiglia |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Supportato| Supportato | Chennai, Mumbai |
| **Intelsat** | Supportato | Supportato | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Supportato |Supportato |Amsterdam, Chicago, Hong Kong, Londra, New York, Parigi, Silicon Valley, Singapore, Washington DC |
| **[Internet 2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Supportato |Supportato |Chicago, Dallas, Silicon Valley, Washington D.C. |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Supportato |Supportato |Osaka, Tokyo |
| **[Soluzioni Internet - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Supportato |Supportato |Città del Capo, Johannesburg, Londra |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Supportato |Supportato |Amsterdam, Amsterdam2, Copenaghen, Dublino, Francoforte, Londra, Marsiglia, Parigi, |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Supportato |Supportato | Amsterdam, Londra2, Silicon Valley, Toronto, Washington DC |
| **Rete Jaguar** |Supportato |Supportato |Marsiglia|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Supportato |Supportato |Londra |
| **[LA proprietà (KINX)](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Supportato |Supportato |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Supportato |Supportato |Auckland |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Supportato | Supportato | Amsterdam |
| **[Kt](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Supportato | Supportato | Seul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Londra, Newport (Galles), San Paolo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Supportato |Supportato |Busan, Seoul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Supportato |Supportato |Città del Capo, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato |Supportato |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublino, Francoforte, Ginevra, Hong Kong SAR, Las Vegas, Londra, Londra2, Los Angeles, Melbourne, Miami, Montreal, New York, Oslo, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Sydney2, Tokyo, Toronto, Washington DC, zurighese |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Supportato |Supportato |Londra |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Supportato |Supportato |Dallas, Los Angeles, Miami, San Paolo, Washington DC |
| **[Dati di nuova generazione](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Supportato |Supportato |Newport(Wales) |
| **[DC di NextDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Supportato |Supportato |Melbourne, Perth, Sydney, Sydney2 |
| **[Comunicazioni NTT](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Supportato |Supportato |Amsterdam, Hong Kong SAR, Londra, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Supportato |Supportato |Tokyo |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Supportato |Supportato |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Supportato |Supportato |Marsiglia |
| **[Optus](https://www.optus.com.au/enterprise/)** |Supportato |Supportato |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Supportato |Supportato |Amsterdam, Amsterdam2, Dubai2, Francoforte, Hong Kong SAR, Johannesburg, Londra, Parigi, San Paolo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Supportato | Supportato | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Supportato |Supportato |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Supportato |Supportato |Chicago, Hong Kong - R.A., Londra |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Supportato |Supportato |Seul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Supportato |Supportato | Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Supportato |Supportato |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Supportato |Supportato |Singapore, Singapore2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Supportato |Supportato |Osaka, Tokyo |
| **[Scintilla N.A.](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Supportato |Supportato |Auckland |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Supportato |Supportato |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Supportato | Supportato | Zurigo |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Supportato |Supportato |Amsterdam, Chennai, Hong Kong SAR, Londra, Mumbai, San Paolo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Supportato |Supportato |Amsterdam, San Paolo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Supportato |Supportato |London, Londra2 |
| **Telenor** |Supportato |Supportato |Amsterdam, Londra, Oslo |
| **[Telia Portatore](https://www.teliacarrier.com/)** | Supportato | Supportato |Amsterdam, Chicago, Dallas, Francoforte, Hong Kong, Londra, Oslo, Parigi, Silicon Valley, Stoccolma, Washington DC |
| **Telmex Uninet**| Supportato | Supportato | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Supportato |Supportato |Melbourne, Singapore, Sydney |
| **[Telus](https://www.telus.com)** |Supportato |Supportato |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Supportato |Supportato |Città del Capo, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Supportato | Supportato | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Supportato |Supportato |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Supportato |Supportato |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Hong Kong SAR, Londra, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Supportato | Supportato | Washington DC2 |
| **[Gruppo Vocus N.](https://www.vocus.co.nz/business/cloud-data-centres)** | Supportato | Supportato | Auckland |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Supportato |Supportato |Amsterdam2, Londra, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Supportato | Supportato | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Denver, Londra, Los Angeles, Montreal, New York, Parigi, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** denota in arrivo

### <a name="national-cloud-environment"></a>Ambiente cloud nazionale

I cloud nazionali di Azure sono isolati l'uno dall'altro e da Azure commerciale globale. ExpressRoute per un cloud di Azure non può connettersi alle aree di Azure nelle altre. 

### <a name="us-government-cloud"></a>Cloud del US Gov

| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Percorsi** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Supportato |Supportato |Chicago, Phoenix, Silicon Valley, Washington D.C. |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Supportato |Supportato |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Supportato |Supportato |Atlanta,Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Supportato |Supportato |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato | Supportato | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Supportato |Supportato |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Cina

| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Percorsi** |
| --- | --- | --- | --- |
| **China Telecom** |Supportato |Non supportato |Pechino, Pechino2, Shanghai, Shanghai2 |
| **Cinese Unicom** | Supportato | Non supportato | Beijing2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Supportato |Non supportato |Beijing2, Shanghai2 |

Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Germania

| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Percorsi** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Supportato |Non supportato |Francoforte |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Supportato |Non supportato |Francoforte |
| **[E-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Supportato |Non supportato |Berlino |
| **Interxion** |Supportato |Non supportato |Francoforte |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato  | Non supportato | Berlino |
| **T-Systems** |Supportato |Non supportato |Berlino |

## <a name="connectivity-through-exchange-providers"></a>Connettività tramite provider di Exchange

Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

* Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC (Paesi in stato di Post](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
  * Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
* Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
  * Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Connettività attraverso operatori satellitari
Se sei remoto e non hai connettività in fibra ottica o vuoi esplorare altre opzioni di connettività, puoi controllare i seguenti operatori satellitari. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connettività tramite provider di servizi aggiuntivi

| **Provider di connettività** | **Exchange** | **Percorsi** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapore |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-connect)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londra |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Francoforte, Londra, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | RAS di Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Francoforte, Amburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapore | 
| **[Tecnologie Aptum](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Londra |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Castello della Corona](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londra, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Esponenziale E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londra |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londra, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C.](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapore|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |RAS di Hong Kong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Città del Capo, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londra |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Francoforte |
| **[POST Telecom Lussemburgo](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublino, Londra, Parigi |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Francoforte |  
| **[RETN (ReTN)](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Impresa Spectrum](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londra | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Francoforte |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londra|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connettività tramite provider di data center

| **Provider** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Banca dati](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Copertura IX, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flessibilità](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | Copertura IX, Megaport, PacketFabric |
| **[Data center QTS](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Centri dati di streaming]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | Copertura IX, Megaport, PacketFabric |
| **[vXchnge (informazioni in inglese)](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connettività attraverso le reti nazionali di ricerca e istruzione (NREN)

| **Provider**|
| --- |
| **AARNET**| 
| **DeIC, tramite GÉANT**|
| **GARR, tramite GÉANT**|
| **GÉANT**|
| **HEAnet, tramite GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, tramite GÉANT**|
| **SINET**|
| **Surfnet, tramite GÉANT**|

* Se il provider di connettività non è elencato qui, verificare se è connesso a uno dei partner di scambio di ExpressRoute indicati sopra.

## <a name="expressroute-system-integrators"></a>Integratori di sistemi ExpressRoute
L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

| **Integratore di sistemi** | **Continent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asia, Europa, America del Nord, America del Sud |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | America del Nord |
| **[FlexManage](https://www.flexmanage.com/cloud)** | America del Nord |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | America del Nord |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australia |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Germania) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | America del Nord |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | America del Nord |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | America del Sud |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle posizioni"
