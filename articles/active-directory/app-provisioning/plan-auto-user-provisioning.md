---
title: Pianificare una distribuzione automatica del provisioning utenti per Azure Active Directory
description: Linee guida per la pianificazione e l'esecuzione del provisioning utenti automatico
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 8855969a9f1e431fc45b831063732501eead7e99
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051788"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Pianificare una distribuzione automatica del provisioning utenti

Molte organizzazioni si basano su applicazioni SaaS (Software as a Service), ad esempio ServiceNow, zscaler e Slack, per la produttività degli utenti finali. Storicamente, il personale IT si è affidato ai metodi di provisioning manuale, ad esempio il caricamento di file CSV o l'uso di script personalizzati per gestire in modo sicuro le identità utente in ogni applicazione SaaS. Questi processi sono soggetti a errori, non sicuri e difficili da gestire.

Azure Active Directory (Azure AD) il provisioning utenti automatico semplifica questo processo, automatizzando in modo sicuro la creazione, la manutenzione e la rimozione delle identità utente nelle applicazioni SaaS in base alle regole di business. Questa automazione consente di scalare in modo efficace i sistemi di gestione delle identità in ambienti solo cloud e ibridi quando si espande la dipendenza dalle soluzioni basate sul cloud.

Vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md) per comprendere meglio le funzionalità.

## <a name="learn"></a>Learn

Il provisioning degli utenti crea una base per la governance delle identità in corso e migliora la qualità dei processi aziendali basati sui dati di identità autorevoli.

### <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'abilitazione del provisioning utenti automatico sono:

* **Maggiore produttività**. È possibile gestire le identità degli utenti in applicazioni SaaS con un'unica interfaccia di gestione del provisioning degli utenti. Questa interfaccia dispone di un singolo set di criteri di provisioning.

* **Gestire i rischi**. È possibile aumentare la sicurezza automatizzando le modifiche in base allo stato dei dipendenti o all'appartenenza a gruppi che definiscono i ruoli e/o l'accesso.

* **Conformità e governance degli indirizzi**. Azure AD supporta i log di controllo nativi per ogni richiesta di provisioning dell'utente. Le richieste vengono eseguite in entrambi i sistemi di origine e di destinazione. In questo modo è possibile tenere traccia degli utenti che hanno accesso alle applicazioni da un'unica schermata.

* **Ridurre i costi**. Il provisioning utenti automatico riduce i costi evitando inefficienze ed errori umani associati al provisioning manuale. Riduce la necessità di soluzioni di provisioning utente personalizzate, script e log di controllo.

### <a name="licensing"></a>Gestione delle licenze

Azure AD fornisce l'integrazione self-service di qualsiasi applicazione usando i modelli forniti nel menu della raccolta di applicazioni. Per un elenco completo dei requisiti di licenza, vedere la pagina relativa alla [gestione delle licenze Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licenze per le applicazioni

Sono necessarie le licenze appropriate per le applicazioni di cui si vuole eseguire automaticamente il provisioning. Discutere con i proprietari dell'applicazione se gli utenti assegnati all'applicazione hanno le licenze appropriate per i loro ruoli applicazione. Se Azure AD gestisce il provisioning automatico basato sui ruoli, i ruoli assegnati in Azure AD devono essere allineati alle licenze dell'applicazione. Le licenze non corrette di proprietà dell'applicazione possono causare errori durante il provisioning o l'aggiornamento di un utente.

### <a name="terms"></a>Termini

Questo articolo usa i termini seguenti:

* Operazioni CRUD-azioni eseguite sugli account utente: creazione, lettura, aggiornamento, eliminazione.

* Single Sign-on (SSO): possibilità per un utente di accedere una sola volta e accedere a tutte le applicazioni abilitate per SSO. Nel contesto del provisioning degli utenti, SSO è il risultato di un singolo account per accedere a tutti i sistemi che usano il provisioning utenti automatico.

* Sistema di origine: repository degli utenti da cui il Azure AD effettua il provisioning. Azure AD è il sistema di origine per la maggior parte dei connettori di provisioning preintegrati. Esistono tuttavia alcune eccezioni per le applicazioni cloud, ad esempio SAP, giornata lavorativa e AWS. Ad esempio, vedere [provisioning utenti da giorni lavorativi AD ad](../saas-apps/workday-inbound-tutorial.md).

* Sistema di destinazione: il repository degli utenti a cui il Azure AD effettua il provisioning. Il sistema di destinazione è in genere un'applicazione SaaS, ad esempio ServiceNow, zscaler e slack. Il sistema di destinazione può anche essere un sistema locale, ad esempio AD.

* [System for Cross-Domain Identity Management (SCIM)](https://aka.ms/scimoverview) : uno standard aperto che consente di automatizzare il provisioning degli utenti. SCIM comunica i dati dell'identità utente tra i provider di identità, ad esempio Microsoft, e i provider di servizi come Salesforce o altre app SaaS che richiedono informazioni sull'identità utente.

### <a name="training-resources"></a>Risorse di formazione

| Risorse| Collegamento e descrizione |
| - | - |
| Webinar on demand| [Gestire le applicazioni aziendali con Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Informazioni su come Azure AD possibile ottenere l'accesso Single Sign-on alle applicazioni SaaS aziendali e procedure consigliate per il controllo dell'accesso. |
| Video| [Che cos'è il provisioning utenti in Active Directory di Azure?](https://youtu.be/_ZjARPpI6NI) <br> [Come distribuire il provisioning utenti in Active Directory di Azure?](https://youtu.be/pKzyts6kfrw) <br> [Integrazione di Salesforce con Azure AD: come automatizzare il provisioning degli utenti](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Corsi online| SkillUp online:  [gestione delle identità](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Informazioni su come integrare Azure AD con molte applicazioni SaaS e proteggere l'accesso degli utenti a tali applicazioni. |
| Libri| [Autenticazione moderna con Azure Active Directory per le applicazioni Web (riferimento per gli sviluppatori) 1 ° edizione](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Si tratta di una guida autorevole e approfondita per la creazione di soluzioni di autenticazione Active Directory per questi nuovi ambienti. |
| Esercitazioni| Vedere l' [elenco delle esercitazioni sull'integrazione di app Saas con Azure ad](../saas-apps/tutorial-list.md). |
| Domande frequenti| [Domande frequenti](../app-provisioning/user-provisioning.md) sul provisioning utenti automatizzato |

### <a name="solution-architectures"></a>Architetture delle soluzioni

Il servizio di provisioning Azure AD esegue il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi agli endpoint dell'API di gestione degli utenti forniti da ogni fornitore dell'applicazione. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Provisioning utenti automatico per le aziende ibride

In questo esempio, gli utenti e i gruppi vengono creati in un database HR connesso a una directory locale. Il servizio di provisioning di Azure AD gestisce il provisioning utenti automatico per le applicazioni SaaS di destinazione.

 ![provisioning utenti](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrizione del flusso di lavoro:**

1. Gli utenti o i gruppi vengono creati in un'applicazione/sistema HR locale, ad esempio SAP. 

1. **Azure ad Connect Agent** esegue sincronizzazioni pianificate di identità (utenti e gruppi) dall'Active Directory locale a Azure ad.

1. Il **servizio di provisioning di Azure ad** avvia un [ciclo iniziale](../app-provisioning/user-provisioning.md) sul sistema di origine e di destinazione. 

1. **Azure ad servizio di provisioning** esegue una query sul sistema di origine per gli utenti e i gruppi modificati dal ciclo iniziale e inserisce le modifiche nei [cicli incrementali](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Provisioning utenti automatico per le aziende solo cloud

In questo esempio la creazione dell'utente avviene in Azure AD e il servizio di provisioning di Azure AD gestisce il provisioning utenti automatico per le applicazioni di destinazione (SaaS).

![Diagramma che illustra il processo di creazione di utenti/gruppi da un'applicazione H R locale tramite il servizio di provisioning di Azure a D per le applicazioni di destinazione a s.](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrizione del flusso di lavoro:**

1. Gli utenti o i gruppi vengono creati in Azure AD.

1. Il **servizio di provisioning di Azure ad** avvia un [ciclo iniziale](../app-provisioning/user-provisioning.md) sul sistema di origine e di destinazione. 

1. **Azure ad servizio di provisioning** esegue una query sul sistema di origine per gli utenti e i gruppi aggiornati dopo il ciclo iniziale ed esegue qualsiasi ciclo [incrementale](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Provisioning utenti automatico per le applicazioni HR cloud 

In questo esempio, gli utenti e i gruppi vengono creati in un'applicazione cloud HR come ad esempio giorno lavorativo e SuccessFactors. Il servizio di provisioning Azure AD e l'agente di provisioning di Azure AD Connect effettua il provisioning dei dati utente dal tenant app Cloud HR in Active Directory. Una volta aggiornati gli account in Active Directory, questo viene sincronizzato con Azure AD tramite Azure AD Connect e gli indirizzi di posta elettronica e gli attributi del nome utente possono essere riscritti nel tenant del cloud HR app.

![Figura 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  Il **team HR** esegue le transazioni nel tenant del cloud HR app.
2.  **Azure ad servizio di provisioning** esegue i cicli pianificati dal tenant dell'app HR cloud e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory.
3.  **Azure ad servizio di provisioning** richiama l'agente di provisioning di Azure ad Connect con un payload di richiesta contenente le operazioni di creazione/aggiornamento/abilitazione/disabilitazione dell'account Active Directory.
4.  **Azure ad Connect agente di provisioning** usa un account del servizio per gestire i dati dell'account Active Directory.
5.  **Azure ad Connect** esegue la sincronizzazione Delta per eseguire il pull degli aggiornamenti in Active Directory.
6.  Gli aggiornamenti di **Active Directory** vengono sincronizzati con Azure ad. 
7.  **Azure ad il servizio di provisioning** writeback l'attributo di posta elettronica e il nome utente da Azure ad al tenant dell'app HR cloud.

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Prendere in considerazione le esigenze dell'organizzazione per determinare la strategia per la distribuzione del provisioning utenti nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, è in genere dovuto a una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](../fundamentals/active-directory-deployment-plans.md) e che i ruoli della parte interessata nel progetto siano ben comprensibili documentando gli stakeholder e i rispettivi input e responsabilità del progetto.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunica in modo proattivo con gli utenti su come cambierà l'esperienza, quando verrà modificata, e su come ottenere supporto in caso di problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile che la configurazione iniziale del provisioning utenti automatico si trovi in un ambiente di test con un piccolo subset di utenti prima di ridimensionarlo a tutti gli utenti nell'ambiente di produzione. Vedere le [procedure consigliate](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) per l'esecuzione di un progetto pilota.

#### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota  

Un progetto pilota consente di eseguire test con un piccolo gruppo prima di distribuire una funzionalità per tutti gli utenti. Assicurarsi che nell'ambito del test ogni caso di utilizzo all'interno dell'organizzazione venga testato accuratamente.

Nella prima ondata, è possibile indirizzare IT, usabilità e altri utenti appropriati che possono testare e fornire commenti e suggerimenti. Usare questo feedback per sviluppare ulteriormente le comunicazioni e le istruzioni inviate agli utenti e per fornire informazioni dettagliate sui tipi di problemi che possono essere visualizzati dal personale di supporto.

Ampliare l'implementazione a gruppi di utenti più grandi aumentando l'ambito dei gruppi di destinazione. Questa operazione può essere eseguita tramite [l'appartenenza dinamica ai gruppi](../enterprise-users/groups-dynamic-membership.md)o aggiungendo manualmente utenti ai gruppi di destinazione.

## <a name="plan-application-connections-and-administration"></a>Pianificare le connessioni e l'amministrazione delle applicazioni

Usare il portale di Azure AD per visualizzare e gestire tutte le applicazioni che supportano il provisioning. Vedere [ricerca delle app nel portale](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Determinare il tipo di connettore da usare

I passaggi necessari per abilitare e configurare il provisioning automatico variano a seconda dell'applicazione. Se l'applicazione di cui si vuole eseguire il provisioning automatico è elencata nella [raccolta di app SaaS di Azure ad](../saas-apps/tutorial-list.md), è necessario selezionare l' [esercitazione sull'integrazione specifica dell'app](../saas-apps/tutorial-list.md) per configurare il connettore di provisioning utenti pre-integrato.

In caso contrario, attenersi alla procedura seguente:

1. [Creare una richiesta](../develop/v2-howto-app-gallery-listing.md) per un connettore di provisioning utenti pre-integrato. Il nostro team collaborerà con te e lo sviluppatore di applicazioni per caricare la tua applicazione sulla nostra piattaforma se supporta SCIM.

1. Usare il supporto del provisioning utenti generico di [BYOA scim](../app-provisioning/use-scim-to-provision-users-and-groups.md) per l'app. Si tratta di un requisito per Azure AD di eseguire il provisioning degli utenti nell'app senza un connettore di provisioning pre-integrato.

1. Se l'applicazione è in grado di usare il connettore BYOA SCIM, fare riferimento a [BYOA scim Integration tutorial](../app-provisioning/use-scim-to-provision-users-and-groups.md) per configurare il connettore BYOA scim per l'applicazione.

Per ulteriori informazioni, vedere [quali applicazioni e sistemi è possibile utilizzare con Azure ad provisioning utenti automatico?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Raccogliere informazioni per autorizzare l'accesso alle applicazioni

La configurazione del provisioning utenti automatico è un processo in base all'applicazione. Per ogni applicazione è necessario fornire le [credenziali di amministratore](../app-provisioning/configure-automatic-user-provisioning-portal.md) per connettersi all'endpoint di gestione utenti del sistema di destinazione.

L'immagine seguente mostra una versione delle credenziali amministrative richieste:

![Schermata Provisioning per la gestione delle impostazioni di provisioning degli account utente](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Mentre alcune applicazioni richiedono il nome utente e la password dell'amministratore, altre possono richiedere un bearer token.

## <a name="plan-user-and-group-provisioning"></a>Pianificare il provisioning di utenti e gruppi

Se si Abilita il provisioning utenti per le app aziendali, il [portale di Azure](https://portal.azure.com/) controlla i valori degli attributi tramite il mapping degli attributi.

### <a name="determine-operations-for-each-saas-app"></a>Determinare le operazioni per ogni app SaaS

Ogni applicazione può disporre di attributi univoci per utenti o gruppi di cui è necessario eseguire il mapping agli attributi nel Azure AD. L'applicazione può avere solo un subset di operazioni CRUD disponibili.

Per ogni applicazione, documentare le informazioni seguenti:

* Operazioni di provisioning CRUD da eseguire sugli oggetti utente e gruppo per i sistemi di destinazione. Ogni proprietario dell'app SaaS, ad esempio, potrebbe non desiderare tutte le operazioni possibili.

* Attributi disponibili nel sistema di origine

* Attributi disponibili nel sistema di destinazione

* Mapping degli attributi tra i sistemi.

### <a name="choose-which-users-and-groups-to-provision"></a>Scegliere gli utenti e i gruppi da provisioning

Prima di implementare il provisioning utenti automatico, è necessario determinare gli utenti e i gruppi di cui eseguire il provisioning per l'applicazione.

* Usare i [filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) per definire regole basate su attributi che determinano gli utenti di cui viene eseguito il provisioning in un'applicazione.

* Usare quindi [le assegnazioni di utenti e gruppi](../manage-apps/assign-user-or-group-access-portal.md) in base alle esigenze per un filtro aggiuntivo.

### <a name="define-user-and-group-attribute-mapping"></a>Definire il mapping degli attributi di utenti e gruppi

Per implementare il provisioning utenti automatico, è necessario definire gli attributi utente e gruppo necessari per l'applicazione. Esiste un set preconfigurato di attributi e [mapping](../app-provisioning/configure-automatic-user-provisioning-portal.md) di attributi tra Azure ad oggetti utente e gli oggetti utente di ogni applicazione SaaS. Non tutte le app SaaS abilitano gli attributi di gruppo.

Azure AD supporta il mapping diretto da attributo a attributo, fornendo valori costanti o [scrivendo espressioni per i mapping degli attributi](../app-provisioning/functions-for-customizing-application-data.md). Questa flessibilità offre un controllo accurato degli elementi che verranno popolati nell'attributo del sistema di destinazione. È possibile usare [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) e Graph Explorer per esportare i mapping degli attributi e lo schema del provisioning degli utenti in un file JSON e importarli di nuovo in Azure ad.

Per altre informazioni, vedere [personalizzazione del provisioning utenti Attribute-Mappings per applicazioni SaaS in Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerazioni speciali per il provisioning degli utenti

Per ridurre i problemi di post-distribuzione, tenere presente quanto segue:

* Verificare che gli attributi usati per eseguire il mapping degli oggetti utente/gruppo tra le applicazioni di origine e di destinazione siano resilienti. Non devono causare il provisioning degli utenti o dei gruppi in modo errato se gli attributi cambiano, ad esempio se un utente passa a una parte diversa della società.

* Per il corretto funzionamento del provisioning degli utenti, le applicazioni possono presentare restrizioni e/o requisiti specifici che devono essere soddisfatti. Ad esempio, Slack tronca i valori per determinati attributi. Vedere le [esercitazioni di provisioning utenti automatico](../saas-apps/tutorial-list.md) specifiche per ogni applicazione.

* Verificare la coerenza dello schema tra i sistemi di origine e di destinazione. I problemi comuni includono attributi come UPN o messaggi non corrispondenti. Ad esempio, UPN in Azure AD impostato come *john_smith@contoso.com* e nell'app, è *jsmith@contoso.com* . Per ulteriori informazioni, vedere il [riferimento allo schema user and Group](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Pianificare test e sicurezza

In ogni fase della distribuzione verificare che i risultati siano quelli previsti e controllare i cicli di provisioning.

### <a name="plan-testing"></a>Panificare i test

Dopo aver configurato il provisioning utenti automatico per l'applicazione, si eseguiranno i test case per verificare che la soluzione soddisfi i requisiti dell'organizzazione.

| Scenari| Risultati previsti |
| - | - |
| L'utente viene aggiunto a un gruppo assegnato al sistema di destinazione | Viene eseguito il provisioning dell'oggetto utente nel sistema di destinazione. <br>L'utente può accedere al sistema di destinazione ed eseguire le azioni desiderate. |
| L'utente viene rimosso da un gruppo assegnato al sistema di destinazione | Viene effettuato il deprovisioning dell'oggetto utente nel sistema di destinazione.<br>L'utente non può accedere al sistema di destinazione. |
| Le informazioni utente vengono aggiornate in Azure AD da qualsiasi metodo | Gli attributi utente aggiornati vengono riflessi nel sistema di destinazione dopo un ciclo incrementale |
| L'utente è esterno all'ambito | L'oggetto utente è disabilitato o eliminato. <br>Nota: questo comportamento viene sostituito per il [provisioning dei giorni lavorativi](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Pianificare la sicurezza

Come parte di una distribuzione, è normale che sia necessaria una revisione della sicurezza. Se è necessaria una verifica della sicurezza, vedere i molti Azure AD [white paper](https://www.microsoft.com/download/details.aspx?id=36391) che fornisce una panoramica dell'identità come servizio.

### <a name="plan-rollback"></a>Pianificare il rollback

Se l'implementazione del provisioning utenti automatico non funziona nel modo desiderato nell'ambiente di produzione, i passaggi di rollback seguenti possono risultare utili per il ripristino di uno stato valido noto precedente:

1. Esaminare il [report di riepilogo del provisioning](../app-provisioning/check-status-user-account-provisioning.md) e i [log di provisioning](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) per determinare le operazioni non corrette eseguite negli utenti e/o nei gruppi interessati.

1. Usare i log di controllo del provisioning per determinare l'ultimo stato valido noto degli utenti e/o dei gruppi interessati. Esaminare anche i sistemi di origine (Azure AD o AD).

1. Collaborare con il proprietario dell'applicazione per aggiornare gli utenti e/o i gruppi interessati direttamente nell'applicazione usando gli ultimi valori di stato validi noti.

## <a name="deploy-automatic-user-provisioning-service"></a>Distribuire il servizio di provisioning utenti automatico

Scegliere i passaggi che si allineano ai requisiti della soluzione.

### <a name="prepare-for-the-initial-cycle"></a>Preparare il ciclo iniziale

Quando il servizio di provisioning Azure AD viene eseguito per la prima volta, il ciclo iniziale sul sistema di origine e i sistemi di destinazione crea uno snapshot di tutti gli oggetti utente per ogni sistema di destinazione.

Quando si Abilita il provisioning automatico per un'applicazione, il ciclo iniziale può richiedere da 20 minuti a diverse ore. La durata dipende dalle dimensioni della directory Azure AD e dal numero di utenti nell'ambito del provisioning.

Il servizio di provisioning archivia lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni dei cicli incrementali successivi.

### <a name="configure-automatic-user-provisioning"></a>Configurare il provisioning automatico degli utenti

Usare il [portale di Azure](https://portal.azure.com/) per gestire il provisioning e il deprovisioning automatici degli account utente per le applicazioni che lo supportano. Seguire la procedura descritta in [ricerca per categorie configurare il provisioning automatico in un'applicazione?](../app-provisioning/user-provisioning.md)

Il servizio di provisioning utenti di Azure AD può essere configurato e gestito anche con l'[API di Microsoft Graph](/graph/api/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gestire il provisioning utenti automatico

Ora che è stato distribuito, è necessario gestire la soluzione.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorare lo stato dell'operazione di provisioning degli utenti

Dopo un [ciclo iniziale](../app-provisioning/user-provisioning.md)riuscito, il servizio di provisioning di Azure ad eseguirà aggiornamenti incrementali a tempo indeterminato, a intervalli specifici per ogni applicazione, fino a quando non si verifica uno degli eventi seguenti:

* Il servizio viene arrestato manualmente e viene attivato un nuovo ciclo iniziale usando il [portale di Azure](https://portal.azure.com/)o usando il comando [Microsoft Graph API](/graph/api/resources/synchronization-overview) appropriato.

* Un nuovo ciclo iniziale viene attivato da una modifica nei mapping degli attributi o nei filtri di ambito.

* Il processo di provisioning viene messo in quarantena a causa di una percentuale elevata di errori e rimane in quarantena per più di quattro settimane quando verrà disabilitato automaticamente.

Per esaminare questi eventi e tutte le altre attività eseguite dal servizio di provisioning, fare riferimento a Azure AD [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

Per comprendere il tempo di esecuzione dei cicli di provisioning e il monitoraggio dello stato del processo di provisioning, è possibile [controllare lo stato del provisioning dell'utente](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Ottieni informazioni dettagliate dai report

Azure AD possibile fornire [informazioni aggiuntive](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) sull'utilizzo del provisioning degli utenti dell'organizzazione e sull'integrità operativa tramite i log di controllo e i report.

Gli amministratori devono controllare il report di riepilogo del provisioning per monitorare l'integrità operativa del processo di provisioning. Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di controllo Azure AD. Vedere [esercitazione: creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

Si consiglia di assumere la proprietà di e di utilizzare questi report in una cadenza che soddisfi i requisiti dell'organizzazione. Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni.

### <a name="troubleshoot"></a>Risolvere problemi

Per risolvere eventuali problemi che possono verificarsi durante il provisioning, vedere i collegamenti seguenti:

* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem.md)

* [Sincronizzare un attributo dal Active Directory locale a Azure AD per il provisioning in un'applicazione](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory](./user-provisioning.md)

* [Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [È in corso il provisioning di un set errato di utenti per un'applicazione della raccolta di Azure AD](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>Documentazione utile

* [Scrittura di espressioni per i mapping degli attributi](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD synchronization API overview](/graph/api/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)

* [Ignora l'eliminazione di account utente che non rientrano nell'ambito](skip-out-of-scope-deletions.md)

* [Agente di provisioning di Azure AD Connect: cronologia delle versioni](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Risorse

* [Inviare commenti e suggerimenti per il prodotto](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Per rimanere aggiornati sulle novità relative a Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Forum Microsoft Q&A Azure AD](https://docs.microsoft.com/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>Passaggi successivi
* [Configurare il provisioning utenti automatico](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Esportare o importare la configurazione di provisioning usando Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Scrittura di espressioni per i mapping degli attributi in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)
