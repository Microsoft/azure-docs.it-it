---
title: Usare le azioni di GitHub per eseguire aggiornamenti del codice in funzioni di Azure
description: Informazioni su come usare le azioni di GitHub per definire un flusso di lavoro per compilare e distribuire progetti di funzioni di Azure in GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.openlocfilehash: dedca6912fd9d9e7b6f5089d02de9e4020e4e0ef
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122333"
---
# <a name="continuous-delivery-by-using-github-action"></a>Recapito continuo tramite l'azione GitHub

Le [azioni di GitHub](https://github.com/features/actions) consentono di definire un flusso di lavoro per compilare e distribuire automaticamente il codice delle funzioni nell'app per le funzioni in Azure. 

Nelle azioni di GitHub un [flusso di lavoro](https://help.github.com/articles/about-github-actions#workflow) è un processo automatico definito nel repository GitHub. Questo processo spiega a GitHub come compilare e distribuire il progetto di app per le funzioni su GitHub. 

Un flusso di lavoro viene definito da un file YAML (. yml) nel `/.github/workflows/` percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro. 

Per un flusso di lavoro di funzioni di Azure, il file è costituito da tre sezioni: 

| Sezione | Attività |
| ------- | ----- |
| **autenticazione** | <ol><li>Definire un'entità servizio.</li><li>Scaricare il profilo di pubblicazione.</li><li>Creare un segreto GitHub.</li></ol>|
| **Build** | <ol><li>Configurare l'ambiente.</li><li>Compilare l'app per le funzioni.</li></ol> |
| **Distribuzione** | <ol><li>Distribuire l'app per le funzioni.</li></ol>|

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](/cli/azure/). È possibile eseguire questo comando usando [Azure cloud Shell](https://shell.azure.com) nel portale di Azure o selezionando il pulsante **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In questo esempio, sostituire i segnaposto nella risorsa con l'ID sottoscrizione, il gruppo di risorse e il nome dell'app per le funzioni. L'output è costituito dalle credenziali di assegnazione di ruolo che consentono l'accesso all'app per le funzioni. Copiare questo oggetto JSON, che è possibile usare per eseguire l'autenticazione da GitHub.

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. Questo è il motivo per cui l'ambito nell'esempio precedente è limitato all'app per le funzioni specifica e non all'intero gruppo di risorse.

## <a name="download-the-publishing-profile"></a>Scaricare il profilo di pubblicazione

Per scaricare il profilo di pubblicazione dell'app per le funzioni:

1. Selezionare la pagina **Panoramica** dell'app per le funzioni e quindi selezionare **Ottieni profilo di pubblicazione**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Scarica profilo di pubblicazione":::

1. Salvare e copiare il contenuto del file di impostazioni di pubblicazione.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

1. In [GitHub](https://github.com)passare al repository e selezionare **Impostazioni**  >  **segreti**  >  **Aggiungi un nuovo segreto**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Aggiungi segreto":::

1. Aggiungere un nuovo segreto.

   * Se si usa l'entità servizio creata usando l'interfaccia della riga di comando di Azure, usare `AZURE_CREDENTIALS` come **nome**. Incollare quindi l'output dell'oggetto JSON copiato per **valore**e selezionare **Aggiungi segreto**.
   * Se si usa un profilo di pubblicazione, usare `SCM_CREDENTIALS` come **nome**. Usare quindi il contenuto del file del profilo di pubblicazione per **valore**e selezionare **Aggiungi segreto**.

GitHub è ora in grado di eseguire l'autenticazione all'app per le funzioni in Azure.

## <a name="set-up-the-environment"></a>Configurare l'ambiente 

La configurazione dell'ambiente viene eseguita utilizzando un'azione di configurazione di pubblicazione specifica del linguaggio.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nell'esempio seguente viene illustrata la parte del flusso di lavoro che utilizza l' `actions/setup-node` azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrata la parte del flusso di lavoro che utilizza l' `actions/setup-python` azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

Nell'esempio seguente viene illustrata la parte del flusso di lavoro che utilizza l' `actions/setup-dotnet` azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata la parte del flusso di lavoro che utilizza l' `actions/setup-java` azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Compilare l'app per le funzioni

Questo dipende dalla lingua e dalle lingue supportate da funzioni di Azure. questa sezione deve essere la procedura standard di compilazione di ogni linguaggio.

Nell'esempio seguente viene illustrata la parte del flusso di lavoro che compila l'app per le funzioni, che è specifica del linguaggio:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Distribuire il codice della funzione

Per distribuire il codice in un'app per le funzioni, sarà necessario usare l' `Azure/functions-action` azione. Questa azione è costituita da due parametri:

|Parametro |Spiegazione  |
|---------|---------|
|**_Nome app_** | Obbligatorio Nome dell'app per le funzioni. |
|_**nome slot**_ | Opzionale Nome dello slot di [distribuzione](functions-deployment-slots.md) in cui si desidera eseguire la distribuzione. Lo slot deve essere già definito nell'app per le funzioni. |


Nell'esempio seguente viene utilizzata la versione 1 di `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare un file con estensione YAML completo del flusso di lavoro, vedere uno dei file nel [repository di esempi del flusso di lavoro azioni di GitHub di Azure](https://aka.ms/functions-actions-samples) `functionapp` con il nome. È possibile usare questi esempi come punto di partenza per il flusso di lavoro.

> [!div class="nextstepaction"]
> [Altre informazioni sulle azioni di GitHub](https://help.github.com/en/articles/about-github-actions)
