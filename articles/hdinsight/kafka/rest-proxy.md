---
title: Proxy REST Apache Kafka - Azure HDInsight
description: Informazioni su come eseguire operazioni di Apache Kafka usando un proxy REST Kafka in Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: bc3cbe5d0d7cf5e5a78112ae5df63ebb88a97f5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864840"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagire con cluster di Apache Kafka in Azure HDInsight usando un proxy REST

Il proxy REST Kafka consente di interagire con il cluster Kafka tramite un'API REST su HTTPS. Questa azione indica che i client Kafka possono trovarsi all'esterno della rete virtuale. I client possono eseguire chiamate HTTPS semplici e sicure al cluster Kafka, invece di basarsi sulle librerie Kafka. Questo articolo illustra come creare un cluster Kafka abilitato per il proxy REST. Fornisce inoltre un esempio di codice che illustra come effettuare chiamate al proxy REST.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Per le operazioni supportate dall'API REST Kafka, vedere le [informazioni di riferimento sull'API del proxy REST Kafka in HDInsight](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Background

:::image type="content" source="./media/rest-proxy/rest-proxy-architecture.png" alt-text="Struttura del proxy REST Kafka" border="false":::

Per la specifica completa delle operazioni supportate dall'API, vedere [API del proxy REST Apache Kafka](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Endpoint proxy REST

Quando si crea un cluster Kafka HDInsight con il proxy REST, viene creato un nuovo endpoint pubblico per il cluster, che è disponibile nelle **proprietà** del cluster HDInsight nel portale di Azure.

### <a name="security"></a>Sicurezza

L'accesso al proxy REST Kafka viene gestito con i gruppi di sicurezza di Azure Active Directory. Durante la creazione del cluster Kafka, fornire il gruppo di sicurezza di Azure AD con accesso all'endpoint REST. I client Kafka che devono accedere al proxy REST devono essere registrati in questo gruppo dal proprietario del gruppo. Il proprietario del gruppo può eseguire la registrazione tramite il portale o PowerShell.

Per le richieste di endpoint proxy REST, le applicazioni client devono ottenere un token OAuth. Il token viene usato per verificare l'appartenenza ai gruppi di sicurezza. Di seguito viene fornito un [esempio di applicazione client](#client-application-sample) che mostra come ottenere un token OAuth. L'applicazione client passa il token OAuth nella richiesta HTTPS al proxy REST.

> [!NOTE]
> Per altre informazioni sui gruppi di sicurezza di AAD, vedere [Gestire le app e l'accesso alle risorse tramite i gruppi di Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Per altre informazioni sul funzionamento dei token OAuth, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Proxy REST Kafka con gruppi di sicurezza di rete
Se si porta la propria rete virtuale e si controlla il traffico di rete con gruppi di sicurezza di rete, consentire il traffico **in ingresso** sulla porta **9400** oltre alla porta 443. In questo modo si garantisce che il server proxy REST Kafka sia raggiungibile.

## <a name="prerequisites"></a>Prerequisiti

1. Registrare un'applicazione con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto di questa applicazione per l'autenticazione con Azure.

1. Creare un gruppo di sicurezza di Azure AD. Aggiungere l'applicazione registrata con Azure AD al gruppo di sicurezza come **membro** del gruppo. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verificare che il gruppo sia di tipo **Sicurezza**.
    :::image type="content" source="./media/rest-proxy/rest-proxy-group.png" alt-text="Gruppo di sicurezza" border="true":::

    Verificare che l'applicazione sia membro del gruppo.
    :::image type="content" source="./media/rest-proxy/rest-proxy-membergroup.png" alt-text="Verificare l'appartenenza" border="true":::

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Creare un cluster Kafka con proxy REST abilitato

Nella procedura seguente si usa il portale di Azure. Per un esempio in cui viene usata l'interfaccia della riga di comando di Azure, vedere [Creare il cluster del proxy REST Apache Kafka con l'interfaccia della riga di comando di Azure](tutorial-cli-rest-proxy.md).

1. Durante il flusso di lavoro di creazione del cluster Kafka nella scheda **Sicurezza + rete** selezionare l'opzione **Abilitare il proxy REST Kafka**.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png" alt-text="Screenshot mostra la pagina Crea cluster H D Insight con sicurezza e rete selezionata." border="true":::

1. Fare clic su **Seleziona il gruppo di sicurezza**. Nell'elenco dei gruppi di sicurezza selezionare il gruppo di sicurezza che deve accedere al proxy REST. È possibile usare la casella di ricerca per trovare il gruppo di sicurezza appropriato. Fare clic sul pulsante **Seleziona** in basso.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png" alt-text="Screenshot mostra la pagina creare un cluster H D Insight con l'opzione per selezionare un gruppo di sicurezza." border="true":::

1. Completare i passaggi rimanenti per creare il cluster come descritto in [Creare il cluster Apache Kafka in Azure HDInsight con il portale di Azure](./apache-kafka-get-started.md).

1. Dopo aver creato il cluster, passare alle proprietà del cluster per prendere nota dell'URL del proxy REST Kafka.

     :::image type="content" source="./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png" alt-text="Visualizzare l'URL del proxy REST" border="true":::

## <a name="client-application-sample"></a>Esempio di applicazione client

È possibile usare il codice Python seguente per interagire con il proxy REST nel cluster Kafka. Per eseguire il codice di esempio, seguire questa procedura:

1. Salvare il codice di esempio in un computer in cui è installato Python.
1. Installare le dipendenze di Python obbligatorie eseguendo `pip3 install msal`.
1. Modificare la sezione di codice **Configure these properties** e aggiornare le proprietà seguenti per l'ambiente:

    |Proprietà |Descrizione |
    |---|---|
    |tenant_id|Tenant di Azure in cui si trova la sottoscrizione.|
    |client_id|ID dell'applicazione registrata nel gruppo di sicurezza.|
    |client_secret|Segreto per l'applicazione registrata nel gruppo di sicurezza.|
    |kafkarest_endpoint|Questo valore è indicato nella scheda **Proprietà** della panoramica del cluster, come descritto nella [sezione relativa alla distribuzione](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve essere specificato nel formato seguente: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Dalla riga di comando eseguire il file Python con il comando `sudo python3 <filename.py>`

Il codice esegue le azioni seguenti:

1. Recupera un token OAuth da Azure AD.
1. Mostra come effettuare una richiesta al proxy REST Kafka.

Per altre informazioni su come ottenere i token OAuth in Python, vedere [Classe AuthenticationContext di Python](/python/api/adal/adal.authentication_context.authenticationcontext). È possibile notare un ritardo nella visualizzazione degli elementi `topics` non creati o eliminati tramite il proxy REST Kafka. Questo ritardo è dovuto all'aggiornamento della cache. Il campo del **valore** dell'API Producer è stato migliorato. A questo punto, accetta oggetti JSON e qualsiasi forma serializzata.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Ecco un altro esempio su come ottenere un token da Azure per il proxy REST usando un comando curl. **Tenere presente che è necessario specificare `scope=https://hib.azurehdinsight.net/.default` durante il recupero di un token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento dell'API del proxy REST Kafka](/rest/api/hdinsight-kafka-rest-proxy/)
