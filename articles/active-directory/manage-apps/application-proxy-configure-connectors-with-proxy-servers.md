---
title: Usare server proxy locali esistenti e Azure Active Directory
description: Illustra come usare server proxy locali esistenti con Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: b6ecea10c884bffb10c936ca7c619d70d2449467
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728492"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Usare server proxy locali esistenti

Questo articolo illustra come configurare i connettori del proxy applicazione di Azure Active Directory (Azure AD) per l'uso di server proxy in uscita. È rivolto ai clienti con ambienti di rete che dispongono di proxy esistenti.

Iniziamo esaminando questi scenari di distribuzione principali:

* Configurare i connettori per ignorare i proxy in uscita locali.
* Configurare i connettori per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD.
* Effettuare la configurazione usando un proxy tra il connettore e l'applicazione back-end.

Per ulteriori informazioni sui connettori, vedere [Informazioni sui connettori proxy di applicazione di Azure AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Ignorare i proxy in uscita

I connettori sono componenti del sistema operativo sottostanti che creano le richieste in uscita. Questi componenti tentano automaticamente di individuare un server proxy nella rete usando WPAD (Web Proxy Auto-Discovery).

I componenti del sistema operativo provano a individuare un server proxy eseguendo una ricerca DNS per wpad.domainsuffix. Se la ricerca restituisce il DNS, viene quindi inviata una richiesta HTTP all'indirizzo IP per wpad.dat. Questa richiesta diventa lo script di configurazione proxy nell'ambiente. Il connettore usa questo script per selezionare un server proxy in uscita. Il traffico del connettore potrebbe tuttavia non riuscire ancora a passare perché sono necessarie altre impostazioni di configurazione nel proxy.

È possibile configurare il connettore in modo che ignori il proxy locale, per garantire che usi la connettività diretta ai servizi di Azure. Se consentita dai criteri di rete, questa è un'operazione consigliata, perché implica una configurazione in meno da gestire.

Per disabilitare l'uso del proxy in uscita per il connettore, modificare il file C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* indicata nell'esempio di codice seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Per assicurarsi che anche il servizio Connector Updater ignori il proxy, apportare una modifica simile al file ApplicationProxyConnectorUpdaterService.exe.config. Questo file si trova in C:\Programmi\Microsoft AAD App Proxy Connector Updater.

Assicurarsi di creare copie dei file originali nel caso sia necessario ripristinare i file .config predefiniti.

## <a name="use-the-outbound-proxy-server"></a>Usare il server proxy in uscita

Alcuni ambienti dei clienti richiedono che tutto il traffico in uscita passi attraverso un proxy in uscita, senza eccezioni. Di conseguenza ignorare il proxy non è possibile.

Si può configurare il traffico del connettore per il passaggio attraverso il proxy in uscita, come illustrato nel diagramma seguente:

 ![Configurazione del traffico del connettore per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Dato che il traffico è solo in uscita, non è necessario configurare l'accesso in ingresso attraverso firewall.

> [!NOTE]
> Il proxy di applicazione non supporta l'autenticazione in altri proxy. Gli account del servizio di rete del connettore/strumento di aggiornamento dovrebbero essere in grado di connettersi al proxy senza ricevere richieste di autenticazione.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passaggio 1: Configurare il connettore e i servizi correlati per il passaggio attraverso il proxy in uscita

Se WPAD è abilitato nell'ambiente e configurato in modo corretto, il connettore individua automaticamente il server proxy in uscita e tenta di usarlo. Tuttavia, è possibile configurare in modo esplicito il connettore per il passaggio attraverso un proxy in uscita.

A tale scopo modificare il file C:\Programmi\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* indicata nell'esempio di codice seguente. Modificare *proxyserver:8080* in modo che corrisponda al nome o all'indirizzo IP del server proxy locale e alla porta su cui è in ascolto. Il valore deve contenere il prefisso http://, anche se si usa un indirizzo IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Configurare quindi il servizio Connector Updater in modo che usi il proxy, apportando una modifica simile al file C:\Programmi\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passaggio 2: Configurare il proxy per consentire il passaggio del traffico proveniente dal connettore e dai servizi correlati

Esistono quattro aspetti da considerare per il proxy in uscita:

* Regole in uscita del proxy
* Autenticazione proxy
* Porte proxy
* Ispezione TLS

#### <a name="proxy-outbound-rules"></a>Regole in uscita del proxy

Consentire l'accesso agli URL seguenti:

| URL | Porta |  Uso |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Comunicazione tra il connettore e il servizio cloud proxy di applicazione |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | Il connettore usa questi URL per verificare i certificati. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | Il connettore usa questi URL durante il processo di registrazione. |
| ctldl.windowsupdate.com | 80/HTTP | Il connettore usa questo URL durante il processo di registrazione. |

È possibile consentire le connessioni a \*.msappproxy.net e \*.servicebus.windows.net se il firewall o il proxy consente di configurare elenchi di elementi consentiti DNS.

Se non è possibile consentire la connettività in base al nome di dominio completo ed è necessario specificare invece intervalli IP, usare queste opzioni:

* Consentire al connettore l'accesso in uscita a tutte le destinazioni.
* Consentire al connettore l'accesso in uscita a tutti gli intervalli di indirizzi IP del data center di Azure. Il problema con l'elenco di intervalli di indirizzi IP del data center di Azure è che viene aggiornato ogni settimana. È necessario implementare un processo per assicurare che le regole di accesso vengano aggiornate di conseguenza. L'uso di un solo subset di indirizzi IP può provocare errori di configurazione. Per scaricare gli intervalli IP del Data Center di Azure più recenti, passare a [https://download.microsoft.com](https://download.microsoft.com) e cercare "Azure IP Ranges and Service Tags". Assicurarsi di selezionare il cloud pertinente. Ad esempio, gli intervalli di indirizzi IP del cloud pubblico sono disponibili con "Azure IP Ranges and Service Tags-Public Cloud". Il cloud per enti pubblici degli Stati Uniti è disponibile cercando "Azure IP Ranges and Service Tags-US Government cloud".

#### <a name="proxy-authentication"></a>Autenticazione proxy

L'autenticazione proxy non è attualmente supportata. Il nostro consiglio è di consentire l'accesso anonimo del connettore alle destinazioni su Internet.

#### <a name="proxy-ports"></a>Porte proxy

Il connettore esegue le connessioni TLS in uscita usando il metodo CONNECT. Questo metodo crea essenzialmente un tunnel attraverso il proxy in uscita. Configurare il server proxy per consentire il tunneling alle porte 443 e 80.

> [!NOTE]
> Quando il bus di servizio viene eseguito su HTTPS, usa la porta 443. Per impostazione predefinita, il bus di servizio prova tuttavia a stabilire connessioni TCP dirette ed esegue il fallback su HTTPS solo se si verifica un errore di connettività diretta.

#### <a name="tls-inspection"></a>Ispezione TLS

Non usare l'ispezione TLS per il traffico del connettore perché causa problemi per tale traffico. Il connettore usa un certificato client per l'autenticazione al servizio proxy di applicazione e il certificato può essere perso durante l'analisi TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Effettuare la configurazione usando un proxy tra il connettore e l'applicazione back-end
L'uso di un proxy di inoltro per la comunicazione verso l'applicazione back-end potrebbe essere un requisito speciale in alcuni ambienti.
Per abilitare questa opzione, procedere come segue:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Passaggio 1: Aggiungere il valore del registro di sistema obbligatorio al server
1. Per abilitare l'uso del proxy predefinito, aggiungere il seguente valore del registro di sistema (DWORD) `UseDefaultProxyForBackendRequests = 1` alla chiave del registro di sistema di configurazione del connettore presente in "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Passaggio 2: Configurare manualmente il server proxy usando il comando netsh
1.  Abilitare i criteri di gruppo Basa impostazioni proxy sul computer. Si trova in: Configurazione computer\Criteri\Modelli amministrativi\Componenti di Windows\Internet Explorer. Deve essere impostato questo criterio invece di impostarlo sul singolo utente.
2.  Eseguire `gpupdate /force` sul server o riavviare il server per assicurarsi che usi le impostazioni dei criteri di gruppo aggiornate.
3.  Avviare un prompt dei comandi con privilegi elevati con diritti di amministratore e immettere `control inetcpl.cpl`.
4.  Configurazione delle impostazioni del proxy necessarie. 

Queste impostazioni consentono al connettore di usare lo stesso proxy di inoltro per la comunicazione con Azure e l'applicazione back-end. Se il connettore per la comunicazione di Azure non richiede un proxy di inoltro o un proxy di inoltro diverso, è possibile configurarlo modificando il file ApplicationProxyConnectorService.exe.config, come descritto nelle sezioni Ignorare i proxy in uscita o Usare il server proxy in uscita.

> [!NOTE]
> Esistono diversi modi per configurare il proxy Internet nel sistema operativo. Le impostazioni proxy configurate tramite NETSH WINHTTP (Run `NETSH WINHTTP SHOW PROXY` to Verify) eseguono l'override delle impostazioni proxy configurate nel passaggio 2. 

Il servizio di aggiornamento del connettore userà anche il proxy del computer. Questo comportamento può essere cambiato modificando il file ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Risolvere i problemi relativi al connettore proxy e alla connettività del servizio

A questo punto si dovrebbe vedere tutto il traffico che passa attraverso il proxy. Se si verificano problemi, potrebbero essere utili le seguenti informazioni per la risoluzione dei problemi.

Il modo migliore per identificare e risolvere i problemi di connettività del connettore è eseguire un'acquisizione di rete durante l'avvio del servizio del connettore. Ecco alcuni semplici suggerimenti per acquisire e filtrare le tracce di rete.

È possibile usare lo strumento di monitoraggio preferito. Ai fini di questo articolo, è stato usato Microsoft Message Analyzer.

> [!NOTE]
> [Microsoft Message Analyzer (MMA) è stato ritirato](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f) e i pacchetti di download sono stati rimossi dai siti microsoft.com il 25 2019 novembre.  Attualmente non è disponibile alcuna sostituzione Microsoft per Microsoft Message Analyzer in fase di sviluppo.  Per una funzionalità simile, provare a usare uno strumento Analizzatore protocollo di rete di terze parti, ad esempio Wireshark.

Gli esempi seguenti sono specifici di Message Analyzer, ma i principi possono essere applicati a qualsiasi strumento di analisi.

### <a name="take-a-capture-of-connector-traffic"></a>Eseguire un'acquisizione del traffico del connettore

Per iniziare la risoluzione dei problemi, procedere come segue:

1. Da services.msc arrestare il servizio connettore del proxy applicazione di Azure AD.

   ![Servizio connettore proxy applicazione di Azure AD in services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Eseguire Message Analyzer come amministratore.
1. Selezionare l'opzione per l'avvio della **traccia locale**.
1. Avviare il servizio connettore del proxy applicazione di Azure AD.
1. Arrestare l'acquisizione di rete.

   ![Screenshot che mostra il pulsante Arrestare l'acquisizione rete](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controllare se il traffico del connettore ignora i proxy in uscita

Se il connettore del proxy di applicazione è stato configurato per ignorare i server proxy e connettersi direttamente al servizio proxy di applicazione, è necessario individuare nell'acquisizione di rete i tentativi di connessione TCP non riusciti.

Usare il filtro di Message Analyzer per identificare questi tentativi. Immettere `property.TCPSynRetransmit` nella casella del filtro e selezionare **Applica**.

Un pacchetto SYN è il primo pacchetto inviato per stabilire una connessione TCP. Se questo pacchetto non restituisce una risposta, il SYN viene ritentato. È possibile usare il filtro precedente per visualizzare qualsiasi SYN ritrasmesso. È quindi possibile vedere se questi SYN corrispondono a traffico correlato al connettore.

Se si prevede che il connettore esegua connessioni dirette ai servizi di Azure, le risposte SynRetransmit sulla porta 443 indicano la presenza di un problema di rete o di firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controllare se il traffico del connettore usa proxy in uscita

Se il traffico del connettore del proxy di applicazione è stato configurato per passare attraverso i server proxy, è necessario individuare le connessioni HTTPS non riuscite nel proxy.

Per filtrare l'acquisizione di rete in modo da individuare questi tentativi di connessione, immettere `(https.Request or https.Response) and tcp.port==8080` nel filtro di Message Analyzer, sostituendo 8080 con la porta del servizio proxy. Selezionare **Applica** per visualizzare i risultati del filtro.

Il filtro precedente mostra solo le richieste e le risposte HTTPS da e verso la porta del proxy. Cercare le richieste CONNECT che indicano la comunicazione con il server proxy. Al completamento dell'operazione si ottiene una risposta HTTP affermativa (200).

Se vengono visualizzati altri codici di risposta, ad esempio 407 o 502, significa che il proxy richiede l'autenticazione o non consente il traffico per altri motivi. A questo punto ci si rivolge al team di supporto del server proxy.

## <a name="next-steps"></a>Passaggi successivi

* [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
* In caso di problemi di connettività del connettore, porre una domanda nella [Pagina delle domande di Domande e risposte Microsoft per Azure Active Directory](/answers/topics/azure-active-directory.html) o creare un ticket per il team di supporto Microsoft.