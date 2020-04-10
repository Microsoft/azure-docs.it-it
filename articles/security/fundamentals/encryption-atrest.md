---
title: Crittografia dei dati inattivi di Microsoft Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della crittografia dei dati inattivi di Microsoft Azure, con una descrizione delle funzionalità e considerazioni generali.
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: mbaldwin
ms.openlocfilehash: c3491a54682e8f2b244c0400480a69e083335f5c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008390"
---
# <a name="azure-data-encryption-at-rest"></a>Crittografia dei dati inattivi di Azure

Microsoft Azure include strumenti per proteggere i dati in base alle esigenze di sicurezza e conformità dell'azienda. Questo documento è incentrato su:

- come proteggere i dati inattivi in Microsoft Azure,
- i vari componenti che compongono l'implementazione di protezione dei dati,
- i pro e i contro dei vari principali approcci alla protezione della gestione.

La crittografia dei dati inattivi è un requisito di sicurezza comune. In Azure, le organizzazioni possono crittografare i dati inattivi senza rischi o senza sostenere il costo di una soluzione di gestione delle chiavi personalizzata. Le organizzazioni hanno la possibilità di lasciare che sia Azure a gestire completamente la crittografia dei dati inattivi. Sono inoltre disponibili varie opzioni per gestire con precisione la crittografia o le chiavi di crittografia.

## <a name="what-is-encryption-at-rest"></a>Che cos'è la crittografia dei dati inattivi?

La crittografia dei dati inattivi consiste nella codifica crittografica (crittografia) dei dati quando questi vengono resi persistenti. La crittografia dei dati inattivi di Azure usa la crittografia simmetrica per crittografare e decrittografare rapidamente grandi quantità di dati in base a un semplice modello concettuale:

- Viene usata una chiave di crittografia simmetrica per crittografare i dati quando vengono scritti nella risorsa di archiviazione.
- La chiave di crittografia viene usata per decrittografare i dati durante la loro preparazione per l'uso in memoria.
- I dati possono essere partizionati ed è possibile usare chiavi diverse per ogni partizione.
- Le chiavi devono essere archiviate in una posizione sicura con controllo di accesso basato su identità e criteri di controllo. Le chiavi di crittografia dei dati vengono spesso crittografate con una chiave di crittografia della chiave in Archiviazione delle chiavi di Azure per limitare ulteriormente l'accesso.

A livello pratico, gli scenari di gestione e di controllo delle chiavi, nonché le garanzie di scalabilità e disponibilità, richiedono costrutti aggiuntivi. Di seguito sono descritti i concetti e i componenti relativi alla crittografia dei dati inattivi di Microsoft Azure.

## <a name="the-purpose-of-encryption-at-rest"></a>Scopo della crittografia dei dati inattivi

La crittografia dei dati inattivi offre la protezione dei dati per i dati archiviati (inattivi). Gli attacchi contro i dati inattivi includono i tentativi di ottenere l'accesso fisico all'hardware in cui sono archiviati i dati e quindi compromettere i dati contenuti. In un attacco di questo tipo, il disco rigido di un server potrebbe essere stato gestito in modo non corretto durante la manutenzione, consentendo a un utente malintenzionato di rimuovere il disco rigido. L'autore dell'attacco potrà quindi inserire il disco rigido in un computer sotto il proprio controllo per tentare di accedere ai dati.

La crittografia dei dati inattivi è progettata per impedire a un utente malintenzionato di accedere ai dati non crittografati, garantendo che i dati siano crittografati quando sono su disco. Se un utente malintenzionato ottiene un disco rigido con i dati crittografati ma non le chiavi di crittografia, l'autore dell'attacco deve decodificare la crittografia per riuscire a leggere i dati. Questo tipo di attacco è molto più complesso e richiede maggiori risorse rispetto all'accesso a dati non crittografati su un disco rigido. Per questo motivo, la crittografia dei dati inattivi è estremamente utile e costituisce un requisito di alta priorità per molte organizzazioni.

La crittografia inattivi può anche essere richiesta dalla necessità di un'organizzazione per la governance dei dati e le attività di conformità. Regolamenti governativi e di settore, come HIPAA, PCI e FedRAMP, definiscono specifiche misure di sicurezza relativi ai requisiti di protezione e crittografia dei dati. La crittografia dei dati inattivi è una misura obbligatoria necessaria per la conformità ad alcune di queste normative. Per ulteriori informazioni sull'approccio di Microsoft alla convalida FIPS 140-2, vedere [Federal Information Processing Standard (FIPS) Publication 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2). 

Oltre a soddisfare i requisiti normativi e di conformità, la crittografia dei dati inattivi rappresenta una misura di protezione e difesa avanzata. Microsoft Azure offre una piattaforma conforme per servizi, applicazioni e dati. Offre inoltre funzionalità complete per la sicurezza fisica e delle strutture, il controllo di accesso ai dati e il controllo. Tuttavia, è importante fornire ulteriori misure di sicurezza "sovrapposte" nel caso in cui una delle altre misure di sicurezza non riesca e la crittografia inattivi fornisce una misura di sicurezza di questo tipo.

Microsoft si impegna a mettere a disposizione opzioni per la crittografia dei dati inattivi per i servizi cloud e offre ai clienti il controllo delle chiavi di crittografia e dei log di uso delle chiavi. Microsoft ha inoltre in progetto di applicare la crittografia dei dati inattivi a tutti i dati dei clienti per impostazione predefinita.

## <a name="azure-encryption-at-rest-components"></a>Componenti della crittografia dei dati inattivi di Azure

Come descritto in precedenza, l'obiettivo della crittografia dei dati inattivi è crittografare i dati resi persistenti su disco con una chiave di crittografia segreta. Per raggiungere tale obiettivo, sono necessarie funzionalità per la creazione, l'archiviazione, il controllo dell'accesso e la gestione delle chiavi di crittografia. Anche se i dettagli possono variare, i servizi per la crittografia dei dati inattivi di Azure possono essere descritti nei termini illustrati nel diagramma seguente.

![Componenti](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

La posizione di archiviazione delle chiavi di crittografia e il controllo dell'accesso a queste chiavi è essenziale per un modello di crittografia dei dati inattivi. Le chiavi devono essere estremamente protette ma gestibili dagli utenti specificati e disponibili per servizi specifici. Per i servizi di Azure, la soluzione di archiviazione delle chiavi consigliata è Azure Key Vault, che fornisce un'esperienza di gestione comune per tutti i servizi. Le chiavi sono archiviate e gestite in insiemi di credenziali delle chiavi e l'accesso a un insieme di credenziali delle chiavi può essere assegnato a utenti o servizi. Azure Key Vault supporta la creazione di chiavi da parte dei clienti o l'importazione di chiavi dei clienti per l'uso in scenari con chiavi di crittografia gestite dal cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Le autorizzazioni per l'uso delle chiavi archiviate in Azure Key Vault (per la gestione o per accedervi per la crittografia e la decrittografia dei dati inattivi) possono essere assegnate agli account di Azure Active Directory.

### <a name="key-hierarchy"></a>Gerarchia delle chiavi

Viene usata più di una chiave di crittografia in un'implementazione della crittografia per i dati inattivi. L'archiviazione di una chiave di crittografia in Azure Key Vault garantisce l'accesso alla chiave sicura e la gestione centralizzata delle chiavi. Tuttavia, l'accesso locale del servizio alle chiavi di crittografia è più efficiente per la crittografia e la decrittografia in blocco rispetto all'interazione con Key Vault per ogni operazione sui dati, consentendo una crittografia più avanzata e prestazioni migliori. Limitare l'uso di una sola chiave di crittografia riduce il rischio di compromissione della chiave e il costo della ripetizione della crittografia quando è necessario sostituire una chiave. I modelli di crittografia di Azure inattivi usano una gerarchia di chiavi costituita dai tipi di chiavi seguenti per soddisfare tutte queste esigenze:Azure encryptions at rest models use a key hierarchy made up of the following types of keys in order to address all these needs:

- **Chiave DEK (Data Encryption Key)**: una chiave AES256 simmetrica usata per crittografare una partizione o un blocco di dati.  Una singola risorsa può avere diverse partizioni e più chiavi DEK. La crittografia di ogni blocco di dati con una chiave diversa rende più complessi gli attacchi di crittoanalisi. È necessario l'accesso alle chiavi DEK per il provider di risorse o l'istanza dell'applicazione che esegue la crittografia e la decrittografia di un blocco specifico. Quando una chiave DEK viene sostituita con una nuova chiave, devono essere nuovamente crittografati con la nuova chiave solo i dati nel blocco associato.
- **Chiave di crittografia chiave (KEK):** chiave di crittografia utilizzata per crittografare le chiavi di crittografia dei dati. L'uso di una chiave di crittografia chiave che non esce mai da Key Vault consente di crittografare e controllare le chiavi di crittografia dei dati stesse. L'entità che ha accesso alla chiave KEK può essere diversa dall'entità che richiede la chiave DEK. Un'entità può gestire l'accesso alla chiave DEK per limitare l'accesso di ogni chiave DEK a una partizione specifica. Poiché è necessaria la chiave KEK per decrittografare le chiavi DEK, la chiave KEK è di fatto un singolo punto che consente di eliminare in modo efficace le chiavi DEK eliminando la chiave KEK.

Le chiavi di crittografia dei dati, crittografate con le chiavi di crittografia delle chiavi, vengono archiviate separatamente e solo un'entità con accesso alla chiave di crittografia della chiave può decrittografare queste chiavi di crittografia dei dati. Sono supportati diversi modelli di archiviazione delle chiavi. Ogni modello verrà illustrato in modo più dettagliato nella sezione successiva.

## <a name="data-encryption-models"></a>Modelli di crittografia dei dati

Comprendere i diversi modelli di crittografia, e i relativi vantaggi e svantaggi, è fondamentale per capire come i vari provider di risorse in Azure implementano la crittografia dei dati inattivi. Queste definizioni sono condivise tra tutti i provider di risorse in Azure per garantire un linguaggio e una tassonomia comuni.

Esistono tre scenari per la crittografia sul lato server:

- Crittografia sul lato server con chiavi gestite dal servizio
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Microsoft gestisce le chiavi
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi tramite Azure Key Vault
  - Funzionalità cloud complete

- Crittografia lato server con chiavi gestite dal cliente su hardware controllato dal cliente
  - I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia
  - Il cliente controlla le chiavi su hardware controllato dal cliente
  - Funzionalità cloud complete

Per la crittografia lato client, considerare quanto segue:

- I servizi di Azure non possono visualizzare i dati decrittografati
- I clienti mantengono e archiviano le chiavi in locale o in altri archivi sicuri. Le chiavi non sono disponibili per i servizi di Azure
- Funzionalità cloud ridotte

I modelli di crittografia supportati in Azure sono divisi in due gruppi principali: "Client Encryption" e "Server-side Encryption" come indicato in precedenza. Indipendentemente dal modello di crittografia dei dati inattivi in uso, per i servizi di Azure è sempre consigliabile usare un trasporto protetto, ad esempio TLS o HTTPS. La crittografia a livello di trasporto deve quindi essere gestita dal protocollo di trasporto e non deve rappresentare un fattore determinante per la scelta del modello di crittografia dei dati inattivi da usare.

### <a name="client-encryption-model"></a>Modello di crittografia client

Il modello di crittografia client fa riferimento alla crittografia che viene eseguita all'esterno del provider di risorse o da Azure dal servizio o dall'applicazione chiamante. La crittografia può essere eseguita dall'applicazione del servizio in Azure o da un'applicazione in esecuzione nel data center del cliente. In entrambi i casi, quando si usa questo modello di crittografia, il provider di risorse di Azure riceve un blob di dati crittografato senza alcuna possibilità di decrittografare i dati o avere accesso alle chiavi di crittografia. In questo modello la gestione delle chiavi viene eseguita dal servizio o dall'applicazione chiamante ed è opaca al servizio di Azure.

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modello di crittografia lato server

I modelli di crittografia lato server fanno riferimento alla crittografia che viene eseguita dal servizio di Azure. In questo modello, le operazioni di crittografia e decrittografia sono eseguite dal provider di risorse. Archiviazione di Azure può ad esempio ricevere i dati in operazioni di testo normale ed esegue internamente la crittografia e la decrittografia. Il provider di risorse può usare chiavi di crittografia gestite da Microsoft o dal cliente, a seconda della configurazione specificata.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelli di gestione delle chiavi per la crittografia lato server

Ognuno dei modelli di crittografia dei dati inattivi lato server implica caratteristiche distintive per la gestione delle chiavi. Questo include le posizioni e le modalità di creazione e archiviazione delle chiavi di crittografia, nonché i modelli di accesso e le procedure di rotazione delle chiavi.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Crittografia lato server con chiavi gestite dal servizio

Per molti clienti, il requisito essenziale consiste nel garantire che i dati siano crittografati ogni volta che sono inattivi. La crittografia sul lato server tramite le chiavi gestite dal servizio rende possibile questo modello, consentendo ai clienti di contrassegnare le specifiche risorse (account di archiviazione, database SQL e così via) per la crittografia e lasciare a Microsoft tutti gli aspetti di gestione delle chiavi, come il rilascio, la rotazione e il backup. La maggior parte dei servizi di Azure che supportano la crittografia dei dati inattivi in genere supporta questo modello di offload della gestione delle chiavi di crittografia in Azure. Il provider di risorse di Azure crea le chiavi, le inserisce in un archivio protetto e le recupera quando necessario. Questo significa che il servizio ha l'accesso completo alle chiavi e il pieno controllo della gestione del ciclo di vita delle credenziali.

![gestito](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

La crittografia sul lato server con chiavi gestite dal servizio consente pertanto di soddisfare rapidamente l'esigenza di implementare la crittografia dei dati inattivi con un sovraccarico limitato per il cliente. Quando disponibile, un cliente apre il portale di Azure per la sottoscrizione e il provider di risorse di destinazione e seleziona una casella per indicare che vuole che i dati vengano crittografati. In alcuni manager delle risorse la crittografia lato server con chiavi gestite dal servizio è attiva per impostazione predefinita.

La crittografia sul lato server con chiavi gestite da Microsoft implica che il servizio ha accesso completo all'archiviazione e gestisce le chiavi. Anche se alcuni clienti potrebbero voler gestire le chiavi perché pensano di garantire una maggiore sicurezza, durante la valutazione di questo modello è importante tenere conto del costo e del rischio associato a una soluzione personalizzata di archiviazione delle chiavi. In molti casi, un'organizzazione può stabilire che i vincoli di risorse o i rischi di una soluzione locale potrebbero essere maggiori rispetto al rischio associato alla gestione nel cloud delle chiavi di crittografia dei dati inattivi.  Tuttavia, questo modello potrebbe non essere sufficiente per le organizzazioni che hanno requisiti per controllare la creazione o il ciclo di vita delle chiavi di crittografia o per avere personale diverso gestire le chiavi di crittografia di un servizio rispetto a quelli che gestiscono il servizio (vale a dire, la separazione della gestione delle chiavi dal modello di gestione generale per il servizio).

##### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia sul lato server con chiavi gestite dal servizio, la creazione delle chiavi, l'archiviazione e l'accesso al servizio sono gestiti dal servizio. In genere, i principali provider di risorse di Azure archiviano le chiavi DEK in un archivio vicino ai dati e rapidamente disponibile e accessibile, mentre le chiavi KEK sono archiviate in un archivio interno protetto.

**Vantaggi**

- Configurazione semplice
- Microsoft gestisce la rotazione, il backup e la ridondanza delle chiavi
- Il cliente non deve sostenere il costo associato all'implementazione o il rischio di uno schema di gestione delle chiavi personalizzato.

**Svantaggi**

- Nessun controllo del cliente sulle chiavi di crittografia (specifica della chiave, ciclo di vita, revoca e così via)
- Nessuna possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault

Per gli scenari in cui il requisito prevede di crittografare i dati inattivi e controllare le chiavi di crittografia, i clienti possono usare la crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault. Alcuni servizi possono archiviare solo la chiave KEK radice in Azure Key Vault e archiviano la chiave DEK crittografata in un percorso interno più vicino ai dati. In questo scenario, i clienti possono usare le proprie chiavi nell'insieme di credenziali delle chiavi (BYOK, Bring Your Own Key) o generare nuove chiavi e usarle per crittografare le risorse desiderate. Mentre il provider di risorse esegue le operazioni di crittografia e decrittografia, utilizza la chiave di crittografia della chiave configurata come chiave radice per tutte le operazioni di crittografia.

Perdita di chiavi di crittografia della chiave significa perdita di dati. Per questo motivo, le chiavi non devono essere eliminate. Il backup delle chiavi deve essere eseguito ogni volta che vengono create o ruotate. [L'eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve essere abilitata in qualsiasi insieme di credenziali che memorizza le chiavi di crittografia della chiave. Anziché eliminare una chiave, impostare enabled su false o impostare la data di scadenza.

##### <a name="key-access"></a>Accesso alle chiavi

Il modello di crittografia sul lato server con chiavi gestite dal cliente in Azure Key Vault prevede che il servizio di accesso alle chiavi possa eseguire la crittografia e la decrittografia in base alle esigenze. Le chiavi per la crittografia dei dati inattivi vengono rese accessibili a un servizio tramite un criterio di controllo di accesso che concede all'identità del servizio l'accesso per ricevere la chiave. Un servizio di Azure in esecuzione per conto di una sottoscrizione associata può essere configurato con un'identità all'interno della sottoscrizione. Il servizio può eseguire l'autenticazione di Azure Active Directory e ricevere un token di autenticazione che lo identifica come un servizio che opera per conto della sottoscrizione. Il token può quindi essere presentato all'insieme di credenziali delle chiavi per ottenere una chiave a cui è stato consentito l'accesso.

Per le operazioni con chiavi di crittografia, può essere concesso l'accesso a un'identità del servizio per qualsiasi delle operazioni seguenti: decrypt, encrypt, unwrapkey, wrapkey, verify, sign, get, list, update, create, import, delete, backup e restore.

Per ottenere una chiave da usare per la crittografia o la decrittografia dei dati inattivi, l'identità del servizio con cui verrà eseguita l'istanza del servizio Resource Manager deve disporre di UnwrapKey (per ottenere la chiave per la decrittografia) e WrapKey (per inserire una chiave nell'insieme di credenziali delle chiavi al momento della creazione di una nuova chiave).

>[!NOTE]
>Per altri dettagli sull'autorizzazione dell'insieme di credenziali delle chiavi, vedere la pagina Proteggere l'insieme di credenziali delle chiavi nella [documentazione di Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md).

**Vantaggi**

- Controllo completo delle chiavi utilizzate: le chiavi di crittografia vengono gestite nell'insieme di credenziali delle chiavi del cliente sotto il controllo del cliente.
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Il cliente ha la piena responsabilità per la gestione dell'accesso alle chiavi
- Il cliente ha la piena responsabilità per la gestione del ciclo di vita delle chiavi
- Sovraccarico aggiuntivo per l'installazione e la configurazione

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Crittografia lato server con chiavi gestite dal cliente nell'hardware controllato dal cliente

Alcuni servizi di Azure consentono il modello di gestione delle chiavi HYOK (Host Your Own Key). Questa modalità di gestione è utile negli scenari in cui è necessario crittografare i dati inattivi e gestire le chiavi in un repository proprietario al di fuori del controllo di Microsoft.This management mode is useful in scenarios where there is a need to encrypt the data at rest and manage the keys in a proprietary repository outside of Microsoft's control. In questo modello il servizio deve recuperare la chiave da un sito esterno. Ciò ha effetto sulle garanzie di prestazioni e disponibilità e la configurazione risulta più complessa. Inoltre, poiché il servizio non ha accesso alla chiave DEK durante le operazioni di crittografia e decrittografia, le garanzie di sicurezza complessiva di questo modello sono simili allo scenario in cui le chiavi sono gestite dal cliente in Azure Key Vault.  Di conseguenza, questo modello non è appropriato per la maggior parte delle organizzazioni, a meno che non siano presenti specifici requisiti di gestione delle chiavi. A causa di queste limitazioni, la maggior parte dei servizi di Azure non supporta la crittografia lato server con chiavi gestite dal server su hardware controllato dal cliente.

##### <a name="key-access"></a>Accesso alle chiavi

Quando si usa la crittografia sul lato server con chiavi gestite dal servizio su hardware controllato dal cliente, le chiavi vengono mantenute in un sistema configurato dal cliente. I servizi di Azure che supportano questo modello forniscono un sistema per stabilire una connessione sicura a un archivio delle chiavi fornito dal cliente.

**Vantaggi**

- Controllo completo della chiave radice in uso: le chiavi di crittografia vengono gestite tramite un archivio fornito dal cliente
- Possibilità di crittografare più servizi in uno master
- Possibilità di separare la gestione delle chiavi dal modello generale di gestione per il servizio
- Possibilità di definire il servizio e la posizione delle chiavi tra diverse aree geografiche

**Svantaggi**

- Piena responsabilità per l'archiviazione di chiavi, sicurezza, prestazioni e disponibilità
- Piena responsabilità per la gestione dell'accesso alle chiavi
- Piena responsabilità per la gestione del ciclo di vita delle chiavi
- Significativi costi di installazione, configurazione e manutenzione
- Maggiore dipendenza dalla disponibilità della rete tra il data center del cliente e i data center di Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Crittografia dei dati inattivi nei servizi cloud Microsoft

I servizi cloud Microsoft vengono usati in tutti e tre i modelli cloud: IaaS, PaaS e SaaS. Di seguito sono riportati alcuni esempi di come si integrano in ciascun modello:

- Servizi software, denominati SaaS (Software as a Service), in cui l'applicazione è fornita dal cloud, come ad esempio Office 365.
- Servizi della piattaforma, con cui i clienti sfruttano il cloud nelle proprie applicazioni, che usano il cloud per elementi come le funzionalità di archiviazione, analisi e bus di servizio.
- Servizi di infrastruttura, o IaaS (Infrastructure as a Service), in cui i clienti distribuiscono sistemi operativi e applicazioni ospitati nel cloud ed eventualmente sfruttano altri servizi cloud.

### <a name="encryption-at-rest-for-saas-customers"></a>Crittografia dei dati inattivi per i clienti SaaS

Per i clienti SaaS (Software as a Service) in genere la crittografia dei dati inattivi è abilitata o disponibile in ogni servizio. Office 365 offre diverse opzioni con cui i clienti possono verificare o abilitare la crittografia dei dati inattivi. Per informazioni sui servizi di Office 365, vedere [Crittografia in Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Crittografia dei dati inattivi per i clienti PaaS

I dati del cliente Platform as a Service (PaaS) risiedono in genere in un servizio di archiviazione, ad esempio l'archiviazione BLOB, ma possono anche essere memorizzati nella cache o archiviati nell'ambiente di esecuzione dell'applicazione, ad esempio una macchina virtuale. Per visualizzare le opzioni disponibili per la crittografia dei dati inattivi, esaminare la tabella seguente per le piattaforme di archiviazione e per le applicazioni in uso.

### <a name="encryption-at-rest-for-iaas-customers"></a>Crittografia dei dati inattivi per i clienti IaaS

I clienti IaaS (Infrastructure as a Service) possono avere un'ampia gamma di servizi e applicazioni in uso. I servizi IaaS possono abilitare la crittografia dei dati inattivi nelle macchine virtuali e nei dischi rigidi virtuali ospitati in Azure usando Crittografia dischi di Azure.

#### <a name="encrypted-storage"></a>Archiviazione crittografata

Come le soluzioni PaaS, quelle IaaS possono sfruttare altri servizi di Azure che archiviano dati inattivi crittografati. In questi casi, è possibile abilitare il supporto per la crittografia dei dati inattivi come specificato da ogni servizio di Azure in uso. Nella tabella seguente sono enumerate le principali piattaforme per l'archiviazione, i servizi e le applicazioni, insieme al modello di crittografia dei dati inattivi supportato. 

#### <a name="encrypted-compute"></a>Calcolo crittografato

Tutti i dischi gestiti, le istantanee e le immagini vengono crittografati utilizzando la crittografia del servizio di archiviazione usando una chiave gestita dal servizio. Una soluzione di crittografia inattivi più completa garantisce che i dati non vengano mai mantenuti in forma non crittografata. Durante l'elaborazione dei dati in una macchina virtuale, i dati possono essere resi persistenti nel file di paging di Windows o nel file di scambio Linux, in un dump di arresto anomalo del sistema o in un log dell'applicazione. Per assicurarsi che questi dati inattivi vengano crittografati, le applicazioni IaaS possono usare Crittografia dischi di Azure su una macchina virtuale IaaS di Azure (Windows o Linux) e un disco virtuale.

#### <a name="custom-encryption-at-rest"></a>Crittografia dei dati inattivi personalizzata

È consigliabile che, quando possibile, le applicazioni IaaS sfruttino Crittografia dischi di Azure e le opzioni di crittografia dei dati inattivi fornite da qualsiasi servizio di Azure in uso. In alcuni casi, ad esempio per requisiti di crittografia irregolari o l'archiviazione non basata su Azure, uno sviluppatore di un'applicazione IaaS potrebbe avere bisogno di implementare autonomamente la crittografia dei dati inattivi. Gli sviluppatori di soluzioni IaaS possono ottimizzare l'integrazione con la gestione di Azure le aspettative dei clienti sfruttando determinati componenti di Azure. In particolare, gli sviluppatori devono usare il servizio Azure Key Vault per fornire l'archiviazione protetta delle chiavi, nonché offrire ai clienti opzioni di gestione delle chiavi coerenti con quelle della maggior parte dei servizi della piattaforma Azure. Le soluzioni personalizzate devono anche usare le identità dei servizi gestiti di Azure per consentire agli account del servizio di accedere alle chiavi di crittografia. Per informazioni per gli sviluppatori su Azure Key Vault e le identità dei servizi gestiti, vedere i rispettivi SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Supporto per il modello di crittografia dei provider di risorse di Azure

Ogni servizio di Microsoft Azure supporta uno o più modelli di crittografia dei dati inattivi. Per alcuni servizi, tuttavia, uno o più dei modelli di crittografia potrebbero non essere applicabili. I servizi che supportano scenari con chiavi gestite dal cliente potrebbero supportare solo un subset dei tipi di chiavi supportati da Azure Key Vault per le chiavi di crittografia della chiave. I servizi possono inoltre rilasciare il supporto per questi scenari e per i tipi di chiave con pianificazioni diverse. In questa sezione viene descritto il supporto per la crittografia dei dati inattivi al momento della stesura del presente documento per ognuno dei principali servizi di archiviazione dati di Azure.

### <a name="azure-disk-encryption"></a>Crittografia dischi di Azure

Tutti i clienti che usano le funzionalità IaaS (Infrastructure as a Service) di Azure possono ottenere la crittografia dei dati inattivi per le macchine virtuali IaaS e i dischi tramite Crittografia dischi di Azure. Per altre informazioni sulla crittografia del disco di Azure, vedere la documentazione relativa a Crittografia disco di Azure.For more information on Azure Disk encryption, see the [Azure Disk Encryption documentation.](../azure-security-disk-encryption-overview.md)

#### <a name="azure-storage"></a>Archiviazione di Azure

Tutti i servizi di Archiviazione di Azure (archiviazione BLOB, Archiviazione code, Archiviazione tabelle e File di Azure) supportano la crittografia lato server inattivi.All Azure Storage services (Blob storage, Queue storage, Table storage, and Azure Files) support server-side encryption at rest. alcuni servizi supportano inoltre le chiavi gestite dal cliente e la crittografia lato client. 

- Lato server: tutti i servizi di archiviazione di Azure abilitano la crittografia lato server per impostazione predefinita con chiavi gestite dal servizio, in modo trasparente all'applicazione. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../../storage/common/storage-service-encryption.md). Archiviazione BLOB di Azure e File di Azure supportano anche chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Lato client: BLOB di Azure, tabelle e code di Azure supportano la crittografia lato client. Quando usano la crittografia lato client, i clienti crittografano i dati e li caricano come un BLOB crittografato. La gestione delle chiavi viene eseguita dal cliente. Per altre informazioni, vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>database SQL di Azure

Database SQL di Azure attualmente supporta la crittografia dei dati inattivi per gli scenari di crittografia sul lato client e sul lato del servizio gestito da Microsoft.

Il supporto per la crittografia server viene attualmente fornito tramite la funzionalità di SQL denominata Transparent Data Encryption. Quando un cliente di Database SQL di Azure abilita TDE, le chiavi vengono create e gestite automaticamente. La crittografia dei dati inattivi può essere abilitata a livello di database e di server. A partire da giugno 2017, la funzionalità [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) è abilitata per impostazione predefinita nei nuovi database creati. Database SQL di Azure supporta chiavi RSA a 2048 bit gestite dal cliente in Azure Key Vault. Per altre informazioni, vedere [Transparent Data Encryption con supporto BYOK (Bring Your Own Key) per il database SQL di Azure e Azure SQL Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

La crittografia lato client dei dati di Database SQL di Azure è supportata tramite la funzionalità [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Always Encrypted usa una chiave creata e archiviata dal client. I clienti possono archiviare la chiave master in un archivio certificati di Windows, in Azure Key Vault o in un modulo di protezione hardware locale. Utilizzando SQL Server Management StudioSQL Server Management Studio, gli utenti SQL scelgono la chiave da usare per crittografare la colonna.

#### <a name="encryption-model-and-key-management-table"></a>Modello di crittografia e tabella di gestione delle chiavi

|                                  |                    | **Modello di crittografia e gestione delle chiavi** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Lato server con chiave gestita dal servizio**     | **Utilizzo della chiave gestita dal cliente sul lato serverServer-Side Using Customer-Managed Key**             | **Lato client che utilizza la chiave gestita dal clientClient-Side Using Client-Managed Key**      |
| **Intelligenza artificiale e Machine Learning**      |                    |                    |                    |
| Ricerca cognitiva di Azure           | Sì                | Sì                | -                  |
| Servizi cognitivi di Azure         | Sì                | Sì                | -                  |
| Azure Machine Learning           | Sì                | Sì                | -                  |
| Azure Machine Learning Studio    | Sì                | Anteprima, RSA a 2048 bit | -               |
| Content Moderator                | Sì                | Sì                | -                  |
| Viso                             | Sì                | Sì                | -                  |
| Language Understanding           | Sì                | Sì                | -                  |
| Personalizza esperienze                     | Sì                | Sì                | -                  |
| QnA Maker                        | Sì                | Sì                | -                  |
| Servizi Voce                  | Sì                | Sì                | -                  |
| Traduzione testuale                  | Sì                | Sì                | -                  |
| Power BI                         | Sì                | Anteprima, RSA a 2048 bit | -                  |
| **Analitica**                    |                    |                    |                    |
| Analisi di flusso di Azure           | Sì                | N/D\*            | -                  |
| Hub eventi                       | Sì                | Sì, tutte le lunghezze RSA. | -                  |
| Funzioni                        | Sì                | Sì, tutte le lunghezze RSA. | -                  |
| Azure Analysis Services          | Sì                | -                  | -                  |
| Azure Data Catalog               | Sì                | -                  | -                  |
| HDInsight di Azure                  | Sì                | Tutti                | -                  |
| Azure Monitor Application Insights | Sì                | Sì                | -                  |
| Analisi dei log di Monitoraggio di AzureAzure Monitor Log Analytics | Sì                | Sì                | -                  |
| Esplora dati di Azure              | Sì                | Sì                | -                  |
| Data factory di Azure               | Sì                | Sì                | -                  |
| Archivio Azure Data Lake            | Sì                | Sì, RSA a 2048 bit  | -                  |
| **Contenitori**                   |                    |                    |                    |
| Servizio Azure Kubernetes         | Sì                | Sì                | -                  |
| Istanze di Container              | Sì                | Sì                | -                  |
| Registro Container               | Sì                | Sì                | -                  |
| **Calcolare**                      |                    |                    |                    |
| Macchine virtuali                 | Sì                | Sì, RSA a 2048 bit  | -                  |
| Set di scalabilità delle macchine virtualiVirtual Machine Scale Set        | Sì                | Sì, RSA a 2048 bit  | -                  |
| SAP HANA                         | Sì                | Sì, RSA a 2048 bit  | -                  |
| Servizio app                      | Sì                | Sì                | -                  |
| Automazione                       | Sì                | Sì                | -                  |
| Portale di Azure                     | Sì                | Sì                | -                  |
| App per la logica                       | Sì                | Sì                | -                  |
| Applicazioni gestite di Azure       | Sì                | Sì                | -                  |
| Bus di servizio                      | Sì                | Sì                | -                  |
| Site Recovery                    | Sì                | Sì                | -                  |
| **Database**                    |                    |                    |                    |
| SQL Server nelle macchine virtuali   | Sì                | Sì, RSA a 2048 bit  | Sì                |
| database SQL di Azure               | Sì                | Sì, RSA a 2048 bit  | Sì                |
| Azure SQL Database for MariaDB   | Sì                | -                  | -                  |
| Azure SQL Database for MySQL     | Sì                | Sì                | -                  |
| Azure SQL Database for PostgreSQL | Sì               | Sì                | -                  |
| Azure Synapse Analytics          | Sì                | Sì, RSA a 2048 bit  | -                  |
| SQL Server Stretch Database      | Sì                | Sì, RSA a 2048 bit  | Sì                |
| Archiviazione tabelle                    | Sì                | Sì                | Sì                |
| Azure Cosmos DB                  | Sì                | Sì                | -                  |
| Azure Databricks                 | Sì                | Sì                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Sì                | -                  | Sì                |
| Azure Repos                      | Sì                | -                  | Sì                |
| **Identità**                     |                    |                    |                    |
| Azure Active Directory           | Sì                | -                  | -                  |
| Servizi di dominio Azure Active Directory | Sì          | Sì, RSA a 2048 bit  | -                  |
| **Integrazione**                  |                    |                    |                    |
| Bus di servizio                      | Sì                | Sì                | Sì                |
| Griglia di eventi                       | Sì                | -                  | -                  |
| Gestione API                   | Sì                | -                  | -                  |
| **Servizi IoT**                 |                    |                    |                    |
| Hub IoT                          | Sì                | Sì                | Sì                |
| Provisioning dei dispositivi hub IoT      | Sì                | Sì                | -                  |
| **Gestione e governance**    |                    |                    |                    |
| Azure Site Recovery              | Sì                | -                  | -                  |
| Azure Migrate                    | Sì                | Sì                | -                  |
| **Supporti**                        |                    |                    |                    |
| Servizi multimediali                   | Sì                | -                  | Sì                |
| **Sicurezza**                     |                    |                    |                    |
| Centro sicurezza di Azure per IoT    | Sì                | Sì                | -                  |
| Azure Sentinel                   | Sì                | Sì                | -                  |
| **Storage**                      |                    |                    |                    |
| Archiviazione BLOB                     | Sì                | Sì, RSA a 2048 bit  | Sì                |
| Archiviazione BLOB PremiumPremium Blob Storage             | Sì                | Sì, RSA a 2048 bit  | Sì                |
| Archiviazione su disco                     | Sì                | Sì                | -                  |
| Spazio di archiviazione su disco ultra               | Sì                | Sì                | -                  |
| Archiviazione su disco gestita             | Sì                | Sì                | -                  |
| Archiviazione file                     | Sì                | Sì, RSA a 2048 bit  | -                  |
| Archiviazione Premium file             | Sì                | Sì, RSA a 2048 bit  | -                  |
| Sincronizzazione file                        | Sì                | Sì, RSA a 2048 bit  | -                  |
| Archiviazione code                    | Sì                | Sì                | Sì                |
| Avere vFXT                       | Sì                | -                  | -                  |
| Azure NetApp Files               | Sì                | Sì                | -                  |
| Spazio di archiviazione                  | Sì                | Sì, RSA a 2048 bit  | -                  |
| StorSimple                       | Sì                | Sì, RSA a 2048 bit  | Sì                |
| Backup di Azure                     | Sì                | Sì                | Sì                |
| Data Box                         | Sì                | -                  | Sì                |
| Data Box Edge                    | Sì                | Sì                | -                  |

\*Questo servizio non rende persistenti i dati. Le cache temporanee, se presenti, vengono crittografate con una chiave Microsoft.Transient caches, if any, are encrypted with a Microsoft key.

## <a name="conclusion"></a>Conclusioni

La protezione dei dati dei clienti archiviati nei servizi di Azure è di importanza fondamentale per Microsoft. Per tutti i servizi di Azure ospitati è prevista la disponibilità di opzioni di crittografia dei dati inattivi. I servizi di Azure supportano le chiavi gestite dal servizio, le chiavi gestite dal cliente o la crittografia lato client. I servizi di Azure migliorano ampiamente la crittografia in posizione resta e nei prossimi mesi sono previste nuove opzioni per l'anteprima e la disponibilità generale.
