---
title: Recapito di eventi, identità del servizio gestito e collegamento privato
description: Questo articolo descrive come abilitare l'identità del servizio gestito per un argomento di Griglia di eventi di Azure e per usarla per inviare eventi alle destinazioni supportate.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: ca154c252976911627184a63386cba1544ed21e0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054418"
---
# <a name="event-delivery-with-a-managed-identity"></a>Recapito di eventi con un'identità gestita
Questo articolo descrive come abilitare un' [identità del servizio gestito](../active-directory/managed-identities-azure-resources/overview.md) per gli argomenti o i domini personalizzati di griglia di eventi di Azure. Usare l'identità per inviare eventi a destinazioni supportate, ad esempio code e argomenti del bus di servizio, Hub eventi e account di archiviazione.

Di seguito sono elencati i passaggi illustrati nel dettaglio in questo articolo:
1. Creare un argomento o un dominio personalizzato con un'identità assegnata dal sistema oppure aggiornare un argomento o un dominio personalizzato esistente per abilitare l'identità. 
1. Aggiungere l'identità a un ruolo appropriato, ad esempio il mittente dei dati del bus di servizio, nella destinazione, ad esempio una coda del bus di servizio.
1. Quando si creano sottoscrizioni di eventi, abilitare l'utilizzo dell'identità per recapitare gli eventi alla destinazione. 

> [!NOTE]
> Attualmente non è possibile recapitare gli eventi usando [endpoint privati](../private-link/private-endpoint-overview.md). Per ulteriori informazioni, vedere la sezione [endpoint privati](#private-endpoints) alla fine di questo articolo. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Creare un argomento o un dominio personalizzato con un'identità
Per prima cosa si vedrà creare un argomento o un dominio con un'identità gestita dal sistema.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
È possibile abilitare l'identità assegnata dal sistema per un argomento o un dominio personalizzato durante la creazione nel portale di Azure. Nell'immagine seguente viene illustrato come abilitare un'identità gestita dal sistema per un argomento personalizzato. In pratica, selezionare l'opzione **Abilita identità assegnata dal sistema** nella pagina **Avanzate** della procedura guidata di creazione dell'argomento. Questa opzione verrà visualizzata nella pagina **Avanzate** della creazione guidata dominio. 

![Abilitare l'identità durante la creazione di un argomento personalizzato](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
È anche possibile usare l'interfaccia della riga di comando di Azure per creare un argomento o un dominio personalizzato con un'identità assegnata dal sistema. Usare il comando `az eventgrid topic create` con il parametro `--identity` impostato su `systemassigned`. Se non si specifica un valore per questo parametro, viene usato il valore predefinito `noidentity`. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Analogamente, è possibile usare il comando `az eventgrid domain create` per creare un dominio con un'identità gestita dal sistema.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Abilitare un'identità per un argomento o un dominio personalizzato esistente
Nella sezione precedente è stato illustrato come abilitare un'identità gestita dal sistema durante la creazione di un argomento personalizzato o di un dominio. In questa sezione viene illustrato come abilitare un'identità gestita dal sistema per un argomento o un dominio personalizzato esistente. 

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
La procedura seguente illustra come abilitare l'identità gestita dal sistema per un argomento personalizzato. I passaggi per l'abilitazione di un'identità per un dominio sono simili. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare gli **argomenti di griglia di eventi** nella barra di ricerca nella parte superiore.
3. Selezionare l' **argomento personalizzato** per il quale si desidera abilitare l'identità gestita. 
4. Passare alla scheda **Identità**. 
5. Attivare l'opzione per **abilitare l'identità** . 
1. Selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Pagina identità per un argomento personalizzato"::: 

È possibile utilizzare passaggi simili per abilitare un'identità per un dominio di griglia di eventi.

### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
Usare il `az eventgrid topic update` comando con `--identity` impostato su `systemassigned` per abilitare l'identità assegnata dal sistema per un argomento personalizzato esistente. Se si vuole disabilitare l'identità, specificare il valore `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Il comando per l'aggiornamento di un dominio esistente è simile (`az eventgrid domain update`).

## <a name="supported-destinations-and-azure-roles"></a>Destinazioni e ruoli di Azure supportati
Dopo aver abilitato l'identità per il dominio o l'argomento personalizzato di griglia di eventi, Azure crea automaticamente un'identità in Azure Active Directory. Aggiungere questa identità ai ruoli appropriati di Azure in modo che l'argomento o il dominio personalizzato possa inviare gli eventi alle destinazioni supportate. Ad esempio, aggiungere l'identità al ruolo di **mittente dei dati di hub eventi di Azure** per uno spazio dei nomi di hub eventi di Azure in modo che l'argomento personalizzato di griglia di eventi possa inviare eventi a hub eventi in tale spazio dei nomi. 

Griglia di eventi di Azure supporta attualmente gli argomenti personalizzati o i domini configurati con un'identità gestita assegnata dal sistema per l'invio di eventi alle destinazioni seguenti. Questa tabella fornisce anche i ruoli in cui deve trovarsi l'identità, in modo che l'argomento personalizzato possa trasmettere gli eventi.

| Destination | Ruolo di Azure | 
| ----------- | --------- | 
| Code e argomenti del bus di servizio | [Mittente dei dati del bus di servizio di Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Hub eventi di Azure | [Mittente dei dati di Hub eventi di Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Archiviazione BLOB di Azure | [Collaboratore ai dati del BLOB di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Archiviazione code di Azure |[Mittente dei messaggi sui dati della coda di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Aggiungere un'identità ai ruoli di Azure nelle destinazioni
Questa sezione descrive come aggiungere l'identità per un argomento o dominio personalizzato a un ruolo di Azure. 

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
È possibile utilizzare il portale di Azure per assegnare l'identità personalizzata o di dominio a un ruolo appropriato, in modo che l'argomento o il dominio personalizzato possano inviare eventi alla destinazione. 

L'esempio seguente aggiunge un'identità gestita per un argomento personalizzato di griglia di eventi denominato **msitesttopic** al ruolo di **mittente dei dati del bus di servizio di Azure** per uno spazio dei nomi del bus di servizio che contiene una risorsa della coda o dell'argomento. Quando si aggiunge al ruolo a livello di spazio dei nomi, l'argomento personalizzato di griglia di eventi può inviare gli eventi a tutte le entità all'interno dello spazio dei nomi. 

1. Passare allo **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com). 
1. Selezionare **controllo di accesso** nel riquadro sinistro. 
1. Nella sezione **Aggiungi un'assegnazione di ruolo** selezionare **Aggiungi**. 
1. Nella pagina **Aggiungi un'assegnazione di ruolo** seguire questa procedura:
    1. Selezionare il ruolo. In questo caso: **Mittente dei dati del bus di servizio di Azure**. 
    1. Selezionare l' **identità** per il dominio o l'argomento personalizzato di griglia di eventi. 
    1. Selezionare **Save (Salva** ) per salvare la configurazione.

Per aggiungere un'identità agli altri ruoli specificati nella tabella, sarà necessario seguire una procedura simile. 

### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
L'esempio in questa sezione illustra come usare l'interfaccia della riga di comando di Azure per aggiungere un'identità a un ruolo di Azure. I comandi di esempio sono per gli argomenti personalizzati di griglia di eventi. ma sono simili a quelli per i domini di Griglia di eventi. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Ottenere l'ID entità per l'identità di sistema dell'argomento personalizzato 
Per prima cosa, ottenere l'ID entità dell'identità gestita dal sistema dell'argomento personalizzato e assegnare l'identità ai ruoli appropriati.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Creare un'assegnazione di ruolo per Hub eventi in diversi ambiti 
L'esempio di interfaccia della riga di comando seguente illustra come aggiungere l'identità di un argomento personalizzato al ruolo di **mittente dei dati di hub eventi di Azure** a livello di spazio dei nomi o a livello di hub eventi. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento personalizzato può inviare gli eventi a tutti gli hub eventi in tale spazio dei nomi. Se si crea un'assegnazione di ruolo a livello di hub eventi, l'argomento personalizzato può inviare gli eventi solo a tale hub eventi specifico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Creare un'assegnazione di ruolo per un argomento del bus di servizio in vari ambiti 
L'esempio di interfaccia della riga di comando seguente illustra come aggiungere l'identità di un argomento personalizzato di griglia di eventi al ruolo di **mittente dei dati del bus di servizio di Azure** a livello di spazio dei nomi o di argomento del bus di servizio. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento di griglia di eventi può inviare eventi a tutte le entità (code o argomenti del bus di servizio) all'interno di tale spazio dei nomi. Se si crea un'assegnazione di ruolo a livello di coda o argomento del bus di servizio, l'argomento personalizzato di griglia di eventi può inviare eventi solo alla coda o all'argomento del bus di servizio specifico. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Creare sottoscrizioni di eventi che usano un'identità
Quando si dispone di un argomento personalizzato di griglia di eventi o di un dominio con un'identità gestita dal sistema e l'identità è stata aggiunta al ruolo appropriato nella destinazione, si è pronti per creare sottoscrizioni che utilizzano l'identità. 

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
Quando si crea una sottoscrizione di eventi, viene visualizzata un'opzione per abilitare l'utilizzo di un'identità assegnata dal sistema per un endpoint nella sezione **Dettagli endpoint** . 

![Abilitare l'identità durante la creazione di una sottoscrizione di eventi per una coda del bus di servizio](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

È anche possibile abilitare l'uso di un'identità assegnata dal sistema da usare per i messaggi non recapitabili nella scheda **funzionalità aggiuntive** . 

![Abilitare l'identità assegnata dal sistema per l'inserimento nella coda di messaggi non recapitabili](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Usare l'interfaccia della riga di comando di Azure-coda Service Bus 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a una coda del bus di servizio. L'identità deve essere un membro del ruolo **Mittente dei dati del bus di servizio di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
Per prima cosa, specificare i valori per le variabili seguenti da usare nel comando dell'interfaccia della riga di comando. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato sulla **coda del bus di servizio**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito e la ricezione di messaggi non recapitabili
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato sulla **coda del bus di servizio**. Specifica inoltre che l'identità gestita dal sistema deve essere utilizzata per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Usare l'interfaccia della riga di comando di Azure-Hub eventi 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a un hub eventi. L'identità deve essere un membro del ruolo **Mittente dei dati di Hub eventi di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato su **Hub eventi**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito + DeadLetter 
Questo comando di esempio crea una sottoscrizione di eventi per un argomento personalizzato di griglia di eventi con un tipo di endpoint impostato su **Hub eventi**. Specifica inoltre che l'identità gestita dal sistema deve essere utilizzata per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Usare l'interfaccia della riga di comando di Azure-coda archiviazione di Azure 
Questa sezione illustra come usare l'interfaccia della riga di comando di Azure per consentire l'uso di un'identità assegnata dal sistema per recapitare gli eventi a una coda di archiviazione di Azure. L'identità deve essere un membro di **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione.

#### <a name="define-variables"></a>Definire le variabili  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione di eventi usando un'identità gestita per il recapito + DeadLetter 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Endpoint privati
Attualmente non è possibile recapitare gli eventi usando [endpoint privati](../private-link/private-endpoint-overview.md). In altre condizioni, non è previsto alcun supporto se si dispone di requisiti di isolamento di rete rigorosi in cui il traffico degli eventi recapitati non deve lasciare lo spazio IP privato. 

Tuttavia, se i requisiti richiedono un modo sicuro per inviare eventi usando un canale crittografato e un'identità nota del mittente (in questo caso, griglia di eventi) usando lo spazio IP pubblico, è possibile recapitare gli eventi a hub eventi, bus di servizio o servizio di archiviazione di Azure usando un argomento personalizzato di griglia di eventi di Azure o un dominio con identità gestita dal sistema configurato come illustrato in Quindi, è possibile usare un collegamento privato configurato in funzioni di Azure o il webhook distribuito nella rete virtuale per eseguire il pull degli eventi. Vedere l'esempio: [connettersi agli endpoint privati con funzioni di Azure](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Con questa configurazione, il traffico passa attraverso l'IP pubblico/Internet da griglia di eventi a hub eventi, bus di servizio o archiviazione di Azure, ma il canale può essere crittografato e viene usata un'identità gestita di griglia di eventi. Se si configurano le funzioni di Azure o il webhook distribuito nella rete virtuale per l'uso di hub eventi, bus di servizio o archiviazione di Azure tramite un collegamento privato, la sezione del traffico sarà chiaramente in Azure.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle identità del servizio gestito, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 
