---
title: Panoramica di Azure Blueprint
description: Informazioni sul servizio Azure Blueprints, che consente di creare, definire e distribuire artefatti nell'ambiente di Azure.
ms.date: 11/21/2019
ms.topic: overview
ms.openlocfilehash: 07d84d658d88e977cd73176861e5c5e080c02857
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74321770"
---
# <a name="what-is-azure-blueprints"></a>Informazioni su Azure Blueprint

Così come un progetto consente a un ingegnere o un architetto di tracciare i parametri di progettazione, Azure Blueprint consente agli architetti cloud e ai gruppi centrali del reparto IT di definire un set ripetibile di risorse di Azure che implementa ed è conforme a standard, criteri e requisiti di un'organizzazione. Azure Blueprint consente ai team di sviluppo di creare e realizzare rapidamente nuovi ambienti sapendo che vengono creati in conformità con l'organizzazione con un set di componenti integrati, ad esempio reti, per velocizzare lo sviluppo e il recapito.

I progetti costituiscono un metodo dichiarativo per orchestrare la distribuzione di vari modelli di risorse e altri artefatti, ad esempio:

- Assegnazioni di ruoli
- Assegnazioni di criteri
- Modelli di Gestione risorse di Azure
- Gruppi di risorse

Il servizio Azure Blueprints è supportato da [Azure Cosmos DB](../../cosmos-db/introduction.md) distribuito a livello globale.
Gli oggetti del progetto vengono replicati in più aree di Azure. Questa replica fornisce bassa latenza, disponibilità elevata e accesso coerente agli oggetti del progetto, indipendentemente dall'area in cui Blueprints distribuisce le risorse.

## <a name="how-its-different-from-resource-manager-templates"></a>In cosa differisce dai modelli di Resource Manager

Il servizio è progettato per facilitare la _configurazione dell'ambiente_. Questa configurazione spesso è costituita da un set di gruppi di risorse, criteri, assegnazioni di ruolo e distribuzioni dei modelli di Resource Manager. Un progetto è un pacchetto che riunisce ognuno di questi tipi di _artefatti_ e consente di comporre e indicare la versione di tale pacchetto, anche tramite una pipeline CI/CD. In definitiva, ognuno viene assegnato a una sottoscrizione in una singola operazione che può essere controllata e monitorata.

Quasi tutto ciò che si desidera includere per la distribuzione nei progetti può essere eseguito con un modello di Resource Manager. Tuttavia, un modello di Resource Manager è un documento che non esiste in modo nativo in Azure: ognuno viene archiviato in locale o nel controllo del codice sorgente. Il modello viene usato per le distribuzioni di una o più risorse di Azure, ma dopo la distribuzione di tali risorse non c'è una connessione o relazione attiva con il modello.

Con i progetti, la relazione tra la definizione di progetto (_cosa_ distribuire) e l'assegnazione di progetto (_cosa è stato_ distribuito) viene mantenuta. Questa connessione supporta un migliore monitoraggio e controllo delle distribuzioni. Blueprint può anche aggiornare più sottoscrizioni contemporaneamente regolate dallo stesso progetto.

Non è necessario scegliere tra un modello di Resource Manager e un progetto. Ogni progetto può essere costituito da zero o più _artefatti_ dei modelli di Resource Manager. Ciò significa che il lavoro richiesto in precedenza per sviluppare e gestire una libreria di modelli di Resource Manager può essere riusato nei progetti.

## <a name="how-its-different-from-azure-policy"></a>In cosa differisce dai Criteri di Azure

Un progetto è un pacchetto o un contenitore per la composizione di specifici set di standard, criteri e requisiti correlati all'implementazione dei servizi cloud, della sicurezza e della progettazione di Azure, che può essere riusato per garantire coerenza e conformità.

I [criteri](../policy/overview.md) costituiscono un sistema predefinito di autorizzazione e negazione esplicita che mira alle proprietà delle risorse durante la distribuzione e viene usato per le risorse già esistenti. I criteri supportano la governance cloud verificando che le risorse all'interno di una sottoscrizione siano conformi a standard e requisiti.

L'inclusione di criteri in un progetto consente non solo di creare il criterio o la progettazione corretta durante l'assegnazione del progetto, ma assicura anche che nell'ambiente vengano eseguite solo le modifiche approvate o previste, in modo da garantire la conformità continua con la finalità del progetto.

I criteri possono essere inclusi nella definizione di un progetto come i vari _artefatti_. In Azure Blueprint è inoltre supportato l'uso di parametri con criteri e iniziative.

## <a name="blueprint-definition"></a>Definizione di progetto

Un progetto è costituito da _artefatti_. Azure Blueprint supporta attualmente le seguenti risorse come artefatti:

|Risorsa  | Opzioni della gerarchia| Descrizione  |
|---------|---------|---------|
|Gruppi di risorse | Subscription | Creare un nuovo gruppo di risorse per l'uso da parte di altri artefatti nel progetto.  Questi gruppi di risorse segnaposto consentono di organizzare le risorse strutturandole esattamente nel modo desiderato e forniscono un limitatore di ambito per i criteri e gli artefatti di assegnazione dei ruoli inclusi, nonché per i modelli di Azure Resource Manager. |
|Modello di Azure Resource Manager | Sottoscrizione, gruppo di risorse | Vengono usati modelli, inclusi quelli annidati e collegati, per comporre ambienti complessi. Esempi di ambienti complessi sono: una farm di SharePoint, la configurazione dello stato di Automazione di Azure o un'area di lavoro Log Analytics. |
|Assegnazione dei criteri | Sottoscrizione, gruppo di risorse | Consente di assegnare criteri o iniziative alla sottoscrizione a cui è assegnato il progetto. I criteri o le iniziative devono trovarsi nell'ambito della posizione della definizione di progetto. Se i criteri o le iniziative includono dei parametri, questi vengono assegnati al momento della creazione del progetto o durante l'assegnazione dello stesso. |
|Assegnazione di ruolo | Sottoscrizione, gruppo di risorse | Aggiungere un utente o gruppo esistente a un ruolo predefinito per assicurarsi che gli utenti corretti possano sempre accedere correttamente alle risorse. Le assegnazioni di ruolo possono essere definite per l'intera sottoscrizione o annidate in un gruppo di risorse specifiche incluso nel progetto. |

### <a name="blueprint-definition-locations"></a>Posizioni delle definizioni di progetto

Durante la creazione di una definizione di progetto, viene definita la posizione di salvataggio del progetto. I progetti possono essere salvati in un [gruppo di gestione](../management-groups/overview.md) o una sottoscrizione a cui si ha accesso come **collaboratore**. Se la posizione è un gruppo di gestione, il progetto è assegnabile a qualsiasi sottoscrizione figlio di tale gruppo di gestione.

### <a name="blueprint-parameters"></a>Parametri di progetto

Azure Blueprint può passare i parametri a criteri o iniziative oppure a un modello di Azure Resource Manager.
Quando si aggiunge un _artefatto_ a un progetto, l'autore decide se fornire un valore definito per ogni assegnazione di progetto o se consentire a ogni assegnazione di progetto di fornire un valore in fase di assegnazione.
Questa flessibilità consente di definire un valore predeterminato per tutti gli usi del progetto o per consentire che tale decisione venga presa al momento dell'assegnazione.

> [!NOTE]
> Un progetto può avere i propri parametri, ma al momento questi possono essere creati solo se un progetto è generato dall'API REST anziché tramite il portale.

Per altre informazioni, vedere [Parametri di progetto](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Pubblicazione di progetti

Quando si crea un progetto per la prima volta, questo viene considerato in modalità **bozza**. Quando è pronto per l'assegnazione, deve essere **pubblicato**. La pubblicazione richiede la definizione di una stringa **versione** (lettere, numeri e trattini con una lunghezza massima di 20 caratteri) insieme a **note di modifica** facoltative. La stringa **versione** consente di distinguere il progetto dalle modifiche future apportate allo stesso e di assegnare ciascuna versione. Questo controllo delle versioni significa anche che è possibile assegnare diverse **versioni** dello stesso progetto alla stessa sottoscrizione. Quando al progetto vengono apportate altre modifiche, la **versione**
**pubblicata** è ancora presente, così come le **modifiche non pubblicate**. Una volta ultimate le modifiche, il progetto aggiornato viene **pubblicato** con una nuova **versione** univoca e può essere ora assegnato.

## <a name="blueprint-assignment"></a>Assegnazione progetto

Ogni **versione** **pubblicata** di un progetto può essere assegnata (con un nome contenente al massimo 90 caratteri) a una sottoscrizione esistente. Nel portale il progetto imposta la **versione** **pubblicata** più di recente come predefinita. Se sono presenti parametri dell'artefatto (o parametri di progetto), questi vengono definiti durante il processo di assegnazione.

## <a name="permissions-in-azure-blueprints"></a>Autorizzazioni in Azure Blueprint

Per usare i progetti è necessario essere autorizzati tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Per creare i progetti l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprints/write` - Creare una definizione di progetto
- `Microsoft.Blueprint/blueprints/artifacts/write` - Creare artefatti su una definizione di progetto
- `Microsoft.Blueprint/blueprints/versions/write` - Pubblicare un progetto

Per eliminare i progetti l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Le autorizzazioni per la definizione di progetto devono essere concesse o ereditate nell'ambito del gruppo di gestione o della sottoscrizione in cui è stato eseguito il salvataggio.

Per assegnare o annullare l'assegnazione di un progetto l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprintAssignments/write` - Assegnare un progetto
- `Microsoft.Blueprint/blueprintAssignments/delete` - Annullare l'assegnazione di un progetto

> [!NOTE]
> Dato che le assegnazioni di progetto vengono create in una sottoscrizione, le autorizzazioni per assegnare o annullare l'assegnazione devono essere concesse nell'ambito della sottoscrizione o essere ereditate nell'ambito della sottoscrizione.

Sono disponibili i ruoli predefiniti seguenti:

|Ruolo Controllo degli accessi in base al ruolo | Descrizione |
|-|-|
|[Proprietario](../../role-based-access-control/built-in-roles.md#owner) | Oltre ad altre autorizzazioni, include tutte le autorizzazioni correlate ad Azure Blueprint. |
|[Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) | Oltre ad altre autorizzazioni, può creare ed eliminare definizioni di progetto, ma non ha le autorizzazioni per l'assegnazione di progetti. |
|[Collaboratore di progetto](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Può gestire le definizioni di progetto, ma non assegnarle. |
|[Operatore di progetto](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Può assegnare i progetti pubblicati esistenti, ma non può creare nuove definizioni di progetto. L'assegnazione di progetti funziona solo se viene eseguita con un'identità gestita assegnata dall'utente. |

Se questi ruoli predefiniti non soddisfano specifiche esigenze di sicurezza, provare a creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Se si utilizza un'identità gestita assegnata al sistema, per poter abilitare la distribuzione l'entità servizio per Azure Blueprint richiede il ruolo **proprietario** nella sottoscrizione assegnata. Se si usa il portale, questo ruolo viene automaticamente concesso e revocato per la distribuzione. Se si usa l'API REST, questo ruolo deve essere concesso manualmente, ma viene comunque revocato automaticamente al termine della distribuzione. Se si usa un'identità gestita assegnata dall'utente, solo l'utente che crea l'assegnazione del progetto necessita dell'autorizzazione `Microsoft.Blueprint/blueprintAssignments/write`, inclusa nei ruoli predefiniti **Proprietario** e **Operatore di progetto**.

## <a name="naming-limits"></a>Limiti di denominazione

Per determinati campi, esistono le limitazioni seguenti:

|Oggetto|Campo|Caratteri consentiti|Max. Length|
|-|-|-|-|
|Progetto|Nome|lettere, numeri, trattini e punti|48|
|Progetto|Versione|lettere, numeri, trattini e punti|20|
|Assegnazione progetto|Nome|lettere, numeri, trattini e punti|90|
|Artefatto del progetto|Nome|lettere, numeri, trattini e punti|48|

## <a name="video-overview"></a>Video introduttivo

La panoramica seguente di Azure Blueprints deriva da Azure Fridays. Per il download del video, visitare [Azure Fridays - Panoramica di Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) su Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Passaggi successivi

- [Creare un progetto - Portale](./create-blueprint-portal.md)
- [Creare un progetto - PowerShell](./create-blueprint-powershell.md)
- [Creare un progetto - API REST](./create-blueprint-rest-api.md)