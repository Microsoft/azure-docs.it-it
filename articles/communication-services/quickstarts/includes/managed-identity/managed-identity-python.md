---
ms.openlocfilehash: 3fcfb364ebffdd3643e803922cbe4f3dd0d87935
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512081"
---
## <a name="setting-up"></a>Configurazione

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>Installare i pacchetti SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Creare un nuovo file
Aprire e salvare un nuovo file all'interno della cartella creata denominata , il codice verrà posizionato `managed-identity.py` all'interno di questo file.

### <a name="use-the-sdk-packages"></a>Usare i pacchetti SDK

Aggiungere le `import` istruzioni seguenti all'inizio del file per usare gli SDK installati.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Creare un valore DefaultAzureCredential

Verrà utilizzato [DefaultAzureCredential.](/python/api/azure-identity/azure.identity.defaultazurecredential) Questa credenziale è adatta per gli ambienti di produzione e sviluppo. Poiché verrà utilizzato in questa guida introduttiva, verrà creato nella parte superiore del file.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Creare un'identità ed emettere un token con identità gestite

A questo punto si aggiungerà il codice che usa le credenziali create per rilasciare un token di accesso VoIP. Questo codice verrà chiamato più avanti:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Inviare un SMS con identità gestite
Come altro esempio di uso delle identità gestite, si aggiungerà questo codice che usa le stesse credenziali per inviare un SMS:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Scrivere il codice principale

Con le funzioni create è ora possibile scrivere il codice principale che chiamerà le funzioni scritte in precedenza.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Il `managed-identity.py` file finale dovrebbe essere simile al seguente:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Eseguire il programma

Al termine, è possibile eseguire il file `python managed-identity.py` immettendo dalla directory del progetto. Se tutto è andato bene, si dovrebbe vedere qualcosa di simile al seguente.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
