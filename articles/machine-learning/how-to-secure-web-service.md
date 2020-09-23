---
title: Proteggere i servizi Web usando TLS
titleSuffix: Azure Machine Learning
description: Informazioni su come abilitare HTTPS per proteggere un servizio Web distribuito tramite Azure Machine Learning. Azure Machine Learning usa TLS versione 1,2 per proteggere i modelli distribuiti come servizi Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b497e0369ab31cc009c9524ffd63ff472013268e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886021"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Usare TLS per proteggere un servizio Web tramite Azure Machine Learning


Questo articolo illustra come proteggere un servizio Web distribuito tramite Azure Machine Learning.

Usare [https](https://en.wikipedia.org/wiki/HTTPS) per limitare l'accesso ai servizi Web e proteggere i dati che i client inviano. HTTPS consente di proteggere le comunicazioni tra un client e un servizio Web mediante la crittografia delle comunicazioni tra i due. La crittografia USA [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS è talvolta ancora indicato come *Secure Sockets Layer* (SSL), che era il predecessore di TLS.

> [!TIP]
> Il Azure Machine Learning SDK usa il termine "SSL" per le proprietà correlate alle comunicazioni sicure. Questo non significa che il servizio Web non usa *TLS*. SSL è semplicemente un termine riconosciuto in modo più comune.
>
> In particolare, i servizi Web distribuiti tramite Azure Machine Learning supportano solo TLS versione 1,1

TLS e SSL si basano entrambi sui *certificati digitali*, che contribuiscono alla crittografia e alla verifica dell'identità. Per ulteriori informazioni sul funzionamento dei certificati digitali, vedere l'argomento di Wikipedia [infrastruttura a chiave pubblica](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se non si usa HTTPS per il servizio Web, i dati inviati da e verso il servizio potrebbero essere visibili ad altri utenti su Internet.
>
> HTTPS consente inoltre al client di verificare l'autenticità del server a cui si connette. Questa funzionalità protegge i client [da attacchi man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Questo è il processo generale per proteggere un servizio Web:

1. Immettere un nome di dominio.

2. Ottenere un certificato digitale.

3. Distribuire o aggiornare il servizio Web con la funzionalità TLS abilitata.

4. Aggiornare il DNS in modo che punti al servizio Web.

> [!IMPORTANT]
> Se si esegue la distribuzione in Azure Kubernetes Service (AKS), è possibile acquistare un certificato personalizzato o usare un certificato fornito da Microsoft. Se si usa un certificato di Microsoft, non è necessario ottenere un nome di dominio o un certificato TLS/SSL. Per altre informazioni, vedere la sezione [abilitare TLS e distribuire](#enable) in questo articolo.

Le differenze tra le [destinazioni di distribuzione](how-to-deploy-and-where.md)sono minime.

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non si dispone già di un nome di dominio, acquistarne uno da un *registrar*. Il processo e il prezzo variano tra i registrar. Il registrar fornisce gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (FQDN) (ad esempio www \. contoso.com) all'indirizzo IP che ospita il servizio Web.

## <a name="get-a-tlsssl-certificate"></a>Ottenere un certificato TLS/SSL

Esistono diversi modi per ottenere un certificato TLS/SSL (certificato digitale). Il più comune consiste nell'acquistarne uno da un' *autorità di certificazione* (CA). Indipendentemente dalla posizione in cui si ottiene il certificato, sono necessari i file seguenti:

* Un **certificato**. Il certificato deve contenere la catena di certificati completa e deve essere "con codifica PEM".
* Una **chiave**. La chiave deve anche essere codificata con PEM.

Quando si richiede un certificato, è necessario fornire il nome di dominio completo dell'indirizzo che si prevede di utilizzare per il servizio Web (ad esempio, www \. contoso.com). Per verificare l'identità del servizio Web, l'indirizzo indicato nel certificato e l'indirizzo utilizzato dai client vengono confrontati. Se gli indirizzi non corrispondono, il client riceve un messaggio di errore.

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [openssl](https://www.openssl.org/) per modificare il formato.

> [!WARNING]
> Usare i certificati *autofirmati* solo per lo sviluppo. Non usarli negli ambienti di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per ulteriori informazioni, vedere la documentazione relativa alle librerie di rete utilizzate dall'applicazione client.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Abilitare TLS e distribuire

Per distribuire (o ridistribuire) il servizio con TLS abilitato, impostare il parametro *ssl_enabled* su "true" laddove applicabile. Impostare il parametro *ssl_certificate* sul valore del file del *certificato* . Impostare il *ssl_key* sul valore del file di *chiave* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Distribuire su AKS e Field-Programmable Gate Array (FPGA)

  > [!NOTE]
  > Le informazioni contenute in questa sezione si applicano anche quando si distribuisce un servizio Web sicuro per la finestra di progettazione. Se non si ha familiarità con l'uso di Python SDK, vedere [che cos'è l'SDK di Azure Machine Learning per Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

Quando si esegue la distribuzione in AKS, è possibile creare un nuovo cluster AKS o alleghirne uno esistente. Per altre informazioni sulla creazione o sul montaggio di un cluster, vedere [distribuire un modello in un cluster del servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  Se si crea un nuovo cluster, usare **[AksCompute. provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Se si connette un cluster esistente, usare **[AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Entrambi restituiscono un oggetto di configurazione con un metodo **enable_ssl** .

Il metodo **enable_ssl** può utilizzare un certificato fornito da Microsoft o da un certificato acquistato.

  * Quando si usa un certificato di Microsoft, è necessario usare il parametro *leaf_domain_label* . Questo parametro genera il nome DNS per il servizio. Ad esempio, il valore "contoso" crea un nome di dominio "Contoso \<six-random-characters> . \<azureregion> .. cloudapp.azure.com ", dove \<azureregion> è l'area che contiene il servizio. Facoltativamente, è possibile usare il parametro *overwrite_existing_domain* per sovrascrivere la *leaf_domain_label*esistente.

    Per distribuire (o ridistribuire) il servizio con TLS abilitato, impostare il parametro *ssl_enabled* su "true" laddove applicabile. Impostare il parametro *ssl_certificate* sul valore del file del *certificato* . Impostare il *ssl_key* sul valore del file di *chiave* .

    > [!IMPORTANT]
    > Quando si usa un certificato di Microsoft, non è necessario acquistare un certificato o un nome di dominio personalizzato.

    Nell'esempio seguente viene illustrato come creare una configurazione che Abilita un certificato TLS/SSL da Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Quando si usa *un certificato acquistato*, usare i parametri *ssl_cert_pem_file*, *ssl_key_pem_file*e *ssl_cname* . Nell'esempio seguente viene illustrato come usare i file con *estensione PEM* per creare una configurazione che usa un certificato TLS/SSL acquistato:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Per ulteriori informazioni su *enable_ssl*, vedere [AksProvisioningConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Eseguire la distribuzione in istanze di contenitore di Azure

Quando si esegue la distribuzione in istanze di contenitore di Azure, si forniscono valori per i parametri correlati a TLS, come illustrato nel frammento di codice seguente:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Per ulteriori informazioni, vedere [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aggiorna DNS

Successivamente, è necessario aggiornare il DNS in modo che punti al servizio Web.

+ **Per le istanze di contenitore:**

  Usare gli strumenti del registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

  È possibile che si verifichi un ritardo di minuti o ore prima che i client possano risolvere il nome di dominio, a seconda del registrar e della "durata (TTL) configurata per il nome di dominio.

+ **Per AKS:**

  > [!WARNING]
  > Se è stato usato *leaf_domain_label* per creare il servizio usando un certificato di Microsoft, non aggiornare manualmente il valore DNS per il cluster. Il valore deve essere impostato automaticamente.

  Aggiornare il DNS dell'indirizzo IP pubblico del cluster AKS nella scheda **configurazione** in **Impostazioni** nel riquadro sinistro. (Vedere l'immagine seguente). L'indirizzo IP pubblico è un tipo di risorsa creato nel gruppo di risorse che contiene i nodi dell'agente AKS e altre risorse di rete.

  [![Azure Machine Learning: protezione dei servizi Web con TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Aggiornare il certificato TLS/SSL

I certificati TLS/SSL scadono e devono essere rinnovati. Questa situazione si verifica in genere ogni anno. Usare le informazioni nelle sezioni riportate di seguito per aggiornare e rinnovare il certificato per i modelli distribuiti nel servizio Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aggiornare un certificato generato da Microsoft

Se il certificato è stato originariamente generato da Microsoft (quando si usa il *leaf_domain_label* per creare il servizio), usare uno degli esempi seguenti per aggiornare il certificato:

> [!IMPORTANT]
> * Se il certificato esistente è ancora valido, usare `renew=True` (SDK) o `--ssl-renew` (CLI) per forzare la configurazione a rinnovarla. Ad esempio, se il certificato esistente è ancora valido per 10 giorni e non si usa `renew=True` , il certificato potrebbe non essere rinnovato.
> * Quando il servizio è stato distribuito in origine, `leaf_domain_label` viene usato per creare un nome DNS usando il modello `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Per mantenere il nome esistente (incluse le 6 cifre generate originariamente), usare il `leaf_domain_label` valore originale. Non includere le 6 cifre generate.

**Usare l'SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Usare l'interfaccia della riga di comando**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Per ulteriori informazioni, vedere la documentazione di riferimento seguente:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

### <a name="update-custom-certificate"></a>Aggiorna certificato personalizzato

Se il certificato è stato originariamente generato da un'autorità di certificazione, attenersi alla procedura seguente:

1. Utilizzare la documentazione fornita dall'autorità di certificazione per rinnovare il certificato. Questo processo crea nuovi file di certificato.

1. Usare l'SDK o l'interfaccia della riga di comando per aggiornare il servizio con il nuovo certificato:

    **Usare l'SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Usare l'interfaccia della riga di comando**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Per ulteriori informazioni, vedere la documentazione di riferimento seguente:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

## <a name="disable-tls"></a>Disabilitare TLS

Per disabilitare TLS per un modello distribuito nel servizio Azure Kubernetes, creare un `SslConfiguration` con `status="Disabled"` , quindi eseguire un aggiornamento:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Passaggi successivi
Viene illustrato come:
+ [Utilizzare un modello di apprendimento automatico distribuito come servizio Web](how-to-consume-web-service.md)
+ [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)
