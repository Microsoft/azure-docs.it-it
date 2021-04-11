---
title: Risolvere i problemi di distribuzione di un dispositivo StorSimple serie 8000 | Microsoft Docs
description: In questo articolo viene descritto come diagnosticare e correggere gli errori che si verificano durante la prima distribuzione di StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566346"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Risoluzione dei problemi di distribuzione del dispositivo StorSimple
## <a name="overview"></a>Panoramica
In questo articolo viene fornita una guida alla risoluzione dei problemi riguardo la distribuzione di Microsoft Azure StorSimple. Vengono descritti problemi comuni, le possibili cause e le procedure consigliate per la risoluzione dei problemi che potrebbero verificarsi durante la configurazione di StorSimple. 

Queste informazioni si applicano sia al dispositivo fisico StorSimple serie 8000 che all'appliance cloud StorSimple.

> [!NOTE]
> Problemi relativi alla configurazione del dispositivo potrebbero verificarsi durante la prima distribuzione o in seguito, durante l'utilizzo del dispositivo. Questo articolo è incentrato sulla risoluzione dei problemi durante la prima distribuzione. Per risolvere un problema relativo al funzionamento del dispositivo, passare alla sezione su come [usare lo strumento di diagnostica per risolvere i problemi di funzionamento di un dispositivo](storsimple-8000-diagnostics.md).

Inoltre, nell'articolo vengono descritti gli strumenti per la risoluzione dei problemi di distribuzioni di StorSimple e viene fornito un esempio di procedura dettagliata di risoluzione dei problemi.

## <a name="first-time-deployment-issues"></a>Problemi relativi alla prima distribuzione
Se si verifica un problema quando si distribuisce il dispositivo per la prima volta, prendere in considerazione le indicazioni seguenti:

* Se si sta eseguendo la risoluzione die problemi di un dispositivo fisico, assicurarsi che l'hardware sia stato installato e configurato come descritto in [Installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Controllare i prerequisiti per la distribuzione. Assicurarsi di disporre di tutte le informazioni descritte nel [elenco di controllo configurazione distribuzione](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Controllare le note sulla versione di StorSimple per vedere se il problema è stato descritto. Le note sulla versione includono soluzioni alternative per i problemi di installazione noti. 

Durante la distribuzione del dispositivo, i problemi più comuni riscontrati dagli utenti, si verificano quando si esegue la configurazione guidata e quando si registra il dispositivo tramite Windows PowerShell per StorSimple. Utilizzare Windows PowerShell per StorSimple per registrare e configurare il dispositivo StorSimple. Per ulteriori informazioni sulla registrazione del dispositivo, vedere [passaggio 3: configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Le sezioni seguenti possono essere utili per risolvere i problemi riscontrati quando si configura il dispositivo StorSimple per la prima volta.

## <a name="first-time-setup-wizard-process"></a>Procedura di configurazione guidata per la prima configurazione
Nei passaggi seguenti viene riepilogato il processo di configurazione guidata. Per informazioni dettagliate sull'installazione, vedere [Distribuire un dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md).

1. Eseguire il cmdlet [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) per avviare la configurazione guidata con le istruzioni relative ai restanti passaggi. 
2. Configurare la rete: la configurazione guidata consente di configurare le impostazioni di rete per l'interfaccia di rete DATA 0 sul dispositivo StorSimple. Sono incluse le impostazioni seguenti:
   * IP virtuale (VIP), subnet mask e gateway: il cmdlet [Set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) viene eseguito in background. Configura indirizzo IP, subnet mask e gateway per l'interfaccia di rete DATA 0 nel dispositivo StorSimple.
   * Server DNS primario: il cmdlet [Set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) viene eseguito in background. Configura le impostazioni DNS per la soluzione StorSimple.
   * Server DNS: il cmdlet [Set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) viene eseguito in background. Configura le impostazioni del server NTP per la soluzione StorSimple.
   * Proxy Web facoltativo: il cmdlet [Set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) viene eseguito in background. Imposta e abilita la configurazione del proxy Web per la soluzione StorSimple.
3. Impostare la password: il passaggio successivo consiste nell'impostare la password amministratore del dispositivo.
   La password di amministratore del dispositivo viene utilizzata per accedere al dispositivo. La password predefinita è **Password1**.
        
     > [!IMPORTANT]
     > Sebbene vengano raccolte prima della registrazione, le password vengono applicate solo dopo aver correttamente registrato il dispositivo. Se si verifica un errore di applicazione della password, verrà richiesto di immetterla nuovamente fino a quando non verranno raccolte le password richieste (che soddisfano i requisiti di complessità).
     
4. Registrare il dispositivo: il passaggio finale consiste nel registrare il dispositivo con il servizio Gestione dispositivi StorSimple in esecuzione su Microsoft Azure. Per la registrazione, è necessario [ottenere il codice di registrazione del servizio](storsimple-8000-manage-service.md#get-the-service-registration-key) dal portale di Azure e specificarlo nella configurazione guidata. **Dopo che il dispositivo è stato registrato correttamente, viene fornita una chiave di crittografia dei dati del servizio. Assicurarsi di tenere questa chiave di crittografia in un luogo sicuro perché sarà necessaria per registrare tutti i dispositivi futuri con il servizio.**

## <a name="common-errors-during-device-deployment"></a>Errori comuni durante la distribuzione del dispositivo
Le tabelle seguenti elencano gli errori più comuni che possono essere riscontrati quando si:

* Configurazione delle impostazioni di rete necessarie.
* Configurazione delle impostazioni del proxy Web facoltativo.
* Impostazione della password amministratore del dispositivo.
* Registrazione del dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Errori durante le impostazioni di rete necessarie
| No. | Messaggio di errore | Possibili cause | Azione consigliata |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: questo comando può essere eseguito solo sul controller attivo. |La configurazione veniva eseguita solo sul controller passivo. |Eseguire il comando dal controller attivo. Per altre informazioni, vedere [Identificare un controller attivo sul dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: dispositivo non pronto. |Sono presenti problemi con la connettività di rete su DATA 0. |Controllare la connettività di rete fisica in DATA 0. |
| 3 |Invoke-HcsSetupWizard: è presente un conflitto di indirizzo IP con un altro sistema della rete (eccezione da HRESULT: 0x80070263). |L'IP fornito per i DATA 0 era già utilizzato da un altro sistema. |Fornire un nuovo IP non in uso. |
| 4 |Invoke-HcsSetupWizard: errore di risorsa cluster (eccezione da HRESULT: 0x800713AE). |Duplica VIP. L'IP fornito è già in uso. |Fornire un nuovo IP non in uso. |
| 5 |Invoke-HcsSetupWizard: indirizzo IPv4 non valido. |L'indirizzo IP è stato fornito in un formato non corretto. |Controllare il formato e fornire nuovamente l'indirizzo IP. Per altre informazioni, vedere [Ipv4 Addressing][1] (Indirizzamento Ipv4). |
| 6 |Invoke-HcsSetupWizard: indirizzo IPv6 non valido. |L'indirizzo IP è stato fornito in un formato non corretto. |Controllare il formato e fornire nuovamente l'indirizzo IP. Per altre informazioni, vedere [Ipv6 Addressing][2] (Indirizzamento Ipv6). |
| 7 |Richiamare HcsSetupWizard: non esistono Nessun endpoint disponibile nel mapping degli endpoint. (Eccezione da HRESULT: 0x800706D9) |La funzionalità del cluster non funziona. |[Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per i passaggi successivi. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Errori durante le impostazioni del proxy web facoltative
| No. | Messaggio di errore | Possibili cause | Azione consigliata |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: parametro non valido (eccezione da HRESULT: 0x80070057) |Uno dei parametri forniti per le impostazioni proxy non è valido. |URI non fornito nel formato corretto. Usare il formato seguente: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: server RPC non disponibile (eccezione da HRESULT: 0x800706ba) |La causa principale è una delle seguenti:<ol><li>Il cluster non è attivo.</li><li>Il controller passivo non può comunicare con quello attivo e il comando viene eseguito dal controller passivo.</li></ol> |A seconda della causa principale:<ol><li>[Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per assicurarsi che il cluster sia attivo.</li><li>Eseguire il comando dal controller attivo. Se si desidera eseguire il comando dal controller passivo, è necessario verificare che il controller passivo sia in grado di comunicare con il controller attivo. Se la connettività è interrotta, è necessario [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md).</li></ol> |
| 3 |Invoke-HcsSetupWizard: chiamata RPC non riuscita (eccezione da HRESULT: 0x800706be) |Cluster non attivo. |[Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per assicurarsi che il cluster sia attivo. |
| 4 |Invoke-HcsSetupWizard: risorsa cluster non trovata (eccezione da HRESULT: 0x8007138f) |La risorsa del cluster non è stata trovata. Questa situazione può verificarsi in caso di installazione non corretta. |Potrebbe essere necessario ripristinare le impostazioni predefinite del dispositivo. [Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per creare una risorsa cluster. |
| 5 |Invoke-HcsSetupWizard: risorsa cluster non online (eccezione da HRESULT: 0x8007138c) |Le risorse del cluster non sono in linea. |[Contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per i passaggi successivi. |

## <a name="errors-related-to-device-administrator-password"></a>Errori legati alla password amministratore del dispositivo
La password di amministratore del dispositivo predefinita è **Password1**. La password scade dopo il primo accesso. Pertanto, sarà necessario utilizzare la configurazione guidata per modificarla. Quando si registra il dispositivo per la prima volta, è necessario fornire una nuova password di amministratore del dispositivo. 

Verificare che le password soddisfino i requisiti seguenti:

* La password dell'amministratore del dispositivo deve avere una lunghezza compreso tra 8 e 15 caratteri.
* Le password devono contenere tre dei tipi di carattere seguenti: lettere minuscole, maiuscole, numeri e speciali. 
* La password non può essere uguale alle ultime 24 password.

Inoltre, considerare che le password scadono ogni anno e possono essere modificate solo dopo aver registrato correttamente il dispositivo. Se la registrazione non riesce per qualsiasi motivo, le password non verranno modificate.

Per altre informazioni sulla password amministratore del dispositivo, vedere [Usare il servizio Gestione dispositivi StorSimple per modificare le password di StorSimple](storsimple-8000-change-passwords.md).

È possibile riscontrare uno o più dei seguenti errori quando si configurano le password di amministratore del dispositivo e Gestione snapshot StorSimple.

| No. | Messaggio di errore | Azione consigliata |
| --- | --- | --- |
| 1 |La password supera la lunghezza massima. |La password di amministratore dispositivo deve avere una lunghezza compresa tra gli 8 e i 15 caratteri. |
| 2 |La password non soddisfa la lunghezza richiesta. |La password di amministratore dispositivo deve avere una lunghezza compresa tra gli 8 e i 15 caratteri.|
| 3 |La password deve contenere caratteri minuscoli. |Le password devono contenere 3 dei seguenti 4 tipi di carattere: minuscole, maiuscole, numeri e caratteri speciali. Assicurarsi che la password soddisfi tali requisiti. |
| 4 |La password deve contenere caratteri numerici. |Le password devono contenere 3 dei seguenti 4 tipi di carattere: minuscole, maiuscole, numeri e caratteri speciali. Assicurarsi che la password soddisfi tali requisiti. |
| 5 |La password deve contenere caratteri speciali. |Le password devono contenere 3 dei seguenti 4 tipi di carattere: minuscole, maiuscole, numeri e caratteri speciali. Assicurarsi che la password soddisfi tali requisiti. |
| 6 |La password deve contenere 3 dei seguenti 4 tipi di carattere: maiuscole, minuscole, numeri e caratteri speciali. |La password non contiene i tipi di carattere richiesti. Assicurarsi che la password soddisfi tali requisiti. |
| 7 |Il parametro non corrisponde alla conferma. |Assicurarsi che la password soddisfi tutti i requisiti e che sia stata immessa correttamente. |
| 8 |La password non può corrispondere a quella predefinita. |La password predefinita è *Password1*. È necessario modificare la password dopo aver effettuato l'accesso per la prima volta. |
| 9 |La password immessa non corrisponde alla password del dispositivo. Digitare nuovamente la password. |Controllare la password e digitarla nuovamente. |

Le password vengono raccolte prima della registrazione del dispositivo ma vengono applicate solo dopo la corretta registrazione. Il flusso di lavoro di ripristino della password richiede che il dispositivo sia registrato.

> [!IMPORTANT]
> In generale, se un tentativo di applicazione della password non riesce, il software tenta ripetutamente di raccogliere le password fino a ottenere un esito positivo. In rari casi, non è possibile applicare la password. In tale situazione, è possibile registrare il dispositivo e procedere; tuttavia le password non verranno modificate. È possibile modificare la password amministratore del dispositivo dopo la registrazione dal portale di Azure.


È possibile reimpostare la password nel portale di Azure tramite il servizio Gestione dispositivi StorSimple. Per altre informazioni, vedere [Modificare la password amministratore del dispositivo](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Errori durante la registrazione del dispositivo
Per registrare il dispositivo, utilizzare il servizio Gestione dispositivi StorSimple in esecuzione su Microsoft Azure. Durante la registrazione del dispositivo, potrebbero essere riscontrati uno o più dei seguenti problemi.

| No. | Messaggio di errore | Possibili cause | Azione consigliata |
| --- | --- | --- | --- |
| 1 |Errore 350027: impossibile registrare il dispositivo con Gestione dispositivi StorSimple. | |Attendere alcuni minuti e ripetere l'operazione. Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Errore 350013: si è verificato un errore nella registrazione del dispositivo. L'errore potrebbe essere dovuto a una chiave di registrazione del servizio non corretta. | |Registrare nuovamente il dispositivo con la chiave di registrazione del servizio corretta. Per altre informazioni, vedere [Ottenere la chiave di registrazione del servizio.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Errore 350063: l'autenticazione al servizio Gestione dispositivi StorSimple è riuscita ma la registrazione no. Ripetere l'operazione dopo alcuni minuti. |Questo errore indica che l'autenticazione con ACS è riuscita ma non la chiamata di registro effettuata al servizio. Potrebbe trattarsi di un risultato di un problema di rete sporadico. |Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Errore 350049: impossibile raggiungere il servizio durante la registrazione. |Quando viene effettuata la chiamata al servizio, viene ricevuta un'eccezione Web. In alcuni casi, il problema potrebbe essere risolto ripetendo l'operazione in un secondo momento. |Controllare l'indirizzo IP e il nome DNS, quindi ripetere l'operazione. Se il problema persiste, [contattare supporto tecnico Microsoft.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Errore 350031: il dispositivo è già stato registrato. | |Nessuna azione necessaria. |
| 6 |Errore 350016: registrazione del dispositivo non riuscita. | |Assicurarsi che il codice di registrazione sia corretto. |
| 7 |Invoke-HcsSetupWizard: si è verificato un errore durante la registrazione del dispositivo. La causa potrebbe essere l’indirizzo IP o il nome DNS non corretto. Verificare le impostazioni di rete e riprovare. Se il problema persiste, [contact Microsoft Support](storsimple-8000-contact-microsoft-support.md). [ERROR] 350050 |Assicurarsi che il dispositivo possa eseguire il ping della rete esterna. Se non si dispone di connettività per una rete esterna, potrebbe verificarsi questo errore e la mancata registrazione. L'errore potrebbe derivare da una combinazione di una o più delle seguenti cause:<ul><li>IP non corretto</li><li>Subnet non corretta</li><li>Gateway non corretto</li><li>Impostazioni DNS non corrette</li></ul> |Vedere i passaggi nell' [esempio dettagliato di risoluzione dei problemi](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: l'operazione corrente non è riuscita a causa di un errore interno del servizio [0x1FBE2]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft. |Si tratta di un errore generico generato per tutti gli errori invisibili all'utente dal servizio o agente. La ragione più comune potrebbe essere che l'autenticazione ACS non è riuscita. Una possibile causa dell'errore è che si verificano problemi con la configurazione del server NTP e che l'orario nel dispositivo non è impostato correttamente. |Correggere l'orario (in caso di problemi) e ripetere l'operazione di registrazione. Se si usa il comando Set-HcsSystem -Timezone per modificare il fuso orario, usare l'iniziale maiuscola per ciascuna parola del fuso orario, ad esempio "Ora Solare Pacifico".  Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per ottenere informazioni sui passaggi successivi. |
| 9 |Avviso: impossibile attivare il dispositivo. Le password di amministratore dispositivo e Gestione snapshot StorSimple non sono state modificate. |Se la registrazione ha esito negativo, le password di amministratore dispositivo e Gestione snapshot StorSimple non sono state modificate. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Strumenti per la risoluzione dei problemi di distribuzioni di StorSimple
StorSimple include numerosi strumenti che è possibile utilizzare per risolvere i problemi della soluzione di StorSimple. Questi strumenti comprendono:

* Pacchetti di supporto e log del dispositivo.
* Cmdlet progettati per la risoluzione dei problemi.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pacchetti di supporto e log del dispositivo disponibili per la risoluzione dei problemi
Un pacchetto di supporto contiene tutti i log pertinenti per assistere il team del supporto tecnico Microsoft durante la risoluzione dei problemi del dispositivo. È possibile utilizzare Windows PowerShell per StorSimple per generare un pacchetto di supporto crittografato che può essere condiviso con il personale di supporto.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Per visualizzare i log o i contenuti del pacchetto di supporto
1. Utilizzare Windows PowerShell per StorSimple per generare un pacchetto di supporto, come descritto in [creare e gestire un pacchetto di supporto](storsimple-8000-create-manage-support-package.md).
2. Scaricare localmente lo [script di decrittografia](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) sul computer client.
3. Utilizzare questa [procedura dettagliata](storsimple-8000-create-manage-support-package.md#edit-a-support-package) per aprire e decrittografare il pacchetto di supporto.
4. I log del pacchetto di supporto decrittografato sono in formato etw/etvx. È possibile eseguire la procedura seguente per visualizzare questi file in Visualizzatore eventi di Windows:
   
   1. Eseguire il comando **eventvwr** nel client Windows per avviare il Visualizzatore eventi.
   2. Nel riquadro **Azioni** fare clic su **Apri registro salvato** e scegliere i file di log in formato etvx/etw (il pacchetto per il supporto). Ora è possibile visualizzare il file. Dopo aver aperto il file, è possibile fare clic con il pulsante destro del mouse e salvare il file come testo.
      
      > [!IMPORTANT]
      > È inoltre possibile utilizzare il cmdlet **Get-WinEvent** per aprire tali file in Windows PowerShell. Per altre informazioni, vedere [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) nella documentazione di riferimento del cmdlet di Windows PowerShell.
     
5. Quando i log vengono aperti nel Visualizzatore eventi, cercare i log seguenti dove sono presenti i problemi relativi alla configurazione del dispositivo:
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. Nei file di registro, ricercare le stringhe relative ai cmdlet chiamati dalla configurazione guidata. Vedere [Configurazione guidata prima installazione](#first-time-setup-wizard-process) per un elenco di tali cmdlet.
7. Se non si riesce a determinare la causa del problema, è possibile [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per avere informazioni sui passaggi successivi da eseguire. Utilizzare i passaggi in [creare una richiesta di supporto](storsimple-8000-contact-microsoft-support.md#create-a-support-request) quando si contatta il supporto Microsoft per assistenza.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet disponibili per la risoluzione dei problemi
Utilizzare i seguenti cmdlet di Windows PowerShell per rilevare errori di connettività.

* `Get-NetAdapter`: Utilizzare questo cmdlet per rilevare lo stato delle interfacce di rete.
* `Test-Connection`: Utilizzare questo cmdlet per verificare la connettività di rete all'interno e all'esterno della rete.
* `Test-HcsmConnection`: Utilizzare questo cmdlet per verificare la connettività di un dispositivo registrato correttamente.
* `Sync-HcsTime`: Utilizzare questo cmdlet per visualizzare l'ora del dispositivo e forzare la sincronizzazione dell'ora con il server NTP.
* `Enable-HcsPing` e `Disable-HcsPing`: utilizzare questi cmdlet per consentire gli host eseguire il ping le interfacce di rete nel dispositivo StorSimple. Per impostazione predefinita, le interfacce di rete StorSimple non rispondono alle richieste di ping.
* `Trace-HcsRoute`: Usare questo cmdlet come uno strumento di analisi della route. Invia pacchetti a ogni router nel percorso verso la destinazione finale in un periodo di tempo e calcola quindi i risultati in base ai pacchetti restituiti da ogni hop. Poiché `Trace-HcsRoute` Mostra il grado di perdita di pacchetti in un dato router o collegamento, è possibile individuare quali router o collegamenti possono causare problemi di rete.
* `Get-HcsRoutingTable`: Utilizzare questo cmdlet per visualizzare la tabella di routing IP locale.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Risoluzione dei problemi con il cmdlet Get-NetAdapter
Quando si configurano le interfacce di rete per una prima distribuzione del dispositivo, lo stato dell'hardware non è disponibile nell'interfaccia utente del servizio Gestione dispositivi StorSimple perché il dispositivo non è ancora registrato con il servizio. Inoltre, il pannello **Integrità hardware** potrebbe non riflettere correttamente lo stato del dispositivo, soprattutto in caso di problemi che influiscono sulla sincronizzazione del servizio. In queste situazioni, è possibile utilizzare il `Get-NetAdapter` cmdlet per determinare l'integrità e lo stato delle interfacce di rete.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Per visualizzare un elenco di tutte le schede di rete sul dispositivo
1. Avviare Windows PowerShell per StorSimple e quindi digitare `Get-NetAdapter`. 
2. Utilizzare l'output del `Get-NetAdapter` cmdlet e le linee guida seguenti per conoscere lo stato dell'interfaccia di rete.
   
   * Se l'interfaccia è integra e abilitato, come stato **ifIndex** viene visualizzato **Up**.
   * Se l'interfaccia è integra ma non è fisicamente collegata (tramite un cavo di rete), come stato **ifIndex** viene visualizzato **Disabled**.
   * Se l'interfaccia è integra ma non abilitata, come stato **ifIndex** viene visualizzato **NotPresent**.
   * Se l'interfaccia non esiste, non viene visualizzata nell'elenco. L'interfaccia utente del servizio Gestione dispositivi StorSimple viene comunque visualizzata con uno stato di errore.

Per altre informazioni su come usare questo cmdlet, vedere [Get-NetAdapter](/powershell/module/netadapter/get-netadapter) nella Guida di riferimento ai cmdlet di Windows PowerShell.

Nelle sezioni seguenti vengono forniti alcuni esempi di output della `Get-NetAdapter` cmdlet.

 In questi esempi, controller 0 era il controller passivo ed è stato configurato come segue:

* Interfacce di rete DATI 0, DATI 1, DATI 2 e DATI 3 esistenti nel dispositivo.
* Schede di interfaccia di rete DATI 4 e DATI 5 non presenti e, pertanto, non elencate nell'output.
* DATI 0 era abilitata.

Controller 1 era il controller attivo ed è stato configurato come segue:

* Interfaccia di rete DATI 0, DATI 1, DATI 2, DATI 3, DATI 4 e DATI 5 esistenti nel dispositivo.
* DATI 0 era abilitata.

**Esempio di output – controller 0**

I dati di esempio seguenti sono l'output del controller 0 (il controller passivo). DATI 1, DATI 2 e DATI 3 non sono connessi. DATI 4 e DATI 5 dati non sono elencati perché non presenti nel dispositivo.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Esempio di output – controller 1**

I dati di esempio seguenti sono l'output del controller 1 (il controller attivo). Solo l'interfaccia di rete DATI 0 nel dispositivo è configurata e funzionante.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Risoluzione dei problemi con il cmdlet Test-Connection
È possibile utilizzare il `Test-Connection` cmdlet per determinare se il dispositivo StorSimple può connettersi alla rete esterna. Se tutti i parametri di rete, tra cui il DNS, sono configurati correttamente nella configurazione guidata, è possibile utilizzare il cmdlet `Test-Connection` per eseguire il ping di un indirizzo noto all'esterno della rete, ad esempio outlook.com.

Se il cmdlet ping è disabilitato, è necessario abilitare il ping per l'utilizzo nella risoluzione dei problemi di connettività.

Vedere i seguenti esempi di output del cmdlet `Test-Connection` .

> [!NOTE]
> Nel primo esempio, il dispositivo è configurato con un DNS non corretto. Nel secondo esempio, il DNS è corretto.

**Esempio di output – DNS non corretto**

Nell'esempio seguente non è incluso alcun output per gli indirizzi IPV4 e IPV6, che indica che il DNS non è stato risolto. Non esiste connettività alla rete esterna ed è necessario fornire un DNS corretto.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Esempio di output – DNS corretto**

Nell'esempio seguente, il DNS restituisce l'indirizzo IPV4, che indica che il DNS è configurato correttamente. L'output conferma che è presente connettività alla rete esterna.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Risoluzione dei problemi con il cmdlet Test-HcsmConnection
Utilizzare il cmdlet `Test-HcsmConnection` per un dispositivo che è già connesso a e registrato con il servizio Gestione dispositivi StorSimple. Questo cmdlet consente di verificare la connettività tra un dispositivo registrato e il corrispondente servizio Gestione dispositivi StorSimple. È possibile eseguire questo comando su Windows PowerShell per StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Per eseguire il cmdlet Test-HcsmConnection
1. Verificare che il dispositivo sia registrato.
2. Verificare lo stato del dispositivo. Se il dispositivo è disattivato, in modalità di manutenzione o non in linea, potrebbe essere visualizzato uno dei seguenti errori:
   
   * ErrorCode. CiSDeviceDecommissioned: indica che il dispositivo è disattivato.
   * ErrorCode. DeviceNotReady: indica che il dispositivo è in modalità di manutenzione.
   * ErrorCode. DeviceNotReady: indica che il dispositivo non è online.
3. Verificare che il servizio Gestione dispositivi StorSimple sia in esecuzione (usare i cmdlet [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10))). Se il servizio non è in esecuzione, è possibile visualizzare i seguenti errori:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode. CisPowershellScriptHcsError: indica che si è verificata un'eccezione durante l'esecuzione di Get-ClusterResource.
4. Verificare il token di ACS (Access Control Service). Se viene generata un'eccezione Web, potrebbe essere il risultato di un problema al gateway, un'autenticazione proxy assente, un DNS non corretto o un errore di autenticazione. È possibile visualizzare i seguenti errori:
   
   * ErrorCode. CiSApplianceGateway: indica un'eccezione HttpStatusCode. BadGateway: il servizio resolver dei nomi non è riuscito a risolvere il nome host.
   * ErrorCode. CiSApplianceProxy: indica un'eccezione HttpStatusCode. ProxyAuthenticationRequired (codice di stato HTTP 407): il client non è stato in grado di eseguire l'autenticazione con il server proxy.
   * ErrorCode. CiSApplianceDNSError: indica un'eccezione WebExceptionStatus. NameResolutionFailure: il servizio resolver dei nomi non è riuscito a risolvere il nome host.
   * ErrorCode. CiSApplianceACSError: indica che il servizio ha restituito un errore di autenticazione, ma è presente connettività.
     
     Se non genera un'eccezione Web, verificare la presenza di ErrorCode. CiSApplianceFailure, che indica che l'appliance non è riuscita.
5. Controllare la connettività del servizio cloud. Se il servizio genera un'eccezione Web, è possibile visualizzare i seguenti errori:
   
   * ErrorCode. CiSApplianceGateway: indica un'eccezione HttpStatusCode. BadGateway: un server proxy intermedio ha ricevuto una richiesta non valida da un altro proxy o dal server originale.
   * ErrorCode. CiSApplianceProxy: indica un'eccezione HttpStatusCode. ProxyAuthenticationRequired (codice di stato HTTP 407): il client non è stato in grado di eseguire l'autenticazione con il server proxy.
   * ErrorCode. CiSApplianceDNSError: indica un'eccezione WebExceptionStatus. NameResolutionFailure: il servizio resolver dei nomi non è riuscito a risolvere il nome host.
   * ErrorCode. CiSApplianceACSError: indica che il servizio ha restituito un errore di autenticazione, ma è presente connettività.
     
     Se non genera un'eccezione Web, verificare la presenza di ErrorCode. CiSApplianceSaasServiceError, che indica un problema con il servizio StorSimple Gestione dispositivi.
6. Verificare la connettività del bus di servizio di Azure. ErrorCode.CiSApplianceServiceBusError indica che il dispositivo non può connettersi al bus di servizio.

I file di registro CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog dispongono di altre informazioni come i dettagli dell'eccezione.

Per altre informazioni su come utilizzare il cmdlet, vedere [Test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) nella documentazione di riferimento di Windows PowerShell.

> [!IMPORTANT]
> È possibile eseguire questo cmdlet per il controller attivo e passivo.

Vedere i seguenti esempi di output del cmdlet `Test-HcsmConnection` .

**Esempio di output: registrazione corretta di un dispositivo che esegue l'aggiornamento 3 di StorSimple**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Esempio di output: dispositivo offline** 

Questo esempio riguarda un dispositivo con stato **Offline** nel portale di Azure.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Il dispositivo non può effettuare la connessione utilizzando la configurazione del proxy Web corrente. Potrebbe essersi verificato un problema con la configurazione del proxy Web o con un problema di connettività di rete. In questo caso, assicurarsi che le impostazioni del proxy Web siano corrette e che i server proxy Web siano online e raggiungibili.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Risoluzione dei problemi con il cmdlet Sync-HcsTime
Usare questo cmdlet per visualizzare l'ora del dispositivo. Se esiste una differenza tra l'ora del dispositivo e quella del server NTP, è possibile usare questo cmdlet per forzare la sincronizzazione dell'ora con il server NTP.
- Se la differenza tra il dispositivo e il server NTP è maggiore di 5 minuti, verrà visualizzato un avviso. 
- Se la differenza è maggiore di 15 minuti, il dispositivo verrà portato offline. È comunque possibile usare questo cmdlet per forzare la sincronizzazione dell'ora. 
- Se la differenza supera le 15 ore, tuttavia, non sarà possibile forzare la sincronizzazione e verrà visualizzato un messaggio di errore.

**Output di esempio - Sincronizzazione dell'ora forzata con Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Risoluzione dei problemi relativi ai cmdlet Enable-HcsPing e Disable-HcsPing
Usare questi cmdlet per garantire che le interfacce di rete nel dispositivo rispondano alle richieste di ping ICMP. Per impostazione predefinita, le interfacce di rete StorSimple non rispondono alle richieste di ping. L'uso di questo cmdlet rappresenta il modo più semplice per sapere se il dispositivo è online e raggiungibile.

**Output di esempio: Enable-HcsPing e Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Risoluzione dei problemi con il cmdlet Trace-HcsRoute
Usare questo cmdlet come uno strumento di analisi della route. Invia pacchetti a ogni router nel percorso verso la destinazione finale in un periodo di tempo e calcola quindi i risultati in base ai pacchetti restituiti da ogni hop. Dato che il cmdlet mostra il livello di perdita di pacchetti in qualsiasi router o collegamento, è possibile individuare quali router o collegamenti potrebbero essere la causa dei problemi di rete.

**Output di esempio che illustra come tracciare la route di un pacchetto con Trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Risoluzione dei problemi con il cmdlet Get-HcsRoutingTable
Usare questo cmdlet per visualizzare la tabella di routing per il dispositivo StorSimple. Una tabella di routing è un set di regole che consentono di determinare dove verranno indirizzati i pacchetti di dati che passano attraverso una rete IP (Internet Protocol).

La tabella di routing mostra le interfacce e il gateway che indirizza i dati alle reti specificate. Fornisce inoltre la metrica di routing, ovvero il decision maker per il percorso adottato per raggiungere una determinata destinazione. Minore è la metrica di routing, maggiore è la preferenza.

Si supponga, ad esempio, di disporre di due interfacce di rete, DATA 2 e DATA 3, connesse a Internet. Se le metriche di routing per DATA 2 e DATA 3 sono rispettivamente 15 e 261, DATA 2, con la metrica di routing inferiore, è l'interfaccia preferita utilizzata per raggiungere Internet.

Se si esegue l'aggiornamento 1 nel dispositivo StorSimple, l'interfaccia di rete DATI 0 ha la preferenza maggiore per il traffico cloud. Con questa configurazione, anche se sono presenti altre interfacce abilitate per il cloud, la maggior parte del traffico cloud viene indirizzata attraverso DATA 0.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

Se si esegue il cmdlet `Get-HcsRoutingTable` senza specificare alcun parametro (come illustrato nell'esempio seguente), il cmdlet memorizzerà le tabelle di routing IPv4 e IPv6. In alternativa, è possibile specificare `Get-HcsRoutingTable -IPv4` o `Get-HcsRoutingTable -IPv6` per ottenere una tabella di routing pertinente.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Esempio dettagliato di risoluzione dei problemi
Nell'esempio seguente viene illustrata la risoluzione dei problemi dettagliata di una distribuzione di StorSimple. Nello scenario di esempio, la registrazione del dispositivo ha esito negativo e viene visualizzato un messaggio di errore che indica che le impostazioni di rete o il nome DNS non è corretto.

Il messaggio di errore restituito è:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

L'errore potrebbe essere causato da uno dei problemi seguenti:

* Installazione hardware non corretta
* Interfacce di rete difettose
* Indirizzo IP errato, subnet mask, gateway, server DNS primario o proxy Web
* Codice di registrazione non corretto
* Impostazioni del firewall non corrette

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Per individuare e risolvere il problema di registrazione del dispositivo
1. Controllare la configurazione del dispositivo: sul controller attivo, eseguire `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Sul controller attivo, è necessario eseguire la configurazione guidata. Per verificare la connessione al controller attivo, esaminare l'intestazione visualizzata nella console seriale. L'intestazione indica se si è connessi al controller 0 o 1 e se il controller è attivo o passivo. Per altre informazioni, vedere [Identificare un controller attivo sul dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Assicurarsi che il dispositivo sia connesso correttamente: controllare i cavi di rete sul piano posteriore del dispositivo. Il cablaggio è specifico per il modello di dispositivo. Per altre informazioni, vedere [Installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Se si utilizzano porte di rete 10 GbE, è necessario utilizzare le schede QSFP-SFP forniti e i cavi SFP. Per altre informazioni, vedere l'[elenco dei cavi, commutatori e ricetrasmettitori consigliati per le porte 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verificare l'integrità dell'interfaccia di rete:
   
   * Utilizzare il cmdlet Get-NetAdapter per rilevare l'integrità delle interfacce di rete per DATA 0. 
   * Se il collegamento non funziona, lo `ifindex` stato indicherà che l'interfaccia è inattiva. In questo caso è necessario verificare la connessione di rete della porta per il dispositivo e il commutatore. È inoltre necessario escludere i cavi non validi. 
   * Se si ritiene che la porta DATA 0 sul controller attivo non sia riuscita, è possibile verificare che connettendosi alla porta DATA 0 sul controller 1. Scollegare il cavo di rete dalla parte posteriore del dispositivo dal controller 0, collegare il cavo al controller 1, quindi eseguire di nuovo il cmdlet Get-NetAdapter.
   
     Se la porta DATA 0 su un controller ha esito negativo, [contattare supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per i passaggi successivi. Potrebbe essere necessario sostituire il controller sul proprio sistema.
4. Verificare la connettività al commutatore:
   
   * Assicurarsi che le interfacce di rete DATA 0 sul controller 0 e controller 1 nello chassis principale siano sulla stessa subnet. 
   * Controllare l'hub o il router. In genere, è consigliabile effettuare la connessione di entrambi i controller allo stesso hub o router. 
   * Assicurarsi che i commutatori utilizzati per la connessione dispongano di DATI 0 per entrambi i controller nella stessa vLAN.
5. Eliminare eventuali errori dell'utente:
   
   * Eseguire nuovamente la configurazione guidata (eseguire **Invoke-HcsSetupWizard**) e immettere nuovamente i valori per assicurarsi che non siano presenti errori. 
   * Verificare il codice di registrazione utilizzato. La stessa chiave di registrazione può essere utilizzata per collegare più periferiche a un servizio Gestione dispositivi StorSimple. Utilizzare la procedura in [Ottenere il codice di registrazione](storsimple-8000-manage-service.md#get-the-service-registration-key) per assicurarsi di utilizzare la chiave di registrazione corretta.
     
     > [!IMPORTANT]
     > Se sono in esecuzione più servizi, è necessario assicurarsi che il codice di registrazione per il servizio appropriato viene utilizzato per registrare il dispositivo. Se è stato registrato un dispositivo con il servizio Gestione dispositivi StorSimple errato, occorre [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per i passaggi successivi. Potrebbe essere necessario eseguire un ripristino delle impostazioni predefinite del dispositivo (che potrebbe comportare la perdita di dati), per connettersi al servizio desiderato.
     > 
     > 
6. Utilizzare il cmdlet Test-Connection per verificare di disporre della connettività alla rete esterna. Per altre informazioni, vedere [Risoluzione dei problemi con il cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Controllare le interferenze del firewall. Se è stato verificato che le impostazioni IP virtuale (VIP), subnet, gateway e DNS sono tutte corrette e si verificano problemi di connettività, è possibile che il firewall blocchi la comunicazione tra il dispositivo e la rete esterna. Assicurarsi che le porte 80 e 443 siano disponibili sul dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [Requisiti di rete per il dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Esaminare i log. Vedere [Pacchetti di supporto e log del dispositivo disponibili per la risoluzione dei problemi](#support-packages-and-device-logs-available-for-troubleshooting).
9. Se la procedura precedente non risolve il problema, [contattare il supporto tecnico Microsoft](storsimple-8000-contact-microsoft-support.md) per assistenza.

## <a name="next-steps"></a>Passaggi successivi
[Usare lo strumento di diagnostica StorSimple per risolvere i problemi di un dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)