---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno: portale di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida di avvio rapido illustra come creare un servizio di bilanciamento del carico interno con il portale di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 14203021846e97a53f59c3bc24a1586774613dec
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704334"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali mediante il portale di Azure

Iniziare a usare Azure Load Balancer con il portale di Azure per creare un servizio di bilanciamento del carico interno e tre macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione.  Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Risorse di Load Balancer Standard create per l'avvio rapido." border="false":::

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. 

Nella rete virtuale viene inoltre configurato un indirizzo IP privato come front-end (denominato **LoadBalancerFrontend** per impostazione predefinita) del servizio di bilanciamento del carico. 

L'indirizzo IP front-end può essere **statico** o **dinamico**.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si crea una rete virtuale e una subnet.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **CreateIntLBQS-rg** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Europa occidentale** |

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **myBackendSubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

7. Selezionare **Salva**.

8. Selezionare la scheda **Sicurezza**.

9. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Crea**.

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare il gruppo **CreateIntLBQS-rg** creato nel passaggio precedente.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | selezionare **Standard** |
    | Rete virtuale | Selezionare la rete **myVNet** creata nel passaggio precedente. |
    | Subnet  | Selezionare la subnet **myBackendSubnet** creata nel passaggio precedente. |
    | Assegnazione indirizzi IP | Selezionare **Dinamico**. |
    | Zona di disponibilità | Selezionare **Con ridondanza della zona**. |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Creare un servizio di bilanciamento del carico interno Standard." border="true":::
 
## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurati:

* Le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end.
* Un probe di integrità.
* Una regola di bilanciamento del carico.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Il servizio di bilanciamento del carico monitora lo stato dell'app con un probe di integrità. 

Il probe di integrità aggiunge o rimuove le macchine virtuali nel servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere **80**.|
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|
    | | |

3. Accettare tutte le impostazioni predefinite e selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. È possibile definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico. La porta di origine e quella di destinazione vengono definite nella regola. 

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool** sulla **porta 80**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Selezionare **IPv4** |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontend** |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15** minuti. |
    | Reimpostazione TCP | Selezionare **Enabled**. |
    | SNAT (Network Address Translation) di origine in uscita | Selezionare **(Scelta consigliata) Usa regole in uscita per fornire l'accesso a Internet ai membri del pool back-end**. |

4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

>[!NOTE]
>Le macchine virtuali nel pool back-end non avranno connettività Internet in uscita con questa configurazione. </br> Per altre informazioni su come fornire la connettività in uscita, vedere: </br> **[Connessioni in uscita in Azure](load-balancer-outbound-connections.md)**</br> Opzioni per fornire la connettività: </br> **[Configurazione del servizio di bilanciamento del carico solo in uscita](egress-only.md)** </br> **[Che cos'è NAT di rete virtuale?](../virtual-network/nat-overview.md)**

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verrà illustrato come:

* Creare tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.
* Installare IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

In questa sezione si creeranno tre VM (**myVM1**, **myVM2** e **myVM3**).

Queste macchine virtuali vengono aggiunte al pool back-end del servizio di bilanciamento del carico creato in precedenza.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **CreateIntLBQS-rg** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **Zone di disponibilità** |
    | Zona di disponibilità | Selezionare **1** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> Nella pagina **Crea gruppo di sicurezza di rete** immettere **myNSG** in **Nome**. </br> Selezionare **OK**. |
    | **Bilanciamento del carico**  |
    | Associare questa macchina virtuale a una soluzione di bilanciamento del carico esistente? | Selezionare **Sì** |
    | **Impostazioni di bilanciamento del carico** |
    | Opzioni di bilanciamento del carico | Selezionare **Bilanciamento del carico di Azure** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**  |
    | Selezionare un pool back-end | Selezionare **myBackendPool** |
   
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

7. Seguire i passaggi da 1 a 8 per creare altre due VM con i valori seguenti e tutte le altre impostazioni identiche a **myVM1**:

    | Impostazione | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Nome |  **myVM2** | **myVM3** |
    | Zona di disponibilità | **2** | **3** |
    | Gruppo di sicurezza di rete | Selezionare il gruppo di sicurezza di rete **myNSG** esistente| Selezionare il gruppo di sicurezza di rete **myNSG** esistente |


# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione.  Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Risorse di Load Balancer Basic create per l'avvio rapido." border="false":::

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. 

Nella rete virtuale viene inoltre configurato un indirizzo IP privato come front-end (denominato **LoadBalancerFrontend** per impostazione predefinita) del servizio di bilanciamento del carico. 

L'indirizzo IP front-end può essere **statico** o **dinamico**.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si crea una rete virtuale e una subnet.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **CreateIntLBQS-rg** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Europa occidentale** |

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **myBackendSubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

7. Selezionare **Salva**.

8. Selezionare la scheda **Sicurezza**.

9. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Crea**.

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare il gruppo **CreateIntLBQS-rg** creato nel passaggio precedente.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | Selezionare **Basic** |
    | Rete virtuale | Selezionare la rete **myVNet** creata nel passaggio precedente. |
    | Subnet  | Selezionare la subnet **myBackendSubnet** creata nel passaggio precedente. |
    | Assegnazione indirizzi IP | Selezionare **Dinamico**. |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Creare un servizio di bilanciamento del carico interno Basic." border="true":::

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurati:

* Le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end.
* Un probe di integrità.
* Una regola di bilanciamento del carico.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** immettere o selezionare:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myBackendPool**. |
    | Rete virtuale | Selezionare **myVNET**. |
    | Associato a | Selezionare **Macchine virtuali** |

4. Selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Il servizio di bilanciamento del carico monitora lo stato dell'app con un probe di integrità. 

Il probe di integrità aggiunge o rimuove le macchine virtuali nel servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere **80**.|
    | Path | Immettere **/** |
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|

3. Selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. È possibile definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico. La porta di origine e quella di destinazione vengono definite nella regola. 

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool** sulla **porta 80**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Selezionare **IPv4** |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontend** |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15** minuti. |
 
4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verrà illustrato come:

* Creare tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.
* Creare un set di disponibilità per le macchine virtuali.
* Installare IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

In questa sezione si creeranno tre VM (**myVM1**, **myVM2**, **myVM3**).

Le due macchine virtuali verranno aggiunte a un set di disponibilità denominato **myAvailabilitySet**.

Queste macchine virtuali vengono aggiunte al pool back-end del servizio di bilanciamento del carico creato in precedenza.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **CreateIntLBQS-rg** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **Set di disponibilità** |
    | Set di disponibilità | Selezionare **Crea nuovo**. </br> Immettere **myAvailabilitySet** in **Nome**. </br> Selezionare **OK**. |
    | Immagine | **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVNet** |
    | Subnet | Selezionare **myBackendSubnet** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> Nella pagina **Crea gruppo di sicurezza di rete** immettere **myNSG** in **Nome**. </br> Selezionare **OK**. |
    | **Bilanciamento del carico**  |
    | Associare questa macchina virtuale a una soluzione di bilanciamento del carico esistente? | Selezionare **No** |

5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

7. Seguire i passaggi da 1 a 8 per creare altre due VM con i valori seguenti e tutte le altre impostazioni identiche a **myVM1**:

    | Impostazione | VM 2 | VM 3 |
    | ------- | ----- | ---- |
    | Nome |  **myVM2** | **myVM3** |
    | Set di disponibilità | Selezionare **myAvailabilitySet** | Selezionare **myAvailabilitySet** |
    | Gruppo di sicurezza di rete | Selezionare il gruppo di sicurezza di rete **myNSG** esistente | Selezionare il gruppo di sicurezza di rete **myNSG** esistente |

### <a name="add-virtual-machines-to-the-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end

Le VM create nei passaggi precedenti devono essere aggiunte al pool back-end **myLoadBalancer**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **myBackendPool**.

3. In **Associato a** selezionare **Macchine virtuali**.

4. Nella sezione **Macchine virtuali** selezionare **+ Aggiungi**.

5. Selezionare le caselle accanto a **myVM1**, **myVM2** e **myVM3**.

6. Selezionare **Aggiungi**.

7. Selezionare **Salva**.
---

## <a name="create-test-virtual-machine"></a>Creare una macchina virtuale di test

In questa sezione si creerà una VM denominata **myTestVM**.  Questa VM verrà usata per verificare la configurazione del servizio di bilanciamento del carico.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **CreateIntLBQS-rg** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myTestVM** |
    | Region | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare il gruppo **MyNSG** creato nel passaggio precedente.|
       
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="install-iis"></a>Installare IIS

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myVM1**, che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

2. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

4. Immettere il nome utente e la password specificati durante la creazione della VM.

5. Selezionare **Connetti**.

6. Nel desktop del server passare a **Strumenti di amministrazione Windows** > **Windows PowerShell**.

7. Nella finestra di PowerShell eseguire i comandi seguenti per:

    * Installare il server IIS
    * Rimuovere il file iisstart.htm predefinito
    * Aggiungere un nuovo file iisstart.htm che visualizzi il nome della macchina virtuale:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Chiudere la sessione di Bastion con **myVM1**.

9. Ripetere i passaggi da 1 a 6 per installare IIS e il file iisstart.htm aggiornato in **myVM2** e **myVM3**.


## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

1. Individuare l'indirizzo IP privato del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer**.

2. Annotare o copiare l'indirizzo accanto a **Indirizzo IP privato** nella pagina **Panoramica** di **myLoadBalancer**.

3. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myTestVM**, che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Screenshot che mostra una finestra del browser che visualizza la pagina predefinita, come previsto." border="true":::
   
Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra entrambe le macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ogni macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse **CreateIntLBQS-rg** che contiene le risorse e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* È stato creato un servizio di bilanciamento del carico di Azure interno Standard o Basic
* Sono state collegate tre macchine virtuali al servizio di bilanciamento del carico.
* È stata configurata la regola del traffico di bilanciamento del carico, il probe di integrità e quindi è stato testato il servizio di bilanciamento del carico. 

Per altre informazioni su Azure Load Balancer, passare a:
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)