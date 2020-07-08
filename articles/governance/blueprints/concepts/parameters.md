---
title: Usare i parametri per creare progetti dinamici
description: Informazioni sui parametri statici e dinamici e su come usarli per creare progetti dinamici e protetti.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 831dd69f58130247518ee7465bc1059aed61b319
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970638"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Creazione di progetti dinamici tramite parametri

Un progetto completamente definito con vari elementi, ad esempio gruppi di risorse, modelli di Azure Resource Manager (modelli ARM), criteri o assegnazioni di ruolo, offre la creazione rapida e la creazione coerente di oggetti all'interno di Azure. Per abilitare l'uso flessibile di questi contenitori e schemi progettuali riutilizzabili, Azure Blueprint supporta i parametri. Il parametro offre flessibilità nella modifica delle proprietà sugli elementi distribuiti dal progetto, sia durante la definizione che durante l'assegnazione.

Un esempio semplice è l'artefatto gruppo di risorse. Quando si crea un gruppo di risorse, occorre fornire due valori obbligatori: nome e località. Quando si aggiunge un gruppo di risorse al progetto, se i parametri non esistono, è necessario definire il nome e il percorso per ogni uso del progetto. Questa ripetizione potrebbe portare alla creazione di artefatti nello stesso gruppo di risorse a ogni uso del progetto. Le risorse all'interno di tale gruppo potrebbero essere duplicate e provocare un conflitto.

> [!NOTE]
> Non è un problema se due diversi progetti diversi includono un gruppo di risorse con lo stesso nome.
> Se un gruppo di risorse incluso in un progetto già esiste, il progetto continua a creare gli artefatti correlati in tale gruppo di risorse. Questo potrebbe causare un conflitto, perché all'interno di una sottoscrizione non possono esistere due risorse con lo stesso nome e tipo di risorsa.

La soluzione a questo problema è rappresentata dai parametri. I progettisti di Azure consentono di definire il valore per ogni proprietà dell'artefatto durante l'assegnazione a una sottoscrizione. Questo parametro rende possibile riutilizzare un progetto che crea un gruppo di risorse e altre risorse all'interno di una singola sottoscrizione senza conflitti.

## <a name="blueprint-parameters"></a>Parametri di progetto

I parametri possono essere creati nel progetto stesso tramite l'API REST. Questi parametri sono diversi da quelli presenti in ciascuno degli elementi supportati. Quando nel progetto viene creato un parametro, questo può essere usato dagli artefatti in tale progetto. Un esempio può essere il prefisso per la denominazione del gruppo di risorse. L'artefatto può usare il parametro del progetto per crearne uno "principalmente dinamico". Poiché il parametro può essere definito anche durante l'assegnazione, questo modello consente una coerenza che può essere conforme alle regole di denominazione. Per la procedura, vedere [Impostazione di parametri statici - Parametro a livello di progetto](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Uso dei parametri secureString e secureObject

Sebbene un _elemento_ modello ARM supporti i parametri dei tipi **secureString** e **secureObject** , i progettisti di Azure richiedono che ognuno di essi sia connesso a un Azure Key Vault. Questa misura di sicurezza impedisce la rischiosa prassi di archiviare i segreti insieme al progetto e incoraggia l'impiego di modelli sicuri. I progettisti di Azure supportano questa misura di sicurezza, individuando l'inclusione di un parametro sicuro in un _elemento_del modello ARM. Il servizio richiede quindi, durante l'assegnazione, le seguenti proprietà di Key Vault per ogni parametro sicuro rilevato:

- ID risorsa dell'insieme di credenziali delle chiavi
- Nome del segreto dell'insieme di credenziali delle chiavi
- Versione del segreto dell'insieme di credenziali delle chiavi

Se l'assegnazione del progetto usa un' **identità gestita assegnata dal sistema**, la Key Vault a cui viene fatto riferimento _deve_ esistere nella stessa sottoscrizione a cui è assegnata la definizione del progetto.

Se l'assegnazione del progetto usa un' **identità gestita assegnata dall'utente**, è possibile che la Key Vault a cui si fa riferimento _sia_ presente in una sottoscrizione centralizzata. È necessario concedere all'identità gestita i diritti appropriati per la Key Vault prima dell'assegnazione del progetto.

> [!IMPORTANT]
> In entrambi i casi, il Key Vault deve disporre dell' **Abilitazione dell'accesso a Azure Resource Manager per la distribuzione dei modelli** configurata nella pagina **criteri di accesso** . Per istruzioni su come abilitare questa funzionalità, vedere [Key Vault - Abilitare la distribuzione di modelli](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Per altre informazioni sull'insieme di credenziali di Azure, vedere [Panoramica di Key Vault](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Tipi di parametro

### <a name="static-parameters"></a>Parametri statici

Un valore di parametro definito nella definizione di un progetto viene chiamato **parametro statico**, perché l'artefatto sarà distribuito con tale valore statico a ogni uso del progetto. Nell'esempio del gruppo di risorse, anche se questo non ha senso per il nome, potrebbe averlo per la località. Ogni assegnazione del progetto creerebbe infatti il gruppo di risorse, comunque sia denominato, nella stessa località. Questa flessibilità consente di essere selettivi nella definizione di ciò che è obbligatorio e di ciò che può essere modificato durante l'assegnazione.

#### <a name="setting-static-parameters-in-the-portal"></a>Impostazione dei parametri statici nel portale

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic su un progetto esistente e quindi fare clic su **modifica progetto** oppure su **+ Crea progetto** e compilare le informazioni nella scheda **nozioni di base** .

1. Fare clic su **Avanti: Artefatti** oppure fare clic sulla scheda **Artefatti**.

1. Gli artefatti aggiunti al progetto che hanno opzioni dei parametri mostrano l'indicazione **X di Y parametri popolati** nella colonna **Parametri**. Fare clic sulla riga dell'artefatto per modificarne i parametri.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Parametri del progetto in una definizione di progetto" border="false":::

1. La pagina **Modifica artefatto** mostra le opzioni del valore appropriate per l'artefatto selezionato. Ogni parametro dell'artefatto ha un titolo, una casella del valore e una casella di controllo. Deselezionare la casella di controllo per renderlo un **parametro statico**. Nell'esempio seguente, solo _Località_ è un **parametro statico** perché la casella è deselezionata, mentre è selezionata in _Nome gruppo di risorse_.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Parametri statici del progetto su un elemento del progetto" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Impostazione dei parametri statici dall'API REST

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourMG}`: sostituire con il nome del gruppo di gestione
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

##### <a name="blueprint-level-parameter"></a>Parametro a livello di progetto

Quando si crea un progetto tramite l'API REST, è possibile creare [parametri del progetto](#blueprint-parameters). A questo scopo, usare il formato del corpo e l'URI dell'API REST seguenti:

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Dopo aver creato un parametro a livello di progetto, è possibile usarlo sugli artefatti aggiunti al progetto.
L'esempio di API REST seguente crea un artefatto assegnazione di ruolo nel progetto e usa il parametro a livello di progetto.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

In questo esempio, la proprietà **principalIds** usa il parametro **owners** a livello di progetto specificando un valore `[parameters('owners')]`. L'impostazione di un parametro in un artefatto usando un parametro a livello di progetto è un altro esempio di **parametro statico**. Il parametro a livello di progetto non può essere impostato durante l'assegnazione del progetto e avrà lo stesso valore per ogni assegnazione.

##### <a name="artifact-level-parameter"></a>Parametro a livello di artefatto

La creazione di **parametri statici** su un artefatto è simile, ma accetta un valore diretto anziché usare la funzione `parameters()`. L'esempio seguente crea due parametri statici, **tagName** e **tagValue**. Il valore di ognuno viene fornito direttamente e non usa una chiamata di funzione.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parametri dinamici

L'opposto di un parametro statico è un **parametro dinamico**. Questo parametro non viene definito nel progetto, bensì durante ogni assegnazione del progetto. Nell'esempio del gruppo di risorse, l'uso di un **parametro dinamico** ha senso per il nome del gruppo di risorse. Questo fornisce un nome diverso per ogni assegnazione del progetto. Per un elenco delle funzioni del progetto, vedere le informazioni di riferimento sulle [funzioni del progetto](../reference/blueprint-functions.md) .

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Impostazione dei parametri dinamici nel portale

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare **Definizioni di progetto** nella pagina a sinistra.

1. Fare clic con il tasto destro del mouse sul progetto che si desidera assegnare. Selezionare **assegna progetto** oppure fare clic sul progetto che si vuole assegnare, quindi fare clic sul pulsante **assegna progetto** .

1. Nella pagina **assegna progetto** individuare la sezione **parametri artefatto** . Ogni artefatto con almeno un **parametro dinamico** mostra l'artefatto e le opzioni di configurazione. Immettere i valori richiesti per i parametri prima di assegnare il progetto. Nell'esempio seguente, _Nome_ è un **parametro dinamico** che deve essere definito per completare l'assegnazione del progetto.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Parametro dinamico progetto durante l'assegnazione del progetto" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Impostazione dei parametri dinamici dall'API REST

L'impostazione dei **parametri dinamici** durante l'assegnazione si esegue inserendo direttamente il valore. Anziché usare una funzione, ad esempio [Parameters ()](../reference/blueprint-functions.md#parameters), il valore specificato è una stringa appropriata. Gli artefatti per un gruppo di risorse sono definiti con un nome di modello e le proprietà **name** e **location**. Tutti gli altri parametri per l'artefatto incluso sono definiti in **Parameters** con una **\<name\>** coppia di chiavi di **valori** e. Se il progetto è configurato per un parametro dinamico non fornito durante l'assegnazione, l'assegnazione avrà esito negativo.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Request Body

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'elenco delle [funzioni di progetto](../reference/blueprint-functions.md).
- Informazioni sul [ciclo di vita del progetto](lifecycle.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).