---
title: Determinare le cause di non conformità
description: Quando una risorsa non è conforme, i motivi possibili sono molti. Informazioni sulle possibili cause di non conformità.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439834"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando si determina che una risorsa di Azure non è conforme a una regola dei criteri, è utile comprendere a quale parte della regola la risorsa non è conforme. È anche importante conoscere quale modifica ha trasformato una risorsa precedentemente conforme in una risorsa non conforme. È possibile trovare queste informazioni in due modi:

- [Dettagli di conformità](#compliance-details)
- [Cronologia modifiche (anteprima)](#change-history)

## <a name="compliance-details"></a>Dettagli di conformità

Quando una risorsa non è conforme, è possibile trovare i dettagli di conformità della risorsa nella pagina **Conformità dei criteri**. Il riquadro dei dettagli di conformità include le informazioni seguenti:

- Dettagli della risorsa, ad esempio nome, tipo, posizione e ID risorsa
- Stato di conformità e timestamp dell'ultima valutazione per l'assegnazione dei criteri corrente
- Elenco dei _motivi_ per cui la risorsa non è conforme

> [!IMPORTANT]
> Poiché i dettagli di conformità per una risorsa _Non conforme_ indicano il valore corrente delle proprietà di tale risorsa, è necessario che l'utente disponga dell'operazione di **lettura** per la proprietà **type** della risorsa. Se ad esempio la risorsa _Non conforme_ è **Microsoft.Compute/virtualMachines**, l'utente deve disporre dell'operazione **Microsoft.Compute/virtualMachines/read**. Se l'utente non dispone dell'operazione necessaria, viene visualizzato un errore di accesso.

Per visualizzare i dettagli di conformità, attenersi alla procedura seguente:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio il cui **stato di conformità** sia _Non conforme_.

1. Nella scheda **Conformità risorsa** della pagina **Conformità dei criteri** fare clic con il pulsante destro del mouse o selezionare i puntini di sospensione di una risorsa il cui **stato di conformità** sia _Non conforme_. Quindi selezionare **Visualizzare i dettagli sulla conformità**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Screenshot del collegamento &quot;Visualizza dettagli conformità&quot; nella scheda conformità risorse." border="false":::

1. Il riquadro **Dettagli conformità** visualizza le informazioni della valutazione più recente della risorsa rispetto all'assegnazione dei criteri corrente. In questo esempio il valore del campo **Microsoft.Sql/servers/version** è _12.0_ mentre la definizione del criterio prevedeva _14.0_. Se la risorsa non è conforme per più motivi, in questo riquadro vengono elencati tutti i motivi.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Screenshot del riquadro dettagli conformità e motivi per la mancata conformità il valore corrente è dodici e il valore di destinazione è quattordici." border="false":::

   Per una definizione dei criteri **auditIfNotExists** o **deployIfNotExists**, i dettagli includono la proprietà **details.type** e tutte le proprietà facoltative. Per un elenco, vedere [Proprietà di AuditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [Proprietà di DeployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Ultima risorsa valutata** collega a una risorsa correlata della sezione dei **dettagli** della definizione.

   Esempio parziale della definizione di **deployIfNotExists**:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Screenshot del riquadro dei dettagli di conformità per ifNotExists, incluso il conteggio delle risorse valutato." border="false":::

> [!NOTE]
> Per proteggere i dati, quando il valore di una proprietà è _segreto_, il valore corrente visualizza gli asterischi.

Questi dettagli spiegano il motivo per cui una risorsa non è attualmente conforme, ma non indicano quando è stata apportata alla risorsa la modifica che l'ha resa non conforme. Per questo tipo di informazioni, vedere la sezione [Cronologia modifiche (anteprima)](#change-history) più avanti.

### <a name="compliance-reasons"></a>Motivi di conformità

Nella matrice seguente viene eseguito il mapping di ogni possibile _motivo_ relativamente alla [condizione](../concepts/definition-structure.md#conditions) responsabile nella definizione dei criteri:

|Motivo | Condizione |
|-|-|
|Il valore corrente deve contenere il valore di destinazione come chiave. |containsKey o **not** notContainsKey |
|Il valore corrente deve contenere il valore di destinazione. |contains o **not** notContains |
|Il valore corrente deve essere uguale al valore di destinazione. |equals o **not** notEquals |
|Il valore corrente deve essere minore del valore di destinazione. |less o **not** greaterOrEquals |
|Il valore corrente deve essere maggiore o uguale al valore di destinazione. |greaterOrEquals o **not** less |
|Il valore corrente deve essere maggiore del valore di destinazione. |greater o **not** lessOrEquals |
|Il valore corrente deve essere minore o uguale al valore di destinazione. |lessOrEquals o **not** greater |
|Il valore corrente deve esistere. |esiste |
|Il valore corrente deve essere nel valore di destinazione. |in o **not** notIn |
|Il valore corrente deve essere uguale al valore di destinazione. |like o **not** notLike |
|Il valore corrente deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |match o **not** notMatch |
|Il valore corrente deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |matchInsensitively o **not** notMatchInsensitively |
|Il valore corrente non deve contenere il valore di destinazione come chiave. |notContainsKey o **not** containsKey|
|Il valore corrente non deve contenere il valore di destinazione. |notContains o **not** contains |
|Il valore corrente non deve essere uguale al valore di destinazione. |notEquals o **not** equals |
|Il valore corrente non deve esistere. |**not** exists  |
|Il valore corrente non deve essere nel valore di destinazione. |notIn o **not** in |
|Il valore corrente non deve essere uguale al valore di destinazione. |notLike o **not** like |
|Il valore corrente non deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |notMatch o **not** match |
|Il valore corrente non deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |notMatchInsensitively o **not** matchInsensitively |
|Non esistono risorse correlate corrispondenti ai dettagli dell'effetto nella definizione dei criteri. |Non esiste una risorsa del tipo definito in **then.details.type** e relativa alla risorsa definita nella parte **if** della regola dei criteri. |

## <a name="component-details-for-resource-provider-modes"></a>Dettagli del componente per le modalità del provider di risorse

Per le assegnazioni con una [modalità provider di risorse](../concepts/definition-structure.md#resource-manager-modes), selezionare la risorsa _non conforme_ per aprire una visualizzazione più approfondita. Nella scheda **conformità componenti** sono presenti informazioni aggiuntive specifiche per la modalità del provider di risorse nel criterio assegnato che mostra il **componente** e l' **ID componente** _non conformi_ .

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Screenshot della scheda conformità componenti e dei dettagli di conformità per un'assegnazione in modalità provider di risorse." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Dettagli di conformità per la configurazione guest

Per i criteri _auditIfNotExists_ nella categoria _configurazione Guest_ , è possibile che vengano valutate più impostazioni all'interno della macchina virtuale e che sia necessario visualizzare i dettagli per ogni impostazione. Ad esempio, se si esegue il controllo di un elenco di criteri password e solo per uno di essi lo stato è _Non conforme_, è necessario stabilire quali criteri password specifici non sono conformi e il motivo della non conformità.

Inoltre, potrebbe non essere possibile accedere direttamente alla macchina virtuale, ma è necessario segnalare il motivo per cui la macchina virtuale non è _conforme_.

### <a name="azure-portal"></a>Portale di Azure

Per iniziare, seguire la stessa procedura descritta nella sezione precedente per visualizzare i dettagli di conformità dei criteri.

Nella visualizzazione riquadro dettagli conformità selezionare il collegamento **ultima risorsa valutata**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Screenshot della visualizzazione dei dettagli di conformità della definizione auditIfNotExists." border="false":::

Nella pagina **Assegnazione guest** vengono visualizzati tutti i dettagli di conformità disponibili. Ogni riga della vista rappresenta una valutazione eseguita all'interno del computer. Nella colonna **Motivo** viene visualizzata una frase che descrive il motivo per cui l'assegnazione guest è _Non conforme_. Se ad esempio si controllano i criteri password, nella colonna **Motivo** verrà visualizzato il testo che include il valore corrente per ogni impostazione.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Screenshot dei dettagli di conformità dell'assegnazione Guest." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Cronologia modifiche (anteprima)

La nuova **anteprima pubblica** include gli ultimi 14 giorni di cronologia delle modifiche per tutte le risorse di Azure che supportano l'[eliminazione in modalità completa](../../../azure-resource-manager/templates/complete-mode-deletion.md). La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Il rilevamento delle modifiche viene attivato quando vengono aggiunte, rimosse o modificate le proprietà del Azure Resource Manager.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio con uno **stato di conformità** qualsiasi.

1. Nella scheda **Conformità risorsa** della pagina **Conformità dei criteri** selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Screenshot della scheda cronologia modifiche e rilevati orari di modifica nella pagina conformità risorse." border="false":::

1. Selezionare una delle modifiche rilevate. Verrà visualizzato il _diff visivo_ per la risorsa nella pagina **Cronologia modifiche**.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Screenshot delle differenze visive della cronologia delle modifiche dello stato before e After delle proprietà nella pagina della cronologia delle modifiche." border="false":::

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Le modifiche rilevate potrebbero non essere correlate allo stato di conformità corrente della risorsa.

I dati della cronologia delle modifiche vengono forniti da [Azure Resource Graph](../../resource-graph/overview.md). Per eseguire una query su queste informazioni all'esterno del portale di Azure, vedere [Ottenere le modifiche delle risorse](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
