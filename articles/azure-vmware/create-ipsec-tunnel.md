---
title: Creare un tunnel IPSec in una soluzione VMware di Azure
description: Informazioni su come creare un hub WAN virtuale per stabilire un tunnel IPSec nelle soluzioni VMware di Azure.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 21df674862b65ef6573a8a3fcfd7538b1053f04e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491845"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Creare un tunnel IPSec in una soluzione VMware di Azure

In questo articolo verranno illustrati i passaggi per stabilire un tunnel VPN da sito a sito VPN (IPsec IKEv1 e IKEv2) che termina nel Microsoft Azure Hub WAN virtuale. Verranno creati un hub WAN virtuale di Azure e un gateway VPN con un indirizzo IP pubblico collegato. Si creerà quindi un gateway Azure ExpressRoute e si creerà un endpoint della soluzione VMware di Azure. Verranno inoltre esaminati i dettagli relativi all'abilitazione di un'installazione locale di VPN basata su criteri. 

## <a name="topology"></a>Topologia

![Diagramma che illustra l'architettura del tunnel VPN da sito a sito.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

L'hub virtuale di Azure contiene il gateway ExpressRoute della soluzione VMware di Azure e il gateway VPN da sito a sito. Connette un dispositivo VPN locale con un endpoint della soluzione VMware di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per creare il tunnel VPN da sito a sito, è necessario creare un indirizzo IP pubblico che termina in un dispositivo VPN locale.

## <a name="create-a-virtual-wan-hub"></a>Creare un hub di rete WAN virtuale

1. Nella portale di Azure cercare le reti **WAN virtuali**. Selezionare **+Aggiungi**. Verrà visualizzata la pagina Crea WAN.  

2. Immettere i campi obbligatori nella pagina **Crea WAN** , quindi selezionare **Verifica + crea**.
   
   | Campo | Valore |
   | --- | --- |
   | **Sottoscrizione** | Il valore è già popolato con la sottoscrizione appartenente al gruppo di risorse. |
   | **Gruppo di risorse** | La rete WAN virtuale è una risorsa globale e non è confinata a un'area specifica.  |
   | **Località del gruppo di risorse** | Per creare l'hub WAN virtuale, è necessario impostare un percorso per il gruppo di risorse.  |
   | **Nome** |   |
   | **Tipo** | Selezionare **standard**, che non consentirà più del solo traffico del gateway VPN.  |

   :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Screenshot che mostra la pagina Crea rete WAN nell'portale di Azure.":::

3. Nella portale di Azure selezionare la rete WAN virtuale creata nel passaggio precedente, selezionare **Crea hub virtuale**, immettere i campi obbligatori e quindi fare clic **su Avanti: da sito a sito**. 

   | Campo | Valore |
   | --- | --- |
   | **Area** | La selezione di un'area è obbligatoria dal punto di vista della gestione.  |
   | **Nome** |    |
   | **Spazio degli indirizzi privato dell'hub** | Immettere la subnet usando un `/24` (minimo).  |

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Screenshot che mostra la pagina Crea hub virtuale.":::

4. Nella scheda da **sito a** sito definire il gateway da sito a sito impostando la velocità effettiva aggregata dall'elenco a discesa **unità di scala gateway** . 

   >[!TIP]
   >Un'unità di scala = 500 Mbps. Le unità di scala sono in coppia per la ridondanza, ciascuna delle quali supporta 500 Mbps.
  
5. Nella scheda **ExpressRoute** creare un gateway ExpressRoute. 

   >[!TIP]
   >Il valore di un'unità di scala è di 2 Gbps. 

    Sono necessari circa 30 minuti per creare ogni hub. 

## <a name="create-a-vpn-site"></a>Creare un sito VPN 

1. In **Risorse recenti** nella portale di Azure selezionare la rete WAN virtuale creata nella sezione precedente.

2. Nella **Panoramica** dell'hub virtuale selezionare VPN **connettività**  >  **(da sito a sito)**, quindi selezionare **Crea nuovo sito VPN**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Screenshot della pagina di panoramica per l'hub virtuale, con VPN (da sito a sito) e la creazione di un nuovo sito VPN selezionato.":::  
 
3. Nella scheda **nozioni di base** immettere i campi obbligatori e quindi selezionare **Avanti: collegamenti**. 

   | Campo | Valore |
   | --- | --- |
   | **Area** | Stessa area specificata nella sezione precedente.  |
   | **Nome** |  |
   | **Fornitore del dispositivo** |  |
   | **Border Gateway Protocol** | Impostare per **consentire** a di verificare che la soluzione VMware di Azure e i server locali annuncino le proprie route nel tunnel. Se disabilitata, le subnet che devono essere annunciate devono essere gestite manualmente. Se le subnet non vengono perse, HCX non riuscirà a formare la mesh del servizio. Per altre informazioni, vedere  [informazioni su BGP con il gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Spazio degli indirizzi privato**  | Immettere il blocco CIDR locale.  Viene usato per indirizzare tutto il traffico associato per l'ambiente locale attraverso il tunnel.  Il blocco CIDR è necessario solo se non si Abilita BGP. |
   | **Server di report di connessione** |   |

4. Nella scheda **collegamenti** compilare i campi obbligatori e selezionare **Verifica + crea**. La specifica dei nomi di collegamento e provider consente di distinguere tra un numero qualsiasi di gateway che può essere creato come parte dell'hub. BGP e il numero di sistema autonomo (ASN) devono essere univoci all'interno dell'organizzazione.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Opzionale Definizione di un sito VPN per i tunnel da sito a sito VPN basati su criteri

Questa sezione si applica solo alle VPN basate su criteri. Le configurazioni VPN basate su criteri o statiche basate su route sono basate sulle funzionalità del dispositivo VPN locale nella maggior parte dei casi. È necessario specificare le reti locali e di soluzioni VMware di Azure. Per la soluzione VMware di Azure con un hub WAN virtuale di Azure, non è possibile selezionare *alcuna* rete. È invece necessario specificare tutti gli intervalli di hub WAN virtuali della soluzione VMware locale e di Azure pertinenti. Questi intervalli di Hub vengono usati per specificare il dominio di crittografia dell'endpoint locale del tunnel VPN di base dei criteri. Il lato della soluzione VMware di Azure richiede l'abilitazione dell'indicatore del selettore di traffico basato su criteri. 

1. Nella portale di Azure accedere al sito dell'hub WAN virtuale. In **connettività** selezionare **VPN (da sito a sito)**.

2. Selezionare il nome del sito VPN, i puntini di sospensione (...) all'estrema destra, quindi **modificare la connessione VPN a questo hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Screenshot della pagina in Azure per il sito hub WAN virtuale che mostra i puntini di sospensione selezionati per accedere alla modifica della connessione VPN a questo hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Modificare la connessione tra il sito VPN e l'hub e quindi selezionare **Save (Salva**).
   - IPSec (Internet Protocol Security) selezionare **personalizzato**.
   - Usare il selettore di traffico basato su criteri, selezionare **Abilita**
   - Specificare i dettagli per **IKE fase 1** e **IKE fase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Screenshot della pagina Edit VPN connection."::: 
 
   I selettori di traffico o le subnet che fanno parte del dominio di crittografia basato su criteri devono essere:
    
   - Hub WAN virtuale/24
   - Cloud privato della soluzione VMware di Azure/22
   - Rete virtuale di Azure connessa (se presente)

## <a name="connect-your-vpn-site-to-the-hub"></a>Connettere il sito VPN all'hub

1. Selezionare il nome del sito VPN e quindi selezionare **Connetti siti VPN**. 
1. Nel campo **chiave precondivisa** immettere la chiave definita in precedenza per l'endpoint locale. 

   >[!TIP]
   >Se non è presente una chiave definita in precedenza, è possibile lasciare vuoto questo campo. Viene generata automaticamente una chiave. 
 
   >[!IMPORTANT]
   >Abilitare la **propagazione della route predefinita** solo se si sta distribuendo un firewall nell'hub ed è l'hop successivo per le connessioni tramite il tunnel.

1. Selezionare **Connetti**. Una schermata di stato della connessione Mostra lo stato della creazione del tunnel.

2. Passare alla panoramica della rete virtuale virtuale e aprire la pagina sito VPN per scaricare il file di configurazione VPN per l'endpoint locale.  

3. Applicare una patch alla soluzione VMware di Azure ExpressRoute nell'hub WAN virtuale. Questo passaggio richiede innanzitutto la creazione del cloud privato.

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Collegare la soluzione VMware di Azure e il gateway VPN nell'hub WAN virtuale. 
   1. Nella portale di Azure aprire la rete WAN virtuale creata in precedenza. 
   1. Selezionare l'hub WAN virtuale creato e quindi selezionare **ExpressRoute** nel riquadro sinistro. 
   1. Selezionare **+ riscattare la chiave di autorizzazione**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Screenshot della pagina ExpressRoute per il cloud privato con la chiave di autorizzazione riscatto selezionata.":::

   1. Incollare la chiave di autorizzazione nel campo chiave di autorizzazione.
   1. Incollare l'ID ExpressRoute nel campo **URI del circuito peer** . 
   1. Selezionare **associa automaticamente questo circuito ExpressRoute all'hub.** 
   1. Selezionare **Aggiungi** per stabilire il collegamento. 

5. Testare la connessione [creando un segmento NSX-T](./tutorial-nsx-t-network-segment.md) ed eseguendo il provisioning di una macchina virtuale nella rete. Effettuare il ping degli endpoint della soluzione VMware locale e di Azure.
