---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: cc4e267c6912b8938db1ba5497a27f9c0026bd79
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887334"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteggi le tue porte di gestione con accesso just-in-time

Se si fa parte del piano tariffario standard del Centro sicurezza (vedere [i prezzi](/azure/security-center/security-center-pricing)), è possibile bloccare il traffico in ingresso verso le macchine virtuali di Azure con accesso a macchine virtuali (VM) JIT (Just-In-Time). In questo modo si riduce l'esposizione agli attacchi, fornendo al contempo un facile accesso per la connessione alle macchine virtuali quando necessario.

> [!NOTE]
> Attualmente, l'accesso JIT alle macchine virtuali in Centro sicurezza supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Per altre informazioni sui modelli di distribuzione classica e con Azure Resource Manager, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Configurare JIT in una macchina virtualeConfigure JIT on a VM

Esistono tre modi per configurare un criterio JIT in una macchina virtuale:There are three ways to configure a JIT policy on a VM:

- [Configurare l'accesso JIT nel Centro sicurezza di AzureConfigure JIT access in Azure Security Center](#jit-asc)
- [Configurare l'accesso JIT in una pagina della macchina virtuale di AzureConfigure JIT access in an Azure VM page](#jit-vm)
- [Configurare un criterio JIT in una macchina virtuale a livello di codiceConfigure a JIT policy on a VM programmatically](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Configurare JIT nel Centro sicurezza di AzureConfigure JIT in Azure Security Center

Dal Centro sicurezza è possibile configurare un criterio JIT e richiedere l'accesso a una macchina virtuale usando un criterio JIT

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Configurare l'accesso JIT in una macchina virtuale nel Centro sicurezza<a name="jit-asc"></a>

1. Aprire il dashboard **Centro sicurezza**.

1. Nel riquadro a sinistra selezionare **Accesso Just-In-Time alla VM**.

    ![Riquadro Accesso Just-In-Time alla VM](./media/security-center-just-in-time/just-in-time.png)

    Viene visualizzata la finestra **Accesso just-in-time alle macchine virtuali** e vengono visualizzate informazioni sullo stato delle macchine virtuali:The Just-in-time VM access window opens and shows information on the state of your VMs:

    - **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso JIT. I dati visualizzati sono relativi all'ultima settimana e includono, per ogni macchina virtuale, il numero di richieste approvate, la data e l'ora dell'ultimo accesso e l'ultimo utente.
    - **Consigliato:** macchine virtuali in grado di supportare l'accesso alle macchine virtuali just-in-time ma che non sono state configurate. È consigliabile abilitare il controllo dell'accesso JIT per queste macchine virtuali.
    - **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:
      - Gruppo di sicurezza di rete mancante - La soluzione JIT richiede la presenza di un gruppo di sicurezza di rete.
      - Macchina virtuale classica - L'accesso JIT alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. La soluzione JIT non supporta la distribuzione classica. 
      - Altro: una macchina virtuale rientra in questa categoria se la soluzione just-in-time è disattivata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse oppure se nella macchina virtuale manca un indirizzo IP pubblico e non è presente un gruppo di sicurezza di rete.

1. Selezionare la scheda **Consigliati**.

1. In **VIRTUAL MACHINE**fare clic sulle macchine virtuali che si desidera abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.

      ![Abilitare l'accesso JIT alla VM](./media/security-center-just-in-time/enable-just-in-time.png)

1. Fare clic su **Abilita JIT nelle macchine virtuali**. Verrà visualizzato un riquadro con le porte predefinite consigliate dal Centro sicurezza di Azure:A pane opens displaying the default ports recommended by Azure Security Center:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM
1. Facoltativamente, è possibile aggiungere porte personalizzate all'elenco:Optionally, you can add custom ports to the list:

      1. Fare clic su **Aggiungi**. Viene visualizzata la finestra **Di configurazione Aggiungi porta.**
      1. Per ogni porta che si sceglie di configurare, sia come predefinita che per quella personalizzata, è possibile personalizzare le impostazioni seguenti:
            - **Tipo di protocollo** - Il protocollo consentito su questa porta quando una richiesta viene approvata.
            - **Allowed source IP addresses** (Indirizzi IP di origine consentiti) - Gli intervalli IP consentiti su questa porta quando una richiesta viene approvata.
            - **Tempo massimo della richiesta** - È l'intervallo di tempo massimo durante il quale una porta specifica può essere aperta.

     1. Fare clic su **OK**.

1. Fare clic su **Salva**.

> [!NOTE]
>Quando JIT VM Access è abilitato per una macchina virtuale, Il Centro sicurezza di Azure crea regole "Nega tutto il traffico in ingresso" per le porte selezionate nei gruppi di sicurezza di rete associati e Firewall di Azure con esso. Se sono state create altre regole per le porte selezionate, le regole esistenti hanno la priorità sulle nuove regole "Nega tutto il traffico in ingresso". Se non sono presenti regole esistenti sulle porte selezionate, le nuove regole "Nega tutto il traffico in ingresso" hanno la massima priorità nei gruppi di sicurezza di rete e nel firewall di Azure.If there are no existing rules on the selected ports, then the new "deny all inbound traffic" rules take top priority in the Network Security Groups and Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>Richiedi l'accesso JIT tramite il Centro sicurezza

To request access to a VM via Security Center:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.

1. In **Macchina virtuale**fare clic sulle macchine virtuali per le quale si vuole richiedere l'accesso. In questo modo viene inserito un segno di spunta accanto alla macchina virtuale.

    - L'icona nella colonna **Dettagli connessione** indica se JIT è abilitato nel gruppo di sicurezza di rete o FW. Se è abilitato su entrambi, viene visualizzata solo l'icona Firewall.

    - La colonna **Dettagli connessione** fornisce le informazioni necessarie per connettere la macchina virtuale e le relative porte aperte.

      ![Richiedere l'accesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Fare clic su **Richiedi accesso**. Verrà visualizzata la finestra **Richiedi accesso.**

      ![Dettagli JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, gli indirizzi IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. Sarà possibile richiedere l'accesso solo alle porte configurate nel criterio just-in-time. Ogni porta ha un tempo massimo consentito, derivato dai criteri JIT.

1. Fare clic su **Apri porte**.

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione **Indirizzo IP personale** potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo di indirizzi IP completo dell'organizzazione.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Modificare un criterio di accesso JIT tramite il Centro sicurezza

È possibile modificare i criteri JIT esistenti di una macchina virtuale aggiungendo e configurando una nuova porta da proteggere per la macchina virtuale o modificando qualsiasi altra impostazione correlata a una porta già protetta.

Per modificare un criterio JIT esistente di una macchina virtuale:

1. Nella scheda **Configurata**, in **VM** selezionare una macchina virtuale a cui aggiungere una porta facendo clic sui tre puntini nella riga della macchina virtuale. 

1. Selezionare **Modifica**.

1. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata. 
  ![accesso jit alle vm](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Controllare l'attività di accesso JIT nel Centro sicurezza

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **Macchine virtuali**selezionare una macchina virtuale per cui visualizzare le informazioni facendo clic sui tre puntini all'interno della riga relativa alla macchina virtuale e scegliere Log **attività** dal menu. Viene **aperto il log attività.**

   ![Selezionare il log attività](./media/security-center-just-in-time/select-activity-log.png)

   **Log attività** fornisce una visualizzazione filtrata delle operazioni precedenti per la macchina virtuale, con data, ora e sottoscrizione.

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e fare clic su **Applica** per creare una ricerca e un registro.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Configurare l'accesso JIT dalla pagina di una macchina virtuale di AzureConfigure JIT access from an Azure VM's page<a name="jit-vm"></a>

Per comodità, è possibile connettersi a una macchina virtuale usando JIT direttamente dalla pagina della macchina virtuale nel Centro sicurezza.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configurare l'accesso JIT in una macchina virtuale tramite la pagina DELLA macchina virtuale di AzureConfigure JIT access on a VM via the Azure VM page

Per semplificare l'implementazione dell'accesso JIT nelle macchine virtuali, è possibile impostare una macchina virtuale per consentire solo l'accesso JIT direttamente dall'interno della macchina virtuale.

1. Dal [portale di Azure](https://ms.portal.azure.com)cercare e selezionare **Macchine virtuali.** 
2. Selezionare la macchina virtuale che si desidera limitare all'accesso just-in-time.
3. Nel menu, selezionare **Configurazione**.
4. In **Accesso Just-In-Time**selezionare **Abilita just-in-time**. 

In questo modo si abilita l'accesso JIT per la macchina virtuale con le impostazioni seguenti:

- Server Windows:
    - Porta RDP 3389
    - Tre ore di accesso massimo consentito
    - Indirizzi IP di origine consentiti è impostato su Qualsiasi
- Server Linux:
    - Porta SSH 22
    - Tre ore di accesso massimo consentito
    - Indirizzi IP di origine consentiti è impostato su Qualsiasi
     
Se in una macchina virtuale è già abilitato JIT, quando si passa alla pagina di configurazione, sarà possibile vedere che JIT è abilitato e si potrà usare il collegamento per aprire il criterio in Centro sicurezza di Azure per visualizzare e modificare le impostazioni.

![configurazione JIT nella macchina virtuale](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Richiedere l'accesso JIT a una macchina virtuale tramite la pagina di una macchina virtuale di AzureRequest JIT access to a VM via an Azure VM's page

Nel portale di Azure, quando si prova a connettersi a una macchina virtuale, Azure verifica se si dispone di un criterio di accesso JIT configurato su tale macchina virtuale.

- Se nella macchina virtuale sono configurati criteri JIT, è possibile fare clic su **Richiedi accesso** per concedere l'accesso in base ai criteri JIT impostati per la macchina virtuale. 

  >![jit richiesta](./media/security-center-just-in-time/jit-request.png)

  L'accesso viene richiesto con i seguenti parametri predefiniti:

  - **IP di origine**: 'Qualsiasi' (non può essere modificato)
  - **intervallo**di tempo : Tre ore (non può essere modificato) <!--Isn't this set in the policy-->
  - **numero di porta** Porta RDP 3389 per Windows / porta 22 per Linux (può essere modificata)

    > [!NOTE]
    > Dopo l'approvazione di una richiesta per una macchina virtuale protetta da Firewall di Azure, il Centro sicurezza fornisce all'utente i dettagli di connessione appropriati (il mapping delle porte dalla tabella DNAT) da usare per connettersi alla macchina virtuale.

- Se JIT non è configurato in una macchina virtuale, verrà richiesto di configurare un criterio JIT su di essa.

  ![prompt jit](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Configurare un criterio JIT in una macchina virtuale a livello di codiceConfigure a JIT policy on a VM programmatically<a name="jit-program"></a>

È possibile configurare e usare l'accesso JIT tramite le API REST e PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>JIT VM access via REST APIs

È possibile usare la funzionalità di accesso JIT alle macchine virtuali tramite l'API Centro sicurezza di Azure. Tramite questa API è possibile ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. Per informazioni sull'API REST Just-In-Time, vedere [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) (Criteri di accesso alla rete JIT).

### <a name="jit-vm-access-via-powershell"></a>JIT VM access via PowerShell

Per usare la soluzione di accesso JIT alle macchine virtuali tramite PowerShell, usare i cmdlet ufficiali di PowerShell di Centro sicurezza di Azure e in particolare `Set-AzJitNetworkAccessPolicy`.

L'esempio seguente imposta criteri di accesso JIT alla macchina virtuale per una macchina virtuale specifica e imposta quanto segue:

1.    Chiudere le porte 22 e 3389.

2.    Impostare un intervallo di tempo massimo di 3 ore per ognuna in modo che possano essere aperte in seguito all'approvazione di una richiesta.
3.    Consente all'utente che richiede l'accesso di controllare gli indirizzi IP di origine e consente all'utente di aprire una sessione riuscita in seguito all'approvazione di una richiesta di accesso JIT.

Eseguire i comandi seguenti in PowerShell per svolgere queste operazioni:

1.    Assegnare una variabile che contiene solo i criteri di accesso JIT alla macchina virtuale per una macchina virtuale:

        $JitPolicy : : id //sottoscrizioni/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" ports        protocollo ";\*        allowedSourceAddressPrefix (");\*        maxRequestAccessDuration, "PT3H", numero di telefono 3389;        protocollo ";\*        allowedSourceAddressPrefix (");\*        maxRequestAccessDuration, "PT3H" ) )

2.    Inserire i criteri di accesso JIT alla macchina virtuale in una matrice:
    
        $JitPolicyArr ($JitPolicy)

3.    Configurare i criteri di accesso JIT alla macchina virtuale nella macchina virtuale selezionata:
    
        Set-AzJitNetworkAccessPolicy -tipo "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGroupGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Richiedere l'accesso a una macchina virtuale tramite PowerShellRequest access to a VM via PowerShell

Nell'esempio seguente, è possibile visualizzare una richiesta di accesso JIT alla macchina virtuale per una macchina virtuale specifica in cui si richiede l'apertura della porta 22 per un indirizzo IP specifico e per un determinato periodo di tempo:

Eseguire questo comando in PowerShell:
1.    Configurare le proprietà della richiesta di accesso alla macchina virtuale

        $JitPolicyVm1: le porte di id/"/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME" porte      endTimeUtc "2018-09-17T17:00:00.3658798";      allowedSourceAddressPrefix,("IPV4ADDRESS") ))
2.    Inserire i parametri della richiesta di accesso alla macchina virtuale in una matrice:

        $JitPolicyArr ($JitPolicyVm1)
3.    Inviare la richiesta di accesso usando l'ID risorsa ottenuto al passaggio 1

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Per ulteriori informazioni, vedere la [documentazione del cmdlet PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Pulizia automatica delle regole JIT ridondanti 

Ogni volta che si aggiorna un criterio JIT, viene eseguito automaticamente uno strumento di pulizia per verificare la validità dell'intero set di regole. Lo strumento cerca le mancate corrispondenze tra le regole nel criterio e le regole nel gruppo di sicurezza di rete. Se lo strumento di pulizia rileva una mancata corrispondenza, determina la causa e, quando è sicuro farlo, rimuove le regole predefinite che non sono più necessarie. Il pulitore non elimina mai le regole che hai creato.

Esempi di scenari in cui l'addetto alle pulizie potrebbe rimuovere una regola incorporata:Examples scenarios when the cleaner might remove a built-in rule:

- Quando esistono due regole con definizioni identiche e una ha una priorità più alta rispetto all'altra (ovvero, la regola di priorità inferiore non verrà mai utilizzata)
- Quando la descrizione di una regola include il nome di una macchina virtuale che non corrisponde all'IP di destinazione nella regolaWhen a rule description includes the name of a VM which doesn't match the destination IP in the rule 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali in Centro sicurezza per controllare l'accesso alle macchine virtuali di Azure.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- Modulo Microsoft Learn Proteggere i server e le [macchine virtuali da attacchi di forza bruta e malware con](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/) il Centro sicurezza di Azure
- [Impostazione dei criteri](tutorial-security-policy.md) di sicurezza: informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.Setting security policies - Learn how to configure security policies for your Azure subscriptions and resource groups.
- [Gestione dei suggerimenti per la sicurezza:](security-center-recommendations.md) informazioni su come i consigli consentono di proteggere le risorse di Azure.Managing security recommendations - Learn how recommendations help you protect your Azure resources.
- [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
