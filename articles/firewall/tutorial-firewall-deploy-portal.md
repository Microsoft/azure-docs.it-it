---
title: 'Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure'
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 54900b7b9089d4a4c6cbc742ecf09aa19ff2a550
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741957"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web. In alternativa, potrebbe essere utile limitare gli indirizzi IP e le porte in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet.
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Per questa esercitazione, viene creata una singola rete virtuale semplificata con due subnet per facilitare la distribuzione.

Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale. I server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

* **AzureFirewallSubnet**: in questa subnet si trova il firewall.
* **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-deploy-portal/tutorial-network.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola dell'applicazione per consentire l'accesso a www.google.com
> * Configurare una regola di rete per consentire l'accesso a server DNS esterni
> * Configurare una regola NAT per consentire un desktop remoto nel server di test
> * Testare il firewall

Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e un server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per l'esercitazione.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nel menu del portale di Azure selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina. Quindi selezionare **Aggiungi**.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Nome del gruppo di risorse** immettere *Test-FW-RG*.
1. In **Località del gruppo di risorse** selezionare una località. Tutte le altre risorse create devono risiedere nella stessa località.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa VNet avrà tre subnet.

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Selezionare **Rete** > **Rete virtuale**.
1. Selezionare **Crea**.
1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **Gruppo di risorse** selezionare **Test-FW-RG**.
1. In **Nome** immettere **Test-FW-VN**.
1. In **Area** selezionare la stessa località usata in precedenza.
1. Selezionare **Avanti: Indirizzi IP**.
1. Per **Spazio indirizzi IPv4**, digitare **10.0.0.0/16**.
1. In **Subnet** selezionare **predefinito**.
1. Per **Nome subnet** immettere **AzureFirewallSubnet**. Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
1. In **Intervallo indirizzi** digitare **10.0.1.0/26**.
1. Selezionare **Salva**.

   Creare quindi una subnet per un server del carico di lavoro.

1. Selezionare **Aggiungi subnet**.
4. Per **Nome subnet** immettere **Workload-SN**.
5. In **Intervallo di indirizzi subnet** digitare **10.0.2.0/24**.
6. Selezionare **Aggiungi**.
7. Selezionare **Rivedi e crea**.
8. Selezionare **Create** (Crea).

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare ora la macchina virtuale del carico di lavoro e inserirla nella subnet **Workload-SN**.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Selezionare **Windows Server 2016 datacenter**.
4. Immettere i valori seguenti per la macchina virtuale:

   |Impostazione  |Valore  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nome macchina virtuale     |**Srv-Work**|
   |Region     |Come precedente|
   |Immagine|Windows Server 2019 Datacenter|
   |Nome utente amministratore     |Digitare un nome utente|
   |Password     |Digitare una password|

4. In **Regole porta in ingresso**, **Porte in ingresso pubbliche** selezionare **Nessuna**.
6. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Dischi**.
7. Accettare tutte le impostazioni predefinite del disco e selezionare **Avanti: Rete**.
8. Assicurarsi che per la rete virtuale sia selezionata l'opzione **Test-FW-VN** e che la subnet sia **Workload-SN**.
9. In **IP pubblico** selezionare **Nessuno**.
11. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Gestione**.
12. Selezionare **Disabilita** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

## <a name="deploy-the-firewall"></a>Distribuire il firewall

Distribuire il firewall nella rete virtuale.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Digitare **firewall** nella casella di ricerca e premere **INVIO**.
3. Selezionare **Firewall**, quindi **Crea**.
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

   |Impostazione  |Valore  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Test-FW-RG** |
   |Nome     |**Test-FW01**|
   |Region     |Selezionare la stessa località usata in precedenza|
   |Gestione del firewall|**Usare le regole del firewall (classiche) per gestire questo firewall**|
   |Scegliere una rete virtuale     |**Use existing** (Usa esistente): **Test-FW-VN**|
   |Indirizzo IP pubblico     |**Aggiungi nuovo**<br>**Nome**: **fw-pip**|

5. Accettare gli altri valori predefiniti, quindi selezionare **Verifica + crea**.
6. Controllare il riepilogo e quindi selezionare **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
7. Al termine della distribuzione, passare al gruppo di risorse **Test-FW-RG** e selezionare il firewall **Test FW01**.
8. Prendere nota degli indirizzi IP pubblico e privato del firewall. Verranno usati in seguito.

## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **Workload-SN** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nel menu del portale di Azure selezionare **Tutti servizi** oppure cercare e selezionare *Tutti i servizi* in qualsiasi pagina.
2. In **Rete** selezionare **Tabelle route**.
3. Selezionare **Aggiungi**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
6. Per **Gruppo di risorse** selezionare **Test-FW-RG**.
7. In **Area** selezionare la stessa località usata in precedenza.
4. In **Nome** immettere **Firewall-route**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

Al termine della distribuzione, selezionare **Vai alla risorsa**.

1. Nella pagina Firewall-Route selezionare **subnet** e quindi fare clic su **associa**.
1. Selezionare **Rete virtuale** > **Test-FW-VN**.
1. In **Subnet** selezionare **Workload-SN**. Assicurarsi di selezionare solo la subnet **Workload-SN** per questa route; in caso contrario, il firewall non funzionerà correttamente.

13. Selezionare **OK**.
14. Selezionare **Route**, quindi **Aggiungi**.
15. In **Nome route** digitare **fw-dg**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Selezionare **OK**.

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

Questa è la regola dell'applicazione che consente l'accesso in uscita a `www.google.com`.

1. Aprire **Test-FW-RG** e selezionare il firewall **Test-FW01**.
2. Nella pagina **test-FW01** , in **Impostazioni**, selezionare **regole (classico)**.
3. Selezionare la scheda **Raccolta regole dell'applicazione**.
4. Selezionare **Aggiungi raccolta regole dell'applicazione**.
5. In **Nome** immettere **App-Coll01**.
6. In **Priorità** immettere **200**.
7. In **Azione** selezionare **Consenti**.
8. In **Regole**, **FQDN di destinazione**, immettere **Allow-Google** in **Nome**.
9. In **Tipo di origine** selezionare **Indirizzo IP**.
10. In **Origine** digitare **10.0.2.0/24**.
11. In **Protocollo:Porta** immettere **http, https**.
12. Per **FQDN di destinazione**, digitare **`www.google.com`**
13. Selezionare **Aggiungi**.

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-a-network-rule"></a>Configurare una regola di rete

Si tratta della regola di rete che consente l'accesso in uscita a due indirizzi IP sulla porta 53 (DNS).

1. Selezionare la scheda **Raccolta regole di rete**.
2. Selezionare **Aggiungi raccolta regole di rete**.
3. In **Nome** immettere **Net-Coll01**.
4. In **Priorità** immettere **200**.
5. In **Azione** selezionare **Consenti**.
6. In **Regole**, **Indirizzi IP** digitare **Allow-DNS** in **Nome**.
7. In **Protocollo** selezionare **UDP**.
9. In **Tipo di origine** selezionare **Indirizzo IP**.
1. In **Origine** digitare **10.0.2.0/24**.
2. Per **Tipo di destinazione** selezionare **Indirizzo IP**.
3. In **Indirizzo di destinazione** digitare **209.244.0.3,209.244.0.4**

   Si tratta di server DNS pubblici gestiti da CenturyLink.
1. In **Porte di destinazione** immettere **53**.
2. Selezionare **Aggiungi**.

## <a name="configure-a-dnat-rule"></a>Configurare una regola DNAT

Questa regola consente di connettere un desktop remoto alla macchina virtuale Srv-Work attraverso il firewall.

1. Selezionare la scheda **Raccolta regole DNAT**.
2. Selezionare **Aggiungi raccolta regole NAT**.
3. Per **Nome** digitare **rdp**.
4. In **Priorità** immettere **200**.
5. In **Regole**, per **Nome**, digitare **rdp-nat**.
6. In **Protocollo** selezionare **TCP**.
7. In **Tipo di origine** selezionare **Indirizzo IP**.
8. Per **Origine**, digitare **\*** .
9. Per **Indirizzo di destinazione**, digitare l'indirizzo IP pubblico del firewall.
10. In **Porte di destinazione** digitare **3389**.
11. Per **Indirizzo convertito** digitare l'indirizzo IP privato di **Srv-work**.
12. Per **Porta tradotta** digitare **3389**.
13. Selezionare **Aggiungi**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modificare l'indirizzo DNS primario e secondario per l'interfaccia di rete **Srv-Work**

Ai fini del test in questa esercitazione vengono configurati gli indirizzi DNS primari e secondari del server. Questo non è un requisito generale di Firewall di Azure.

1. Nel menu del portale di Azure selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina. Selezionare il gruppo di risorse **Test-FW-RG**.
2. Selezionare l'interfaccia di rete per la macchina virtuale **Srv-Work**.
3. In **Impostazioni** selezionare **Server DNS**.
4. In **Server DNS** selezionare **Personalizzato**.
5. Immettere **209.244.0.3** nella casella di testo **Aggiungi server DNS** e **209.244.0.4** nella casella di testo successiva.
6. Selezionare **Salva**.
7. Riavviare la macchina virtuale **Srv-Work**.

## <a name="test-the-firewall"></a>Testare il firewall

A questo punto testare il firewall per verificare che funzioni come previsto.

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall e accedere alla macchina virtuale **Srv-Work**. 
3. Aprire Internet Explorer e passare a `https://www.google.com`.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page di Google.

5. Passare a `https://www.microsoft.com`.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile passare al nome di dominio completo consentito ma non agli altri.
* È possibile risolvere i nomi DNS con il server DNS esterno configurato.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **Test-FW-RG** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./firewall-diagnostics.md)