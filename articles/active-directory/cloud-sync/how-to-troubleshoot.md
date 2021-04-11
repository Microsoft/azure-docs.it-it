---
title: Azure AD Connect la risoluzione dei problemi di sincronizzazione cloud
description: Questo articolo descrive come risolvere i problemi che possono verificarsi con l'agente di provisioning cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 174ec8c42ea17ccae04769d7c0baaa91b8e7025b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517872"
---
# <a name="cloud-sync-troubleshooting"></a>Risoluzione dei problemi di sincronizzazione cloud

La sincronizzazione cloud riguarda numerosi fattori e ha varie dipendenze diverse. L'ampiezza di questo ambito può comportare diversi problemi. Questo articolo consente di risolvere questi problemi. Illustra le aree più comuni su cui concentrarsi, come raccogliere informazioni aggiuntive e le diverse tecniche che si possono usare per tenere traccia dei problemi.


## <a name="common-troubleshooting-areas"></a>Aree comuni per la risoluzione dei problemi

|Nome|Descrizione|
|-----|-----|
|[Problemi relativi all'agente](#agent-problems)|Verificare che l'agente sia stato installato correttamente e che comunichi con Azure Active Directory (Azure AD).|
|[Problemi di sincronizzazione degli oggetti](#object-synchronization-problems)|Usare i log di provisioning per risolvere i problemi di sincronizzazione degli oggetti.|
|[Provisioning di problemi in quarantena](#provisioning-quarantined-problems)|Informazioni sui problemi di quarantena per il provisioning e su come risolverli.|


## <a name="agent-problems"></a>Problemi relativi all'agente
Di seguito sono riportate alcune delle prime operazioni da verificare con l'agente:

-  È installato?
-  L'agente è in esecuzione in locale?
-  L'agente è presente nel portale?
-  L'agente è contrassegnato come integro?

Questi elementi possono essere verificati nella portale di Azure e nel server locale in cui è in esecuzione l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure

Per verificare che l'agente sia visualizzato da Azure ed è integro, attenersi alla seguente procedura.

1. Accedere al portale di Azure.
1. Sulla sinistra, selezionare **Azure Active Directory** > **Azure AD Connect**. In centro selezionare **Gestisci sincronizzazione**.
1. Nella schermata **Azure ad Connect sincronizzazione cloud** selezionare **verifica tutti gli agenti**.

   ![Esaminare tutti gli agenti](media/how-to-install/install-7.png)</br>
 
1. Nella schermata **agenti di provisioning locali** vengono visualizzati gli agenti installati. Verificare che l'agente in questione sia presente ed è contrassegnato come *integro*.

   ![Schermata Agenti di provisioning locali](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Verificare la porta

Verificare che Azure sia in ascolto sulla porta 443 e che l'agente sia in grado di comunicare con esso. 

Questo test verifica che gli agenti possano comunicare con Azure tramite la porta 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità della porta](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Nel server locale

Per verificare se l'agente è in esecuzione, seguire questa procedura.

1. Nel server in cui è installato l'agente, aprire **Servizi** passando a tale server oppure **avviando**  >  **Esegui**  >  **Services. msc**.
1. In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia *In esecuzione*.

   ![Schermata Servizi](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Problemi comuni di installazione dell'agente

Nelle sezioni seguenti vengono descritti alcuni problemi comuni di installazione dell'agente e le risoluzioni tipiche.

#### <a name="agent-failed-to-start"></a>Impossibile avviare l'agente

È possibile che venga visualizzato un messaggio di errore che indica:

**Impossibile avviare il servizio ' Microsoft Azure AD connettere l'agente di provisioning '. Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema.** 

Questo problema è in genere causato da criteri di gruppo che impediscono l'applicazione delle autorizzazioni all'account di accesso locale del servizio NT creato dal programma di installazione (NT SERVICE\AADConnectProvisioningAgent). Queste autorizzazioni sono necessarie per avviare il servizio.

Per risolvere il problema, attenersi alla seguente procedura.

1. Accedere al server con un account amministratore.
1. Aprire **Servizi** passando all'opzione relativa oppure selezionando **Start** > **Esegui** > **Services.msc**.
1. In **Servizi** fare doppio clic su **Microsoft Azure ad Connetti agente di provisioning**.
1. Nella scheda **accesso** modificare **questo account** in un amministratore di dominio. Riavviare quindi il servizio. 

   ![Scheda accesso](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Timeout dell'agente o certificato non valido

Quando si tenta di registrare l'agente, è possibile che venga ricevuto il messaggio di errore seguente.

![Messaggio di errore di timeout](media/how-to-troubleshoot/troubleshoot-4.png)

Questo problema in genere è dovuto al fatto che l'agente non riesce a connettersi al servizio di gestione delle identità ibride e richiede la configurazione di un proxy HTTP. Per risolvere il problema, configurare un proxy in uscita. 

L'agente di provisioning supporta l'uso di un proxy in uscita. È possibile configurarlo modificando il file di configurazione dell'agente *c:\programmi\microsoft Azure ad Connect provisioning Agent\AADConnectProvisioningAgent.exe.config*. Aggiungere le righe seguenti, verso la fine del file immediatamente prima del tag di chiusura `</configuration>` .
Sostituire le variabili `[proxy-server]` e `[proxy-port]` con i valori di nome e porta del server proxy.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>La registrazione dell'agente non riesce con errore di sicurezza

Quando si installa l'agente di provisioning cloud, è possibile che venga ricevuto un messaggio di errore.

Questo problema in genere è dovuto al fatto che l'agente non è in grado di eseguire gli script di registrazione di PowerShell a causa dei criteri di esecuzione di PowerShell locali.

Per risolvere questo problema, modificare i criteri di esecuzione di PowerShell nel server. È necessario che i criteri computer e utente siano impostati come *undefined* o *RemoteSigned*. Se sono impostati come *senza restrizioni*, verrà visualizzato questo errore. Per altre informazioni, vedere [criteri di esecuzione di PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>File di registro

Per impostazione predefinita, l'agente genera un numero ridotto di messaggi di errore e informazioni minime di analisi dello stack. È possibile trovare i log di traccia nella cartella **C:\ProgramData\Microsoft\Azure il provisioning di AGENT\TRACE ad Connect**.

Per raccogliere ulteriori dettagli per la risoluzione dei problemi relativi agli agenti, attenersi alla seguente procedura.

1.  Installare il modulo AADCloudSyncTools di PowerShell come descritto [qui](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. Usare il `Export-AADCloudSyncToolsLogs` cmdlet di PowerShell per acquisire le informazioni.  Per ottimizzare la raccolta dei dati, è possibile usare le opzioni seguenti.
      - SkipVerboseTrace esportare solo i log correnti senza acquisire i log dettagliati (impostazione predefinita = false)
      - TracingDurationMins per specificare una durata di acquisizione diversa (impostazione predefinita: 3 minuti)
      - OutputPath per specificare un percorso di output diverso (impostazione predefinita = documenti dell'utente)


## <a name="object-synchronization-problems"></a>Problemi di sincronizzazione degli oggetti

La sezione seguente contiene informazioni sulla risoluzione dei problemi di sincronizzazione degli oggetti.

### <a name="provisioning-logs"></a>Log di provisioning

Nella portale di Azure è possibile usare i log di provisioning per individuare e risolvere i problemi di sincronizzazione degli oggetti. Per visualizzare i log, selezionare **log**.

![Pulsante Log](media/how-to-troubleshoot/log-1.png)

I log di provisioning forniscono un'ampia gamma di informazioni sullo stato degli oggetti sincronizzati tra l'ambiente di Active Directory locale e Azure.

![Schermata dei log di provisioning](media/how-to-troubleshoot/log-2.png)

È possibile utilizzare le caselle di riepilogo a discesa nella parte superiore della pagina per filtrare la visualizzazione in base a problemi specifici, ad esempio le date. Fare doppio clic su un singolo evento per visualizzare informazioni aggiuntive.

![Informazioni sulla casella di riepilogo a discesa log di provisioning](media/how-to-troubleshoot/log-3.png)

Queste informazioni forniscono i passaggi dettagliati e la posizione in cui si verifica il problema di sincronizzazione. In questo modo, è possibile individuare il punto esatto del problema.


## <a name="provisioning-quarantined-problems"></a>Provisioning di problemi in quarantena

Cloud Sync monitora l'integrità della configurazione e inserisce oggetti non integri in uno stato di quarantena. Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore, ad esempio, credenziali di amministratore non valide, il processo di sincronizzazione è contrassegnato come in quarantena.

![Stato di quarantena](media/how-to-troubleshoot/quarantine-1.png)

Selezionando lo stato, è possibile visualizzare informazioni aggiuntive sulla quarantena. È anche possibile ottenere il codice di errore e il messaggio.

![Screenshot che mostra informazioni aggiuntive sulla quarantena.](media/how-to-troubleshoot/quarantine-2.png)

Facendo clic con il pulsante destro del mouse sullo stato si otterranno opzioni aggiuntive:
    
   - visualizzare i log di provisioning
   - Visualizza agente
   - Cancella quarantena

![Screenshot che mostra le opzioni del menu di scelta rapida.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Risolvere una quarantena
Esistono due modi diversi per risolvere la quarantena.  Ad esempio:

  - Cancella quarantena: Cancella la filigrana ed esegue una sincronizzazione Delta
  - riavvia il processo di provisioning: Cancella la filigrana ed esegue una sincronizzazione iniziale

#### <a name="clear-quarantine"></a>Cancella quarantena
Per cancellare la filigrana ed eseguire una sincronizzazione delta nel processo di provisioning dopo averla verificata, è sufficiente fare clic con il pulsante destro del mouse sullo stato e scegliere **Cancella quarantena**.

Si noterà che la quarantena sta per essere cancellata.

![Screenshot che mostra l'avviso che la quarantena sta cancellando.](media/how-to-troubleshoot/quarantine-5.png)

Quindi lo stato dell'agente verrà visualizzato come integro.

![Informazioni sullo stato della quarantena](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Riavviare il processo di provisioning
Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina Configurazione agente selezionare **Riavvia provisioning**.

  ![Riavviare il provisioning](media/how-to-troubleshoot/quarantine-3.png)

- Usare Microsoft Graph per [riavviare il processo di provisioning](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Si avrà il controllo completo su ciò che viene riavviato. È possibile scegliere di cancellare:
  - Escrow, per riavviare il contatore del deposito per la quarantena.
  - Quarantena, per rimuovere l'applicazione dalla quarantena.
  - Filigrane. 
  
  Usare la richiesta seguente:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Ripristino dell'account del servizio di sincronizzazione cloud
Se è necessario ripristinare l'account del servizio di sincronizzazione cloud, è possibile usare `Repair-AADCloudSyncToolsAccount` .  


   1.  Usare la procedura di installazione illustrata di [seguito](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) per iniziare e continuare con i passaggi rimanenti.
   2.  Da una sessione di Windows PowerShell con privilegi amministrativi, digitare o copiare e incollare quanto segue: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Immettere le credenziali di amministratore globale Azure AD
   4. Digitare o copiare e incollare quanto segue: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Al termine dell'operazione, l'account è stato ripristinato correttamente.

## <a name="next-steps"></a>Passaggi successivi 

- [Limitazioni note](how-to-prerequisites.md#known-limitations)
- [Codici di errore](reference-error-codes.md)
