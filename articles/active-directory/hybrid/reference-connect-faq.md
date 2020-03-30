---
title: Domande frequenti su Azure Active Directory Connect | Microsoft Docs
description: Questo articolo contiene domande frequenti su Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149814"
---
# <a name="azure-active-directory-connect-faq"></a>Domande frequenti su Azure Active Directory Connect

## <a name="general-installation"></a>Installazione generale

**D: In che modo è possibile eseguire la protezione dell'utente con protezione dell'utente per ridurre la superficie di attacco alla sicurezza?**

Microsoft consiglia di eseguire la protezione avanzata del server Azure AD Connect per ridurre la superficie di attacco alla sicurezza per questo componente critico dell'ambiente IT.  Seguire le indicazioni riportate di seguito ridurrà i rischi per la sicurezza dell'organizzazione.

* Distribuire Azure AD Connect in un server aggiunto a un dominio e limitare l'accesso amministrativo agli amministratori di dominio o ad altri gruppi di sicurezza strettamente controllatiDeploy Azure AD Connect on a domain joined server and restrict administrative access to domain administrators or other tightcontrolled security groups

Per altre informazioni, vedere: 

* [Protezione dei gruppi di amministratori](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protezione degli account amministratore predefiniti](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Miglioramento e mantenimento della sicurezza riducendo le superfici di attacco](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Riduzione della superficie di attacco di Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**D: L'installazione funziona se per l'amministrazione globale di Azure Active Directory (Azure AD) è abilitata l'autenticazione a due fattori (2FA)?**  
A partire dalle build di febbraio 2016, questo scenario è supportato.

**D: Esiste un modo per eseguire l'installazione automatica di Azure AD Connect?**  
L'installazione di Azure AD Connect è supportata solo se si usa l'installazione guidata. L'installazione automatica invisibile all'utente non è supportata.

**D: Ho una foresta in cui un dominio non può essere contattato. Come installare Azure AD Connect?**  
A partire dalle build di febbraio 2016, questo scenario è supportato.

**D: L'agente integrità Azure Active Directory Domain Services (Azure AD DS) funziona in Server Core?**  
Sì. Dopo aver installato l'agente, è possibile completare il processo di registrazione tramite il cmdlet di PowerShell seguente: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**D: Azure AD Connect supporta la sincronizzazione da due domini ad Azure AD?**  
Sì, questo scenario è supportato. Fare riferimento a [Supporto di più domini](how-to-connect-install-multiple-domains.md).
 
**D: È possibile avere più connettori per lo stesso dominio di Active Directory in Azure AD Connect?**  
No, la presenza di più connettori per lo stesso dominio di AD non è supportata. 

**D: È possibile spostare il database di Azure AD Connect dal database locale a un'istanza remota di SQL Server?**   
Sì. I passaggi seguenti rappresentano materiale sussidiario su come eseguire questa operazione. È attualmente in corso la preparazione di un documento più dettagliato.
1. Eseguire il backup del database LocalDB ADSync.
Il modo più semplice per eseguire questa operazione consiste nell'usare SQL Server Management Studio, installato nello stesso computer in cui è installato Azure AD Connect. Connettersi a *(LocalDb).*

2. Ripristinare il database ADSync nell'istanza remota di SQL Server.

3. Installare Azure AD Connect per il [database SQL remoto](how-to-connect-install-existing-database.md) esistente.
   L'articolo illustra come eseguire la migrazione per passare all'uso di un database SQL locale. Se si esegue la migrazione per passare all'uso di un database SQL remoto, nel passaggio 5 di questo processo è anche necessario immettere un account esistente per l'esecuzione del servizio di sincronizzazione di Windows. Il processo di sincronizzazione di questo account del servizio viene descritto di seguito:
   
      Usare un account di **servizio esistente:** per impostazione predefinita, Azure AD Connect usa un account di servizio virtuale per i servizi di sincronizzazione da usare. Se si usa un'istanza di SQL Server remota o un proxy che richiede l'autenticazione, usare un account del servizio gestito o un account del servizio nel dominio di cui si conosce la password. In questi casi, immettere l'account da usare. Assicurarsi che gli utenti che eseguono l'installazione siano amministratori di sistema per SQL Server, in modo che sia possibile la creazione delle credenziali di accesso per l'account del servizio. Per altre informazioni, vedere [Account e autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Con la build più recente, l'amministratore SQL può ora effettuare il provisioning del database fuori banda e quindi l'amministratore di Azure AD Connect può eseguire l'installazione con diritti di proprietario del database. Per altre informazioni, vedere [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md).

Per semplicità, è consigliabile che gli utenti che installano Azure AD Connect siano amministratori di sistema in SQL Server. Con le build recenti, tuttavia, l'installazione può essere eseguita anche da amministratori SQL con delega, come descritto in [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md).

**D: Quali sono alcune delle best practice del campo?**  

Di seguito è riportato un documento informativo che presenta alcune delle migliori pratiche che l'ingegneria, il supporto e i nostri consulenti hanno sviluppato nel corso degli anni.  Questo è presentato in un elenco puntato che può essere fatto rapidamente riferimento.  Anche se questo elenco tenta di essere completo, ci possono essere ulteriori best practice che potrebbero non aver fatto ancora nell'elenco.

- Se si utilizza Full SQL allora dovrebbe rimanere locale rispetto remoto
    - Meno hop
    - Risoluzione dei problemi più semplice
    - Meno complessità
    - Necessità di designare le risorse per SQL e consentire l'overhead per Azure AD Connect e il sistema operativo
- Ignora proxy se possibile, se non si riesce a ignorare il proxy, è necessario assicurarsi che il valore di timeout sia maggiore di 5 minuti.
- Se è necessario il proxy, è necessario aggiungere il proxy al file machine.config
- Tenere presente i processi SQL locali e la manutenzione e il modo in cui avranno un impatto su Azure AD Connect, in particolare la reindicizzazione
- Assicurarsi che DNS possa risolversi esternamente
- Assicurarsi che [le specifiche](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) del server siano per ogni raccomandazione se si utilizzano server fisici o virtuali
- Assicurarsi che se si utilizza un server virtuale le risorse necessarie siano dedicate
- Assicurarsi di avere la configurazione del disco e del disco soddisfare le procedure consigliate per SQL Server
- Installare e configurare Azure AD Connect Health per il monitoraggioInstall and configure Azure AD Connect Health for monitoring
- Usare la soglia di eliminazione incorporata in Azure AD Connect.Use the Delete Threshold that is built into Azure AD Connect.
- Esaminare attentamente gli aggiornamenti della versione da preparare per tutte le modifiche e i nuovi attributi che possono essere aggiunti
- Backup di tutto
    - Chiavi di backup
    - Regole di sincronizzazione dei backup
    - Configurazione del server di backup
    - Backup SQL Database
- Assicurarsi che non siano presenti agenti di backup di terze parti che eseguono il backup di SQL senza SQL VSS Writer (comune nei server virtuali con snapshot di terze parti)
- Limitare la quantità di regole di sincronizzazione personalizzate utilizzate quando aggiungono complessità
- Considerare i server Azure AD Connect come server di livello 0Treat Azure AD Connect Servers as Tier 0 Servers
- Essere invia di modificare le regole di sincronizzazione cloud senza grande comprensione dell'impatto e dei driver di business giusti
- Assicurarsi che le porte corrette per gli URL e il firewall siano aperte per il supporto di Azure AD Connect e Azure AD Connect Health
- Sfruttare l'attributo filtrato cloud per risolvere i problemi e impedire gli oggetti fantasma
- Con il server di gestione temporanea assicurarsi di utilizzare Azure AD Connect Configuration Documenter per la coerenza tra i server
- I server di gestione temporanea devono trovarsi in data center separati (posizioni fisiche
- I server di gestione temporanea non devono essere una soluzione a disponibilità elevata, ma è possibile avere più server di gestione temporanea
- L'introduzione di un server di gestione temporanea "Lag" potrebbe ridurre alcuni potenziali tempi di inattività in caso di errore
- Verificare e convalidare prima tutti gli aggiornamenti nel server di gestione temporanea
- Convalidare sempre le esportazioni prima di passare al server di staging.  Sfruttare il server di gestione temporanea per le importazioni complete e le sincronizzazioni complete per ridurre l'impatto aziendale
- Mantenere il più possibile la coerenza delle versioni tra i server Azure AD ConnectKeep version consistency between Azure AD Connect Servers il più possibile 

**D: È possibile consentire ad Azure AD Connect di creare l'account Connettore Azure AD nel computer del gruppo di lavoro?**
No.  Per consentire ad Azure AD Connect di creare automaticamente l'account del connettore di Azure AD, il computer deve essere aggiunto a un dominio.  

## <a name="network"></a>Rete
**D: Ho un firewall, un dispositivo di rete o qualcos'altro che limita il tempo in cui le connessioni possono rimanere aperte sulla rete. Quale deve essere la soglia di timeout sul lato client quando si usa Azure AD Connect?**  
Tutto il software di rete e tutti i dispositivi fisici o i meccanismi di altro tipo che limitano la durata delle connessioni devono applicare una soglia di almeno cinque minuti (300 secondi) per la connettività tra il server in cui è installato il client AD Azure Connect ed Azure Active Directory. Questo vale anche per tutti gli strumenti di sincronizzazione delle identità di Microsoft rilasciati in precedenza.

**D: I nomi di dominio con etichetta singola sono supportati?**  
Sebbene questa configurazione di rete non sia consigliata ([vedere l'articolo](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), l'uso della sincronizzazione di Azure AD Connect con un dominio con etichetta singola è supportato, a condizione che la configurazione di rete per il dominio a livello singolo funzioni correttamente.

**D: Le foreste con domini di Active Directory non contigui sono supportate?**  
No, Azure AD Connect non supporta foreste locali contenenti spazi dei nomi non contigui.

**D: I nomi NetBIOS contenenti un punto sono supportati?**  
No, Azure AD Connect non supporta foreste e domini locali il cui nome NetBIOS contenga un punto (.).

**D: L'ambiente puro IPv6 è supportato?**  
No, Azure AD Connect non supporta un ambiente IPv6 puro.

**Q:I disporre di un ambiente a più foreste e la rete tra le due foreste utilizza NAT (Network Address Translation). È supportato l'uso di Azure AD Connect tra queste due foreste?**</br>
No, l'uso di Azure AD Connect con NAT non è supportato. 

## <a name="federation"></a>Federazione
**D: Cosa occorre fare se si riceve un messaggio di posta elettronica che richiede il rinnovo del certificato di Office 365?**  
Per materiale sussidiario sul rinnovo del certificato, vedere [Rinnovare i certificati](how-to-connect-fed-o365-certs.md).

**D: Ho impostato "Aggiorna automaticamente relying party" per la relying party di Office 365. È necessario eseguire un'azione quando il certificato per la firma di token viene esulato automaticamente?**  
Seguire il materiale sussidiario illustrato nell'articolo [Rinnovare i certificati](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Environment
**D: Il fatto di rinominare il server dopo l'installazione di Azure AD Connect è supportato?**  
No. La modifica del nome del server impedisce al motore di sincronizzazione di connettersi all'istanza del database SQL e il servizio non può essere avviato.

**D: Le regole di sincronizzazione di crittografia di nuova generazione (NGC) sono supportate in un computer abilitato per FIPS?**  
No.  Ciò non è supportato.

**D: Se ho disabilitato un dispositivo sincronizzato (ad esempio: HAADJ) nel portale di Azure, perché viene riabilitato?**<br>
I dispositivi sincronizzati possono essere creati o masterizzati in locale. Se un dispositivo sincronizzato è abilitato in locale, potrebbe essere riabilitato nel portale di Azure anche se in precedenza è stato disabilitato da un amministratore. Per disabilitare un dispositivo sincronizzato, utilizzare Active Directory locale per disabilitare l'account computer.

**D: Se si blocca l'accesso degli utenti nel portale di Office 365 o Azure AD per gli utenti sincronizzati, perché viene sbloccato al momento dell'accesso?**<br>
Gli utenti sincronizzati possono essere creati o masterizzati in locale. Se l'account è abilitato in locale, può sbloccare il blocco di accesso inserito dall'amministratore.

## <a name="identity-data"></a>Dati di identità
**D: Perché l'attributo userPrincipalName (UPN) in Azure AD non corrisponde all'UPN locale?**  
Per informazioni, vedere questi articoli:

* [Usernames in Office 365, Azure, or Intune don't match the on-premises UPN or alternate login ID](https://support.microsoft.com/kb/2523192) (I nomi utente in Office 365, Azure e Intune non corrispondono all'UPN locale o all'ID di accesso alternativo)
* [Le modifiche non vengono sincronizzate tramite lo strumento di sincronizzazione con Active Directory Azure dopo aver modificato l'UPN di un account utente per l'utilizzo di un altro dominio federato](https://support.microsoft.com/kb/2669550)

È anche possibile configurare Azure AD in modo da consentire al motore di sincronizzazione di aggiornare l'UPN come descritto in [Funzionalità del servizio di sincronizzazione di Azure AD Connect](how-to-connect-syncservice-features.md).

**D: È supportata la corrispondenza flessibile degli oggetti contatto o gruppo di Azure AD locali con gli oggetti contatto o gruppo di Azure AD esistenti?**  
Sì, la corrispondenza si basa su proxyAddress. La corrispondenza flessibile non è supportata per i gruppi non abilitati alla posta elettronica.

**D: È supportata l'impostazione manuale dell'attributo ImmutableId di oggetti contatto o gruppo di Azure AD esistenti per ottenere una corrispondenza rigida con oggetti contatto o gruppo di Azure AD locali?**  
No, l'impostazione manuale dell'attributo ImmutableId di oggetti contatto o gruppo di Azure AD esistenti per ottenere una corrispondenza rigida non è attualmente supportata.

## <a name="custom-configuration"></a>Configurazione personalizzata
**D: Dove sono documentati i cmdlet PowerShell per Azure AD Connect?**  
Fatta eccezione per i cmdlet documentati in questo sito, i cmdlet di PowerShell disponibili in Azure AD Connect non sono supportati per l'uso da parte degli utenti.

**D: È possibile usare le opzioni "Server esportazione e "Server importazione" disponibili in Synchronization Service Manager per spostare la configurazione da un server all'altro?**  
No. Questa opzione non recupera tutte le impostazioni di configurazione e non deve essere usata. Usare invece la procedura guidata per creare la configurazione di base per il secondo server e usare l'editor delle regole di sincronizzazione per generare script di PowerShell e spostare regole personalizzate da un server all'altro. Per altre informazioni, vedere [Migrazione swing](how-to-upgrade-previous-version.md#swing-migration).

**D: È possibile memorizzare nella cache le password per la pagina di accesso di Azure ed è possibile evitare questa memorizzazione se la password contiene un elemento di input con l'attributo *autocomplete = "false"*?**  
La modifica degli attributi HTML del campo **Password**, tra cui il tag autocomplete, non è attualmente supportata. È attualmente in fase di elaborazione una funzionalità che consente la creazione di codice JavaScript personalizzato che consenta di aggiungere qualsiasi attributo al campo **Password**.

**D: Nella pagina di accesso di Azure vengono visualizzati i nomi utente degli utenti che hanno eseguito correttamente l'accesso in precedenza. È possibile disattivare questo comportamento?**  
Attualmente, la modifica degli attributi HTML del campo di input **Password,** incluso il tag di completamento automatico, non è supportata. È attualmente in fase di elaborazione una funzionalità che consente la creazione di codice JavaScript personalizzato che consenta di aggiungere qualsiasi attributo al campo **Password**.

**D: Esiste un modo per impedire sessioni simultanee?**  
No.

## <a name="auto-upgrade"></a>Aggiornamento automatico

**D: Quali sono i vantaggi e le conseguenze derivanti dall'uso dell'aggiornamento automatico?**  
È consigliabile abilitare l'aggiornamento automatico dell'installazione di Azure AD Connect in uso. In questo modo si riceveranno sempre le patch più recenti, inclusi gli aggiornamenti di sicurezza per le vulnerabilità rilevate in Azure AD Connect. Il processo di aggiornamento non comporta alcun problema e viene eseguito automaticamente non appena è disponibile una nuova versione. Diverse migliaia di clienti di Azure AD Connect si avvalgono dell'aggiornamento automatico ogni volta che viene rilasciata una nuova versione.

Il processo di aggiornamento automatico stabilisce sempre inizialmente se un'installazione è idonea per l'aggiornamento automatico. Se è idonea, l'aggiornamento viene eseguito e testato. Il processo prevede anche la ricerca di modifiche personalizzate a regole e fattori ambientali specifici. Se i test indicano che un aggiornamento non è riuscito, la versione precedente viene ripristinata automaticamente.

A seconda delle dimensioni dell'ambiente, il processo può richiedere circa due ore. Durante l'aggiornamento, non viene eseguita alcuna sincronizzazione tra Windows Server Active Directory e Azure AD.

**D: Ho ricevuto un'e-mail che mi informa che l'aggiornamento automatico non funziona più e ho bisogno di installare una nuova versione. Perché devo farlo?**  
Lo scorso anno è stata rilasciata una versione di Azure AD Connect che, in determinate circostanze, può aver disabilitato la funzionalità di aggiornamento automatico nel server. Il problema è stato risolto in Azure AD Connect versione 1.1.750.0. Gli utenti interessati dal problema possono attenuarlo eseguendo uno script di PowerShell o eseguire manualmente l'aggiornamento alla versione più recente di Azure AD Connect. 

Per eseguire lo script di PowerShell, [scaricarlo](https://aka.ms/repairaadconnect) ed eseguirlo nel server Azure AD Connect in una finestra amministrativa di PowerShell. Per informazioni su come eseguire lo script, [guardare questo breve video](https://aka.ms/repairaadcau).

Per eseguire l'aggiornamento manuale, è sufficiente scaricare ed eseguire la versione più recente del file AADConnect.msi.
 
-  Se la versione in uso è precedente alla versione 1.1.750.0, [scaricare e la versione più recente ed eseguire l'aggiornamento](https://www.microsoft.com/download/details.aspx?id=47594).
- Se si usa Azure AD Connect 1.1.750.0 o versione successiva, non è necessaria alcuna azione, perché si sta già usando la versione che contiene la correzione dell'aggiornamento automatico. 

**D: Ho ricevuto un'e-mail che mi informa di eseguire l'aggiornamento alla versione più recente per riattivare l'aggiornamento automatico. Sto usando la versione 1.1.654.0. Devo eseguire l'aggiornamento?**  
Sì, per riabilitare l'aggiornamento automatico è necessario eseguire l'aggiornamento alla versione 1.1.750.0 o a una versione successiva. [Scaricare la versione più recente ed eseguire l'aggiornamento](https://www.microsoft.com/download/details.aspx?id=47594).

**D: Ho ricevuto un'e-mail che mi informa di eseguire l'aggiornamento alla versione più recente per riattivare l'aggiornamento automatico. Se è stato usato PowerShell per abilitare l'aggiornamento automatico, è comunque necessario installare la versione più recente?**  
Sì, è comunque necessario eseguire l'aggiornamento alla versione 1.1.750.0 o a una versione successiva. L'abilitazione del servizio di aggiornamento automatico con PowerShell non risolve il problema dell'aggiornamento automatico rilevato nelle versioni precedenti alla 1.1.750.0.

**D: Si desidera eseguire l'aggiornamento a una versione più recente, ma non sono sicuro di chi ha installato Azure AD Connect e non sono disponibili il nome utente e la password. Abbiamo bisogno di questo?**
Per aggiornare Azure AD Connect, non è necessario conoscere il nome utente e la password usati la prima volta. Usare un qualsiasi account di Azure AD che abbia il ruolo Amministratore globale.

**D: Come si può conoscere la versione di Azure AD Connect in uso?**  
Per verificare quale versione di Azure AD Connect è installata nel server, passare al Pannello di controllo e cercare la versione installata di Microsoft Azure AD Connect selezionando **Programmi** > **e funzionalità**, come illustrato di seguito:

![Versione di Azure AD Connect nel Pannello di controllo](./media/reference-connect-faq/faq1.png)

**D: Come si esegue l'aggiornamento alla versione più recente di Azure AD Connect?**  
Per informazioni sull'aggiornamento alla versione più recente, vedere [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](how-to-upgrade-previous-version.md). 

**D: L'anno scorso è già stato eseguito l'aggiornamento alla versione più recente di Azure AD Connect. È necessario eseguire di nuovo l'aggiornamento?**  
Il team di Azure AD Connect crea numerosi aggiornamenti del servizio. Per trarre vantaggio dalle correzioni di bug e dagli aggiornamenti della sicurezza, nonché dalle nuove funzionalità, è importante mantenere il server aggiornato alla versione più recente. Se si abilita l'aggiornamento automatico, la versione del software viene aggiornata automaticamente. Per trovare la cronologia del rilascio delle versioni di Azure AD Connect, vedere [Azure AD Connect: Cronologia delle versioni](reference-connect-version-history.md).

**D: Quanto tempo richiede l'aggiornamento e qual è l'impatto sugli utenti?**  
Il tempo necessario per eseguire l'aggiornamento dipende dalle dimensioni del tenant. Per organizzazioni di grandi dimensioni, può essere consigliabile eseguire l'aggiornamento la sera o nel fine settimana. Durante l'aggiornamento non viene eseguita alcuna attività di sincronizzazione.

**D: Credo di aver eseguito l'aggiornamento ad Azure AD Connect, ma il portale di Office menziona ancora DirSync. Perché?**  
Il team di Office è al lavoro perché gli aggiornamenti al portale di Office riflettano i nomi di prodotto correnti, non lo strumento di sincronizzazione usato dagli utenti.

**D: Lo stato di aggiornamento automatico è "Sospeso". Perché è sospeso? Devo abilitarlo?**  
Per un bug in una versione precedente, in determinate circostanze lo stato dell'aggiornamento automatico rimane impostato su "Sospeso". L'abilitazione manuale è tecnicamente possibile, ma richiede diversi passaggi complessi. La soluzione migliore è scaricare e installare la versione più recente di Azure AD Connect.

**D: La mia azienda ha requisiti rigorosi di gestione delle modifiche e voglio controllare quando viene espulsa. Posso controllare quando viene avviato l'aggiornamento automatico?**  
No, al momento una funzione di questo tipo non è disponibile. La funzione è in corso di valutazione per una versione futura.

**D: Se l'aggiornamento automatico non è riuscito, si ricevo un messaggio di posta elettronica? Come faccio a sapere che ha avuto successo?**  
Non si riceve una notifica del risultato dell'aggiornamento. La funzione è in corso di valutazione per una versione futura.

**D: Viene pubblicato un piano di rilascio degli aggiornamenti automatici?**  
L'aggiornamento automatico è il primo passaggio nel processo di rilascio di una versione più recente. Ogni volta che è disponibile una nuova versione, gli aggiornamenti vengono rilasciati automaticamente. Le nuove versioni di Azure AD Connect vengono pre-annunciate nella [roadmap di Azure Active Directory](../fundamentals/whats-new.md).

**D: Con l'aggiornamento automatico viene aggiornato anche Azure AD Connect Health?**  
Sì, con l'aggiornamento automatico viene aggiornato anche Azure AD Connect Health.

**D: Viene eseguito l'aggiornamento automatico anche dei server Azure AD Connect in modalità di gestione temporanea?**  
Sì, è possibile eseguire l'aggiornamento automatico di un server Azure AD Connect in modalità di staging.

**D: Se l'aggiornamento automatico non riesce e il server Azure AD Connect non si avvia, cosa è necessario fare?**  
In rari casi, il servizio Azure AD Connect non si avvia dopo l'aggiornamento. In questi casi, il riavvio del server in genere risolve il problema. Se il servizio Azure AD Connect continua a non avviarsi, aprire un ticket di supporto. Per altre informazioni, vedere [Create a service request to contact Office 365 support](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) (Creare una richiesta di servizio per contattare il supporto di Office 365). 

**D: Non sono sicuro di quali siano i rischi quando si esegue l'aggiornamento a una versione più recente di Azure AD Connect.D: I's not sure what the risks are when I upgrade to a newer version of Azure AD Connect. Puoi chiamarmi per aiutarmi con l'aggiornamento?**  
Se è necessaria assistenza per l'aggiornamento a una versione di Azure AD Connect più recente, aprire un ticket di supporto nella pagina [Create a service request to contact Office 365 support](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/) (Creare una richiesta di servizio per contattare il supporto di Office 365).

## <a name="troubleshooting"></a>Risoluzione dei problemi
**D: Come è possibile ottenere informazioni su Azure AD Connect?**

[Ricercare nella Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Cercare nella KB soluzioni tecniche per problemi comuni in garanzia riguardanti il supporto per Azure AD Connect.

[Forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Cercare domande e risposte tecniche o porre le proprie domande visitando [la community di Azure AD](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Ottenere supporto per Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**D: Perché vengono visualizzati gli eventi 6311 e 6401 dopo gli errori del passaggio di sincronizzazione?**

Gli eventi 6311 - **Il server ha rilevato un errore imprevisto durante l'esecuzione** di un callback e 6401 - il controller dell'agente di gestione ha rilevato un errore **imprevisto** - vengono sempre registrati dopo un errore di passaggio di sincronizzazione. Per risolvere questi errori, è necessario pulire gli errori del passaggio di sincronizzazione.  Per altre informazioni, vedere [Risoluzione degli errori durante la sincronizzazione](tshoot-connect-sync-errors.md) e [Risoluzione dei problemi di sincronizzazione degli oggetti con](tshoot-connect-objectsync.md) la sincronizzazione di Azure AD ConnectFor more information, see Troubleshooting errors during synchronization and Troubleshoot object synchronization with Azure AD Connect sync
