---
title: Domande frequenti su Azure Active Directory Connect Healtht | Microsoft Docs
description: Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d15b12b758adbf99ddabc88eb06be9daba1ece3e
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89276202"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Domande frequenti su Azure AD Connect Health
Questo articolo include risposte alle domande frequenti su Azure Active Directory (Azure AD) Connect Health. Le domande sono relative all'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali
**D: si gestiscono più directory Azure AD. Ricerca per categorie passare a quello con Azure Active Directory Premium?**

Per spostarsi tra tenant di Azure AD diversi, selezionare il **Nome utente** attualmente connesso nell'angolo in alto a destra e scegliere l'account appropriato. Se l'account non è incluso nell'elenco, selezionare **Disconnetti** e quindi usare le credenziali di amministratore globale della directory con Azure Active Directory Premium abilitato per l'accesso.

**D: Quali versioni dei ruoli di identità sono supportate da Azure AD Connect Health?**

La tabella seguente elenca i ruoli e le versioni del sistema operativo supportate.

|Ruolo| Sistema operativo/Versione|
|--|--|
|Active Directory Federation Services (ADFS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Versione 1.0.9125 o successiva|
|Active Directory Domain Services (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Le installazioni di Windows Server Core non sono supportate.

Si noti che le funzionalità offerte dal servizio possono variare in base al ruolo e al sistema operativo. In altre parole, è possibile che non tutte le funzionalità siano disponibili per tutte le versioni del sistema operativo. Vedere le descrizioni delle funzionalità per i dettagli.

**D: Quante licenze è necessario avere per monitorare l'infrastruttura?**

* Per il primo agente di Connect Health è necessaria almeno una licenza Premium di Azure AD.
* Ogni agente registrato successivamente richiede altre 25 licenze Azure AD Premium.
* Il numero di agenti è uguale al numero totale di agenti registrati in tutti i ruoli monitorati (AD FS, Azure AD Connect e/o AD DS).
* Le licenze di Azure Active Directory Connect Health non richiedono l'assegnazione a utenti specifici. È necessario soltanto disporre del numero di licenze valide.

È inoltre possibile trovare le informazioni sulle licenze sulla [pagina dei prezzi di Azure AD](https://aka.ms/aadpricing).

Esempio:

| Agenti registrati | Licenze necessarie | Esempio di configurazione di monitoraggio |
| ------ | --------------- | --- |
| 1 | 1 | 1 server di Azure AD Connect |
| 2 | 26| 1 server di Azure AD Connect e 1 controller di dominio |
| 3 | 51 | 1 server di Active Directory Federation Services (AD FS), 1 proxy di AD FS e 1 controller di dominio |
| 4 | 76 | 1 server di AD FS, 1 proxy di AD FS e 2 controller di dominio |
| 5 | 101 | 1 server di Azure AD Connect, 1 server di AD FS, 1 proxy di AD FS e 2 controller di dominio |

**D: Azure AD Connect Health supporta il cloud di Azure Germania?**

Azure AD Connect Health non è supportato nel cloud di Azure Germania ad eccezione della [funzionalità di report degli errori di sincronizzazione](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Ruoli | Funzionalità | Supportato nel cloud di Azure in Germania |
| ------ | --------------- | --- |
| Connect Health per la sincronizzazione | Monitoraggio/informazioni dettagliate/avvisi/analisi | No |
|  | Report degli errori di sincronizzazione | Sì |
| Connect Health per file system distribuito di Azure | Monitoraggio/informazioni dettagliate/avvisi/analisi | No |
| Connect Health per ADDS | Monitoraggio/informazioni dettagliate/avvisi/analisi | No |

Per garantire la connettività degli agenti di Connect Health per la sincronizzazione, configurare il [requisito di installazione](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) di conseguenza.

## <a name="installation-questions"></a>Domande sull'installazione

**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione dell'agente di Microsoft Azure AD Connect Health, di AD FS, dei server proxy applicazione Web, dei server di Azure AD Connect (sincronizzazione) e dei controller di dominio è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda della rete e archiviazione.

I numeri seguenti sono approssimativi:

* Utilizzo della CPU: ~1-5% di incremento.
* Utilizzo della memoria: fino a al 10% della memoria di sistema totale.

> [!NOTE]
> Se l'agente non può comunicare con Azure, archivia i dati localmente per un limite massimo definito. L'agente sovrascrive i dati "memorizzati nella cache" secondo un criterio di tipo "intervento di manutenzione meno recente".
>
>

* Archiviazione buffer locale per gli agenti di Azure AD Connect Health: ~20 MB.
* Per i server di AD FS, è consigliabile effettuare il provisioning di 1.024 MB (1 GB) di spazio su disco per il canale di controllo di AD FS per consentire agli agenti di Azure AD Connect Health di elaborare tutti i dati di controllo prima che vengano sovrascritti.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No. Per l'installazione degli agenti non è necessario il riavvio del server. L'installazione di alcuni passaggi preliminare può richiedere tuttavia un riavvio del server.

In Windows Server 2008 R2, ad esempio, l'installazione di .NET 4.5 Framework richiede un riavvio del server.

**D: Il servizio Azure AD Connect Health usa un proxy HTTP pass-through?**

Sì. Per le operazioni in corso, è possibile configurare l'agente per l'integrità in modo che usi un proxy HTTP per inoltrare le richieste HTTP in uscita.
Altre informazioni sulla [configurazione del proxy HTTP per gli agenti di integrità](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se è necessario configurare un proxy durante la registrazione dell'agente, modificare prima di tutto le impostazioni del proxy di Internet Explorer.

1. Aprire Internet Explorer > **Impostazioni**  >  **Opzioni Internet**  >  **connessioni**  >  **Impostazioni LAN**.
2. Selezionare **Usa un server proxy per la rete LAN**.
3. Selezionare **Avanzate** se sono presenti porte proxy diverse per http e HTTPS/protetto.

**D: Il servizio Azure AD Connect Health supporta l'autenticazione di base per la connessione ai proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.

**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

Vedere la [sezione requisiti](how-to-connect-health-agent-install.md#requirements) per l'elenco delle porte del firewall e altri requisiti di connettività.

**D: perché vengono visualizzati due server con lo stesso nome nel portale di Azure AD Connect Health?**

Quando si rimuove un agente da un server, tale server non viene automaticamente rimosso dal portale di Azure AD Connect Health. Se si rimuove manualmente un agente da un server o se si rimuove il server stesso, è necessario eliminare manualmente la voce relativa al server dal portale di Azure AD Connect Health.

È possibile ricreare l'immagine di un server o creare un nuovo server con gli stessi dettagli, ad esempio il nome del computer. Se il server già registrato non è stato rimosso dal portale di Azure AD Connect Health e l'agente è stato installato nel nuovo server, è possibile che vengano visualizzate due voci con lo stesso nome.

In questo caso, eliminare manualmente la voce appartenente al server meno recente. I dati per questo server non dovrebbero essere aggiornati.

## <a name="health-agent-registration-and-data-freshness"></a>Registrazione dell'agente per l'integrità e aggiornamento dati

**D: Quali sono le cause più comuni che generano errori nella registrazione dell'agente per l'integrità e come risolverle?**

Le possibili cause per cui un agente per l'integrità non riesce a eseguire la registrazione sono elencate di seguito:

* L'agente non può comunicare con gli endpoint necessari perché un firewall blocca il traffico. Questa situazione è molto comune nei server proxy applicazione Web. Assicurarsi di aver abilitato la comunicazione in uscita per le porte e gli endpoint obbligatori. Per informazioni dettagliate, vedere la [sezione requisiti](how-to-connect-health-agent-install.md#requirements) .
* La comunicazione in uscita è soggetta a un'ispezione TLS da parte del livello di rete. In questo modo il certificato usato dall'agente viene sostituito dall'entità o dal server per l'ispezione e non è possibile eseguire i passaggi necessari per completare la registrazione dell'agente.
* L'utente non dispone dell'accesso per eseguire la registrazione dell'agente. Per impostazione predefinita, agli amministratori globali l'accesso è consentito. Per delegare l'accesso ad altri utenti, è possibile usare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) .

**D: viene generato un avviso che indica che "i dati Servizio integrità non sono aggiornati". Ricerca per categorie risolvere il problema?**

Azure AD Connect Health genera l'avviso quando non riceve tutti i punti dati dal server nelle ultime due ore. [Altre informazioni](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Domande sulle operazioni
**D: è necessario abilitare il controllo nei server proxy applicazione Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione Web.

**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. In altri termini, se entro 72 ore dalla generazione dell'avviso non viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.

**D: si riceve un avviso che segnala che la richiesta di autenticazione di test (transazione sintetica) non è riuscita a ottenere un token ". Ricerca per categorie risolvere il problema?**

Azure AD Connect Health per AD FS genera questo avviso quando l'agente installato in un server AD FS non riesce a ottenere un token in quanto parte di una transazione sintetica avviata dall'agente stesso. L'agente di Azure AD Connect Health usa il contesto di sistema locale e tenta di ottenere un token per un self relying party. Si tratta di un test di tipo catch-all per assicurarsi che AD FS sia in uno stato di rilascio di token.

Spesso questo test ha esito negativo perché l'agente di Azure AD Connect Health non può risolvere il nome della farm AD FS. Questa situazione può verificarsi se i server AD FS si trovano dietro un bilanciamento del carico di rete e la richiesta viene avviata da un nodo che si trova dietro il bilanciamento del carico, in contrapposizione a un client normale che si trova davanti al bilanciamento del carico. Questo problema può essere risolto aggiornando il file "hosts" in "C:\Windows\System32\drivers\etc" per includere l'indirizzo IP del server AD FS o un indirizzo IP di loopback (127.0.0.1) per il nome della farm AD FS, come ad esempio sts.contoso.com. Quando si aggiunge il file host, la chiamata di rete viene messa in corto circuito, consentendo in tal modo all'agente di Azure AD Connect Health di ottenere il token.

**D: è stato ricevuto un messaggio di posta elettronica che indica che i computer non sono corretti per gli attacchi ransomware recenti. Perché ho ricevuto questo messaggio di posta elettronica?**

Il servizio di Azure AD Connect Health ha analizzato tutti i computer di cui esegue il monitoraggio per verificare che le patch necessarie siano installate. Se almeno un computer non dispone delle patch critiche, viene inviato tale messaggio di posta elettronica agli amministratori del tenant. Per arrivare a tale decisione, è stata usata la logica seguente.
1. Trovare tutti gli hotfix installati nel computer.
2. Controllare se è presente almeno uno degli hotfix inclusi nell'elenco definito.
3. Se sì, il computer è protetto. In caso contrario, il computer è a rischio di attacco.

Per eseguire manualmente questo controllo, è possibile usare lo script di PowerShell seguente. In questo modo viene implementata la logica precedente.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**D: Perché nei risultati del cmdlet di PowerShell <i>Get-MsolDirSyncProvisioningError</i> ci sono meno errori di sincronizzazione?**

<i>Get-MsolDirSyncProvisioningError</i> restituisce solo errori di provisioning DirSync. Oltre a questo, il portale Connect Health mostra altri tipi di errore di sincronizzazione, ad esempio errori di esportazione, in conformità con i risultati Delta di Azure AD Connect. Ulteriori informazioni sugli [errori di sincronizzazione di Azure AD Connect](./tshoot-connect-sync-errors.md).

**D: Perché i controlli AD FS non vengono generati?**

Usare il cmdlet di PowerShell <i>Get-AdfsProperties -AuditLevel</i> per assicurarsi che i log di controllo non siano disabilitati. Altre informazioni sui [log di controllo di ADFS](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Si noti che in presenza di impostazioni di controllo avanzate di cui è stato eseguito il push nel server AD FS, le eventuali modifiche con auditpol.exe verranno sovrascritte, anche se l'impostazione Generato dall'applicazione non è configurata. In questo caso, impostare i criteri di sicurezza locale per la registrazione degli esiti negativi e positivi di Generato dall'applicazione.

**D: quando il certificato dell'agente viene rinnovato automaticamente prima della scadenza?**
La certificazione dell'agente verrà rinnovata automaticamente **6 mesi** prima della data di scadenza. Se non viene rinnovato, verificare che la connessione di rete dell'agente sia stabile. È possibile risolvere il problema anche riavviare i servizi dell'agente o eseguire l'aggiornamento alla versione più recente.


## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installazione agente Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](how-to-connect-health-adds.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)