---
title: È possibile configurare il tunneling forzato?
description: Informazioni su come abilitare l'ambiente del servizio app per i casi in cui il traffico in uscita è sottoposto a tunneling forzato nella rete virtuale.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 3334a19b1ba0e3949ab2670c5d2f70d3bcd02fe8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983911"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurare Ambiente del servizio app con il tunneling forzato

L'ambiente del servizio app (ASE) è una distribuzione del servizio app di Azure in una rete virtuale di Azure di un cliente. Molti clienti configurano le proprie reti virtuali di Azure come estensioni delle proprie reti locali con VPN o connessioni Azure ExpressRoute. Per tunneling forzato si intende il reindirizzamento del traffico associato a Internet alla propria VPN o a un'appliance virtuale. Le appliance virtuali vengono spesso usate per esaminare e controllare il traffico di rete in uscita. 

L'ambiente del servizio app ha diverse dipendenze esterne, descritte nel documento sull'[architettura di rete dell'ambiente del servizio app][network]. In genere tutto il traffico in uscita dell'ambiente del servizio app relativo alle dipendenze deve transitare attraverso l'indirizzo VIP di cui è stato eseguito il provisioning con l'ambiente del servizio app. Se si modifica il routing per il traffico da o verso l'ambiente del servizio app senza attenersi alle indicazioni seguenti, l'ambiente del servizio app smetterà di funzionare.

In una rete virtuale di Azure il routing viene eseguito in base all'algoritmo LPM (Longest Prefix Match). Se è presente più di una route con la stessa corrispondenza LPM, viene selezionata la route in base all'origine nell'ordine seguente:

* Route definita dall'utente
* Route BGP (quando viene utilizzato ExpressRoute)
* Route di sistema

Per altre informazioni sul routing in una rete virtuale, vedere [Route definite dall'utente e inoltro degli indirizzi IP][routes]. 

Se non si desidera instradare il traffico in uscita dell'ambiente del servizio app direttamente a Internet ma a una destinazione diversa, le scelte sono:

* Abilitare un accesso a Internet diretto per l'ambiente del servizio di app
* Configurare la subnet dell'ambiente del servizio app per ignorare le route BGP
* Configurare la subnet dell'ambiente del servizio app per l'uso degli endpoint servizio di SQL di Azure e Archiviazione di Azure
* Aggiungere i propri indirizzi IP al firewall SQL di Azure dell'ambiente del servizio app

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Concedere ad Ambiente del servizio app l'accesso a Internet diretto

Per consentire all'ambiente del servizio app l'accesso diretto a Internet anche se la rete virtuale di Azure è configurata con ExpressRoute, è possibile:

* Configurare ExpressRoute perché annunci 0.0.0.0/0. Per impostazione predefinita, tutto il traffico in uscita viene instradato in locale.
* Creare un routing definito dall'utente con prefisso indirizzo uguale a 0.0.0.0/0 e tipo di hop successivo uguale a Internet e applicarlo alla subnet dell'ambiente del servizio app.

Se si apportano queste due modifiche, il traffico destinato a Internet proveniente dalla subnet dell'ambiente del servizio app non verrà forzato attraverso la connessione ExpressRoute.

Se la rete indirizza già il traffico in locale, è necessario creare la subnet per l'hosting dell'ambiente del servizio app e configurare la rispettiva route definita dall'utente prima di provare a distribuire l'ambiente del servizio app.  

> [!IMPORTANT]
> Le route definite in una route definita dall'utente devono essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio precedente viene usato l'intervallo di indirizzi ampio 0.0.0.0/0. Può essere accidentalmente sostituito dagli annunci di route che usano intervalli di indirizzi più specifici.
>
> Le istanze di Ambiente del servizio app non sono supportate con configurazioni ExpressRoute con annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Le configurazioni di ExpressRoute con peering pubblico configurato riceveranno gli annunci di route da Microsoft. Gli annunci contengono un ampio set di intervalli di indirizzi di Microsoft Azure. In caso di annuncio incrociato degli intervalli di indirizzi nel percorso di peering privato, tutti i pacchetti di rete in uscita dalla subnet dell'ambiente del servizio app verranno instradati verso un'infrastruttura di rete locale del cliente. Questo flusso di rete non è supportato per impostazione predefinita con gli ambienti del servizio app. Una soluzione a questo problema consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Un'altra soluzione consiste nel consentire ad Ambiente del servizio app di operare in una configurazione con tunneling forzato.

![Accesso diretto a Internet][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Configurare la subnet dell'ambiente del servizio app per ignorare le route BGP ## 

Puoi configurare la subnet dell'ambiente del servizio app per ignorare tutte le route BGP.  Quando configurate in modo da ignorare le route BGP, l'ambiente del servizio app riesce ad accedere alle relative dipendenze senza problemi.  Dovrai creare route definite dall'utente, tuttavia per abilitare le app accedi alle risorse locali.

Per configurare la subnet dell'ambiente del servizio app al fine di ignorare le route BGP:

* crea una route definita dall'utente e assegnala alla subnet dell'ambiente del servizio app se non ne disponi già.
* Nel portale di Azure, apri l'interfaccia utente per la tabella di route assegnata alla subnet dell'ambiente del servizio app.  Seleziona Configurazione.  Impostare Propagazione route del gateway di rete virtuale su Disabilitata.  Fare clic su Salva. La documentazione sulla disattivazione è consultabile nell'articolo [Creare una tabella di route][routetable].

Dopo aver configurato la subnet dell'ambiente del servizio app di Azure in modo da ignorare tutte le route BGP, le app non riusciranno più a raggiungere l'ambiente locale. Per abilitare le app ad accedere alle risorse in locale, modificare la route definita dall'utente assegnata alla subnet dell'ambiente del servizio app e aggiungere le route per gli intervalli degli indirizzi locali. Il tipo di hop successivo deve essere impostato su Gateway di rete virtuale. 


## <a name="configure-your-ase-with-service-endpoints"></a>Configurare l'ambiente del servizio app con endpoint servizio ##

Per instradare tutto il traffico in uscita dall'ambiente del servizio app, ad eccezione del traffico diretto a SQL di Azure e Archiviazione di Azure, procedere come segue:

1. Creare una tabella di route e assegnarla alla subnet dell'ambiente del servizio app. Trovare gli indirizzi corrispondenti alla propria area qui: [Indirizzi di gestione dell'Ambiente del servizio app][management]. Creare route per tali indirizzi con hop successivo uguale a Internet. Queste route sono necessarie perché il traffico di gestione in ingresso dell'ambiente del servizio app deve rispondere dallo stesso indirizzo al quale è stato inviato.   

2. Abilitare gli endpoint servizio con SQL di Azure e Archiviazione di Azure con la subnet dell'ambiente del servizio app.  Dopo il completamento di questo passaggio, è possibile configurare la rete virtuale con il tunneling forzato.

Per creare un ambiente del servizio app in una rete virtuale già configurata per l'indirizzamento di tutto il traffico in locale, è necessario creare l'ambiente del servizio app usando un modello di Resource Manager.  Non è possibile creare un ambiente del servizio app con il portale in una subnet già esistente.  Quando si distribuisce l'ambiente del servizio app in una rete virtuale già configurata per l'indirizzamento del traffico in uscita in locale, è necessario creare l'ambiente del servizio app usando un modello di Resource Manager che consente di specificare una subnet già esistente. Per informazioni dettagliate sulla distribuzione di un ambiente del servizio app con un modello, vedere [Creare un ambiente del servizio app usando un modello][template].

Gli endpoint servizio consentono di limitare l'accesso ai servizi multi-tenant a un set di reti e subnet virtuali di Azure. Per altre informazioni sugli endpoint servizio, vedere la pagina [Endpoint servizio di rete virtuale][serviceendpoints]. 

Quando si abilitano gli endpoint del servizio su una risorsa, alcune route sono create con una priorità maggiore rispetto a tutte le altre route. Se si usano gli endpoint servizio con un ambiente del servizio app con tunneling forzato, il tunneling del traffico di gestione di SQL di Azure e Archiviazione di Azure non viene forzato. L'altro traffico dell'ambiente del servizio app relativo alle dipendenze viene sottoposto a tunneling forzato e non può andare perso. In caso contrario, l'ambiente del servizio app non funzionerà correttamente.

Quando gli endpoint servizio sono abilitati in una subnet con un'istanza di SQL di Azure, tutte le istanze di SQL di Azure verso cui si esegue la connessione da tale subnet devono avere gli endpoint servizio abilitati. Se si desidera accedere a più istanze di SQL di Azure dalla stessa subnet, non è possibile abilitare gli endpoint servizio in un'istanza di SQL di Azure e non in un'altra.  Il comportamento di Archiviazione di Azure è diverso da quello di SQL di Azure.  Quando si abilitano gli endpoint servizio con Archiviazione di Azure, si blocca l'accesso a tale risorsa dalla propria subnet ma è comunque possibile accedere ad altri account di archiviazione di Azure, anche se non hanno gli endpoint servizio abilitati.  

Se si configura il tunneling forzato con un'appliance di filtro rete, tenere presente che l'ambiente del servizio app ha alcune dipendenze oltre a SQL di Azure e Archiviazione di Azure Se il traffico verso tali dipendenze è bloccato, l'ambiente del servizio app non funzionerà correttamente.

![Tunneling forzato con gli endpoint servizio][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Aggiungere i propri indirizzi IP al firewall SQL di Azure dell'ambiente del servizio app ##

Per il tunneling di tutto il traffico in uscita dall'ambiente del servizio app, ad eccezione del traffico diretto ad Archiviazione di Azure, procedere come segue:

1. Creare una tabella di route e assegnarla alla subnet dell'ambiente del servizio app. Trovare gli indirizzi corrispondenti alla propria area qui: [Indirizzi di gestione dell'Ambiente del servizio app][management]. Creare route per tali indirizzi con hop successivo uguale a Internet. Queste route sono necessarie perché il traffico di gestione in ingresso dell'ambiente del servizio app deve rispondere dallo stesso indirizzo al quale è stato inviato. 

2. Abilitare gli endpoint servizio con Archiviazione di Azure con la subnet dell'ambiente del servizio app

3. Ottenere gli indirizzi che saranno usati per tutto il traffico in uscita dall'ambiente del servizio app a Internet. Se si esegue il routing del traffico in locale, questi indirizzi corrisponderanno ai NAT o agli IP del gateway. Se si vuole instradare il traffico in uscita di Ambiente del servizio app attraverso un'appliance virtuale di rete, l'indirizzo in uscita è l'indirizzo IP pubblico dell'appliance virtuale di rete.

4. _Per impostare gli indirizzi in uscita in un ambiente del servizio app esistente:_ passare a resources.azure.com e quindi a Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name>. Sarà possibile visualizzare il codice JSON che descrive Ambiente del servizio app. Assicurarsi che sia specificato **Lettura/Scrittura** nella parte superiore. Selezionare **Modifica**. Scorrere fino alla fine. Modificare il valore di **userWhitelistedIpRanges** da **null** a un altro valore simile al seguente. Usare gli indirizzi che si desidera impostare come intervallo degli indirizzi in uscita. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Selezionare **PUT** nella parte superiore. Questa opzione attiva un'operazione di ridimensionamento in Ambiente del servizio app e modifica il firewall.

_Per creare l'ambiente del servizio app con gli indirizzi in uscita_: seguire le indicazioni riportate in [Creare un ambiente del servizio app con un modello][template] e scaricare il modello appropriato.  Modificare la sezione "resources" nel file azuredeploy.json, ma non nel blocco "properties", e includere una riga per **userWhitelistedIpRanges** con i propri valori.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

Con queste modifiche, il traffico verrà inviato ad Archiviazione di Azure direttamente dall'ambiente del servizio app e sarà consentito l'accesso a SQL di Azure da altri indirizzi, oltre che dall'indirizzo VIP dell'ambiente del servizio app.

   ![Tunneling forzato con elenco elementi consentiti SQL][3]

## <a name="preventing-issues"></a>Prevenzione dei problemi ##

Se la comunicazione tra l'ambiente del servizio app e le relative dipendenze si interrompe, l'integrità dell'ambiente del servizio app sarà compromessa.  Se lo stato di mancata integrità permane troppo a lungo, l'ambiente del servizio app verrà sospeso. Per annullare la sospensione dell'ambiente del servizio app, seguire le istruzioni fornite nel portale dell'ambiente del servizio app.

Oltre alla semplice interruzione della comunicazione, anche l'introduzione di un'eccessiva latenza può avere effetti negativi sull'ambiente del servizio app. Una latenza eccessiva può verificarsi se l'ambiente del servizio app è troppo lontano dalla rete locale.  Esempi di distanza eccessiva sono l'attraversamento di un oceano o un continente per raggiungere la rete locale. La latenza può essere causata anche da una congestione in Internet o da vincoli di larghezza di banda in uscita.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
