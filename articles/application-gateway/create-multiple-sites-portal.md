---
title: 'Esercitazione: Ospitare più siti Web con il portale di Azure'
titleSuffix: Azure Application Gateway
description: Questa esercitazione descrive come creare un gateway applicazione che ospita più siti Web usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: cfbd5301bc2b24c4d5614e5f88c6ae18d4affc66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721631"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Esercitazione: Creare e configurare un gateway applicazione per ospitare più siti Web usando il portale di Azure

È possibile usare il portale di Azure per configurare l'[hosting di più siti Web](multiple-site-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione si definiranno pool di indirizzi back-end usando macchine virtuali. Si configurano quindi i listener e le regole in base a due domini per assicurarsi che il traffico Web raggiunga i server appropriati nei pool. Questa esercitazione usa esempi di *www.contoso.com* e *www.fabrikam.com*.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gateway applicazione
> * Creare macchine virtuali per i server back-end
> * Creare pool back-end con i server back-end
> * Creare i listener back-end
> * Creare le regole di routing
> * Modificare il file degli host per la risoluzione dei nomi

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Gateway applicazione multisito":::

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

1. Selezionare **Crea una risorsa** nel menu a sinistra del portale di Azure. Verrà visualizzata la finestra **Nuovo**.

2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco **In primo piano**.

### <a name="basics-tab"></a>Scheda Informazioni di base

1. Nella scheda **Informazioni di base** immettere questi valori per le impostazioni del gateway applicazione seguenti:

   - **Gruppo di risorse**: selezionare **myResourceGroupAG** come gruppo di risorse. Se non esiste, selezionare **Crea nuovo** per crearlo.
   - **Nome del gateway applicazione**: immettere *myAppGateway* come nome del gateway applicazione.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Creare un gateway applicazione":::

2.  Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale oppure usarne una esistente. In questo esempio verrà creata una nuova rete virtuale in concomitanza con la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

    In **Configura rete virtuale** selezionare **Crea nuovo** per creare una nuova rete virtuale. Nella finestra **Crea rete virtuale** visualizzata immettere i valori seguenti per creare la rete virtuale e due subnet:

    - **Name**: immettere *myVnet* come nome della rete virtuale.

    - **Nome subnet** (subnet del gateway applicazione): Nella griglia **Subnet** verrà visualizzata una subnet denominata *Predefinita*. Modificare il nome della subnet in *myAGSubnet*.<br>La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.

    - **Nome subnet** (subnet del server back-end): Nella seconda riga della griglia **Subnet** immettere *myBackendSubnet* nella colonna **Nome subnet**.

    - **Intervallo di indirizzi** (subnet del server back-end): Nella seconda riga della griglia **Subnet** immettere un intervallo di indirizzi che non si sovrappone all'intervallo di indirizzi di *myAGSubnet*. Ad esempio, se l'intervallo di indirizzi di *myAGSubnet* è 10.0.0.0/24, immettere *10.0.1.0/24* per l'intervallo di indirizzi di *myBackendSubnet*.

    Selezionare **OK** per chiudere la finestra **Crea rete virtuale** e salvare le impostazioni della rete virtuale.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="Creare una rete virtuale":::
    
3. Nella scheda **Informazioni di base** accettare i valori predefiniti per le altre impostazioni e quindi selezionare **Successivo: Front-end**.

### <a name="frontends-tab"></a>Scheda Front-end

1. Nella scheda **Front- end** verificare che **Tipo di indirizzo IP front-end** sia impostato su **Pubblico**. <br>È possibile configurare l'indirizzo IP front-end come pubblico o privato in base al caso d'uso. In questo esempio si sceglierà un indirizzo IP front-end pubblico.
   > [!NOTE]
   > Per lo SKU v2 del gateway applicazione, è possibile scegliere solo la configurazione **pubblica** dell'IP front-end. La configurazione di indirizzi IP front-end privati non è attualmente abilitata per questo SKU v2.

2. Selezionare **Aggiungi nuovo** per l' **indirizzo IP pubblico** e immettere *myAGPublicIPAddress* per il nome dell'indirizzo IP pubblico e quindi fare clic su **OK**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Creare un'altra rete virtuale":::

3. Selezionare **Avanti: Back-end**.

### <a name="backends-tab"></a>Scheda Back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. In questo esempio verrà creato un pool back-end vuoto con il gateway applicazione a cui verranno aggiunte le destinazioni back-end.

1. Nella scheda Back- **end** selezionare **Aggiungi un pool back-end**.

2. Nella finestra **Aggiungi un pool back-end** visualizzata immettere i valori seguenti per creare un pool back-end vuoto:

    - **Name**: immettere *contosoPool* come nome del pool back-end.
    - **Aggiungi pool back-end senza destinazioni**: selezionare **Sì** per creare un pool back-end senza destinazioni. Le destinazioni back-end verranno aggiunte dopo la creazione del gateway applicazione.

3. Nella finestra **Aggiungi un pool back-end** selezionare **Aggiungi** per salvare la configurazione del pool back-end e tornare alla scheda **Back-end**.
4. Aggiungere ora un altro pool back-end denominato *fabrikamPool* nello stesso modo in cui è stato aggiunto il pool precedente.
1. Selezionare **Aggiungi**.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Creare back-end":::

4. Nella scheda **Back-end** selezionare **Passaggio successivo: Configurazione**.

### <a name="configuration-tab"></a>Scheda Configurazione

Nella scheda **Configurazione** verranno connessi i pool front-end e back-end creati tramite una regola di routing.

1. Selezionare **Aggiungi una regola di routing** nella colonna **regole di routing** .

2. Nella finestra **Aggiungi una regola di routing** visualizzata immettere *contosoRule* per **Nome regola**.

3. Una regola di routing richiede un listener. Nella scheda **Listener** nella finestra **Aggiungi una regola di routing** immettere i valori seguenti per il listener:

    - **Nome regola**: *contosoRule*.
    - **Nome del listener**: *contosoListener*.
    - **IP front-end**: selezionare **Pubblico** per scegliere l'indirizzo IP pubblico creato per il front-end.

   In **Impostazioni aggiuntive**:
   - **Tipo di listener**: Più siti
   - **Nome host**: **www.contoso.com**

   Accettare i valori predefiniti per le altre impostazioni nella scheda **Listener**, quindi selezionare la scheda **Destinazioni back-end** per configurare il resto della regola di routing.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Creare una regola di routing":::

4. Nella scheda **Destinazioni back-end** selezionare **contosoPool** per **Destinazione back-end**.

5. Per l' **impostazione http**, selezionare **Aggiungi nuovo** per creare una nuova impostazione http. L'impostazione HTTP determinerà il comportamento della regola di routing. Nella finestra **Aggiungi un'impostazione HTTP** visualizzata immettere *contosoHTTPSetting* per **Nome impostazione HTTP**. Accettare i valori predefiniti per le altre impostazioni nella finestra **Aggiungi un'impostazione HTTP** e quindi selezionare **Aggiungi** per tornare alla finestra **Aggiungi una regola di routing**. 

6. Nella finestra **Aggiungi una regola di routing** selezionare **Aggiungi** per salvare la regola di routing e tornare alla scheda **Configurazione**.
7. Selezionare **Aggiungi una regola di routing** e aggiungere una regola, un listener, una destinazione back-end e un'impostazione http simili per fabrikam.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Regola per Fabrikam":::

7. Selezionare **Avanti: Tag** e quindi **Passaggio successivo: Rivedi e crea**.

### <a name="review--create-tab"></a>Scheda Rivedi e crea

Rivedere le impostazioni nella scheda **Rivedi e crea** e quindi selezionare **Crea** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione in Azure può richiedere diversi minuti.

Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-targets"></a>Aggiungere destinazioni back-end

In questo esempio vengono usate macchine virtuali come back-end di destinazione. È possibile usare macchine virtuali esistenti o crearne di nuove. Vengono create due macchine virtuali, usate da Azure come server back-end per il gateway applicazione.

Per aggiungere destinazioni back-end:

1. Creare due nuove macchine virtuali, *contosoVM* e *fabrikamVM*, da usare come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end ai pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nel portale di Azure fare clic su **Crea una risorsa**. Verrà visualizzata la finestra **Nuovo**.
2. Selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**. Viene visualizzata la pagina **Creare una macchina virtuale**.<br>Il gateway applicazione può indirizzare il traffico a qualsiasi tipo di macchina virtuale usato nel pool back-end. In questo esempio si usa Windows Server 2016 Datacenter.
3. Immettere questi valori nella scheda **Informazioni di base** per le seguenti impostazioni della macchina virtuale:

    - **Sottoscrizione**: selezionare la propria sottoscrizione.
    - **Gruppo di risorse**: selezionare **myResourceGroupAG** come nome del gruppo di risorse.
    - **Nome macchina virtuale**: immettere *contosoVM* come nome della macchina virtuale.
    - **Region**: selezionare la stessa area usata in precedenza.
    - **Nome utente**: Immettere un nome per il nome utente amministratore.
    - **Password**: Immettere la password dell'amministratore.
1. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Dischi**.  
2. Accettare le impostazioni predefinite della scheda **Dischi** e quindi selezionare **Avanti: Rete**.
3. Nella scheda **Rete** verificare che **myVNet** sia selezionato per la **Rete virtuale** e che la **Subnet** sia **myBackendSubnet**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Gestione**.<br>Il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale in cui si trova, ma è necessario verificare che ci sia la connettività IP.
4. Nella scheda **Gestione** impostare **Diagnostica di avvio** su **Disabilita**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
5. Nella scheda **Rivedi e crea** rivedere le impostazioni, correggere eventuali errori di convalida e quindi selezionare **Crea**.
6. Attendere il termine della creazione della macchina virtuale prima di continuare.

### <a name="install-iis-for-testing"></a>Installare IIS a scopo di test

In questo esempio viene installato IIS nelle macchine virtuali solo per verificare che il gateway applicazione sia stato creato correttamente da Azure.

1. Aprire [Azure PowerShell](../cloud-shell/quickstart-powershell.md). A tale scopo, selezionare **Cloud Shell** dalla barra di spostamento superiore del portale di Azure e quindi selezionare **PowerShell** nell'elenco a discesa. 

    ![Installare l'estensione personalizzata](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Eseguire il comando seguente per installare IIS nella macchina virtuale, sostituendo l'area del gruppo di risorse per <località\>: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura precedentemente completata. Usare *fabrikamVM* come nome della macchina virtuale e per l'impostazione **VMName** del cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pools"></a>Aggiungere i server back-end ai pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **Pool back-end** dal menu a sinistra.

3. Selezionare **contosoPool**.

4. In **tipo di destinazione** selezionare **macchina virtuale** dall'elenco a discesa.

5. In **destinazione** selezionare l'interfaccia di rete della macchina virtuale **contosoVM** dall'elenco a discesa.

    ![Aggiungere i server back-end](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selezionare **Salva**.
7. Ripetere la procedura per aggiungere *fabrikamVM* e l'interfaccia a *fabrikamPool*.

Attendere il completamento della distribuzione prima di procedere al passaggio successivo.

## <a name="edit-your-hosts-file-for-name-resolution"></a>Modificare il file degli host per la risoluzione dei nomi

Dopo aver creato il gateway applicazione con il relativo indirizzo IP pubblico, è possibile ottenere l'indirizzo IP e usarlo per modificare il file degli host per risolvere `www.contoso.com` e `www.fabrikam.com` . In un ambiente di produzione, è possibile creare un `CNAME` in DNS per la risoluzione dei nomi.

1. Fare clic su **Tutte le risorse** e quindi su **myAGPublicIPAddress**.

    ![Registrare l'indirizzo DNS del gateway applicazione](./media/create-multiple-sites-portal/public-ip.png)

2. Copiare l'indirizzo IP e usarlo come valore per le nuove voci del `hosts` file.
1. Nel computer locale aprire un prompt dei comandi amministrativo e passare a `c:\Windows\System32\drivers\etc` .
1. Aprire il `hosts` file e aggiungere le voci seguenti, dove `x.x.x.x` è l'indirizzo IP pubblico del gateway applicazione:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Salvare il file.
1. Eseguire i comandi seguenti per caricare e visualizzare le modifiche apportate al file hosts:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Digitare un nome di dominio nella barra degli indirizzi del browser. Ad esempio: `http://www.contoso.com`.

    ![Testare il sito contoso nel gateway applicazione](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Modificare l'indirizzo nell'altro dominio. verrà visualizzato un esempio simile al seguente:

    ![Testare il sito fabrikam nel gateway applicazione](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate.

Per rimuovere il gruppo di risorse:

1. Nel menu a sinistra del portale di Azure, selezionare **Gruppi di risorse**.
2. Nella pagina **Gruppo di risorse** cercare **myResourceGroupAG** nell'elenco e selezionarlo.
3. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**.
4. Immettere *myResourceGroupAG* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e quindi selezionare **Elimina**.

Per ripristinare il file hosts:
1. Eliminare le `www.contoso.com` `www.fabrikam.com` righe e dal file host ed eseguire `ipconfig/registerdns` e `ipconfig/flushdns` dal prompt dei comandi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione di Azure](./overview.md)