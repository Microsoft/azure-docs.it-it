---
title: Usare l'identità gestita con un'applicazione
description: Come usare le identità gestite in Azure Service Fabric il codice dell'applicazione per accedere ai servizi di Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: bec640077a17ccef822afb85fc104728aabb13b1
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791323"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Come sfruttare un'identità gestita dell'applicazione Service Fabric per accedere ai servizi di Azure

Service Fabric applicazioni possono sfruttare le identità gestite per accedere ad altre risorse di Azure che supportano l'autenticazione basata su Azure Active Directory. Un'applicazione può ottenere un [token di accesso](../active-directory/develop/developer-glossary.md#access-token) che rappresenta l'identità, che può essere assegnata dal sistema o assegnata dall'utente e usarla come token di connessione per autenticarsi a un altro servizio, noto anche come [server di risorse protetto](../active-directory/develop/developer-glossary.md#resource-server). Il token rappresenta l'identità assegnata all'applicazione Service Fabric e verrà emesso solo per le risorse di Azure (incluse le applicazioni SF) che condividono tale identità. Per una descrizione dettagliata delle identità gestite, vedere la documentazione relativa alla [Panoramica dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) , nonché la distinzione tra identità assegnate dal sistema e identità assegnate dall'utente. Si fa riferimento a un'applicazione Service Fabric abilitata per l'identità gestita come [applicazione client](../active-directory/develop/developer-glossary.md#client-application) in questo articolo.

Vedere un'applicazione di esempio complementare che illustra l'uso di [identità gestite dall'applicazione](https://github.com/Azure-Samples/service-fabric-managed-identity) e assegnate dal sistema Service Fabric con Reliable Services e contenitori.

> [!IMPORTANT]
> Un'identità gestita rappresenta l'associazione tra una risorsa di Azure e un'entità servizio nel tenant di Azure AD corrispondente associato alla sottoscrizione che contiene la risorsa. Di conseguenza, nel contesto di Service Fabric, le identità gestite sono supportate solo per le applicazioni distribuite come risorse di Azure. 

> [!IMPORTANT]
> Prima di usare l'identità gestita di un'applicazione Service Fabric, è necessario concedere all'applicazione client l'accesso alla risorsa protetta. Vedere l'elenco dei servizi di [Azure che supportano l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) per verificare il supporto e quindi la documentazione del rispettivo servizio per i passaggi specifici per concedere a un'identità l'accesso alle risorse di interesse. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Sfruttare un'identità gestita usando Azure. Identity

Azure Identity SDK supporta ora Service Fabric. L'uso di Azure. Identity rende più semplice la scrittura di codice per usare Service Fabric identità gestite dall'app perché gestisce il recupero dei token, la memorizzazione dei token nella cache e l'autenticazione del server. Quando si accede alla maggior parte delle risorse di Azure, il concetto di token è nascosto.

Il supporto Service Fabric è disponibile nelle versioni seguenti per queste lingue: 
- [C# nella versione 1.3.0](https://www.nuget.org/packages/Azure.Identity). Vedere un [esempio di C#](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python nella versione 1.5.0](https://pypi.org/project/azure-identity/). Vedere un [esempio di Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java nella versione 1.2.0](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

Esempio C# di inizializzazione delle credenziali e utilizzo delle credenziali per recuperare un segreto da Azure Key Vault:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Acquisizione di un token di accesso tramite l'API REST
Nei cluster abilitati per l'identità gestita, il runtime di Service Fabric espone un endpoint localhost che le applicazioni possono usare per ottenere i token di accesso. L'endpoint è disponibile in ogni nodo del cluster ed è accessibile a tutte le entità in tale nodo. I chiamanti autorizzati possono ottenere i token di accesso chiamando questo endpoint e presentando un codice di autenticazione; il codice viene generato dal Runtime Service Fabric per ogni attivazione del pacchetto di codice del servizio distinto ed è associato alla durata del processo che ospita il pacchetto di codice del servizio.

In particolare, l'ambiente di un servizio di Service Fabric abilitato per l'identità gestita viene sottoposta a seeding con le variabili seguenti:
- ' IDENTITY_ENDPOINT ': endpoint localhost corrispondente all'identità gestita del servizio
- ' IDENTITY_HEADER ': codice di autenticazione univoco che rappresenta il servizio nel nodo corrente
- ' IDENTITY_SERVER_THUMBPRINT ': identificazione personale del server di identità gestito di Service Fabric

> [!IMPORTANT]
> Il codice dell'applicazione deve considerare il valore della variabile di ambiente ' IDENTITY_HEADER ' come dati sensibili. non deve essere registrato o distribuito in altro modo. Il codice di autenticazione non ha alcun valore all'esterno del nodo locale o dopo che il processo che ospita il servizio è stato terminato, ma rappresenta l'identità del servizio Service Fabric e pertanto deve essere trattato con le stesse precauzioni del token di accesso stesso.

Per ottenere un token, il client esegue i passaggi seguenti:
- forma un URI concatenando l'endpoint di identità gestito (IDENTITY_ENDPOINT valore) con la versione dell'API e la risorsa (audience) richiesta per il token.
- Crea una richiesta GET HTTP (s) per l'URI specificato
- aggiunge la logica di convalida del certificato server appropriata
- aggiunge il codice di autenticazione (IDENTITY_HEADER valore) come intestazione alla richiesta.
- Invia la richiesta

Una risposta con esito positivo conterrà un payload JSON che rappresenta il token di accesso risultante, oltre ai metadati che lo descrivono. Una risposta non riuscita includerà anche una spiegazione dell'errore. Per ulteriori informazioni sulla gestione degli errori, vedere di seguito.

I token di accesso verranno memorizzati nella cache da Service Fabric a diversi livelli (nodo, cluster, servizio del provider di risorse), quindi una risposta con esito positivo non implica necessariamente che il token sia stato emesso direttamente in risposta alla richiesta dell'applicazione utente. I token verranno memorizzati nella cache per un periodo di validità inferiore rispetto alla loro durata, quindi è garantita la ricezione di un token valido per un'applicazione. È consigliabile che il codice dell'applicazione memorizza nella cache i token di accesso acquisiti; la chiave di memorizzazione nella cache deve includere, ovvero derivare, il gruppo di destinatari. 

Richiesta di esempio:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
dove:

| Elemento | Descrizione |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Endpoint di identità gestita per Service Fabric applicazioni, fornito tramite la variabile di ambiente IDENTITY_ENDPOINT. |
| `api-version` | Parametro della stringa di query che specifica la versione dell'API del servizio token di identità gestito. Attualmente l'unico valore accettato è `2019-07-01-preview` ed è soggetto a modifiche. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Questo verrà riflesso come `aud` attestazione (audience) del token emesso. Questo esempio richiede un token per accedere a Azure Key Vault, il cui URI ID app è https: \/ /Vault.Azure.NET/. |
| `Secret` | Campo di intestazione della richiesta HTTP, richiesto dal servizio token di identità gestito Service Fabric per Service Fabric Services per l'autenticazione del chiamante. Questo valore viene fornito dal runtime SF tramite IDENTITY_HEADER variabile di ambiente. |


Risposta di esempio:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
dove:

| Elemento | Descrizione |
| ------- | ----------- |
| `token_type` | Tipo di token. in questo caso, un token di accesso "Bearer", che significa che il presentatore (' Bearer ') di questo token è l'oggetto previsto del token. |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `expires_on` | Timestamp della scadenza del token di accesso. rappresentata come numero di secondi da "1970-01-01T0:0: 0Z UTC" e corrisponde all' `exp` attestazione del token. In questo caso, il token scade il 2019-08-08T06:10:11 + 00:00 (in RFC 3339)|
| `resource` | Risorsa per cui è stato emesso il token di accesso, specificata tramite il `resource` parametro della stringa di query della richiesta; corrisponde all'attestazione ' AUD ' del token. |


## <a name="acquiring-an-access-token-using-c"></a>Acquisizione di un token di accesso con C #
Il precedente diventa, in C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Accesso a Key Vault da un'applicazione Service Fabric mediante identità gestita
Questo esempio si basa su quello precedente per illustrare l'accesso a un segreto archiviato in un Key Vault usando l'identità gestita.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Gestione degli errori
Il campo ' status code ' dell'intestazione della risposta HTTP indica lo stato di esito positivo della richiesta. uno stato "200 OK" indica l'esito positivo e la risposta includerà il token di accesso come descritto in precedenza. Di seguito è riportata una breve enumerazione delle possibili risposte di errore.

| Codice di stato | Motivo dell'errore | Come gestirlo |
| ----------- | ------------ | ------------- |
| 404 - Non trovato. | Codice di autenticazione sconosciuto oppure all'applicazione non è stata assegnata un'identità gestita. | Correggere l'installazione dell'applicazione o il codice di acquisizione dei token. |
| 429 - Numero eccessivo di richieste. |  Limite di limitazione raggiunto, imposto da AAD o SF. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |
| Errore 4xx nella richiesta. | Uno o più dei parametri della richiesta non sono corretti. | Non riprovare.  Esaminare i dettagli dell'errore per maggiori informazioni.  Errori 4xx in fase di progettazione.|
| Errore 5xx dal servizio. | Il sottosistema di identità gestito o Azure Active Directory ha restituito un errore temporaneo. | È possibile riprovare dopo un breve periodo di tempo. È possibile che si verifichi una condizione di limitazione delle richieste (429) quando si esegue un nuovo tentativo.|

Se si verifica un errore, il corpo della risposta HTTP corrispondente contiene un oggetto JSON con i dettagli dell'errore:

| Elemento | Descrizione |
| ------- | ----------- |
| codice | Codice di errore. |
| correlationId | ID di correlazione utilizzabile per il debug. |
| message | Descrizione dettagliata dell'errore. **Le descrizioni degli errori possono cambiare in qualsiasi momento. Non dipendono dal messaggio di errore stesso.**|

Errore di esempio:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

Di seguito è riportato un elenco di errori di Service Fabric tipici specifici per le identità gestite:

| Codice | Message | Descrizione | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Il segreto non è stato trovato nelle intestazioni della richiesta. | Il codice di autenticazione non è stato fornito con la richiesta. | 
| ManagedIdentityNotFound | Identità gestita non trovata per l'host applicazioni specificato. | L'applicazione non ha identità oppure il codice di autenticazione è sconosciuto. |
| ArgumentNullOrEmpty | Il parametro ' Resource ' non deve essere null o una stringa vuota. | La risorsa (audience) non è stata specificata nella richiesta. |
| InvalidApiVersion | La versione API '' non è supportata. La versione supportata è "2019-07-01-Preview". | Versione API mancante o non supportata specificata nell'URI della richiesta. |
| InternalServerError | Si è verificato un errore. | Si è verificato un errore nel sottosistema di identità gestito, possibilmente all'esterno dello stack Service Fabric. La causa più probabile è un valore non corretto specificato per la risorsa (verificare la presenza di '/' finali?) | 

## <a name="retry-guidance"></a>Materiale sussidiario sulla ripetizione di tentativi 

In genere, l'unico codice di errore irreversibile è 429 (troppe richieste); gli errori interni del server o i codici di errore 5xx possono essere ritentabili, anche se la causa potrebbe essere permanente. 

I limiti di limitazione si applicano al numero di chiamate effettuate al sottosistema di identità gestite, in particolare le dipendenze "upstream" (il servizio di identità gestito di Azure o il servizio token di sicurezza). Service Fabric memorizza nella cache i token a diversi livelli della pipeline, ma data la natura distribuita dei componenti implicati, il chiamante può riscontrare risposte di limitazione incoerenti (ad esempio, la limitazione di un nodo o un'istanza di un'applicazione, ma non di un nodo diverso e la richiesta di un token per la stessa identità). Quando viene impostata la condizione di limitazione delle richieste, le richieste successive provenienti dalla stessa applicazione potrebbero avere esito negativo con il codice di stato HTTP 429 (troppe richieste) finché la condizione non viene cancellata.  

È consigliabile che le richieste non siano riuscite a causa della limitazione delle richieste con un backoff esponenziale, come indicato di seguito: 

| Indice delle chiamate | Azione di ricezione 429 | 
| --- | --- | 
| 1 | Attendere 1 secondo e riprovare |
| 2 | Attendere 2 secondi e riprovare |
| 3 | Attendere 4 secondi e riprovare |
| 4 | Attendere 8 secondi e riprovare |
| 4 | Attendere 8 secondi e riprovare |
| 5 | Attendere 16 secondi e riprovare |

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure
Vedere i [servizi di Azure che supportano l'autenticazione Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) per un elenco di risorse che supportano Azure ad e i rispettivi ID di risorsa.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dal sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuire un'applicazione Service Fabric di Azure con un'identità gestita assegnata dall'utente](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Concedere a un'applicazione Service Fabric di Azure l'accesso ad altre risorse di Azure](./how-to-grant-access-other-resources.md)
* [Esplorare un'applicazione di esempio usando Service Fabric identità gestita](https://github.com/Azure-Samples/service-fabric-managed-identity)
