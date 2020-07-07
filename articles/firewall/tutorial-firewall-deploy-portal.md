---
title: 'Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure'
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 151e7d286dac91ddd0e988027968f2e44a83e35e
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362646"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web. In alternativa, potrebbe essere utile limitare gli indirizzi IP e le porte in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet.
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

Per questa esercitazione viene creata una singola rete virtuale semplificata con tre subnet per facilitare la distribuzione.

Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale. I server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

* **AzureFirewallSubnet**: in questa subnet si trova il firewall.
* **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
* **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola dell'applicazione per consentire l'accesso a www.google.com
> * Configurare una regola di rete per consentire l'accesso a server DNS esterni
> * Testare il firewall

Se si preferisce, è possibile completare questa esercitazione usando [Azure PowerShell](deploy-ps.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per l'esercitazione.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nel menu del portale di Azure selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina. Quindi selezionare **Aggiungi**.
3. In **Nome del gruppo di risorse** immettere *Test-FW-RG*.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
5. In **Località del gruppo di risorse** selezionare una località. Tutte le altre risorse create devono risiedere nella stessa località.
6. Selezionare **Crea**.

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa rete virtuale conterrà tre subnet.

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
1. Selezionare **Rete** > **Rete virtuale**.
1. In **Nome** immettere **Test-FW-VN**.
1. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **Gruppo di risorse** selezionare **Test-FW-RG**.
1. In **Località** selezionare la stessa località usata in precedenza.
1. In **Subnet** immettere **AzureFirewallSubnet** per **Nome**. Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
1. In **Intervallo indirizzi** digitare **10.0.1.0/26**.
1. Accettare le altre impostazioni predefinite e quindi selezionare **Crea**.

### <a name="create-additional-subnets"></a>Creare subnet aggiuntive

Successivamente, creare le subnet per il jump server e una subnet per i server del carico di lavoro.

1. Nel menu del portale di Azure selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina. Selezionare quindi **Test-FW-RG**.
2. Selezionare la rete virtuale **Test-FW-VN**.
3. Selezionare **Subnet** >  **+Subnet**.
4. In **Nome** immettere **Workload-SN**.
5. In **Intervallo indirizzi** immettere **10.0.2.0/24**.
6. Selezionare **OK**.

Creare un'altra subnet denominata **Jump-NS** e con **10.0.3.0/24** come intervallo indirizzi.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Selezionare **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

   |Impostazione  |valore  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nome macchina virtuale     |**Srv-Jump**|
   |Region     |Come precedente|
   |Nome utente amministratore     |**azureuser**|
   |Password     |**Azure123456!**|

4. In **Regole porta in ingresso** selezionare **Consenti porte selezionate** per **Porte in ingresso pubbliche**.
5. In **Selezionare le porte in ingresso** selezionare **RDP (3389)** .

6. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Dischi**.
7. Accettare tutte le impostazioni predefinite del disco e selezionare **Avanti: Rete**.
8. Assicurarsi che **Test-FW-VN** sia selezionato per la rete virtuale e che la subnet sia **Jump-SN**.
9. Per **Indirizzo IP pubblico**, accettare il nome predefinito del nuovo indirizzo IP pubblico indirizzo (Srv-Jump-ip).
11. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Gestione**.
12. Selezionare **Disattivato** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

Usare le informazioni nella tabella seguente per configurare un'altra macchina virtuale denominata **Srv-Work**. Il resto della configurazione è uguale a quella della macchina virtuale Srv-Jump.

|Impostazione  |valore  |
|---------|---------|
|Subnet|**Workload-SN**|
|IP pubblico|**Nessuno**|
|Porte in ingresso pubbliche|**Nessuno**|

## <a name="deploy-the-firewall"></a>Distribuire il firewall

Distribuire il firewall nella rete virtuale.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.
2. Digitare **firewall** nella casella di ricerca e premere **INVIO**.
3. Selezionare **Firewall**, quindi **Crea**.
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

   |Impostazione  |valore  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Test-FW-RG** |
   |Nome     |**Test-FW01**|
   |Location     |Selezionare la stessa località usata in precedenza|
   |Scegliere una rete virtuale     |**Use existing** (Usa esistente): **Test-FW-VN**|
   |Indirizzo IP pubblico     |**Aggiungi nuovo**. L'indirizzo IP pubblico deve essere di tipo SKU Standard.|

5. Selezionare **Rivedi e crea**.
6. Controllare il riepilogo e quindi selezionare **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
7. Al termine della distribuzione, passare al gruppo di risorse **Test-FW-RG** e selezionare il firewall **Test FW01**.
8. Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **Workload-SN** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nel menu del portale di Azure selezionare **Tutti servizi** oppure cercare e selezionare *Tutti i servizi* in qualsiasi pagina.
2. In **Rete** selezionare **Tabelle route**.
3. Selezionare **Aggiungi**.
4. In **Nome** immettere **Firewall-route**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
6. Per **Gruppo di risorse** selezionare **Test-FW-RG**.
7. In **Località** selezionare la stessa località usata in precedenza.
8. Selezionare **Crea**.
9. Selezionare **Aggiorna** e quindi selezionare la tabella di route **Firewall-route**.
10. Selezionare **Subnet** e quindi **Associa**.
11. Selezionare **Rete virtuale** > **Test-FW-VN**.
12. In **Subnet** selezionare **Workload-SN**. Assicurarsi di selezionare solo la subnet **Workload-SN** per questa route; in caso contrario, il firewall non funzionerà correttamente.

13. Selezionare **OK**.
14. Selezionare **Route**, quindi **Aggiungi**.
15. In **Nome route** digitare **fw-dg**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Selezionare **OK**.

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

Si tratta della regola di applicazione che consente l'accesso in uscita a www.google.com.

1. Aprire **Test-FW-RG** e selezionare il firewall **Test-FW01**.
2. Nella pagina **Test-FW01** selezionare **Regole** in **Impostazioni**.
3. Selezionare la scheda **Raccolta regole dell'applicazione**.
4. Selezionare **Aggiungi raccolta regole dell'applicazione**.
5. In **Nome** immettere **App-Coll01**.
6. In **Priorità** immettere **200**.
7. In **Azione** selezionare **Consenti**.
8. In **Regole**, **FQDN di destinazione**, immettere **Allow-Google** in **Nome**.
9. In **Tipo di origine** selezionare **Indirizzo IP**.
10. In **Origine** digitare **10.0.2.0/24**.
11. In **Protocollo:Porta** immettere **http, https**.
12. In **FQDN di destinazione** immettere **www.google.com**
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
2. In **Indirizzo di destinazione** digitare **209.244.0.3,209.244.0.4**

   Si tratta di server DNS pubblici gestiti da CenturyLink.
1. In **Porte di destinazione** immettere **53**.
2. Selezionare **Aggiungi**.

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

1. Nel portale di Azure rivedere le impostazioni di rete per la macchina virtuale **Srv-Work** e annotare l'indirizzo IP privato.
2. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e accedere. Da qui aprire una connessione Desktop remoto all'indirizzo IP privato **Srv-Work**.
3. Aprire Internet Explorer e passare a https://www.google.com.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page di Google.

5. Passare a https://www.microsoft.com.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile passare al nome di dominio completo consentito ma non agli altri.
* È possibile risolvere i nomi DNS con il server DNS esterno configurato.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **Test-FW-RG** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
