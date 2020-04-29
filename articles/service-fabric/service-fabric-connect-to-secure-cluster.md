---
title: Connettersi in modo sicuro a un cluster di Azure Service Fabric
description: Descrive come autenticare l'accesso client a un cluster di Service Fabric e come proteggere la comunicazione tra i client e un cluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258577"
---
# <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro

Quando un client si connette a un nodo di un cluster di Service Fabric, è possibile autenticare il client e proteggere la comunicazione stabilita usando la sicurezza basata su certificati o Azure Active Directory (AAD). Questa autenticazione garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione.  La sicurezza basata su certificati o AAD deve essere stata abilitata in precedenza nel cluster durante la creazione del cluster stesso.  Per altre informazioni sugli scenari di sicurezza dei cluster, vedere [Sicurezza del cluster](service-fabric-cluster-security.md). Se ci si connette a un cluster protetto con certificati, [configurare il certificato client](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) nel computer che si connette al cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Connettersi a un cluster sicuro usando l'interfaccia della riga di comando di Azure Service Fabric (sfctl)

Esistono modi diversi per connettersi a un cluster protetto usando l'interfaccia della riga di comando di Service Fabric (sfctl). Quando si usa un certificato client per l'autenticazione, i dettagli del certificato devono corrispondere al certificato distribuito ai nodi del cluster. Se il certificato dispone di autorità di certificazione (CA), è necessario specificare anche le autorità di certificazione attendibili.

È possibile connettersi a un cluster con il comando `sfctl cluster select`.

È possibile specificare i certificati client in due modi diversi, come una coppia chiave-certificato o come un singolo file PFX. Per i file PEM protetti da password, verrà chiesto automaticamente di immettere la password. Se si è ottenuto il certificato client come file PFX, convertire prima il file PFX in un file PEM usando il comando seguente. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Se il file con estensione pfx non è protetto da password, usare -passin pass: come ultimo parametro.

Per specificare il certificato client come file con estensione pem, specificare il percorso del file nell'argomento `--pem`. Ad esempio:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

I file con estensione pem protetti da password chiederanno la password prima di eseguire qualsiasi comando.

Per specificare una coppia certificato-chiave, usare gli argomenti `--cert` e `--key` per specificare i percorsi di file a ogni rispettivo file.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

In alcuni casi i certificati usati per proteggere i cluster di test o di sviluppo non superano la convalida. Per ignorare la verifica del certificato, specificare l'opzione `--no-verify`. Ad esempio:

> [!WARNING]
> Non usare l'opzione `no-verify` quando ci si connette a cluster di Service Fabric di produzione.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

È anche possibile specificare i percorsi a directory di certificati CA attendibili o a certificati individuali. Per specificare questi percorsi, usare l'argomento `--ca`. Ad esempio:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Dopo la connessione, sarà possibile [eseguire altri comandi sfctl](service-fabric-cli.md) per interagire con il cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Connessione a un cluster mediante PowerShell
Prima di eseguire operazioni su un cluster tramite PowerShell, stabilire una connessione al cluster. La connessione al cluster verrà usata per tutti i successivi comandi nella specifica sessione di PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Connettersi a un cluster non sicuro

Per connettersi a un cluster non sicuro, specificare l'indirizzo dell'endpoint del cluster nel comando **Connect-ServiceFabricCluster**:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Connettersi a un cluster sicuro con Azure Active Directory

Per connettersi a un cluster sicuro che usa Azure Active Directory per autorizzare l'accesso di amministratore al cluster, specificare l'identificazione personale del certificato del cluster e usare il flag *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client
Per connettersi a un cluster sicuro che usa certificati client per autorizzare l'accesso di amministratore, eseguire il comando di PowerShell seguente. 

#### <a name="connect-using-certificate-common-name"></a>Connettersi con il nome comune del certificato
Specificare il nome comune del certificato del cluster e il nome comune del certificato client a cui sono state concesse le autorizzazioni per la gestione del cluster. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* è il nome comune del certificato del server installato nei nodi del cluster. *FindValue* è il nome comune del certificato client di amministrazione. Dopo l'immissione dei parametri, il comando sarà simile all'esempio seguente:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Connettersi con l'identificazione personale del certificato
Specificare l'identificazione personale del certificato del cluster e l'identificazione personale del certificato client a cui sono state concesse le autorizzazioni per la gestione del cluster. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* è l'identificazione personale del certificato del server installato nei nodi del cluster. *FindValue* è l'identificazione personale del certificato client di amministrazione.  Dopo l'immissione dei parametri, il comando sarà simile all'esempio seguente:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Connettersi a un cluster sicuro con Windows Active Directory
Se il cluster autonomo viene distribuito usando la protezione di Active Directory, connettersi al cluster aggiungendo l'opzione "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Connettersi a un cluster mediante le API FabricClient
Service Fabric SDK fornisce la classe [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) per la gestione del cluster. Per usare le API di FabricClient, è necessario disporre del pacchetto NuGet Microsoft.ServiceFabric.

### <a name="connect-to-an-unsecure-cluster"></a>Connettersi a un cluster non sicuro

Per connettersi a un cluster remoto non protetto, creare un'istanza di FabricClient e specificare l'indirizzo del cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Per il codice in esecuzione in un cluster, ad esempio in un servizio Reliable Services, creare un'istanza di FabricClient *senza* specificare l'indirizzo del cluster. FabricClient si connette al gateway di gestione locale nel nodo in cui il codice è attualmente in esecuzione, evitando un hop di rete aggiuntivo.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client

I nodi del cluster devono avere certificati validi il cui nome comune o nome DNS nella rete SAN è contenuto nella [proprietà RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) impostata in [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Questo processo consente l'autenticazione reciproca tra il client e i nodi del cluster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Connessione interattiva a un cluster protetto con Azure Active Directory

Nell'esempio seguente viene utilizzata Azure Active Directory per l'identità del client e il certificato del server per l'identità del server.

Al momento della connessione al cluster viene visualizzata automaticamente una finestra di dialogo per l'accesso interattivo.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Connessione non interattiva a un cluster protetto con Azure Active Directory

L'esempio è basato su Microsoft.IdentityModel.Clients.ActiveDirectory, Versione: 2.19.208020213.

Per ulteriori informazioni sull'acquisizione di token di AAD, vedere [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Connessione a un cluster protetto senza conoscere a priori i metadati tramite Azure Active Directory

L'esempio seguente usa l'acquisizione dei token non interattiva, ma lo stesso approccio consente di creare un'esperienza di acquisizione dei token interattiva personalizzata. I metadati di Azure Active Directory necessari per l'acquisizione dei token vengono letti dalla configurazione del cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Connettersi a un cluster sicuro usando Service Fabric Explorer
Per raggiungere [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) per un determinato cluster, inserire nel browser l'indirizzo seguente:

`http://<your-cluster-endpoint>:19080/Explorer`

L'URL completo è disponibile anche nel riquadro essentials del cluster del portale di Azure.

Per la connessione a un cluster sicuro in Windows o OS X tramite un browser, è possibile importare il certificato client e il browser richiederà il certificato da usare per la connessione al cluster.  Nei computer Linux, il certificato dovrà essere importato usando le impostazioni avanzate del browser (ogni browser ha meccanismi diversi) ed è necessario puntare alla posizione del certificato sul disco. Per altre informazioni, vedere [Configurare un certificato client](#connectsecureclustersetupclientcert).

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Connettersi a un cluster sicuro con Azure Active Directory

Per connettersi a un cluster protetto con AAD, inserire nel browser l'indirizzo seguente:

`https://<your-cluster-endpoint>:19080/Explorer`

Verrà automaticamente richiesto di accedere con AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client

Per connettersi a un cluster protetto con certificati, inserire nel browser l'indirizzo seguente:

`https://<your-cluster-endpoint>:19080/Explorer`

Viene automaticamente richiesto di selezionare un certificato del client.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurare un certificato client nel computer remoto

È necessario usare almeno due certificati per proteggere il cluster, uno per il certificato del server e del cluster e un altro per l'accesso client.  È consigliabile usare anche altri certificati secondari e certificati di accesso client.  Per proteggere la comunicazione tra un client e un nodo del cluster con la sicurezza basata su certificati, è prima necessario ottenere e installare il certificato client. Il certificato può essere installato nell'archivio personale del computer locale o dell'utente corrente.  È necessaria anche l'identificazione personale del certificato del server in modo che il client possa autenticare il cluster.

* In Windows: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nell'archivio personale, `Certificates - Current User\Personal\Certificates`. In alternativa, è possibile usare il comando di PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Se si tratta di un certificato autofirmato, è necessario importarlo nell'archivio "Persone attendibili" del computer prima di poterlo usare per la connessione a un cluster sicuro.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* In Mac: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nel keychain.

## <a name="next-steps"></a>Passaggi successivi

* [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
* [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)
* [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)
* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
