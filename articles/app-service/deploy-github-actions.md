---
title: Configurare CI/CD con azioni di GitHub
description: Informazioni su come distribuire il codice nel servizio app Azure da una pipeline CI/CD con azioni di GitHub. Personalizzare le attività di compilazione ed eseguire distribuzioni complesse.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084992"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Eseguire la distribuzione nel servizio app usando le azioni di GitHub

[Azioni di GitHub](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con le azioni di servizio app Azure per GitHub, è possibile automatizzare il flusso di lavoro per la distribuzione in [app Azure servizio](overview.md) usando le azioni di GitHub.

> [!IMPORTANT]
> Le azioni di GitHub sono attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando il proprio account github.
> 

Un flusso di lavoro viene definito da un file YAML (. yml) `/.github/workflows/` nel percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del servizio app Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. definire un'entità servizio <br /> 2. creare un segreto GitHub |
|**Build** | 1. configurare l'ambiente <br /> 2. compilare l'app Web |
|**Distribuzione** | 1. distribuire l'app Web |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [AZ ad SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

In questo esempio, sostituire i segnaposto nella risorsa con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è costituito dalle credenziali di assegnazione di ruolo che consentono l'accesso all'app del servizio app. Copiare questo oggetto JSON, che è possibile usare per eseguire l'autenticazione da GitHub.

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. Questo è il motivo per cui l'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

È anche possibile usare le credenziali a livello di app, ad esempio il profilo di pubblicazione per la distribuzione. Seguire i passaggi per configurare il segreto:

1. Scaricare il profilo di pubblicazione per l'app del servizio app dal portale usando l'opzione **Ottieni profilo di pubblicazione** .

2. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**

    ![chiavi private](media/app-service-github-actions/secrets.png)

3. Incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto.

4. A questo punto, `.github/workflows/workflow.yml` nel file del flusso di lavoro nel ramo sostituire il segreto `publish-profile` per l'input dell'azione Distribuisci app Web di Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Il segreto viene visualizzato come illustrato di seguito una volta definito.

    ![chiavi private](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Configurare l'ambiente

La configurazione dell'ambiente può essere eseguita utilizzando una delle azioni di installazione.

|**Lingua**  |**Azione di installazione**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Negli esempi seguenti viene illustrata la parte del flusso di lavoro che configura l'ambiente per le varie lingue supportate:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Compilare l'app Web

Ciò dipende dalla lingua e dalle lingue supportate dal servizio app Azure, questa sezione deve essere la procedura di compilazione standard di ogni lingua.

Gli esempi seguenti illustrano la parte del flusso di lavoro che compila l'app Web, nei vari linguaggi supportati.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Distribuire nel servizio app

Per distribuire il codice in un'app del servizio app, usare `azure/webapps-deploy@v2` l'azione. Questa azione ha quattro parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **Nome app** | Necessaria Nome dell'app del servizio app | 
| **pubblica-profilo** | Opzionale Pubblicare il contenuto del file di profilo con Distribuzione Web Secrets |
| **package** | Opzionale Percorso del pacchetto o della cartella. *. zip, *. War, *. jar o una cartella da distribuire |
| **nome slot** | Opzionale Immettere uno slot esistente diverso dallo slot di produzione |

### <a name="deploy-using-publish-profile"></a>Eseguire la distribuzione usando il profilo di pubblicazione

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app node. js in Azure usando il profilo di pubblicazione.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Eseguire la distribuzione usando l'entità servizio di Azure

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app node. js in Azure usando un'entità servizio di Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare il set di azioni raggruppate in repository diversi su GitHub, ognuno dei quali contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Flusso di lavoro azioni da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp per i contenitori](https://github.com/Azure/webapps-container-deploy)

- [Accesso/disconnessione di Docker](https://github.com/Azure/docker-login)

- [Eventi che attivano flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione di K8s](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro Starter](https://github.com/actions/starter-workflows)
