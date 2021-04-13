---
title: Regole di azione per gli avvisi di monitoraggio di Azure
description: Informazioni sulle regole di azione in monitoraggio di Azure e su come configurarle e gestirle.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: df71883d04106dd341af4571c13cc55f35a1ecc3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304818"
---
# <a name="action-rules-preview"></a>Regole di azione (anteprima)

Le regole di azione consentono di aggiungere o escludere i gruppi di azioni negli avvisi attivati. Una singola regola può coprire diversi ambiti di risorse di destinazione, ad esempio, qualsiasi avviso su una risorsa specifica, ad esempio una macchina virtuale specifica, o qualsiasi avviso generato per qualsiasi risorsa in una sottoscrizione. Facoltativamente, è possibile aggiungere diversi filtri per controllare quali avvisi sono coperti da una regola e definire una pianificazione, ad esempio per attivarli solo fuori dall'orario di ufficio o durante una finestra di manutenzione pianificata.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Perché e quando utilizzare le regole di azione?

### <a name="suppression-of-alerts"></a>Eliminazione di avvisi

Esistono molti scenari in cui è utile disattivare le notifiche generate dagli avvisi. Questi scenari variano dall'eliminazione durante una finestra di manutenzione pianificata per l'eliminazione durante le ore non lavorative. Ad esempio, il team responsabile di  **ContosoVM** desidera disattivare le notifiche di avviso per il prossimo weekend, perché **ContosoVM** è in fase di manutenzione pianificata.

Sebbene il team possa disabilitare manualmente ogni regola di avviso configurata in **ContosoVM** (e abilitarla di nuovo dopo la manutenzione), non si tratta di un processo semplice. Le regole di azione consentono di definire l'eliminazione degli avvisi su larga scala con la possibilità di configurare in modo flessibile il periodo di eliminazione. Nell'esempio precedente, il team può definire una regola di azione in **ContosoVM** che elimina tutte le notifiche di avviso per il fine settimana.

### <a name="actions-at-scale"></a>Azioni su larga scala

Sebbene le regole di avviso consentano di definire il gruppo di azioni che viene attivato quando viene generato l'avviso, i clienti hanno spesso un gruppo di azioni comune nell'ambito delle operazioni. Ad esempio, un team responsabile del gruppo di risorse **ContosoRG** definirà probabilmente lo stesso gruppo di azioni per tutte le regole di avviso definite in **ContosoRG**.

Le regole di azione consentono di semplificare questo processo. Definendo azioni su larga scala, un gruppo di azioni può essere attivato per qualsiasi avviso generato nell'ambito configurato. Nell'esempio precedente, il team può definire una regola di azione in **ContosoRG** che attiverà lo stesso gruppo di azione per tutti gli avvisi generati al suo interno.

> [!NOTE]
> Le regole di azione non si applicano agli avvisi di integrità del servizio di Azure.

## <a name="configuring-an-action-rule"></a>Configurazione di una regola di azione

### <a name="portal"></a>[Portale](#tab/portal)

È possibile accedere alla funzionalità selezionando **Gestisci azioni** dalla pagina di destinazione **avvisi** in monitoraggio di Azure. Selezionare quindi **regole di azione (anteprima)**. È possibile accedere alle regole selezionando **regole di azione (anteprima)** dal dashboard della pagina di destinazione per gli avvisi.

![Regole di azione dalla pagina di destinazione di monitoraggio di Azure](media/alerts-action-rules/action-rules-landing-page.png)

Selezionare **+ nuova regola di azione**.

![Screenshot mostra la pagina Gestisci azioni con il pulsante nuova regola azione evidenziato.](media/alerts-action-rules/action-rules-new-rule.png)

In alternativa, è possibile creare una regola di azione durante la configurazione di una regola di avviso.

![Screenshot mostra la pagina Crea regola con il pulsante Crea regola di azione evidenziato.](media/alerts-action-rules/action-rules-alert-rule.png)

A questo punto verrà visualizzata la pagina flusso per la creazione di regole di azione. Configurare gli elementi seguenti:

![Nuovo flusso di creazione della regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Ambito

Scegliere prima di tutto l'ambito (sottoscrizione di Azure, gruppo di risorse o risorsa di destinazione). È anche possibile selezionare una combinazione di ambiti all'interno di una singola sottoscrizione.

![Ambito della regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Criteri di filtro

Facoltativamente, è possibile definire i filtri in modo che la regola venga applicata a un subset specifico di avvisi o a eventi specifici per ogni avviso, ad esempio solo "attivato" o solo "risolto".

I filtri disponibili sono:

* **Gravità**  
Questa regola si applica solo agli avvisi con i livelli di gravità selezionati.  
Ad esempio, **Severity = "Sev1"** indica che la regola si applica solo agli avvisi con gravità Sev1.
* **Monitorare il servizio**  
Questa regola si applica solo agli avvisi provenienti dai servizi di monitoraggio selezionati.  
Ad esempio, **Monitor Service = "backup di Azure"** significa che la regola verrà applicata solo agli avvisi di backup (provenienti da backup di Azure).
* **Tipo di risorsa**  
Questa regola si applica solo agli avvisi relativi ai tipi di risorse selezionati.  
Ad esempio, **Resource Type = "Virtual Machines"** significa che la regola si applica solo agli avvisi sulle macchine virtuali.
* **ID regola di avviso**  
Questa regola si applica solo agli avvisi provenienti da una regola di avviso specifica. Il valore deve corrispondere all'ID Gestione risorse della regola di avviso.  
Ad esempio, la regola di **avviso ID = "/subscriptions/SubId1/resourceGroups/RG1/Providers/Microsoft.Insights/metricalerts/API-latency"** indica che questa regola verrà applicata solo agli avvisi provenienti dalla regola di avviso della metrica "latenza API".  
_Nota: è possibile ottenere l'ID corretto della regola di avviso elencando le regole di avviso dall'interfaccia della riga di comando oppure aprendo una regola di avviso specifica nel portale, facendo clic su "proprietà" e copiando il valore "ID risorsa"._
* **Condizione di monitoraggio**  
Questa regola si applica solo agli eventi di avviso con la condizione di monitoraggio specificata, ovvero **"attivato"** o **"risolto"**.
* **Descrizione**  
Questa regola si applica solo agli avvisi che contengono una stringa specifica nel campo Descrizione avviso. Il campo contiene la descrizione della regola di avviso.  
Ad esempio, la **Descrizione contiene "prod"** significa che la regola corrisponderà solo agli avvisi che contengono la stringa "prod" nella descrizione.
* **Contesto avviso (payload)**  
Questa regola si applica solo agli avvisi che contengono uno o più valori specifici nei campi del contesto dell'avviso.  
Ad esempio, il **contesto dell'avviso (payload) contiene "computer-01"** significa che la regola si applica solo agli avvisi il cui payload contiene la stringa "computer-01".

> [!NOTE]
> Ogni filtro può includere fino a cinque valori.  
> Ad esempio, un filtro sul servizio di monitoraggio può includere fino a cinque nomi di servizio di monitoraggio.




Se si impostano più filtri in una regola, tutti si applicano. Se ad esempio si imposta **Resource Type = "Virtual Machines"** e **Severity = "Sev0"**, la regola verrà applicata solo agli avvisi Sev0 sulle macchine virtuali.

![Filtri delle regole azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Eliminazione o configurazione del gruppo di azioni

Configurare quindi la regola di azione per il supporto dell'eliminazione avvisi o del gruppo di azioni. Non è possibile scegliere entrambi. La configurazione agisce su tutte le istanze di avviso che corrispondono all'ambito e ai filtri definiti in precedenza.

#### <a name="suppression"></a>Eliminazione degli avvisi

Se si seleziona l' **eliminazione**, configurare la durata per l'eliminazione di azioni e notifiche. Scegliere una delle seguenti opzioni:
* **Da Now (always)**: Disattiva tutte le notifiche a tempo indefinito.
* **All'ora pianificata**: disattiva le notifiche entro una durata limitata.
* **Con una ricorrenza**: disattiva le notifiche in base a una pianificazione ricorrente giornaliera, settimanale o mensile.

![Eliminazione regola azione](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Gruppo di azioni

Se si seleziona **gruppo di azioni** nell'interruttore, aggiungere un gruppo di azioni esistente o crearne uno nuovo.

> [!NOTE]
> È possibile associare un solo gruppo di azione a una regola di azione.

![Aggiungere o creare una nuova regola di azione selezionando il gruppo di azioni](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Dettagli regola azione

Infine, configurare i dettagli seguenti per la regola di azione:
* Nome
* Gruppo di risorse in cui viene salvato
* Descrizione

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile creare regole di azione con l'interfaccia della riga di comando di Azure usando il comando [AZ monitor Action-Rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .  Il `az monitor action-rule` riferimento è solo uno dei numerosi riferimenti dell'interfaccia della riga di comando di [Azure per monitoraggio di Azure](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Preparare l'ambiente

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

   Se si preferisce, è anche possibile usare Azure Cloud Shell per completare la procedura descritta in questo articolo.  Azure Cloud Shell è un ambiente di shell interattivo utilizzabile tramite il browser.  Per avviare Cloud Shell, usare uno di questi metodi:

   - Aprire Cloud Shell passando a [https://shell.azure.com](https://shell.azure.com)

   - Selezionare il pulsante **Cloud Shell** sulla barra dei menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com)

1. Accedere.

   Se si usa un'installazione locale dell'interfaccia della riga di comando, eseguire l'accesso con il comando [AZ login](/cli/azure/reference-index#az-login) .  Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

    ```azurecli
    az login
    ```

1. Installare l'estensione `alertsmanagement`

   `az monitor action-rule`Si tratta di un'estensione sperimentale dell'interfaccia della riga di comando di Azure core. Per altre informazioni sui riferimenti all'estensione, vedere [usare l'estensione con l'interfaccia](/cli/azure/azure-cli-extensions-overview?)della riga di comando

   ```azurecli
   az extension add --name alertsmanagement
   ```

   È previsto il seguente avviso.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Creare regole azione con l'interfaccia della riga di comando di Azure

Per informazioni sui parametri obbligatori e facoltativi, vedere il contenuto di riferimento dell'interfaccia della riga di comando di Azure per [AZ monitor Action-Rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .

Creare una regola di azione per eliminare le notifiche in un gruppo di risorse.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Creare una regola di azione per eliminare le notifiche per tutti gli avvisi di Sev4 su tutte le macchine virtuali all'interno della sottoscrizione ogni fine settimana.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: eliminazione di avvisi in base alla gravità

Contoso vuole escludere le notifiche per tutti gli avvisi Sev4 su tutte le macchine virtuali nella sottoscrizione **ContosoSub** ogni fine settimana.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoSub**
* Filtri
    * Gravità = **Sev4**
    * Tipo di risorsa = **macchine virtuali**
* Eliminazione con ricorrenza impostata su settimanale, **sabato** e **domenica** controllati

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: eliminazione di avvisi in base al contesto dell'avviso (payload)

Contoso desidera disattivare le notifiche per tutti gli avvisi del log generati per **computer-01** in **ContosoSub** per un periodo di tempo indefinito durante la manutenzione.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoSub**
* Filtri
    * Servizio di monitoraggio = **log Analytics**
    * Il contesto dell'avviso (payload) contiene **computer-01**
* Eliminazione impostata su **da ora (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: gruppo di azioni definito in un gruppo di risorse

Contoso ha definito [un avviso di metrica a livello di sottoscrizione](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Ma desidera definire le azioni che vengono attivate in modo specifico per gli avvisi generati dal gruppo di risorse **ContosoRG**.

**Soluzione:** Creare una regola di azione con:
* Ambito = **ContosoRG**
* Nessun filtro
* Gruppo di azioni impostato su **ContosoActionGroup**

> [!NOTE]
> *I gruppi di azioni definiti nelle regole di azione e nelle regole di avviso operano in modo indipendente, senza deduplicazione.* Nello scenario descritto in precedenza, se per la regola di avviso viene definito un gruppo di azioni, questo viene attivato insieme al gruppo di azioni definito nella regola di azione.

## <a name="managing-your-action-rules"></a>Gestione delle regole di azione

### <a name="portal"></a>[Portale](#tab/portal)

È possibile visualizzare e gestire le regole di azione dalla visualizzazione elenco:

![Visualizzazione elenco regole di azione](media/alerts-action-rules/action-rules-list-view.png)

Da qui è possibile abilitare, disabilitare o eliminare le regole di azione su larga scala selezionando la casella di controllo accanto. Quando si seleziona una regola di azione, viene visualizzata la pagina di configurazione. La pagina consente di aggiornare la definizione della regola di azione e di abilitarla o disabilitarla.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile visualizzare e gestire le regole di azione usando il comando [AZ monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) dall'interfaccia della riga di comando di Azure.

Prima di gestire le regole di azione con l'interfaccia della riga di comando di Azure, preparare l'ambiente seguendo le istruzioni fornite in [configurazione di una regola di azione](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Procedure consigliate

Gli avvisi del log creati con l'opzione [numero di risultati](./alerts-unified-log.md) generano una singola istanza di avviso utilizzando l'intero risultato della ricerca, che può estendersi su più computer. In questo scenario, se una regola di azione usa il filtro del **contesto dell'avviso (payload)** , agisce sull'istanza di avviso purché esista una corrispondenza. Nello scenario 2, descritto in precedenza, se i risultati della ricerca per l'avviso del log generato contengono sia **computer-01** che **computer-02**, viene eliminato l'intera notifica. Non viene generata alcuna notifica per **computer-02** .

![Il diagramma mostra le regole di azione e gli avvisi del log con una singola istanza di avviso evidenziata.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Per usare al meglio gli avvisi del log con le regole di azione, creare gli avvisi del log con l'opzione [misurazione metrica](./alerts-unified-log.md) . Le istanze di avviso separate vengono generate da questa opzione, in base al campo gruppo definito. Quindi, nello scenario 2 vengono generate istanze di avviso separate per **computer-01** e **computer-02**. A causa della regola di azione descritta nello scenario, viene eliminata solo la notifica per **computer-01** . La notifica per **computer-02** continua a essere attivata normalmente.

![Regole di azione e avvisi del log (numero di risultati)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Domande frequenti

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Durante la configurazione di una regola di azione, desidero visualizzare tutte le possibili regole di azione sovrapposte, in modo da evitare notifiche duplicate. È possibile eseguire questa operazione?

Dopo aver definito un ambito durante la configurazione di una regola di azione, è possibile visualizzare un elenco di regole di azione sovrapposte allo stesso ambito (se presente). Questa sovrapposizione può essere una delle seguenti opzioni:

* Una corrispondenza esatta: ad esempio, la regola di azione che si sta definendo e la regola azione sovrapposta si trovano nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di azione che si sta definendo si trova in una sottoscrizione e la regola azione sovrapposta si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di azione che si sta definendo si trova in un gruppo di risorse e la regola di azione sovrapposta si trova nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: ad esempio, la regola di azione che si sta definendo si trova in **VM1** e **VM2** e la regola di azione sovrapposta si trova in **VM2** e **VM3**.

![Screenshot mostra la pagina nuova regola azione con le regole di azione sovrapposte visualizzate nelle regole di azione definite nella stessa finestra ambito.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Durante la configurazione di una regola di avviso, è possibile verificare se sono già state definite regole di azione che potrebbero agire sulla regola di avviso che sto definendo?

Dopo aver definito la risorsa di destinazione per la regola di avviso, è possibile visualizzare l'elenco delle regole di azione che agiscono sullo stesso ambito, se presente, selezionando **Visualizza azioni configurate** nella sezione **azioni** . Questo elenco viene popolato in base agli scenari seguenti per l'ambito:

* Una corrispondenza esatta: ad esempio, la regola di avviso che si sta definendo e la regola di azione si trovano nella stessa sottoscrizione.
* Un subset: ad esempio, la regola di avviso che si sta definendo si trova in una sottoscrizione e la regola di azione si trova in un gruppo di risorse all'interno della sottoscrizione.
* Un superset: ad esempio, la regola di avviso che si sta definendo si trova in un gruppo di risorse e la regola di azione si trova nella sottoscrizione che contiene il gruppo di risorse.
* Un'intersezione: ad esempio, la regola di avviso che si sta definendo si trova in **VM1** e **VM2** e la regola di azione si trova in **VM2** e **VM3**.

![Regole di azione sovrapposte](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>È possibile visualizzare gli avvisi che sono stati eliminati da una regola di azione?

Nella [pagina elenco avvisi](./alerts-managing-alert-instances.md)è possibile scegliere una colonna aggiuntiva denominata **stato di eliminazione**. Se la notifica relativa a un'istanza di avviso è stata eliminata, questo stato verrà visualizzato nell'elenco.

![Istanze di avvisi eliminati](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se è presente una regola di azione con un gruppo di azioni e un'altra con eliminazione attiva nello stesso ambito, cosa accade?

L'eliminazione ha sempre la precedenza sullo stesso ambito.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Cosa accade se si dispone di una risorsa coperta da due regole di azione? Sono state ricevute una o due notifiche? Ad esempio, **VM2** nello scenario seguente:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Per ogni avviso in VM1 e VM3, il gruppo di azioni AG1 verrebbe attivato una sola volta. Per ogni avviso in **VM2**, il gruppo di azioni AG1 verrebbe attivato due volte, perché le regole di azione non deduplicano le azioni.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Cosa accade se una risorsa viene monitorata in due regole di azione separate e una viene chiamata per l'azione mentre un'altra per l'eliminazione? Ad esempio, **VM2** nello scenario seguente:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Per ogni avviso in VM1, il gruppo di azioni AG1 verrebbe attivato una sola volta. Le azioni e le notifiche per ogni avviso in VM2 e VM3 verranno eliminati.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Cosa accade se si dispone di una regola di avviso e di una regola di azione definita per la stessa risorsa che chiama gruppi di azioni diversi? Ad esempio, **VM1** nello scenario seguente:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Per ogni avviso in VM1, il gruppo di azioni AG1 verrebbe attivato una sola volta. Ogni volta che viene attivata la regola di avviso "rule1", attiverà anche AG2. I gruppi di azioni definiti nelle regole di azione e nelle regole di avviso operano in modo indipendente, senza deduplicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sugli avvisi in Azure](./alerts-overview.md)
