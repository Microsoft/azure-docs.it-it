---
title: Impostazioni di proxy e firewall locali di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni Sincronizzazione file di Azure impostazioni proxy e firewall locali. Esaminare i dettagli di configurazione per porte, reti e connessioni speciali ad Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2c77c20bfb6fff60f2242d1ac2dad7b3fc9f6fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796895"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Impostazioni di proxy e firewall di Sincronizzazione file di Azure
Sincronizzazione file di Azure connette i server locali a File di Azure abilitando la sincronizzazione tra più siti e funzionalità di suddivisione in livelli cloud. È necessario quindi che un server locale sia connesso a Internet e che un amministratore IT scelga il percorso migliore per consentire al server di accedere ai servizi cloud di Azure.

Questo articolo offre informazioni dettagliate sui requisiti e le opzioni disponibili per connettere un server a Sincronizzazione file di Azure in modo sicuro ed efficiente.

Prima di seguire questa guida pratica, è consigliabile leggere [Considerazioni sulla rete per Sincronizzazione file di Azure](file-sync-networking-overview.md).

## <a name="overview"></a>Panoramica
Sincronizzazione file di Azure svolge la funzione di servizio di orchestrazione tra Windows Server, la condivisione file di Azure e altri servizi di Azure per la sincronizzazione dei dati, come descritto nel gruppo di sincronizzazione. Per consentire il corretto funzionamento di Sincronizzazione file di Azure, è necessario configurare i server in modo che possano comunicare con i servizi di Azure seguenti:

- Archiviazione di Azure
- Sincronizzazione file di Azure
- Azure Resource Manager
- Servizi di autenticazione

> [!Note]  
> L'agente Sincronizzazione file di Azure in Windows Server avvia tutte le richieste ai servizi cloud ed è quindi necessario tenere conto del traffico in uscita solo dal punto di vista del firewall. <br /> Nessun servizio di Azure avvia una connessione all'agente Sincronizzazione file di Azure.

## <a name="ports"></a>Porte
Sincronizzazione file di Azure sposta i dati e i metadati dei file solo su connessioni HTTPS e richiede che la porta 443 sia aperta in uscita.
Tutto il traffico, quindi, viene crittografato.

## <a name="networks-and-special-connections-to-azure"></a>Reti e connessioni speciali ad Azure
L'agente Sincronizzazione file di Azure non presenta alcun requisito in merito a canali speciali come [ExpressRoute](../../expressroute/expressroute-introduction.md) e altri diretti ad Azure.

Sincronizzazione file di Azure interagisce con qualsiasi mezzo disponibile che consenta di accedere ad Azure adattandosi automaticamente alle varie caratteristiche di rete, come la latenza e la larghezza di banda, e offrendo il controllo amministrativo per l'ottimizzazione. Non sono ancora disponibili tutte le funzionalità. Per configurare un comportamento specifico, è possibile usare [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Sincronizzazione file di Azure supporta impostazioni proxy a livello di computer specifiche per l'app.

**Le impostazioni proxy specifiche dell'app** consentono la configurazione di un proxy specificamente per Sincronizzazione file di Azure traffico. Le impostazioni proxy specifiche per le app sono supportate nella versione 4.0.1.0 o versioni successive dell'agente e possono essere configurate durante l'installazione dell'agente oppure usando il cmdlet Set-StorageSyncProxyConfiguration di PowerShell.

Comandi di PowerShell per configurare le impostazioni proxy specifiche dell'app:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Ad esempio, se il server proxy richiede l'autenticazione con un nome utente e una password, eseguire i comandi di PowerShell seguenti:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**Le impostazioni proxy a livello di** computer sono trasparenti per l'agente Sincronizzazione file di Azure poiché l'intero traffico del server viene instradato attraverso il proxy.

Per configurare le impostazioni proxy a livello di computer, attenersi alla procedura seguente: 

1. Configurare le impostazioni proxy per le applicazioni .NET 

   - Modificare questi due file:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Aggiungere la sezione <system.net> nel file machine.config (sotto la sezione <system.serviceModel>).  Modificare 127.0.01:8888 per l'indirizzo IP e la porta del server proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Configurare le impostazioni del proxy WinHTTP 

   > [!Note]  
   > Esistono diversi metodi (WPAD, file PAC, netsh e così via) per configurare Windows Server per l'uso di un server proxy. La procedura seguente illustra come configurare le impostazioni del proxy usando netsh, ma qualsiasi metodo elencato nella documentazione Configurare le impostazioni [del server proxy nella documentazione di Windows](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-proxy-server-settings) è supportato.


   - Eseguire il comando seguente da un prompt dei comandi con privilegi elevati o PowerShell per visualizzare le impostazioni del proxy esistente:   

     Visualizzare il proxy netsh winhttp

   - Eseguire il comando seguente da un prompt dei comandi con privilegi elevati o PowerShell per impostare le impostazioni del proxy (modificare 127.0.01:8888 per l'indirizzo IP e la porta del server proxy):  

     Impostare il proxy netsh winhttp 127.0.0.1:8888

3. Riavviare il servizio Storage Sync Agent eseguendo il comando seguente da un prompt dei comandi con privilegi elevati o da PowerShell: 

      net stop filesyncsvc

      Nota: il servizio Storage Sync Agent (filesyncsvc) si avvia in modo automatico dopo l'arresto.

## <a name="firewall"></a>Firewall
Come indicato in una sezione precedente, la porta 443 deve essere aperta in uscita. In base ai criteri definiti a livello di data center, ramo o area geografica, è possibile che sia necessario o preferibile limitare il traffico su questa porta a domini specifici.

La tabella seguente illustra i domini necessari per la comunicazione:

| Servizio | Endpoint cloud pubblico | Endpoint di Azure per enti pubblici | Utilizzo |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Qualsiasi chiamata utente (ad esempio, PowerShell) giunge o passa attraverso questo URL, inclusa la chiamata di registrazione iniziale del server. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Le chiamate di Azure Resource Manager devono essere effettuate da un utente autenticato. Per l'autenticazione utente viene usato questo URL. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Nel corso del processo di distribuzione di Sincronizzazione file di Azure verrà creata un'entità servizio nella sessione di Azure Active Directory associata alla sottoscrizione. A tale scopo viene usato questo URL. Questa entità viene usata per delegare una quantità minima di diritti al servizio Sincronizzazione file di Azure. L'utente che esegue la configurazione iniziale di Sincronizzazione file di Azure deve essere un utente autenticato con privilegi di proprietario della sottoscrizione. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Usare l'URL dell'endpoint pubblico. | Questo URL è accessibile dalla libreria di autenticazione di Active Directory utilizzata dall'interfaccia Sincronizzazione file di Azure di registrazione del server per accedere all'amministratore. |
| **Archiviazione di Azure** | &ast;.core.windows.net | &ast;File con estensione core.usgovcloudapi.net | Quando il server scarica un file, esegue lo spostamento dati in modo più efficiente se comunica direttamente con la condivisione file di Azure nell'account di archiviazione. Il server ha una chiave di firma di accesso condiviso che consente l'accesso solo a specifiche condivisioni file. |
| **Sincronizzazione file di Azure** | &ast;.one.microsoft.com<br>&ast;File con estensione afs.azure.net | &ast;.afs.azure.us | Dopo la registrazione iniziale, il server riceve un URL regionale relativo all'istanza del servizio Sincronizzazione file di Azure disponibile in quell'area. Il server può usare l'URL per comunicare direttamente e in modo efficiente con l'istanza che gestisce la sincronizzazione. |
| **Infrastruttura a chiave pubblica Microsoft** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Dopo l'installazione dell'agente di Sincronizzazione file di Azure, l'URL dell'infrastruttura a chiave pubblica viene usato per scaricare i certificati intermedi necessari per comunicare con il servizio Sincronizzazione file di Azure e la condivisione file di Azure. L'URL OCSP viene usato per controllare lo stato di un certificato. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Dopo aver installato Sincronizzazione file di Azure agent, gli URL Microsoft Update vengono usati per scaricare gli Sincronizzazione file di Azure dell'agente. |

> [!Important]
> Quando si consente il traffico &ast; verso afs.azure.net, il traffico è possibile solo per il servizio di sincronizzazione. Non sono presenti altri servizi Microsoft che usano questo dominio.
> Quando si consente il traffico a &ast;.one.microsoft.com, il traffico dal server non è più limitato solo al servizio di sincronizzazione. Nei sottodomini sono infatti disponibili molti altri servizi Microsoft.

Se il file con estensione afs.azure.net o one.microsoft.com è troppo ampio, è possibile limitare la comunicazione del server consentendo la comunicazione solo alle istanze locali esplicite del servizio File di Azure &ast; &ast; sincronizzazione dati. Le istanze da scegliere dipendono dall'area del servizio di sincronizzazione archiviazione in cui è stato distribuito e registrato il server. Tale area è chiamata "URL dell'endpoint primario" nella tabella seguente.

Per ragioni di continuità aziendale e ripristino di emergenza (BCDR) è possibile che siano state specificate condivisioni file di Azure in un account di archiviazione con ridondanza geografica (GRS). In tal caso, le condivisioni file di Azure eseguiranno il failover nell'area abbinata se si verifica un'interruzione duratura a livello di area. Sincronizzazione file di Azure usa le stesse associazioni a livello di area della risorsa di archiviazione. Pertanto, se si usano account di archiviazione GRS, è necessario abilitare URL aggiuntivi per consentire al server di parlare con l'area abbinata per Sincronizzazione file di Azure. La tabella seguente chiama questa "area abbinata". Inoltre, vi è anche un URL del profilo di gestione traffico che deve essere abilitato. In questo modo il traffico di rete può essere indirizzato di nuovo con facilità all'area abbinata in caso di failover ed è chiamato "URL di individuazione" nella tabella seguente.

| Cloud  | Region | URL dell'endpoint primario | Area associata | URL di individuazione |
|--------|--------|----------------------|---------------|---------------|
| Blockchain pubblica |Australia orientale | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Australia sud-orientale | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Blockchain pubblica |Australia sud-orientale | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Australia orientale | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Blockchain pubblica | Brasile meridionale | https: \/ /brazilsouth01.afs.azure.net | Stati Uniti centro-meridionali | https: \/ /tm-brazilsouth01.afs.azure.net |
| Blockchain pubblica | Canada centrale | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Canada orientale | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Blockchain pubblica | Canada orientale | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Canada centrale | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Blockchain pubblica | India centrale | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | India meridionale | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Blockchain pubblica | Stati Uniti centrali | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | Stati Uniti orientali 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Blockchain pubblica | Asia orientale | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Asia sud-orientale | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Blockchain pubblica | Stati Uniti orientali | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | Stati Uniti occidentali | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Blockchain pubblica | Stati Uniti orientali 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | Stati Uniti centrali | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Blockchain pubblica | Germania settentrionale | https: \/ /germanynorth01.afs.azure.net | Germania centro-occidentale | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Blockchain pubblica | Germania centro-occidentale | https: \/ /germanywestcentral01.afs.azure.net | Germania settentrionale | https: \/ /tm-germanynorth01.afs.azure.net |
| Blockchain pubblica | Giappone orientale | https: \/ /japaneast01.afs.azure.net | Giappone occidentale | https: \/ /tm-japaneast01.afs.azure.net |
| Blockchain pubblica | Giappone occidentale | https: \/ /japanwest01.afs.azure.net | Giappone orientale | https: \/ /tm-japanwest01.afs.azure.net |
| Blockchain pubblica | Corea centrale | https: \/ /koreacentral01.afs.azure.net/ | Corea meridionale | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Blockchain pubblica | Corea meridionale | https: \/ /koreasouth01.afs.azure.net/ | Corea centrale | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Blockchain pubblica | Stati Uniti centro-settentrionali | https: \/ /northcentralus01.afs.azure.net | Stati Uniti centro-meridionali | https: \/ /tm-northcentralus01.afs.azure.net |
| Blockchain pubblica | Europa settentrionale | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Europa occidentale | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Blockchain pubblica | Stati Uniti centro-meridionali | https: \/ /southcentralus01.afs.azure.net | Stati Uniti centro-settentrionali | https: \/ /tm-southcentralus01.afs.azure.net |
| Blockchain pubblica | India meridionale | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | India centrale | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Blockchain pubblica | Asia sud-orientale | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Asia orientale | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Blockchain pubblica | Svizzera settentrionale | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Svizzera occidentale | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Blockchain pubblica | Svizzera occidentale | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Svizzera settentrionale | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Blockchain pubblica | Regno Unito meridionale | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Regno Unito occidentale | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Blockchain pubblica | Regno Unito occidentale | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Regno Unito meridionale | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Blockchain pubblica | Stati Uniti centro-occidentali | https: \/ /westcentralus01.afs.azure.net | West US 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Blockchain pubblica | Europa occidentale | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa settentrionale | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Blockchain pubblica | Stati Uniti occidentali | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | Stati Uniti orientali | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Blockchain pubblica | West US 2 | https: \/ /westus201.afs.azure.net | Stati Uniti centro-occidentali | https: \/ /tm-westus201.afs.azure.net |
| Enti governativi | US Gov Arizona | https: \/ /usgovarizona01.afs.azure.us | US Gov Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Enti governativi | US Gov Texas | https: \/ /usgovtexas01.afs.azure.us | US Gov Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Se si usano account di archiviazione con ridondanza locale (LRS) o con ridondanza della zona (ZRS), è sufficiente abilitare l'URL elencato in "URL dell'endpoint primario".

- Se si usano account di archiviazione con ridondanza geografica (GRS), abilitare tre URL.

**Esempio:** si distribuisce un servizio di sincronizzazione archiviazione in `"West US"` e si registra il server con esso. Gli URL con cui consentire al server di comunicare per questo caso sono:

> - https: \/ /westus01.afs.azure.net (endpoint primario: Stati Uniti occidentali)
> - https: \/ /eastus01.afs.azure.net (area di failover abbinata: Stati Uniti orientali)
> - https: \/ /tm-westus01.afs.azure.net (URL di individuazione dell'area primaria)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Elenco di indirizzi IP consentiti Sincronizzazione file di Azure indirizzi IP
Sincronizzazione file di Azure supporta l'uso di tag di [servizio](../../virtual-network/service-tags-overview.md), che rappresentano un gruppo di prefissi di indirizzi IP per un determinato servizio di Azure. È possibile usare i tag di servizio per creare regole del firewall che consentono la comunicazione con il Sincronizzazione file di Azure servizio. Il tag di servizio per Sincronizzazione file di Azure è `StorageSyncService` .

Se si usa un Sincronizzazione file di Azure all'interno di Azure, è possibile usare il nome del tag di servizio direttamente nel gruppo di sicurezza di rete per consentire il traffico. Per altre informazioni, vedere [Gruppi di sicurezza di rete](../../virtual-network/network-security-groups-overview.md).

Se si usa Sincronizzazione file di Azure in locale, è possibile usare l'API dei tag di servizio per ottenere gli specifici intervalli di indirizzi IP per l'elenco di elementi consentiti del firewall. Per recuperare queste informazioni, sono disponibili due metodi:

- L'elenco corrente degli intervalli di indirizzi IP per tutti i servizi di Azure che supportano i tag di servizio viene pubblicato settimanalmente nell'Area download Microsoft sotto forma di documento JSON. Ogni cloud di Azure è associato a uno specifico documento JSON con gli intervalli di indirizzi IP pertinenti:
    - [Pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)
- L'API di individuazione di tag di servizio (anteprima) consente il recupero a livello di codice dell'elenco corrente di tag di servizio. Nella versione di anteprima questa API potrebbe restituire informazioni meno aggiornate di quelle restituite dai documenti JSON pubblicati nell'Area download Microsoft. È possibile usare la superficie dell'API in base alle preferenze di automazione:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfaccia della riga di comando di Azure](/cli/azure/network#az_network_list_service_tags)

Poiché l'API di individuazione dei tag di servizio non viene aggiornata con la frequenza dei documenti JSON pubblicati nell'Area download Microsoft, è consigliabile usare il documento JSON per aggiornare l'elenco di elementi consentiti del firewall locale. Attenersi alla procedura riportata di seguito:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

È quindi possibile usare gli intervalli di indirizzi IP in `$ipAddressRanges` per aggiornare il firewall. Per informazioni su come aggiornare il firewall, controllare il sito Web del firewall o dell'appliance di rete.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testare la connettività di rete agli endpoint di servizio
Dopo la registrazione di un server con il servizio Sincronizzazione file di Azure, il cmdlet Test-StorageSyncNetworkConnectivity e il ServerRegistration.exe possono essere usati per testare le comunicazioni con tutti gli endpoint (URL) specifici di questo server. Questo cmdlet consente di risolvere i problemi quando la comunicazione incompleta impedisce al server di usare completamente Sincronizzazione file di Azure e può essere usato per ottimizzare le configurazioni del proxy e del firewall.

Per eseguire il test della connettività di rete, installare Sincronizzazione file di Azure agent versione 9.1 o successiva ed eseguire i comandi di PowerShell seguenti:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Riepilogo e limitazione dei rischi
Gli elenchi riportati in questo documento contengono gli URL con cui comunica attualmente Sincronizzazione file di Azure. I firewall devono essere in grado di consentire il traffico in uscita da questi domini. Microsoft si impegna a mantenere l'elenco costantemente aggiornato.

La configurazione di regole del firewall con limitazione del dominio può contribuire a migliorare la sicurezza. Se vengono usate queste configurazioni del firewall, è necessario tenere presente che nel tempo verranno aggiunti nuovi URL e potrebbero essere modificati quelli esistenti. Controllare periodicamente questo articolo.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)
- [Come distribuire Sincronizzazione file di Azure](file-sync-deployment-guide.md)
- [Monitorare Sincronizzazione file di Azure](file-sync-monitoring.md)
