---
title: Panoramica della configurazione del gateway applicazione Azure
description: Questo articolo descrive come configurare i componenti di applicazione Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: absha
ms.openlocfilehash: 20d1dfea251fdfd0bd6e8432d1ea0c7af7284cb5
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428182"
---
# <a name="application-gateway-configuration-overview"></a>Panoramica della configurazione del gateway applicazione

Applicazione Azure gateway è costituito da diversi componenti che è possibile configurare in vari modi per diversi scenari. Questo articolo illustra come configurare ogni componente.

![Diagramma di flusso dei componenti del gateway applicazione](./media/configuration-overview/configuration-overview1.png)

Questa immagine illustra un'applicazione con tre listener. I primi due sono listener multisito `http://acme.com/*` rispettivamente per e `http://fabrikam.com/*` . Entrambi sono in ascolto sulla porta 80. Il terzo è un listener di base con terminazione Transport Layer Security (TLS) end-to-end, nota in precedenza come terminazione Secure Sockets Layer (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rete virtuale di Azure e subnet dedicata

Un gateway applicazione è una distribuzione dedicata nella rete virtuale. All'interno della rete virtuale è necessaria una subnet dedicata per il gateway applicazione. È possibile avere più istanze di una determinata distribuzione del gateway applicazione in una subnet. È anche possibile distribuire altri gateway applicazione nella subnet. Tuttavia, non è possibile distribuire altre risorse nella subnet del gateway applicazione.

> [!NOTE]
> Non è possibile combinare Standard_v2 e il gateway applicazione Azure standard nella stessa subnet.

#### <a name="size-of-the-subnet"></a>Dimensioni della subnet

Il gateway applicazione usa un indirizzo IP privato per istanza, oltre a un altro indirizzo IP privato se è configurato un IP front-end privato.

Azure riserva inoltre cinque indirizzi IP in ogni subnet per uso interno: i primi quattro e gli ultimi indirizzi IP. Si considerino, ad esempio, 15 istanze del gateway applicazione senza IP front-end privato. Sono necessari almeno 20 indirizzi IP per questa subnet: cinque per uso interno e 15 per le istanze del gateway applicazione. Quindi, è necessario disporre di una o più dimensioni della subnet/27.

Si consideri una subnet con 27 istanze del gateway applicazione e un indirizzo IP per un indirizzo IP front-end privato. In questo caso sono necessari 33 indirizzi IP: 27 per le istanze del gateway applicazione, uno per il front-end privato e cinque per uso interno. Quindi, è necessario disporre di una o più dimensioni della subnet/26.

Si consiglia di usare una dimensione della subnet pari ad almeno/28. Questa dimensione fornisce 11 indirizzi IP utilizzabili. Se il carico dell'applicazione richiede più di 10 istanze del gateway applicazione, prendere in considerazione una dimensione della subnet/27 o/26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Gruppi di sicurezza di rete nella subnet del gateway applicazione

I gruppi di sicurezza di rete (gruppi) sono supportati nel gateway applicazione. Esistono tuttavia alcune restrizioni:

- È necessario consentire il traffico Internet in ingresso sulle porte TCP 65503-65534 per lo SKU del gateway applicazione v1 e sulle porte TCP 65200-65535 per lo SKU v2 con la subnet di destinazione impostata su **Qualsiasi** e l'origine impostata sul tag di servizio **GatewayManager**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Le entità esterne, inclusi i clienti di tali gateway, non possono comunicare su questi endpoint.

- La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite in NSG consentono la connettività Internet. È consigliabile:

  - Non rimuovere le regole in uscita predefinite.
  - Non creare altre regole in uscita che negano la connettività in uscita.

- È necessario consentire il traffico dal tag **AzureLoadBalancer** con la **subnet di destinazione** .

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Consentire l'accesso del gateway applicazione ad alcuni IP di origine

Per questo scenario, usare gruppi nella subnet del gateway applicazione. Inserire le restrizioni seguenti sulla subnet in questo ordine di priorità:

1. Consentire il traffico in ingresso da un IP di origine o un intervallo IP con la destinazione come l'intero intervallo di indirizzi della subnet del gateway applicazione e la porta di destinazione come porta di accesso in ingresso, ad esempio la porta 80 per l'accesso HTTP.
2. Consente le richieste in ingresso dall'origine come tag del servizio **GatewayManager** e la destinazione come **qualsiasi** porta di destinazione e come 65503-65534 per lo SKU del gateway applicazione v1 e le porte 65200-65535 per lo SKU V2 per la [comunicazione dello stato di integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dai certificati di Azure. Senza i certificati appropriati, le entità esterne non possono avviare le modifiche in tali endpoint.
3. Consentire i probe di Azure Load Balancer in ingresso (tag*AzureLoadBalancer* ) e il traffico di rete virtuale in ingresso (tag*virtualnetwork* ) nel [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloccare tutto il traffico in ingresso usando una regola Deny-all.
5. Consentire il traffico in uscita a Internet per tutte le destinazioni.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Route definite dall'utente supportate nella subnet del gateway applicazione

> [!IMPORTANT]
> L'uso di UdR nella subnet del gateway applicazione può causare lo stato di integrità nella [visualizzazione integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) in modo che venga visualizzato come **sconosciuto**. Potrebbe anche causare un errore di generazione dei log e delle metriche del gateway applicazione. Si consiglia di non usare UdR nella subnet del gateway applicazione per poter visualizzare l'integrità, i log e le metriche del back-end.

- **v1**

   Per lo SKU V1, le route definite dall'utente (UDR) sono supportate nella subnet del gateway applicazione, purché non modifichino la comunicazione di richiesta/risposta end-to-end. Ad esempio, è possibile configurare un UDR nella subnet del gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti. È tuttavia necessario assicurarsi che il pacchetto possa raggiungere la destinazione prevista dopo l'ispezione. In caso contrario, potrebbe verificarsi un comportamento non corretto del probe di integrità o del routing del traffico. Sono incluse le route apprese o le route 0.0.0.0/0 predefinite propagate dai gateway VPN o ExpressRoute di Azure nella rete virtuale. Qualsiasi scenario in cui 0.0.0.0/0 deve essere reindirizzato in locale (tunneling forzato) non è supportato per V1.

- **v2**

   Per lo SKU V2 sono disponibili scenari supportati e non supportati:

   **V2 scenari supportati**
   > [!WARNING]
   > Una configurazione non corretta della tabella di route può causare il routing asimmetrico nel gateway applicazione V2. Assicurarsi che tutto il traffico del piano di gestione/controllo venga inviato direttamente a Internet e non tramite un appliance virtuale. Potrebbero essere interessate anche la registrazione e le metriche.


  **Scenario 1**: UdR per disabilitare la propagazione della route BGP (Border Gateway Protocol) alla subnet del gateway applicazione

   In alcuni casi la route del gateway predefinita (0.0.0.0/0) viene annunciata tramite il ExpressRoute o i gateway VPN associati alla rete virtuale del gateway applicazione. Questo problema interrompe il traffico del piano di gestione, che richiede un percorso diretto a Internet. In questi scenari, è possibile usare un UDR per disabilitare la propagazione di route BGP. 

   Per disabilitare la propagazione della route BGP, attenersi alla procedura seguente:

   1. Creare una risorsa della tabella di route in Azure.
   2. Disabilitare il parametro di **propagazione della route del gateway di rete virtuale** . 
   3. Associare la tabella di route alla subnet appropriata. 

   L'abilitazione di UDR per questo scenario non dovrebbe interrompere le configurazioni esistenti.

  **Scenario 2**: UdR per indirizzare 0.0.0.0/0 a Internet

   È possibile creare un UDR per inviare il traffico 0.0.0.0/0 direttamente a Internet. 

  **Scenario 3**: UdR per il servizio Kubernetes di Azure con kubenet

  Se si usa kubenet con Azure Kubernetes Service (AKS) e il controller di ingresso del gateway applicazione (AGIC), è necessaria una tabella di route per consentire il routing del traffico ai pod dal gateway applicazione al nodo corretto. Questa operazione non è necessaria se si usa Azure CNI. 

  Per usare la tabella di route per consentire il funzionamento di kubenet, attenersi alla procedura seguente:

  1. Passare al gruppo di risorse creato da AKS (il nome del gruppo di risorse deve iniziare con "MC_")
  2. Trovare la tabella di route creata da AKS in tale gruppo di risorse. La tabella di route deve essere popolata con le seguenti informazioni:
     - Il prefisso dell'indirizzo deve essere l'intervallo IP dei pod che si vuole raggiungere in AKS. 
     - Il tipo di hop successivo deve essere appliance virtuale. 
     - L'indirizzo hop successivo deve essere l'indirizzo IP del nodo che ospita i pod.
  3. Associare la tabella di route alla subnet del gateway applicazione. 
    
  **V2 scenari non supportati**

  **Scenario 1**: UdR per appliance virtuali

  Qualsiasi scenario in cui 0.0.0.0/0 deve essere reindirizzato tramite qualsiasi appliance virtuale, rete virtuale hub/spoke o locale (tunneling forzato) non è supportato per V2.

## <a name="front-end-ip"></a>IP front-end

È possibile configurare il gateway applicazione in modo che disponga di un indirizzo IP pubblico, un indirizzo IP privato o entrambi. Un indirizzo IP pubblico è obbligatorio quando si ospita un back-end a cui i client devono accedere tramite Internet tramite un indirizzo IP virtuale (VIP) con connessione Internet. 

Un indirizzo IP pubblico non è obbligatorio per un endpoint interno non esposto a Internet. Noto come endpoint di *bilanciamento del carico interno* (ILB) o IP front-end privato. Un ILB del gateway applicazione è utile per le applicazioni line-of-business interne che non sono esposte a Internet. È utile anche per i servizi e i livelli in un'applicazione multilivello all'interno di un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico Round Robin, la appiccicosità della sessione o la terminazione TLS.

È supportato un solo indirizzo IP pubblico o un indirizzo IP privato. Quando si crea il gateway applicazione, è possibile scegliere l'indirizzo IP front-end.

- Per un indirizzo IP pubblico è possibile creare un nuovo indirizzo IP pubblico o usare un indirizzo IP pubblico esistente nello stesso percorso del gateway applicazione. Per ulteriori informazioni, vedere [static vs. Dynamic Public IP address](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Per un indirizzo IP privato, è possibile specificare un indirizzo IP privato dalla subnet in cui viene creato il gateway applicazione. Se non si specifica un indirizzo IP arbitrario, viene selezionato automaticamente dalla subnet. Il tipo di indirizzo IP selezionato (statico o dinamico) non può essere modificato in un secondo momento. Per altre informazioni, vedere [creare un gateway applicazione con un servizio di bilanciamento del carico interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Un indirizzo IP front-end è associato a un *listener*, che controlla le richieste in ingresso sull'IP front-end.

## <a name="listeners"></a>Listener

Un listener è un'entità logica che controlla le richieste di connessione in ingresso tramite la porta, il protocollo, l'host e l'indirizzo IP. Quando si configura il listener, è necessario immettere i valori per questi che corrispondono ai valori corrispondenti nella richiesta in ingresso sul gateway.

Quando si crea un gateway applicazione usando il portale di Azure, si crea anche un listener predefinito scegliendo il protocollo e la porta per il listener. È possibile scegliere se abilitare il supporto di HTTP2 nel listener. Dopo aver creato il gateway applicazione, è possibile modificare le impostazioni del listener predefinito (*appGatewayHttpListener*) o creare nuovi listener.

### <a name="listener-type"></a>Tipo di listener

Quando si crea un nuovo listener, è possibile scegliere tra [le funzionalità di *base* e *multisito*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se si desidera che tutte le richieste (per qualsiasi dominio) vengano accettate e inviate ai pool back-end, scegliere Basic. Informazioni [su come creare un gateway applicazione con un listener di base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se si desidera inviare richieste a diversi pool back-end in base all'intestazione *host* o ai nomi host, scegliere listener multisito, in cui è necessario specificare anche un nome host corrispondente alla richiesta in ingresso. Questo perché il gateway applicazione si basa sulle intestazioni host HTTP 1,1 per ospitare più siti Web nello stesso indirizzo IP pubblico e porta. Per altre informazioni, vedere [hosting di più siti con il gateway applicazione](multiple-site-overview.md).

#### <a name="order-of-processing-listeners"></a>Ordine dei listener di elaborazione

Per lo SKU V1, le richieste vengono confrontate in base all'ordine delle regole e al tipo di listener. Se una regola con listener di base viene innanzitutto eseguita nell'ordine, viene elaborata per prima e accetterà qualsiasi richiesta per la combinazione di porta e indirizzo IP. Per evitare questo problema, configurare prima le regole con listener multisito ed effettuare il push della regola con il listener di base all'ultimo nell'elenco.

Per lo SKU V2, i listener multisito vengono elaborati prima dei listener di base.

### <a name="front-end-ip"></a>IP front-end

Scegliere l'indirizzo IP front-end che si intende associare a questo listener. Il listener resterà in ascolto delle richieste in ingresso sull'IP.

### <a name="front-end-port"></a>Porta front-end

Scegliere la porta front-end. Selezionare una porta esistente o crearne una nuova. Scegliere qualsiasi valore dall' [intervallo di porte consentito](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). È possibile utilizzare non solo le porte note, ad esempio 80 e 443, ma qualsiasi porta personalizzata consentita. Una porta può essere usata per i listener pubblici o per i listener privati.

### <a name="protocol"></a>Protocollo

Scegliere HTTP o HTTPS:

- Se si sceglie HTTP, il traffico tra il client e il gateway applicazione non è crittografato.

- Scegliere HTTPS se si vuole la [terminazione TLS](features.md#secure-sockets-layer-ssltls-termination) o la [crittografia TLS end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Il traffico tra il client e il gateway applicazione è crittografato. E la connessione TLS termina nel gateway applicazione. Se si desidera la crittografia TLS end-to-end, è necessario scegliere HTTPS e configurare l'impostazione **http back-end** . Ciò garantisce che il traffico venga nuovamente crittografato durante il trasferimento dal gateway applicazione al back-end.


Per configurare la terminazione TLS e la crittografia TLS end-to-end, è necessario aggiungere un certificato al listener per consentire al gateway applicazione di derivare una chiave simmetrica. Questa operazione è determinata dalla specifica del protocollo TLS. La chiave simmetrica viene usata per crittografare e decrittografare il traffico inviato al gateway. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato consente di esportare la chiave privata che il gateway usa per crittografare e decrittografare il traffico.

#### <a name="supported-certificates"></a>Certificati supportati

Vedere [certificati supportati per la terminazione TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Supporto del protocollo aggiuntivo

#### <a name="http2-support"></a>Supporto di HTTP2

Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo ai listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il seguente frammento di codice Azure PowerShell Mostra come abilitare questa procedura:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Supporto per WebSocket

Il supporto di WebSocket è abilitato per impostazione predefinita. Non è disponibile alcuna impostazione configurabile dall'utente per abilitarla o disabilitarla. È possibile usare WebSocket con listener HTTP e HTTPS.

### <a name="custom-error-pages"></a>Pagine di errore personalizzate

È possibile definire un errore personalizzato a livello globale o a livello di listener. Tuttavia, la creazione di pagine di errore personalizzate a livello globale dalla portale di Azure non è attualmente supportata. È possibile configurare una pagina di errore personalizzata per un errore 403 web application firewall o una pagina di manutenzione 502 a livello di listener. È inoltre necessario specificare un URL BLOB accessibile pubblicamente per il codice di stato di errore specificato. Per altre informazioni, vedere [Create Application Gateway custom error pages](https://docs.microsoft.com/azure/application-gateway/custom-error) (Creare pagine di errore personalizzate del gateway applicazione).

![Codici di errore del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Per configurare una pagina di errore globale personalizzata, vedere [configurazione Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Criteri TLS

È possibile centralizzare la gestione dei certificati TLS/SSL e ridurre l'overhead di decrittografia della crittografia per un server farm back-end. La gestione centralizzata di TLS consente inoltre di specificare un criterio TLS centrale adatto ai propri requisiti di sicurezza. È possibile scegliere i criteri *predefined*TLS *predefiniti, predefiniti*o *personalizzati* .

Il criterio TLS viene configurato per controllare le versioni del protocollo TLS. È possibile configurare un gateway applicazione per usare una versione minima del protocollo per gli handshake TLS da TLS 1.0, TLS 1.1 e TLS 1.2. Per impostazione predefinita, SSL 2,0 e 3,0 sono disabilitati e non sono configurabili. Per altre informazioni, vedere [Panoramica dei criteri TLS del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Dopo aver creato un listener, associarlo a una regola di routing delle richieste. Tale regola determina il modo in cui le richieste ricevute nel listener vengono instradate al back-end.

## <a name="request-routing-rules"></a>Regole di routing richieste

Quando si crea un gateway applicazione usando il portale di Azure, si crea una regola predefinita (*Rule1*). Questa regola associa il listener predefinito (*appGatewayHttpListener*) con il pool back-end predefinito (*appGatewayBackendPool*) e le impostazioni http back-end predefinite (*appGatewayBackendHttpSettings*). Dopo aver creato il gateway, è possibile modificare le impostazioni della regola predefinita o creare nuove regole.

### <a name="rule-type"></a>Tipo regola

Quando si crea una regola, è possibile scegliere tra [ *base* e *basata sul percorso*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Scegliere di base se si desidera inviare tutte le richieste sul listener associato (ad esempio, *Blog <i></i> . contoso.com/ \* )* a un singolo pool back-end.
- Scegliere basato sul percorso se si desidera instradare le richieste da percorsi URL specifici a pool back-end specifici. Il modello di percorso viene applicato solo al percorso dell'URL, non ai parametri della query.

#### <a name="order-of-processing-rules"></a>Ordine delle regole di elaborazione

Per lo SKU V1 e V2, i criteri di ricerca delle richieste in ingresso vengono elaborati nell'ordine in cui i percorsi sono elencati nella mappa del percorso URL della regola basata sul percorso. Se una richiesta corrisponde al modello in due o più percorsi nella mappa di percorso, il percorso elencato per primo viene associato. E la richiesta viene trasmessa al back-end associato a tale percorso.

### <a name="associated-listener"></a>Listener associato

Associare un listener alla regola in modo che la *regola di routing richiesta* associata al listener venga valutata per determinare il pool back-end a cui indirizzare la richiesta.

### <a name="associated-back-end-pool"></a>Pool back-end associato

Associare alla regola il pool back-end che contiene le destinazioni di back-end che gestiscono le richieste ricevute dal listener.

 - Per una regola di base è consentito un solo pool back-end. Tutte le richieste sul listener associato vengono inviate al pool back-end.

 - Per una regola basata sul percorso, aggiungere più pool back-end che corrispondono a ogni percorso URL. Le richieste che corrispondono al percorso URL immesso vengono inviate al pool back-end corrispondente. Aggiungere inoltre un pool back-end predefinito. Le richieste che non corrispondono ad alcun percorso URL nella regola vengono inviate a tale pool.

### <a name="associated-back-end-http-setting"></a>Impostazione HTTP back-end associata

Aggiungere un'impostazione HTTP back-end per ogni regola. Le richieste vengono indirizzate dal gateway applicazione alle destinazioni di back-end usando il numero di porta, il protocollo e altre informazioni specificate in questa impostazione.

Per una regola di base è consentita una sola impostazione HTTP back-end. Tutte le richieste sul listener associato vengono inviate alle destinazioni back-end corrispondenti utilizzando questa impostazione HTTP.

Per una regola basata sul percorso, aggiungere più impostazioni HTTP back-end corrispondenti a ogni percorso URL. Le richieste che corrispondono al percorso URL in questa impostazione vengono inviate alle destinazioni back-end corrispondenti usando le impostazioni HTTP che corrispondono a ogni percorso URL. Aggiungere inoltre un'impostazione HTTP predefinita. Le richieste che non corrispondono ad alcun percorso URL in questa regola vengono inviate al pool back-end predefinito usando l'impostazione HTTP predefinita.

### <a name="redirection-setting"></a>Impostazione del reindirizzamento

Se il reindirizzamento è configurato per una regola di base, tutte le richieste sul listener associato vengono reindirizzate alla destinazione. Si tratta di un reindirizzamento *globale* . Se il reindirizzamento è configurato per una regola basata sul percorso, vengono reindirizzate solo le richieste in un'area specifica del sito. Un esempio è un'area del carrello acquisti indicata da */cart/ \* *. Si tratta del reindirizzamento *basato sul percorso* .

Per altre informazioni sui reindirizzamenti, vedere [Panoramica del reindirizzamento del gateway applicazione](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo di Reindirizzamento

Scegliere il tipo di reindirizzamento necessario: *permanente (301*), *temporaneo (307)*, *trovato (302)* o *vedere altro (303)*.

#### <a name="redirection-target"></a>Destinazione di Reindirizzamento

Scegliere un altro listener o un sito esterno come destinazione del reindirizzamento.

##### <a name="listener"></a>Listener

Scegliere listener come destinazione di reindirizzamento per reindirizzare il traffico da un listener a un altro sul gateway. Questa impostazione è obbligatoria quando si desidera abilitare il Reindirizzamento da HTTP a HTTPS. Reindirizza il traffico dal listener di origine che controlla le richieste HTTP in ingresso al listener di destinazione che controlla le richieste HTTPS in ingresso. È anche possibile scegliere di includere la stringa di query e il percorso dalla richiesta originale nella richiesta che viene trasmessa alla destinazione di reindirizzamento.

![Finestra di dialogo componenti gateway applicazione](./media/configuration-overview/configure-redirection.png)

Per ulteriori informazioni sul Reindirizzamento da HTTP a HTTPS, vedere:
- [Reindirizzamento da HTTP a HTTPS tramite il portale di Azure](redirect-http-to-https-portal.md)
- [Reindirizzamento da HTTP a HTTPS tramite PowerShell](redirect-http-to-https-powershell.md)
- [Reindirizzamento da HTTP a HTTPS usando l'interfaccia della riga di comando di Azure](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Sito esterno

Scegliere sito esterno quando si desidera reindirizzare il traffico sul listener associato a questa regola a un sito esterno. È possibile scegliere di includere la stringa di query dalla richiesta originale nella richiesta che viene trasmessa alla destinazione di reindirizzamento. Non è possibile inviare il percorso al sito esterno che era presente nella richiesta originale.

Per ulteriori informazioni sul reindirizzamento, vedere:
- [Reindirizzare il traffico a un sito esterno tramite PowerShell](redirect-external-site-powershell.md)
- [Reindirizzare il traffico a un sito esterno tramite l'interfaccia della riga di comando](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>Riscrivere l'URL e le intestazioni HTTP

Con le regole di riscrittura è possibile aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP (S), nonché il percorso URL e i parametri della stringa di query, perché i pacchetti di richiesta e risposta passano tra il client e i pool back-end tramite il gateway applicazione.

I parametri Header e URL possono essere impostati su valori statici o ad altre intestazioni e variabili server. Questo consente di utilizzare casi di utilizzo importanti, ad esempio l'estrazione di indirizzi IP del client, la rimozione di informazioni riservate sul back-end, l'aggiunta di ulteriore sicurezza e così via.
Per altre informazioni, vedere:

 - [Riscrivere le intestazioni HTTP e la panoramica degli URL](rewrite-http-headers-url.md)
 - [Configurare la riscrittura dell'intestazione HTTP](rewrite-http-headers-portal.md)
 - [Configurare la riscrittura URL](rewrite-url-portal.md)

## <a name="http-settings"></a>Impostazioni HTTP

Il gateway applicazione instrada il traffico verso i server back-end usando la configurazione specificata qui. Dopo aver creato un'impostazione HTTP, è necessario associarla a una o più regole di routing delle richieste.

### <a name="cookie-based-affinity"></a>Affinità basata sui cookie

Applicazione Azure gateway usa i cookie gestiti dal gateway per la gestione delle sessioni utente. Quando un utente invia la prima richiesta al gateway applicazione, imposta un cookie di affinità nella risposta con un valore hash che contiene i dettagli della sessione, in modo che le richieste successive che trasportano il cookie di affinità vengano indirizzate allo stesso server back-end per mantenere la viscosità. 

Questa funzionalità è utile quando si desidera conservare una sessione utente sullo stesso server e quando lo stato della sessione viene salvato localmente nel server per una sessione utente. Se l'applicazione non è in grado di gestire l'affinità basata su cookie, non è possibile usare questa funzionalità. Per usarlo, assicurarsi che i client supportino i cookie.

L' [aggiornamento V80](https://chromiumdash.appspot.com/schedule) del [browser Chromium](https://www.chromium.org/Home) ha introdotto un mandato in cui i cookie HTTP senza attributo [navigava sullostesso sito](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) devono essere considerati navigava sullostesso sito = LAX. Se il cookie deve essere inviato in un contesto di terze parti, nel caso di richieste di condivisione risorse tra le origini (CORS), deve usare *navigava sullostesso sito = None; Proteggere* gli attributi e deve essere inviati solo tramite HTTPS. In caso contrario, in uno scenario solo HTTP, il browser non invia i cookie nel contesto di terze parti. L'obiettivo di questo aggiornamento di Chrome è quello di migliorare la sicurezza ed evitare attacchi di richiesta intersito falsa (CSRF). 

Per supportare questa modifica, a partire dal 17 2020 febbraio, il gateway applicazione (tutti i tipi di SKU) inserirà un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente. Al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi (*"navigava sullostesso sito = None; Secure "*) in modo che la sessione appiccicosa venga mantenuta anche per le richieste tra origini.

Si noti che il nome del cookie di affinità predefinito è *ApplicationGatewayAffinity* ed è possibile modificarlo. Se si sta usando un nome di cookie di affinità personalizzato, viene aggiunto un cookie aggiuntivo con CORS come suffisso. Ad esempio, *CustomCookieNameCORS*.

> [!NOTE]
> Se l'attributo *navigava sullostesso sito = None* è impostato, è obbligatorio che il cookie contenga anche il flag *Secure* e che sia necessario inviarlo tramite HTTPS.  Se è necessaria l'affinità di sessione su CORS, è necessario eseguire la migrazione del carico di lavoro a HTTPS. Per un gateway applicazione, vedere la [Panoramica sull'](ssl-overview.md)offload TLS e la documentazione TLS end-to-end, [configurare un gateway applicazione con terminazione tls usando il portale di Azure](create-ssl-portal.md), [configurare TLS end-to-end usando il gateway applicazione con il portale](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento delle connessioni consente di rimuovere normalmente i membri del pool back-end durante gli aggiornamenti pianificati dei servizi. È possibile applicare questa impostazione a tutti i membri di un pool back-end abilitando lo svuotamento della connessione sull'impostazione HTTP. Garantisce che tutte le istanze di annullamento della registrazione di un pool back-end continuino a mantenere le connessioni esistenti e a gestire richieste in corso per un timeout configurabile e non ricevano nuove richieste o connessioni. L'unica eccezione è costituita dalle richieste associate per la deregistrazione delle istanze a causa dell'affinità di sessione gestita dal gateway e continueranno a essere inviate alle istanze di annullamento della registrazione. Lo svuotamento della connessione si applica alle istanze back-end che vengono rimosse in modo esplicito dal pool back-end.

### <a name="protocol"></a>Protocollo

Il gateway applicazione supporta sia HTTP che HTTPS per il routing delle richieste ai server back-end. Se si sceglie HTTP, il traffico verso i server back-end non è crittografato. Se la comunicazione non crittografata non è accettabile, scegliere HTTPS.

Questa impostazione combinata con HTTPS nel listener supporta [TLS end-to-end](ssl-overview.md). Ciò consente di trasmettere in modo sicuro dati sensibili crittografati al back-end. Ogni server back-end nel pool back-end con TLS abilitato end-to-end deve essere configurato con un certificato per consentire la comunicazione protetta.

### <a name="port"></a>Porta

Questa impostazione specifica la porta in cui i server back-end ascoltano il traffico dal gateway applicazione. È possibile configurare porte comprese tra 1 e 65535.

### <a name="request-timeout"></a>Timeout richiesta

Questa impostazione indica il numero di secondi di attesa del gateway applicazione per ricevere una risposta dal server back-end.

### <a name="override-back-end-path"></a>Esegui override del percorso back-end

Questa impostazione consente di configurare un percorso di invio personalizzato facoltativo da usare quando la richiesta viene trasmessa al back-end. Qualsiasi parte del percorso in ingresso che corrisponde al percorso personalizzato nel campo **percorso back-end di sostituzione** viene copiata nel percorso inviato. La tabella seguente illustra il funzionamento di questa funzionalità:

- Quando l'impostazione HTTP è associata a una regola di routing richiesta di base:

  | Richiesta originale  | Esegui override del percorso back-end | Richiesta da inviare al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override.            | /override/home/              |
  | /home/secondhome/ | /override.            | /override/home/secondhome/   |

- Quando l'impostazione HTTP è associata a una regola di routing delle richieste basata sul percorso:

  | Richiesta originale           | Regola percorso       | Esegui override del percorso back-end | Richiesta da inviare al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | regola      | /override.            | /override/home/              |
  | /pathrule/home/secondhome/ | regola      | /override.            | /override/home/secondhome/   |
  | /Home/                     | regola      | /override.            | /override/home/              |
  | /home/secondhome/          | regola      | /override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override.            | /override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override.            | /override/secondhome/        |
  | regola                 | regola      | /override.            | /override.                   |

### <a name="use-for-app-service"></a>Usare per il servizio app

Si tratta di un collegamento solo interfaccia utente che seleziona le due impostazioni necessarie per il back-end del servizio app Azure. Consente **di selezionare il nome host dall'indirizzo back-end**e crea un nuovo probe personalizzato se non ne è già presente uno. Per ulteriori informazioni, vedere la sezione [pick host name from back-end Address](#pick) setting di questo articolo. Viene creato un nuovo probe e l'intestazione del probe viene prelevata dall'indirizzo del membro back-end.

### <a name="use-custom-probe"></a>USA Probe personalizzato

Questa impostazione associa un [Probe personalizzato](application-gateway-probe-overview.md#custom-health-probe) a un'impostazione http. È possibile associare un solo Probe personalizzato a un'impostazione HTTP. Se non si associa in modo esplicito un probe personalizzato, viene usato il [Probe predefinito](application-gateway-probe-overview.md#default-health-probe-settings) per monitorare l'integrità del back-end. Si consiglia di creare un probe personalizzato per un maggiore controllo sul monitoraggio dello stato dei back-end.

> [!NOTE]
> Il probe personalizzato non monitora l'integrità del pool back-end, a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener.

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>Selezionare il nome host dall'indirizzo back-end

Questa funzionalità imposta dinamicamente l'intestazione *host* nella richiesta sul nome host del pool back-end. Usa un indirizzo IP o un FQDN.

Questa funzionalità aiuta quando il nome di dominio del back-end è diverso dal nome DNS del gateway applicazione e il back-end si basa su un'intestazione host specifica per la risoluzione nell'endpoint corretto.

Un caso di esempio è un servizio multi-tenant come back-end. Un servizio app è un servizio multi-tenant che usa uno spazio condiviso con un singolo indirizzo IP. È quindi possibile accedere a un servizio app solo tramite i nomi host configurati nelle impostazioni del dominio personalizzato.

Per impostazione predefinita, il nome di dominio personalizzato è *example.azurewebsites.NET*. Per accedere al servizio app usando un gateway applicazione tramite un nome host non registrato in modo esplicito nel servizio app o tramite il nome di dominio completo del gateway applicazione, è necessario sostituire il nome host nella richiesta originale con il nome host del servizio app. A tale scopo, abilitare l'impostazione del **nome host pick dall'indirizzo back-end** .

Per un dominio personalizzato il cui nome DNS personalizzato esistente è mappato al servizio app, non è necessario abilitare questa impostazione.

> [!NOTE]
> Questa impostazione non è necessaria per ambiente del servizio app, ovvero una distribuzione dedicata.

### <a name="host-name-override"></a>Override del nome host

Questa funzionalità sostituisce l'intestazione *host* nella richiesta in ingresso nel gateway applicazione con il nome host specificato.

Se, ad esempio, si specifica *www.contoso.com* nell'impostazione **nome host** , la richiesta originale * `https://appgw.eastus.cloudapp.azure.com/path1` viene modificata in * `https://www.contoso.com/path1` quando la richiesta viene trasmessa al server back-end.

## <a name="back-end-pool"></a>Pool back-end

È possibile puntare un pool back-end a quattro tipi di membri back-end: una macchina virtuale specifica, un set di scalabilità di macchine virtuali, un indirizzo IP/FQDN o un servizio app. 

Dopo aver creato un pool back-end, è necessario associarlo a una o più regole di routing delle richieste. È inoltre necessario configurare i probe di integrità per ogni pool back-end nel gateway applicazione. Quando viene soddisfatta una condizione della regola di routing richieste, il gateway applicazione lo trasmette ai server integri (come determinato dai Probe di integrità) nel pool back-end corrispondente.

## <a name="health-probes"></a>Probe di integrità

Per impostazione predefinita, un gateway applicazione monitora l'integrità di tutte le risorse nel back-end. È tuttavia consigliabile creare un probe personalizzato per ogni impostazione HTTP back-end per ottenere un maggiore controllo sul monitoraggio dell'integrità. Per informazioni su come configurare un probe personalizzato, vedere [impostazioni del probe di integrità personalizzato](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Dopo aver creato un probe di integrità personalizzato, è necessario associarlo a un'impostazione HTTP back-end. Un probe personalizzato non monitorerà l'integrità del pool back-end, a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener usando una regola.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i componenti del gateway applicazione, è possibile:

- [Creare un gateway applicazione nella portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione tramite PowerShell](quick-create-powershell.md)
- [Creare un gateway applicazione con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
