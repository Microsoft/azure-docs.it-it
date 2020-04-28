---
title: Regole della rete virtuale-database di Azure per PostgreSQL-server singolo
description: Informazioni su come usare gli endpoint di servizio della rete virtuale (VNET) per connettersi al database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 512ad8f93da53afb618491cd1769645d8edb0b14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75965835"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Usare gli endpoint del servizio rete virtuale e le regole per database di Azure per PostgreSQL-server singolo

Le *regole di rete virtuale* rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Database di Azure per PostgreSQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali. Questo articolo spiega i motivi per cui la funzione delle regole di rete virtuale è talvolta la scelta ideale per consentire le comunicazioni con il server di Database di Azure per PostgreSQL.

Per creare una regola di rete virtuale, devono innanzitutto essere disponibili una [rete virtuale][vm-virtual-network-overview] (VNet) e un [endpoint servizio di rete virtuale][vm-virtual-network-service-endpoints-overview-649d] a cui la regola possa fare riferimento. La figura seguente illustra il funzionamento di un endpoint servizio di rete virtuale con Database di Azure per PostgreSQL:

![Esempio del funzionamento di un endpoint di servizio di rete virtuale](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree del cloud pubblico di Azure in cui Database di Azure per PostgreSQL viene distribuito nei server per utilizzo generico e ottimizzati per la memoria.
> In caso di peering di reti virtuali, se il traffico scorre attraverso un gateway di rete virtuale comune con endpoint di servizio e deve raggiungere il peer, creare una regola di elenco di controllo di accesso o di rete virtuale per consentire a Macchine virtuali di Azure all'interno della rete virtuale del gateway di accedere al server di Database di Azure per PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrizione

**Rete virtuale:** è possibile associare reti virtuali alla sottoscrizione di Azure.

**Subnet:** una rete virtuale contiene **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedervi, a meno che non si configuri la sicurezza in modo da consentirne l'accesso.

**Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale][vm-virtual-network-service-endpoints-overview-649d] è una subnet in cui i valori di proprietà includono uno o più nomi formali di tipi di servizi Azure. Questo articolo è incentrato sul nome del tipo **Microsoft.Sql**, che fa riferimento al servizio Azure denominato Database SQL. Questo tag di servizio si applica ai servizi di Database di Azure per PostgreSQL e MySQL. È importante tenere presente che, quando si applica il tag di servizio **Microsoft.Sql** a un endpoint di servizio di rete virtuale, viene configurato il traffico dell'endpoint per tutti i server di Database SQL di Azure, Database di Azure per PostgreSQL e Database di Azure per MySQL nella subnet. 

**Regola di rete virtuale:** una regola di rete virtuale per il server di Database di Azure per PostgreSQL è una subnet presente nell'elenco di controllo di accesso (ACL) del server di Database di Azure per PostgreSQL. Per essere inclusa nell'elenco ACL del server di Database di Azure per PostgreSQL, la subnet deve contenere il nome del tipo **Microsoft.Sql**.

Una regola di rete virtuale indica al server di Database di Azure per PostgreSQL di accettare le comunicazioni da ogni nodo che si trova nella subnet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Vantaggi di una regola di rete virtuale

Finché non si interviene, le VM nelle subnet non possono comunicare con il server di Database di Azure per PostgreSQL. Un'azione che stabilisce la comunicazione è la creazione di una regola di rete virtuale. La base logica per la scelta dell'approccio delle regole di rete virtuale richiede una discussione di confronto riguardo le opzioni di sicurezza concorrenti offerte dal firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Possibilità di accedere ai servizi di Azure

Il riquadro Sicurezza connessione contiene un pulsante **ON/OFF** con l'etichetta **Consenti l'accesso a Servizi di Azure**. L'impostazione **ON** consente le comunicazioni da tutti gli indirizzi IP di Azure e tutte le subnet di Azure. Questi indirizzi IP o subnet di Azure potrebbero non essere di proprietà dell'utente. Questa impostazione **ON** è probabilmente più aperta rispetto al livello desiderato per l'istanza di Database di Azure per PostgreSQL. La funzione delle regole di rete virtuale offre un controllo molto più granulare.

### <a name="b-ip-rules"></a>B. Regole IP

Il firewall di Database di Azure per PostgreSQL consente di specificare gli intervalli di indirizzi IP da cui vengono accettate le comunicazioni nell'istanza di Database di Azure per PostgreSQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, ma molti nodi all'interno della rete privata di Azure sono configurati con indirizzi IP *dinamici*. Gli indirizzi IP dinamici potrebbero cambiare, ad esempio al riavvio di una macchina virtuale. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

È possibile recuperare l'opzione IP ottenendo un indirizzo IP *statico* per la macchina virtuale. Per i dettagli, vedere [Configurare indirizzi IP privati per una VM mediante il portale di Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è dispendioso a livello di scalabilità. Le regole di rete virtuale sono più semplici da creare e gestire.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Non è ancora possibile disporre di Database di Azure per PostgreSQL su una subnet, se non si definisce un endpoint di servizio

Se il server **Microsoft.Sql** è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il server di Database di Azure per PostgreSQL. In questo caso, le VM possono comunicare con Database di Azure per PostgreSQL senza richiedere regole di rete virtuale o IP.

Tuttavia, a partire da agosto 2018, il servizio Database di Azure per PostgreSQL non è ancora tra i servizi che possono essere assegnati direttamente a una subnet.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Dettagli sulle regole di rete virtuale

Questa sezione illustra alcuni dettagli sulle regole di rete virtuale.

### <a name="only-one-geographic-region"></a>Una sola area geografica

Ogni endpoint servizio di rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet.

Ogni regola di rete virtuale è limitata all'area a cui si applica il relativo endpoint sottostante.

### <a name="server-level-not-database-level"></a>A livello di server, non a livello di database

Ogni regola di rete virtuale si applica all'intero server di Database di Azure per PostgreSQL, non solo a un determinato database nel server. In altre parole, la regola di rete virtuale si applica a livello di server, non a livello di database.

#### <a name="security-administration-roles"></a>Ruoli di amministrazione di sicurezza

I ruoli di sicurezza sono distinti nell'amministrazione degli endpoint servizio di rete virtuale. Ogni ruolo indicato di seguito deve svolgere determinate azioni:

- **Amministratore di rete:** &nbsp; attivare l'endpoint.
- **Amministratore di database:** &nbsp; aggiornare l'elenco di controllo di accesso (ACL) per aggiungere la subnet specificata al server di Database di Azure per PostgreSQL.

*Alternativa del controllo degli accessi in base al ruolo:*

I ruoli di amministratore di rete e amministratore di database hanno più funzionalità di quelle necessarie a gestire le regole di rete virtuale. È necessario solo un subset delle relative funzionalità.

È possibile scegliere di usare il [controllo degli accessi in base al ruolo (RBAC)][rbac-what-is-813s] in Azure per creare un singolo ruolo personalizzato che contiene solo il subset necessario di funzionalità. Il ruolo personalizzato può essere utilizzato invece di coinvolgere l'amministratore di rete o l'amministratore del database. La superficie di attacco dell'esposizione alla sicurezza è inferiore se si aggiunge un utente a un ruolo personalizzato, anziché aggiungere l'utente agli altri due ruoli di amministratore principali.

> [!NOTE]
> In alcuni casi, Database di Azure per PostgreSQL e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi è necessario garantire le configurazioni seguenti:
> - Entrambe le sottoscrizioni devono essere nello stesso tenant di Azure Active Directory.
> - L'utente ha le autorizzazioni necessarie per avviare le operazioni, ad esempio abilitare gli endpoint di servizio e aggiungere una subnet della rete virtuale al server specificato.
> - Assicurarsi che la sottoscrizione disponga del provider di risorse **Microsoft. SQL** registrato. Per altre informazioni, vedere [Resource-Manager-Registration][resource-manager-portal]

## <a name="limitations"></a>Limitazioni

Per Database di Azure per PostgreSQL, la funzionalità delle regole di rete virtuale presenta le limitazioni seguenti:

- Un'app Web può essere mappata a un indirizzo IP privato in una rete virtuale/subnet. Anche se gli endpoint di servizio sono attivati dalla rete virtuale/subnet specificata, le connessioni dall'app Web al server avranno come origine l'indirizzo IP pubblico di Azure, non la rete virtuale/subnet. Per abilitare la connettività da un'app Web a un server che dispone di regole del firewall della rete virtuale, è necessario selezionare Consenti ai servizi di Azure di accedere al server sul server.

- Nel firewall per Database di Azure per PostgreSQL ogni regola di rete virtuale fa riferimento a una subnet. Tutte queste subnet cui viene fatto riferimento devono essere ospitate nella stessa area geografica che ospita Database di Azure per PostgreSQL.

- Ogni server di Database di Azure per PostgreSQL può includere fino a 128 voci ACL per qualsiasi rete virtuale specificata.

- Le regole di rete virtuale si applicano solo alle reti virtuali di Azure Resource Manager e non alle reti con un [modello di distribuzione classica][arm-deployment-model-568f].

- L'attivazione degli endpoint servizio di rete virtuale su Database di Azure per PostgreSQL tramite il tag di servizio **Microsoft.Sql** abilita anche gli endpoint per tutti i servizi di Database di Azure: Database di Azure per MySQL, Database di Azure per PostgreSQL, Database SQL di Azure e Azure SQL Data Warehouse.

- Gli endpoint di servizio di rete virtuale sono supportati solo per i server per utilizzo generico e ottimizzati per la memoria.

- Nel firewall, gli intervalli di indirizzi IP si applicano ai seguenti elementi di rete, ma non le regole di rete virtuale:
    - [VPN (rete privata virtuale) da sito a sito (S2S)][vpn-gateway-indexmd-608y]
    - Locale tramite [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se la rete è connessa alla rete di Azure con [ExpressRoute][expressroute-indexmd-744v], ogni circuito è configurato con due indirizzi IP pubblici in Microsoft Edge. I due indirizzi IP vengono usati per connettersi ai servizi Microsoft, ad esempio ad Archiviazione di Azure, usando il peering pubblico di Azure.

Per consentire le comunicazioni tra il circuito e Database di Azure per PostgreSQL, è necessario creare regole di rete IP per gli indirizzi IP pubblici dei circuiti. Per trovare gli indirizzi IP pubblici del circuito ExpressRoute, aprire un ticket di supporto in ExpressRoute tramite il portale di Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Aggiunta di una regola del firewall della rete virtuale al server senza attivare gli endpoint di servizio di rete virtuale

La semplice impostazione di una regola del firewall non consente di proteggere il server nella rete virtuale. Per garantire la sicurezza, è anche necessario **attivare** gli endpoint servizio di rete virtuale. Quando si **attivano** gli endpoint servizio, la subnet della rete virtuale entra in un periodo di inattività fino al termine della transizione dallo stato **inattivo** a quello **attivo**. Questo vale soprattutto per le reti virtuali di grandi dimensioni. È possibile usare il flag **IgnoreMissingServiceEndpoint** per ridurre o eliminare il tempo di inattività durante la transizione.

È possibile impostare il flag **IgnoreMissingServiceEndpoint** usando l'interfaccia della riga di comando di Azure o il portale di Azure.

## <a name="related-articles"></a>Articoli correlati
- [Reti virtuali di Azure][vm-virtual-network-overview]
- [Endpoint servizio di rete virtuale di Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Passaggi successivi
Per articoli relativi alla creazione di regole di rete virtuale, vedere:
- [Creare e gestire le regole di rete virtuale per Database di Azure per PostgreSQL tramite il portale di Azure](howto-manage-vnet-using-portal.md)
- [Creare e gestire le regole di rete virtuale per Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
