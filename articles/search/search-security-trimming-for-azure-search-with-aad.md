---
title: Filtri di sicurezza per tagliare i risultati utilizzando Active Directory
titleSuffix: Azure Cognitive Search
description: Controllo dell'accesso al contenuto di Ricerca cognitiva di Azure usando i filtri di sicurezza e le identità di Azure Active Directory (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794311"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtri di sicurezza per la limitazione dei risultati di Ricerca cognitiva di Azure usando le identità di Active DirectorySecurity filters for trimming Azure Cognitive Search results using Active Directory identities

Questo articolo illustra come usare le identità di sicurezza di Azure Active Directory (AAD) con i filtri in Ricerca cognitiva di Azure per tagliare i risultati della ricerca in base all'appartenenza al gruppo di utenti.

Questo articolo illustra le attività seguenti:
> [!div class="checklist"]
> - Creare gruppi e utenti AAD
> - Associare l'utente al gruppo creato
> - Memorizzare i nuovi gruppi nella cache
> - Indicizzare i documenti con i gruppi associati
> - Emettere una richiesta di ricerca con il filtro degli identificatori di gruppo
> 
> [!NOTE]
> I frammenti di codice di esempio in questo articolo sono scritti in C#. Il codice sorgente completo è disponibile [in GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Prerequisiti

L'indice in Ricerca cognitiva di Azure deve avere un campo di [sicurezza](search-security-trimming-for-azure-search.md) per archiviare l'elenco delle identità di gruppo con accesso in lettura al documento. Questo caso d'uso presuppone una corrispondenza uno a uno tra un elemento a protezione diretta (come la domanda di iscrizione all'università di un candidato) e un campo di sicurezza che specifica chi ha accesso a tale elemento (ad esempio, il personale dell'ufficio ammissioni).

È necessario disporre delle autorizzazioni di amministratore AAD, indispensabili in questa procedura dettagliata per la creazione di utenti, gruppi e associazioni in AAD.

Anche l'applicazione in uso deve essere registrata con AAD, come descritto nella procedura seguente.

### <a name="register-your-application-with-aad"></a>Registrare l'applicazione con AAD

Questo passaggio integra l'applicazione in uso con AAD allo scopo di accettare gli accessi di account di gruppi e utenti. Se non si è un amministratore AAD all'interno dell'organizzazione, potrebbe essere necessario [creare un nuovo tenant](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) per eseguire la procedura seguente.

1. Passare > all'app [**Portale**](https://apps.dev.microsoft.com) >  di registrazione applicazioni**convergente****Aggiungere un'app**.
2. Immettere un nome per l'applicazione e quindi fare clic su **Crea**. 
3. Selezionare l'applicazione appena registrata nella pagina Applicazioni personali.
4. Nella pagina di registrazione dell'applicazione > **Piattaforma** > **Aggiungi piattaforma**scegliere API **Web**.
5. Sempre nella pagina di registrazione dell'applicazione, passare a > **Microsoft Graph Permissions** > **Add**.
6. In Selezionare le autorizzazioni aggiungere le autorizzazioni delegate seguenti e quindi fare clic su **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph fornisce un'API che consente l'accesso a livello di programmazione ad AAD tramite un'API REST. Il codice di esempio per questa procedura dettagliata usa le autorizzazioni per chiamare l'API Microsoft Graph per la creazione di gruppi, utenti e associazioni. Le API vengono usate anche per memorizzare nella cache gli identificatori di gruppo per un filtraggio più rapido.

## <a name="create-users-and-groups"></a>Creare utenti e gruppi

Se si aggiunge una ricerca a un'applicazione stabilita, in AAD potrebbero esserci già identificatori di gruppi e utenti. In questo caso è possibile ignorare i tre passaggi successivi. 

Invece, se non ci sono utenti esistenti, è possibile usare le API Microsoft Graph per creare le entità di sicurezza. I frammenti di codice seguenti illustrano come generare identificatori che diventano valori di dati per il campo di sicurezza nell'indice di Ricerca cognitiva di Azure.The following code snippets demonstrate how to generate identifiers, which become data values for the security field in your Azure Cognitive Search index. Nell'ipotetica domanda di iscrizione all'università si tratterebbe degli identificatori di sicurezza per il personale dell'ufficio ammissioni.

L'appartenenza di utenti e gruppi potrebbe essere molto fluida, soprattutto nelle organizzazioni di grandi dimensioni. Il codice per la creazione delle identità di utenti e gruppi dovrebbe essere eseguito abbastanza spesso per individuare i cambiamenti intervenuti nell'appartenenza dell'organizzazione. Analogamente, l'indice di Ricerca cognitiva di Azure richiede una pianificazione di aggiornamento simile per riflettere lo stato corrente di utenti e risorse consentiti.

### <a name="step-1-create-aad-group"></a>Passaggio 1: creare il [gruppo AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Passaggio 2: creare l'[utente AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Passaggio 3: associare utente e gruppo
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Passaggio 4: memorizzare nella cache gli identificatori di gruppo
Facoltativamente, per ridurre la latenza di rete, è possibile memorizzare nella cache le associazioni utente-gruppo in modo che quando viene emessa una richiesta di ricerca, i gruppi vengono restituiti dalla cache, senza generare un round trip in AAD. È possibile usare [l'API Batch AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) per inviare una singola richiesta Http con più utenti e creare la cache.

Microsoft Graph è progettato per gestire un volume elevato di richieste. Se si verifica un numero eccessivo di richieste, la richiesta con codice di stato HTTP 429 non verrà eseguita. Per altre informazioni, vedere [Microsoft Graph throttling](https://developer.microsoft.com/graph/docs/concepts/throttling) (Limitazione delle richieste di Microsoft Graph).

## <a name="index-document-with-their-permitted-groups"></a>Indicizzare un documento con i relativi gruppi consentiti

Le operazioni di query in Ricerca cognitiva di Azure vengono eseguite su un indice di Ricerca cognitiva di Azure.Query operations in Azure Cognitive Search are executed over an Azure Cognitive Search index. In questo passaggio un'operazione di indicizzazione importa i dati ricercabili in un indice, includendo gli identificatori usati come filtri di sicurezza. 

Ricerca cognitiva di Azure non autentica le identità utente né fornisce la logica per stabilire il contenuto che un utente è autorizzato a visualizzare. Il caso d'uso per la limitazione per motivi di sicurezza presuppone che venga fornita l'associazione tra un documento riservato e l'identificatore di gruppo che ha accesso a tale documento, importata intatta in un indice di ricerca. 

Nell'esempio ipotetico, il corpo della richiesta PUT in un indice di Ricerca cognitiva di Azure includeil saggio o la trascrizione del college di un richiedente insieme all'identificatore di gruppo che dispone dell'autorizzazione per visualizzare tale contenuto. 

Nell'esempio generico usato nel codice di esempio per questa procedura dettagliata l'operazione relativa all'indice potrebbe avere l'aspetto seguente:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Emettere una richiesta di ricerca

A scopo di limitazione per motivi di sicurezza, i valori nel campo di sicurezza nell'indice sono valori statici, usati per includere o escludere i documenti nei risultati della ricerca. Ad esempio, se l'identificatore di gruppo per le ammissioni è "A11B22C33D4-E55F66G77-H88I99JKK", tutti i documenti in un indice di Ricerca cognitiva di Azure con tale identificatore nella sicurezza archiviata vengono inclusi (o esclusi) nei risultati della ricerca inviati al richiedente.

Per filtrare i documenti restituiti nei risultati della ricerca in base ai gruppi dell'utente che ha emesso la richiesta, esaminare i passaggi seguenti.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passaggio 1: recuperare gli identificatori di gruppo dell'utente

Se i gruppi dell'utente non sono stati ancora memorizzati nella cache o se la cache è scaduta, emettere la richiesta per [groups](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0).
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Passaggio 2: comporre la richiesta di ricerca

Presupponendo che si disponga dell'appartenenza ai gruppi dell'utente, è possibile emettere la richiesta di ricerca con i valori di filtro appropriati.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Passaggio 3: gestire i risultati

La risposta include un elenco filtrato dei documenti, costituito da quelli che l'utente è autorizzato a visualizzare. A seconda di come viene creata la pagina dei risultati della ricerca, potrebbero essere inclusi segnali visivi per riflettere il set di risultati filtrato.

## <a name="conclusion"></a>Conclusioni

In questa procedura dettagliata sono state illustrate le tecniche per l'uso degli accessi ad AAD per filtrare i documenti nei risultati di Ricerca cognitiva di Azure, tagliando i risultati dei documenti che non corrispondono al filtro fornito nella richiesta.

## <a name="see-also"></a>Vedere anche

+ [Controllo degli accessi basato sulle identità con i filtri di Ricerca cognitiva di AzureIdentity-based access control using Azure Cognitive Search filters](search-security-trimming-for-azure-search.md)
+ [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
+ [Sicurezza dei dati e controllo degli accessi nelle operazioni di Ricerca cognitiva di AzureData security and access control in Azure Cognitive Search operations](search-security-overview.md)
