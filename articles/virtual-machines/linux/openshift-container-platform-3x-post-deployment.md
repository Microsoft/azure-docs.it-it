---
title: OpenShift container Platform 3,11 nelle attività post-distribuzione di Azure
description: Attività aggiuntive per dopo la distribuzione di un cluster OpenShift container Platform 3,11.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd967ad08b628f9073edfe548033f7e97845d047
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735114"
---
# <a name="post-deployment-tasks"></a>Attività di post-distribuzione

Dopo aver distribuito un cluster OpenShift, è possibile configurare elementi aggiuntivi. Questo articolo riguarda:

- Come configurare l'accesso Single Sign-On usando Azure Active Directory (Azure AD)
- Come configurare i log di monitoraggio di Azure per monitorare OpenShift
- Come configurare le metriche e la registrazione
- Come installare Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurare Single Sign-On usando Azure Active Directory

Per usare Azure Active Directory per l'autenticazione, è necessario creare prima di tutto la registrazione di un'app Azure AD. Questo processo include due passaggi: la creazione della registrazione dell'app e la configurazione delle autorizzazioni.

### <a name="create-an-app-registration"></a>Creare la registrazione di un'app

Si userà l'interfaccia della riga di comando di Azure per creare la registrazione dell'app e l'interfaccia utente grafica (portale) per impostare le autorizzazioni. Per creare la registrazione dell'app, saranno necessarie le cinque informazioni seguenti:

- Nome visualizzato: nome di registrazione dell'app, ad esempio OCPAzureAD
- Home page: URL della console OpenShift (ad esempio, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- URI dell'identificatore: URL della console OpenShift (ad esempio, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- URL di risposta: URL pubblico master e nome della registrazione dell'app (ad esempio, `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Password: password sicura. Usare una password complessa

L'esempio seguente crea una registrazione dell'app usando le informazioni indicate sopra:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Se il comando riesce, viene restituito un output JSON simile al seguente:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Prendere nota della proprietà appId restituita dal comando per un passaggio successivo.

Nel portale di Azure:

1. Selezionare **Azure Active Directory**  >  **registrazione dell'app** .
2. Cercare la registrazione dell'app, ad esempio OCPAzureAD.
3. Nei risultati fare clic sulla registrazione dell'app.
4. Nel pannello **Impostazioni** selezionare **Autorizzazioni necessarie** .
5. In **Autorizzazioni necessarie** selezionare **Aggiungi** .

   ![Registrazione delle app](media/openshift-post-deployment/app-registration.png)

6. Fare clic su Passaggio 1: Seleziona API, su **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)** . Fare clic su **Seleziona** nella parte inferiore.

   ![Registrazione dell'app, Seleziona API](media/openshift-post-deployment/app-registration-select-api.png)

7. In Passaggio 2: Selezionare le autorizzazioni selezionare **Accedi e leggi il profilo di un altro utente** in **Autorizzazioni delegate** e quindi fare clic su **Seleziona** .

   ![Accesso alla registrazione dell'app](media/openshift-post-deployment/app-registration-access.png)

8. Selezionare **Operazione completata** .

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurare OpenShift per l'autenticazione di Azure AD

Per configurare OpenShift per l'uso di Azure AD come provider di autenticazione, il file /etc/origin/master/master-config.yaml deve essere modificato in tutti i nodi master.

È possibile trovare l'ID tenant usando il comando dell'interfaccia della riga di comando seguente:

```azurecli
az account show
```

Nel file YAML individuare le righe seguenti:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Inserire le righe seguenti immediatamente dopo le righe indicate sopra:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Verificare che il testo si allinei correttamente in identityProviders. È possibile trovare l'ID tenant usando il comando dell'interfaccia della riga di comando seguente: ```az account show```

Riavviare i servizi master OpenShift in tutti i nodi master:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

Nella console OpenShift verranno visualizzate due opzioni per l'autenticazione, htpasswd_auth e [Registrazione app].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorare OpenShift con i log di monitoraggio di Azure

Esistono tre modi per aggiungere l'agente di Log Analytics a OpenShift.
- Installare l'agente di Log Analytics per Linux direttamente in ogni nodo OpenShift
- Abilitare l'estensione della macchina virtuale di monitoraggio di Azure in ogni nodo OpenShift
- Installare l'agente di Log Analytics come set di daemon OpenShift

Leggere le [istruzioni](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) complete per altri dettagli.

## <a name="configure-metrics-and-logging"></a>Configurare le metriche e la registrazione

In base al ramo, i modelli di Azure Resource Manager per OpenShift Container Platform e OKD possono fornire i parametri di input per l'abilitazione di metriche e la registrazione come parte dell'installazione.

L'offerta OpenShift Container Platform Marketplace include anche un'opzione per abilitare le metriche e la registrazione durante l'installazione del cluster.

Se l'abilitazione delle metriche o della registrazione non è stata eseguita durante l'installazione del cluster, possono essere abilitate facilmente al termine dell'attività.

### <a name="azure-cloud-provider-in-use"></a>Provider di servizi cloud di Azure in uso

SSH al nodo bastion o al primo nodo master (in base al modello e al ramo in uso) usando le credenziali specificate durante la distribuzione. Immettere il comando seguente:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Provider di servizi cloud di Azure non in uso

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Installare Open Service Broker for Azure (OSBA)

Open Service Broker for Azure o OSBA consente di effettuare il provisioning dei servizi cloud di Azure direttamente da OpenShift. OSBA in un'implementazione Open Service Broker API per Azure. Open Service Broker API è una specifica che definisce un linguaggio comune per i provider di cloud utilizzabile dalle applicazioni native cloud per gestire i servizi cloud senza blocco.

Per installare OSBA su OpenShift, seguire le istruzioni disponibili qui: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Completare i passaggi nella sezione del modello di progetto OpenShift e non nell'intera sezione di installazione.

## <a name="next-steps"></a>Passaggi successivi

- [Getting started with OpenShift Container Platform](https://docs.openshift.com) (Introduzione a OpenShift Container Platform)
