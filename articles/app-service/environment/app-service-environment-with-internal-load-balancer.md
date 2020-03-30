---
title: Creare un'istruzione ILB ASE v1Create an ILB ASE v1
description: Creazione e utilizzo di un'insieme di informazioni del bene con un'ilB. Questo documento viene fornito solo per i clienti che utilizzano l'app ase versione 21 legacy.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294728"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app

> [!NOTE] 
> Questo articolo riguarda l'ambiente del servizio app v1. È disponibile una versione più recente dell'ambiente del servizio app più facile da usare ed eseguita in un'infrastruttura più potente. Per altre informazioni sulla nuova versione, vedere [Introduzione ad Ambiente del servizio app](intro.md).
>

La funzionalità degli ambienti del servizio app è un'opzione di servizio Premium del servizio app di Azure che offre una funzionalità di configurazione avanzata non disponibile negli stamp multi-tenant. In breve, la funzionalità Ambiente del servizio app distribuisce il servizio app di Azure nella rete virtuale di Azure (VNet). Per informazioni approfondite delle funzionalità offerte dagli ambienti del servizio app, vedere [Informazioni sull'ambiente del servizio app][WhatisASE]. Se non si conoscono i vantaggi derivanti dall'uso di una rete virtuale, vedere [Domande frequenti sulla rete virtuale di Azure][virtualnetwork]. 

## <a name="overview"></a>Panoramica
Un ambiente del servizio app può essere distribuito con un endpoint accessibile da Internet o con un indirizzo IP nella rete virtuale. Per impostare l'indirizzo IP su un indirizzo di rete virtuale, è necessario distribuire l'ambiente del servizio app con un bilanciamento del carico interno (ILB). Quando l'ambiente è configurato con un bilanciamento del carico interno, fornire:

* il proprio dominio o sottodominio. Per maggiore semplicità, in questo documento si presuppone l'uso di un sottodominio, ma sono possibili entrambe le configurazioni. 
* il certificato usato per HTTPS
* la gestione del DNS per il sottodominio. 

È possibile eseguire operazioni come:

* l'hosting di applicazioni intranet, ad esempio applicazioni line of business, in modo sicuro nel cloud a cui si accede tramite una VPN ExpressRoute o da sito a sito
* l'hosting delle app nel cloud non presenti nei server DNS pubblici
* la creazione di applicazioni back-end isolate da Internet con cui le app front-end possono integrarsi in modo sicuro

#### <a name="disabled-functionality"></a>Funzionalità disabilitata
Non è possibile eseguire alcune operazioni quando si usa un ambiente del servizio app con bilanciamento del carico interno. Alcuni esempi sono:

* uso di IPSSL
* assegnazione di indirizzi IP ad app specifiche
* acquisto e uso di un certificato con un'app tramite il portale. È ovviamente ancora possibile ottenere i certificati direttamente con un'autorità di certificazione e usarli con le app, ma non tramite il portale di Azure.

## <a name="creating-an-ilb-ase"></a>Creazione di un ambiente del servizio app con bilanciamento del carico interno
La creazione di un ambiente del servizio app con bilanciamento del carico interno non è molto diversa dalla creazione di un ambiente del servizio app regolare. Per un esame più approfondito della creazione di un ambiente del servizio app, vedere [Come creare un ambiente del servizio app][HowtoCreateASE]. La procedura di creazione di un ambiente del servizio app con bilanciamento del carico interno è la stessa nella creazione di una rete virtuale durante la creazione dell'ambiente del servizio app o nella selezione di una rete virtuale già esistente. Per creare un ambiente del servizio app con bilanciamento del carico interno: 

1. Nel portale di Azure selezionare **Crea una risorsa -> Web e dispositivi mobili-> Ambiente del servizio app**.
2. Selezionare la propria sottoscrizione.
3. Selezionare o creare un gruppo di risorse.
4. Selezionare o creare una rete virtuale.
5. Creare una subnet se si seleziona una rete virtuale.
6. Selezionare Rete virtuale/Percorso -> Configurazione di rete virtuale e impostare Il tipo di VIP su Interno.Select **Virtual Network/Location -> VNet Configuration** and set the VIP Type to Internal.
7. Fornire un nome di sottodominio; questo sarà il sottodominio usato per le app create con questo ambiente del servizio app.
8. Selezionare **OK**, quindi **Crea**.

![][1]

Nel riquadro Rete virtuale è disponibile l'opzione Configurazione rete virtuale che consente di scegliere tra un indirizzo VIP esterno o interno. L'opzione predefinita è Esterno. Se si seleziona Esterno, l'ambiente del servizio app userà un indirizzo VIP accessibile da Internet. Se si seleziona l'opzione Interno, l'ambiente del servizio app verrà configurato con un bilanciamento del carico interno su un indirizzo IP all'interno della rete virtuale. 

Dopo aver selezionato Interno, non sarà possibile aggiungere altri indirizzi IP all'ambiente del servizio app e sarà invece necessario specificare il sottodominio dell'ambiente del servizio app. In un ambiente del servizio app con un indirizzo VIP esterno viene usato il nome dell'ambiente nel sottodominio per le app create in questo ambiente. Se l'ambiente del servizio app è denominato ***contosotest*** e l'app in questo ambiente è denominata ***mytest***, il formato del sottodominio sarà ***contosotest.p.azurewebsites.net*** e l'URL di questa app sarà ***mytest.contosotest.p.azurewebsites.net***. Se si imposta il tipo di indirizzo VIP su Interno, il nome dell'ambiente del servizio app non viene usato nel sottodominio per l'ambiente. Specificare il sottodominio in modo esplicito. Se il sottodominio è ***contoso.corp.net*** ed è stata creata un'app in un ambiente del servizio app denominata ***timereporting***, l'URL per questa app sarà ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>App in un ambiente del servizio app con bilanciamento del carico interno
La creazione di un'app in un ambiente del servizio app con bilanciamento del carico interno è la stessa che in un ambiente del servizio app regolare. 

1. Nel portale di Azure selezionare **Crea una risorsa -> Web e dispositivi mobili -> Web** oppure **Dispositivi mobili** o **App per le API**.
2. Immettere il nome dell'app.
3. Selezionare la propria sottoscrizione.
4. Selezionare o creare un gruppo di risorse.
5. Selezionare o creare un piano di servizio app. Se si crea un nuovo piano di servizio app, selezionare l'ambiente del servizio app come posizione e il pool di lavoro in cui si vuole creare il piano di servizio. Quando si crea il piano di servizio app, selezionare l'ambiente del servizio app come posizione e il pool di lavoro. Quando si specifica il nome dell'app, si noterà che il sottodominio sotto il nome dell'app verrà sostituito con il sottodominio dell'ambiente del servizio app. 
6. Selezionare **Crea**. Selezionare la casella di controllo **Aggiungi al dashboard** se si vuole che l'app venga visualizzata nel dashboard. 

![][2]

Sotto il nome dell'app il nome del sottodominio viene aggiornato per riflettere il sottodominio dell'ambiente del servizio app. 

## <a name="post-ilb-ase-creation-validation"></a>Convalida dopo la creazione dell'ambiente del servizio app con bilanciamento del carico interno
Un ambiente del servizio app con bilanciamento del carico interno è leggermente diverso rispetto all'ambiente del servizio app senza bilanciamento del carico interno. Come già notato è necessario gestire il proprio DNS e fornire un proprio certificato per le connessioni HTTPS. 

Dopo la creazione dell'ambiente del servizio app, si noterà che il sottodominio mostra il sottodominio specificato e che esiste un nuovo elemento nel menu **Impostazione** denominato **Certificato ILB**. L'ambiente del servizio app viene creato con un certificato autofirmato, che semplifica i test di HTTPS. Il portale indica che è necessario specificare un certificato per HTTPS, in modo che sia disponibile un certificato associato al sottodominio. 

![][3]

Se si stanno semplicemente eseguendo alcune prove e non si conosce la procedura per creare un certificato, è possibile usare l'applicazione della console MMC IIS per creare un certificato autofirmato. Dopo la creazione è possibile esportarlo come file PFX e caricarlo nell'interfaccia utente dei certificati ILB. Quando si accede a un sito protetto con un certificato autofirmato, il browser visualizza un avviso per indicare che il sito a cui si sta accedendo non è protetto perché non è possibile convalidare il certificato. Per evitare che venga visualizzato l'avviso, è necessario un certificato firmato correttamente che corrisponda al sottodominio e abbia una catena di certificati riconosciuta dal browser.

![][6]

Se si vuole provare il flusso con i propri certificati e testare l'accesso HTTP e HTTPS all'ambiente del servizio app:

1. Passare all'interfaccia utente dell'ambiente del servizio app dopo la creazione dell'ambiente **Ambiente del servizio app -> Impostazioni -> Certificati ILB**.
2. Impostare il certificato ILB selezionando il file PFX e fornire la password. L'elaborazione di questo passaggio richiede tempo e verrà visualizzato il messaggio indicante un'operazione di ridimensionamento in corso.
3. Ottenere l'indirizzo ILB per l'ambiente del servizio app (**Ambiente del servizio app -> Proprietà -> Indirizzo IP virtuale**).
4. Creare un'app Web nell'ambiente del servizio app dopo la creazione. 
5. Creare una VM, se non è disponibile in questa rete virtuale; non crearla nella stessa subnet dell'ambiente del servizio app per evitare errori.
6. Impostare il DNS per il sottodominio. È possibile usare un carattere jolly con il sottodominio nel DNS o se si vuole eseguire alcuni semplici test, modificare il file hosts nella VM per impostare il nome della app Web all'indirizzo IP VIP. Se il nome di sottodominio dell'ambiente del servizio app è ilbase.com e l'app Web mytestapp è stata creata in modo da essere indirizzata a mytestapp.ilbase.com, impostare questo valore nel file hosts. In Windows il file hosts si trova in C:\Windows\System32\drivers\etc\\)
7. Usare un browser in questa macchina virtuale e passare a `https://mytestapp.ilbase.com` o qualunque sia il nome dell'app Web con il sottodominio.
8. Usando un browser nella macchina virtuale, passare a `https://mytestapp.ilbase.com`. Se si usa un certificato autofirmato, è necessario accettare l'assenza di sicurezza. 

L'indirizzo IP per il bilanciamento del carico interno è elencato nelle proprietà come indirizzo IP virtuale.

![][4]

## <a name="using-an-ilb-ase"></a>Uso di un ambiente del servizio app con bilanciamento del carico interno
#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Un ambiente del servizio app con bilanciamento del carico interno consente l'isolamento della rete per le app perché le app non sono accessibili o neanche note in Internet. Questo approccio è ideale per ospitare i siti intranet, ad esempio le applicazioni line of business. Quando è necessario limitare ulteriormente l'accesso, è comunque possibile usare i gruppi di sicurezza di rete (NSG) per controllare l'accesso a livello di rete. 

Per usare i gruppi di sicurezza di rete per limitare ulteriormente l'accesso, è necessario assicurarsi di non interrompere la comunicazione necessaria per il funzionamento dell'ambiente del servizio app. Anche se l'accesso HTTP/HTTPS avviene solo tramite il bilanciamento del carico interno usato dall'ambiente del servizio app, l'ambiente dipende comunque da risorse esterne alla rete virtuale. Per verificare quale accesso di rete è necessario, vedere [Come controllare il traffico in ingresso a un ambiente del servizio app][ControlInbound] e [Dettagli della configurazione di rete per gli ambienti del servizio app con ExpressRoute][ExpressRoute]. 

Per configurare i gruppi di sicurezza di rete è necessario conoscere l'indirizzo IP usato da Azure per la gestione dell'ambiente del servizio app. Questo indirizzo IP è l'indirizzo IP in uscita dell'ambiente del servizio app se questo esegue richieste tramite internet. L'indirizzo IP in uscita per l'ambiente del servizio app rimane statico per l'intera durata dell'ambiente. Se si elimina e si ricrea l'ambiente del servizio app, si ottiene un nuovo indirizzo IP. Per trovare l'indirizzo IP, passare a **Impostazioni -> Proprietà** e trovare **Indirizzo IP in uscita**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Gestione generale dell'ambiente del servizio app con bilanciamento del carico interno
La gestione di un ambiente del servizio app con bilanciamento del carico interno è sostanzialmente la stessa che in un ambiente del servizio app regolare. È necessario aumentare le prestazioni dei pool di lavoro per ospitare più istanze del piano di servizio app e aumentare le prestazioni dei server front-end per gestire un maggiore traffico HTTP/HTTPS. Per informazioni generali sulla gestione della configurazione di un ambiente del servizio app, vedere [Configurazione di un ambiente del servizio app][ASEConfig]. 

Le attività di gestione aggiuntive sono la gestione dei certificati e la gestione del DNS. È necessario ottenere e caricare il certificato usato per HTTPS dopo la creazione dell'ambiente del servizio app con bilanciamento del carico interno e sostituirlo prima della scadenza. Azure possiede il dominio base, quindi può fornire i certificati per gli ambienti del servizio app con un indirizzo VIP esterno. Poiché il sottodominio usato da un ambiente del servizio app con bilanciamento del carico interno può essere un qualsiasi elemento, è necessario fornire un certificato per HTTPS. 

#### <a name="dns-configuration"></a>Configurazione del DNS
Quando si usa un indirizzo VIP esterno, il DNS viene gestito da Azure. Qualsiasi app creata nell'ambiente del servizio app viene aggiunta automaticamente al DNS di Azure, che è un DNS pubblico. In un ambiente del servizio app ILB, è necessario gestire il proprio DNS. Per un sottodominio specifico, ad esempio contoso.corp.net, è necessario creare record A DNS che puntano all'indirizzo del bilanciamento del carico interno per:

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere Introduzione agli ambienti del [servizio appTo][WhatisASE] get started with App Service Environments, see Introduction to App Service Environments

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
