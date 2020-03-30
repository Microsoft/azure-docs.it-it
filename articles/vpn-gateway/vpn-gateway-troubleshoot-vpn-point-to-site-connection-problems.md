---
title: Risolvere i problemi di connessione da punto ad azure
titleSuffix: Azure VPN Gateway
description: Informazioni su come risolvere i problemi di connessione da punto a sito.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297632"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Risoluzione dei problemi: problemi di connessione da punto a sito di Azure

Questo articolo elenca i problemi comuni di connessione da punto a sito che l'utente potrebbe riscontrare. e le possibili cause e soluzioni.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Errore del client VPN: Impossibile trovare un certificato

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Impossibile trovare un certificato che può essere utilizzato con questo protocollo di autenticazione estensibile. (Errore 798)**

### <a name="cause"></a>Causa

Questo problema si verifica se il certificato client non è presente in **Certificates - Current User\Personal\Certificates** (Certificati - Utente corrente\Personale\Certificati).

### <a name="solution"></a>Soluzione

Per risolvere il problema, seguire questa procedura:

1. Aprire Gestione certificati: fare clic sul pulsante **Start**, digitare **gestisci i certificati computer** e quindi fare clic su **gestisci i certificati computer** nei risultati della ricerca.

2. Verificare che i certificati seguenti siano nel percorso corretto:

    | Certificato | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utente corrente\Personale\Certificati |
    | AzureRoot.cer    | Computer locale\Autorità di certificazione radice attendibili|

3. Passare a C:\Users\<NomeUtente>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID> e installare manualmente il certificato ( file *.cer) nell'archivio dell'utente e del computer.

Per altre informazioni su come installare il certificato client, vedere [Generare ed esportare i certificati per le connessioni da punto a sito](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Quando si importa il certificato client, non selezionare l'opzione **Abilita protezione avanzata chiave privata**.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Impossibile stabilire la connessione di rete tra il computer e il server VPN perché il server remoto non risponde

### <a name="symptom"></a>Sintomo

Quando si tenta di connettersi a un gateway di rete virtuale di Azure utilizzando IKEv2 in Windows, viene visualizzato il seguente messaggio di errore:

**Impossibile stabilire la connessione di rete tra il computer e il server VPN perché il server remoto non risponde**

### <a name="cause"></a>Causa
 
 Il problema si verifica se la versione di Windows non dispone del supporto per la frammentazione IKE
 
### <a name="solution"></a>Soluzione

IKEv2 è supportato in Windows 10 e Server 2016. Per poter usare IKEv2, è però necessario installare gli aggiornamenti e impostare in locale un valore della chiave del Registro di sistema. Le versioni del sistema operativo precedenti a Windows 10 non sono supportate e possono usare solo SSTP.

Per preparare Windows 10 o Server 2016 per IKEv2:

1. Installare l'aggiornamento.

   | Versione del sistema operativo | Data | Numero/collegamento |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 versione 1607 | 17 gennaio 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versione 1703 | 17 gennaio 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versione 1709 | 22 marzo 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Impostare il valore della chiave del Registro di sistema. Creare o `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` impostare REG_DWORD chiave del Registro di sistema su 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Errore del client VPN: Il messaggio ricevuto era imprevisto o con formattazione scorretta

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Il messaggio ricevuto è imprevisto o formattato in modo errato. (Errore 0x80090326)**

### <a name="cause"></a>Causa

Questo problema si verifica in presenza di una delle condizioni seguenti:

- Le route definite dall'utente con la route predefinita nella subnet del gateway sono impostate in modo non corretto.
- La chiave pubblica del certificato radice non viene caricata nel gateway VPN di Azure. 
- La chiave è danneggiata o scaduta.

### <a name="solution"></a>Soluzione

Per risolvere il problema, seguire questa procedura:

1. Rimuovere la route definita dall'utente nella subnet del gateway. Assicurarsi che la route definita dall'utente inoltri tutto il traffico correttamente.
2. Controllare lo stato del certificato radice nel portale di Azure per verificare che non sia stato revocato. Se non è stato revocato, provare a eliminare il certificato radice e a ricaricarlo. Per altre informazioni, vedere [Creare certificati](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Errore del client VPN: Una catena di certificati è stata elaborata correttamente, ma termina 

### <a name="symptom"></a>Sintomo 

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Una catena di certificati è stata elaborata correttamente, ma termina con un certificato radice considerato non attendibile dal provider di attendibilità.**

### <a name="solution"></a>Soluzione

1. Verificare che i certificati seguenti siano nel percorso corretto:

    | Certificato | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utente corrente\Personale\Certificati |
    | Azuregateway-*GUID*.cloudapp.net  | Utente corrente\Autorità di certificazione radice attendibili|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Computer locale\Autorità di certificazione radice attendibili|

2. Se i certificati sono già presenti nel percorso, provare a eliminarli e reinstallarli. Il certificato **azuregateway-*GUID*.cloudapp.net** si trova nel pacchetto di configurazione del client VPN scaricato dal portale di Azure. Per estrarre i file dal pacchetto, è possibile usare un archiver di file.

## <a name="file-download-error-target-uri-is-not-specified"></a>Errore di download del file: L'URI di destinazione non è specificato

### <a name="symptom"></a>Sintomo

Viene visualizzato il messaggio di errore seguente:

**Errore di download del file. L'URI di destinazione non è specificato.**

### <a name="cause"></a>Causa 

Questo problema si verifica a causa di un tipo di gateway non corretto. 

### <a name="solution"></a>Soluzione

Il tipo di gateway VPN deve essere **VPN** e il tipo di VPN deve essere **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Errore del client VPN: Azure VPN custom script failed (Impossibile eseguire lo script personalizzato per la VPN di Azure) 

### <a name="symptom"></a>Sintomo

Quando si cerca di connettersi alla rete virtuale di Azure usando il client VPN, viene visualizzato il messaggio di errore seguente:

**Lo script personalizzato (per aggiornare la tabella di routing) non è riuscito. (Errore 8007026f)**

### <a name="cause"></a>Causa

Questo problema può verificarsi se si prova ad aprire la connessione VPN da sito a punto usando un collegamento.

### <a name="solution"></a>Soluzione 

Aprire direttamente il pacchetto VPN invece di aprirlo dal collegamento.

## <a name="cannot-install-the-vpn-client"></a>Non è possibile installare il client VPN

### <a name="cause"></a>Causa 

È necessario un certificato aggiuntivo per considerare attendibile il gateway VPN per la rete virtuale. Il certificato è incluso nel pacchetto di configurazione del client VPN generato dal portale di Azure.

### <a name="solution"></a>Soluzione

Estrarre il pacchetto di configurazione del client VPN e trovare il file con estensione CER. Per installare il certificato, seguire questa procedura:

1. Aprire mmc.exe.
2. Aggiungere lo snap-in **Certificati**.
3. Selezionare l'account **Computer** per il computer locale.
4. Fare clic con il pulsante destro del mouse sul nodo **Autorità di certificazione radice attendibili**. Fare clic su**Importazione** **di tutte le attività** > e individuare il file CER estratto dal pacchetto di configurazione del client VPN.
5. Riavviare il computer. 
6. Provare a installare il client VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Errore del portale di Azure: Failed to save the VPN gateway, and the data is invalid (Impossibile salvare il gateway VPN. I dati non sono validi)

### <a name="symptom"></a>Sintomo

Quando si prova a salvare le modifiche per il gateway VPN nel portale di Azure, viene visualizzato il messaggio di errore seguente:

**Impossibile salvare il &lt; *nome*&gt;del gateway di rete virtuale. I dati &lt;per l'ID *certificate ID* &gt; certificato del certificato non sono validi.**

### <a name="cause"></a>Causa 

Questo problema può verificarsi se la chiave pubblica del certificato radice caricata contiene caratteri non validi, ad esempio uno spazio.

### <a name="solution"></a>Soluzione

Verificare che i dati nel certificato non contengano caratteri non validi, ad esempio interruzioni di riga, quali ritorni a capo. L'intero valore deve essere un'unica riga lunga. Il testo seguente è un esempio del certificato:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Errore del portale di Azure: Failed to save the VPN gateway, and the resource name is invalid (Non è stato possibile salvare il gateway VPN. Nome risorsa non valido)

### <a name="symptom"></a>Sintomo

Quando si prova a salvare le modifiche per il gateway VPN nel portale di Azure, viene visualizzato il messaggio di errore seguente: 

**Impossibile salvare il &lt; *nome*&gt;del gateway di rete virtuale. Il nome della risorsa *nome certificato che si tenta di caricare* &gt; non è valido. &lt;**

### <a name="cause"></a>Causa

Questo problema si verifica perché il nome del certificato contiene caratteri non validi, ad esempio uno spazio. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Errore del portale di Azure: VPN package file download error 503 (Errore 503 relativo al download del file del pacchetto VPN)

### <a name="symptom"></a>Sintomo

Quando si prova a scaricare il pacchetto di configurazione del client VPN, viene visualizzato il messaggio di errore seguente:

**Impossibile scaricare il file. Dettagli errore: errore 503. Il server è occupato.**
 
### <a name="solution"></a>Soluzione

Questo errore può essere causato da un problema di rete temporaneo. Provare di nuovo a scaricare il pacchetto VPN dopo alcuni minuti.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Aggiornamento del gateway VPN di Azure: tutti i client da punto a sito non riescono a connettersi

### <a name="cause"></a>Causa

Se il certificato ha superato il 50% del ciclo di vita, ne viene eseguito il rollover.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, scaricare nuovamente e ridistribuire il pacchetto Point to Site su tutti i client.

## <a name="too-many-vpn-clients-connected-at-once"></a>Troppi client VPN connessi contemporaneamente

È stato raggiunto il numero massimo di connessioni consentite. È possibile visualizzare il numero totale di client connessi nel portale di Azure.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Il client VPN non riesce ad accedere alle condivisioni file di rete

### <a name="symptom"></a>Sintomo

Il client VPN si è connesso alla rete virtuale di Azure, ma non riesce ad accedere alle condivisioni di rete.

### <a name="cause"></a>Causa

Per l'accesso alle condivisioni file, viene usato il protocollo SMB. Quando si avvia la connessione il client VPN aggiunge le credenziali della sessione e si verifica l'errore. Dopo che la connessione è stata stabilita, il client è forzato a usare le credenziali della cache per l'autenticazione Kerberos. Questo processo avvia le query al Centro distribuzione chiavi, ovvero un controller di dominio, per ottenere un token. Poiché il client si connette da Internet, potrebbe non riuscire a raggiungere il controller di dominio. Pertanto non riesce a effettuare il failover da Kerberos a NTLM. 

L'unica volta in cui vengono richieste le credenziali al client è quando dispone di un certificato valido, con SAN=UPN, rilasciato dal dominio in cui viene aggiunto. Il client deve anche essere fisicamente connesso alla rete di dominio. In questo caso, il client prova a usare il certificato e a raggiunge il controller di dominio. Il Centro distribuzione chiavi restituisce quindi un errore "KDC_ERR_C_PRINCIPAL_UNKNOWN". Il client deve effettuare il failover a NTLM. 

### <a name="solution"></a>Soluzione

Per risolvere il problema, disabilitare la memorizzazione nella cache delle credenziali di dominio dalla sottochiave del registro di sistema seguente: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Impossibile trovare la connessione VPN da punto a sito in Windows dopo la reinstallazione del client VPN

### <a name="symptom"></a>Sintomo

Si rimuove la connessione VPN da punto a sito e quindi si reinstalla il client VPN. In questo caso, la connessione VPN non viene configurata correttamente. La connessione VPN non viene visualizzata nelle impostazioni di **Connessioni di rete** in Windows.

### <a name="solution"></a>Soluzione

Per risolvere il problema, eliminare i file di configurazione del client VPN precedenti da **C:\Utenti\NomeUtente\AppData\Roaming\Microsoft\Network\Connections\<IDReteVirtuale>** e quindi eseguire di nuovo il programma di installazione del client VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Il client VPN da punto a sito non è in grado di risolvere il nome di dominio completo delle risorse nel dominio locale

### <a name="symptom"></a>Sintomo

Quando il client si connette ad Azure tramite una connessione VPN da punto a sito, non è in grado di risolvere il nome di dominio completo delle risorse nel dominio locale.

### <a name="cause"></a>Causa

Il client VPN da punto a sito usa i server DNS di Azure che vengono configurati nella rete virtuale di Azure. I server DNS di Azure hanno la precedenza sui server DNS locali che vengono configurati nel client, pertanto tutte le query DNS vengono inviate ai server DNS di Azure. Se i server DNS di Azure non dispongono di record per le risorse locali, la query ha esito negativo.

### <a name="solution"></a>Soluzione

Per risolvere il problema, assicurarsi che i server DNS di Azure usati nella rete virtuale di Azure siano in grado di risolvere i record DNS per le risorse locali. A tale scopo, è possibile usare server di inoltro DNS o server di inoltro condizionali. Per altre informazioni, vedere [Risoluzione dei nomi usando il server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>La connessione VPN da punto a sito viene stabilita, ma non è possibile connettersi alle risorse di Azure 

### <a name="cause"></a>Causa

Questo problema può verificarsi se il client VPN non ottiene le route dal gateway VPN di Azure.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, [reimpostare il gateway VPN di Azure](vpn-gateway-resetgw-classic.md). Per assicurarsi che vengano usate le nuove route, scaricare di nuovo i client VPN da punto a sito dopo la configurazione del peering della rete virtuale.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Errore: "La funzione di revoca non è in grado di completare il controllo di revoca perché il server di revoca è offline (errore 0x80092013)"

### <a name="causes"></a>Cause
Questo messaggio di errore viene visualizzato se il client non può accedere a http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.  La verifica delle revoche richiede l'accesso a questi due siti.  Questo problema si verifica in genere nel client che ha un server proxy configurato. In alcuni ambienti se le richieste non passano attraverso il server proxy, verranno rifiutate a livello di firewall di confine.

### <a name="solution"></a>Soluzione

Controllare le impostazioni del server proxy, assicurarsi che il client possa accedere a http://crl3.digicert.com/ssca-sha2-g1.crl e http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Errore del client VPN: Impossibile stabilire la connessione a causa di un criterio configurato nel server RAS/VPN (errore 812)

### <a name="cause"></a>Causa

Questo errore si verifica se il server RADIUS usato per l'autenticazione del client VPN ha impostazioni non corrette o se il gateway di Azure non è in grado di raggiungere il server Radius.

### <a name="solution"></a>Soluzione

Assicurarsi che il server RADIUS sia configurato correttamente. Per altre informazioni, vedere [Integrare l'autenticazione con il server Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Errore 405" quando si scarica il certificato radice dal gateway VPN

### <a name="cause"></a>Causa

Il certificato radice non è stato installato. Il certificato radice è installato nell'archivio **certificati attendibili** del client.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Errore del client VPN: Impossibile stabilire la connessione remota. Tentativi di tunnel VPN non riusciti (errore 800) 

### <a name="cause"></a>Causa

Il driver della scheda di interfaccia di rete non è aggiornato.

### <a name="solution"></a>Soluzione

Aggiornare il driver della scheda di interfaccia di rete:

1. Fare clic su **Start**, digitare **Gestione dispositivi** e selezionarlo dall'elenco dei risultati. Se viene chiesto di immettere una password dell'amministratore o è richiesta una conferma, digitare la password o fornire la conferma.
2. Nelle categorie **Schede di rete** trovare la scheda di interfaccia di rete che si vuole aggiornare.  
3. Fare doppio clic sul nome del dispositivo, selezionare **Aggiorna driver** e quindi **Cerca automaticamente un driver aggiornato**.
4. Se Windows non trova un nuovo driver, è possibile cercarne uno nel sito Web del produttore del dispositivo e seguire le istruzioni.
5. Riavviare il computer e riprovare la connessione.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Errore del client VPN: <VPN Connection Name>connessione VPN di composizione , Stato e piattaforma VPN non ha attivato la connessione

È inoltre possibile che venga visualizzato il seguente errore <User> nel Visualizzatore <VPN Connection Name> eventi da RasClient: "L'utente ha registrato una connessione denominata non riuscita. Il codice di errore restituito in caso di errore è 1460."

### <a name="cause"></a>Causa

Il client VPN di Azure non ha l'autorizzazione dell'app "App in background" abilitata in Impostazioni app per Windows.

### <a name="solution"></a>Soluzione

1. In Windows, vai a Impostazioni -> Privacy -> App in background
2. Impostare l'opzione "Consenti l'esecuzione delle app in background" su Attivato

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Errore: "Errore di download del file. L'URI di destinazione non è specificato"

### <a name="cause"></a>Causa

Questo problema è causato dalla configurazione di un tipo di gateway non corretto.

### <a name="solution"></a>Soluzione

Il tipo di gateway VPN di Azure deve essere VPN e il tipo di VPN deve essere **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Il programma di installazione del pacchetto VPN non viene completato

### <a name="cause"></a>Causa

Questo problema può essere causato dalle installazioni di client VPN precedenti. 

### <a name="solution"></a>Soluzione

Eliminare i file di configurazione dei client VPN precedenti da **C:\Utenti\NomeUtente\AppData\Roaming\Microsoft\Network\Connections\<IDReteVirtuale>** ed eseguire di nuovo il programma di installazione del client VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Il client VPN entra in stato di ibernazione o va in sospensione dopo un certo periodo di tempo

### <a name="solution"></a>Soluzione

Controllare le impostazioni relative alla sospensione e all'ibernazione nel computer in cui è in esecuzione il client VPN.
