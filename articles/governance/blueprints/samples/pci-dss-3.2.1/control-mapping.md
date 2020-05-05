---
title: Controlli dell'esempio di progetto PCI-DSS v3.2.1
description: Mapping dei controlli dell'esempio di progetto Payment Card Industry Data Security Standard v3.2.1 con Criteri di Azure e Controllo degli accessi in base al ruolo.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76905644"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapping dei controlli dell'esempio di progetto PCI-DSS v3.2.1

L'articolo seguente descrive il mapping dei controlli dell'esempio di progetto PCI-DSS v3.2.1 di Azure Blueprints con i controlli PCI-DSS v3.2.1. Per altre informazioni sui controlli, vedere [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

I mapping seguenti fanno riferimento ai controlli **PCI-DSS v3.2.1:2018**. Usare la barra di spostamento a destra per passare direttamente a uno specifico mapping. Molti controlli mappati vengono implementati con un'iniziativa di [Criteri di Azure](../../../policy/overview.md). Per esaminare l'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**. Quindi, trovare e selezionare l'iniziativa dei criteri predefinita **\[Anteprima\]: Verifica controlli PCI v3.2.1:2018 e distribuisci estensioni macchina virtuale specifiche per supportare i requisiti di controllo**.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../../../policy/overview.md). Questi criteri possono aiutare a [valutare la conformità](../../../policy/how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza 1:1 o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo ai criteri stessi e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra i controlli e le definizioni di Criteri di Azure per questo esempio di progetto di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 Protezione dei limiti

Questo progetto consente di gestire e controllare le reti assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitora i gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive potrebbero consentire l'accesso non autorizzato alla rete e dovranno essere riviste. Questo progetto assegna una definizione di Criteri di Azure che monitora endpoint, applicazioni e account di archiviazione non protetti. Gli endpoint e le applicazioni non protetti da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute nel sistema informativo.

- Controlla l'accesso di rete senza restrizioni agli account di archiviazione
- L'accesso tramite endpoint con connessione Internet deve essere limitato

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 Protezione crittografica

Questo progetto consente di applicare i criteri con l'uso dei controlli crittografici assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che applicano specifici controlli crittografici e controllano l'uso di impostazioni di crittografia meno sicure. Identificando le risorse di Azure le cui configurazioni di crittografia potrebbero non essere ottimali, è possibile adottare azioni correttive per assicurarsi che le risorse siano configurate in conformità ai criteri di sicurezza delle informazioni. In particolare, i criteri assegnati da questo progetto richiedono l'applicazione di Transparent Data Encryption per i database SQL e controllano se manca la crittografia in account di archiviazione e variabili degli account di automazione. Esistono anche criteri che consentono di controllare le connessioni non sicure con account di archiviazione, app per le funzioni, app Web, app per le API e Cache Redis, nonché le comunicazioni non crittografate con Service Fabric.

- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- L'applicazione Web deve essere accessibile solo tramite HTTPS
- L'app per le API deve essere accessibile solo tramite HTTPS
- Transparent Data Encryption deve essere abilitata nei database SQL
- La crittografia del disco deve essere applicata nelle macchine virtuali
- Le variabili dell'account di automazione devono essere crittografate
- Devono essere abilitate solo connessioni sicure alla Cache Redis
- Il trasferimento sicuro negli account di archiviazione deve essere abilitato
- La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign
- Transparent Data Encryption deve essere abilitata nei database SQL
- Distribuisci Transparent Data Encryption nel database SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 Analisi delle vulnerabilità e aggiornamenti di sistema

Questo progetto consente di gestire le vulnerabilità dei sistemi informativi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che monitorano gli aggiornamenti di sistema mancanti, oltre alle vulnerabilità del sistema operativo, di SQL e delle macchine virtuali nel Centro sicurezza di Azure. Centro sicurezza di Azure fornisce funzionalità di report che consentono di ricevere informazioni dettagliate in tempo reale sullo stato di sicurezza delle risorse di Azure distribuite.

- Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure
- Distribuisci estensione IaaSAntimalware Microsoft predefinita per Windows Server
- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- Gli aggiornamenti di sistema devono essere installati nelle macchine
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte
- Le vulnerabilità dei database SQL devono essere risolte
- Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 Separazione dei compiti

La presenza di un solo proprietario di sottoscrizioni di Azure non consente la ridondanza amministrativa. Al contrario, la presenza di troppi proprietari di sottoscrizioni di Azure può aumentare la probabilità che si verifichi una violazione tramite un account di proprietario compromesso. Questo progetto consente di mantenere un numero appropriato di proprietari di sottoscrizioni di Azure assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano questo numero. La gestione delle autorizzazioni dei proprietari di sottoscrizioni consente di implementare la separazione appropriata dei compiti.

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Per la sottoscrizione devono essere designati al massimo 3 proprietari 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b Gestione dei diritti di accesso con privilegi

Questo progetto consente di restringere e controllare i diritti di accesso con privilegi assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account esterni con autorizzazioni di proprietario, di scrittura e/o di lettura e gli account di dipendenti con autorizzazioni di proprietario e/o di scrittura per cui non è abilitata l'autenticazione a più fattori. Azure implementa il controllo degli accessi in base al ruolo per gestire chi ha accesso alle risorse di Azure. Identificando dove vengono implementate regole personalizzate del controllo degli accessi in base al ruolo, è possibile verificare l'esigenza e la corretta implementazione, perché tali regole sono soggette a errore. Questo progetto assegna inoltre definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare l'uso dell'autenticazione di Azure Active Directory per SQL Server. L'uso dell'autenticazione di Azure Active Directory semplifica la gestione delle autorizzazioni e centralizza la gestione delle identità degli utenti di database e di altri servizi  
Microsoft.
 
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione
- È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 Privilegi minimi e revisione dei diritti di accesso degli utenti

Azure implementa il controllo degli accessi in base al ruolo per consentire di gestire chi ha accesso alle risorse di Azure. Usando il portale di Azure, è possibile verificare chi ha accesso alle risorse di Azure e le relative autorizzazioni. Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account la cui verifica dovrebbe essere prioritaria, inclusi gli account deprecati e quelli esterni con autorizzazioni elevate.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Rimozione o modifica dei diritti di accesso

Azure implementa il controllo degli accessi in base al ruolo per consentire di gestire chi ha accesso alle risorse di Azure. Usando Azure Active Directory e il controllo degli accessi in base al ruolo, è possibile aggiornare i ruoli degli utenti in base alle modifiche organizzative. Se necessario, è possibile bloccare l'accesso degli account (oppure rimuoverli), rimuovendo immediatamente i diritti di accesso alle risorse di Azure. Questo progetto assegna definizioni di [Criteri di Azure](../../../policy/overview.md) per controllare gli account deprecati che sarebbe preferibile rimuovere.

- Gli account deprecati devono essere rimossi dalla sottoscrizione
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 Autenticazione basata su password

Questo progetto consente di imporre password complesse assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le VM Windows per cui non sono applicati i requisiti minimi di complessità o di altro tipo per le password. Identificando le VM in violazione dei criteri di complessità delle password, è possibile adottare azioni correttive per assicurarsi che le password di tutti gli account utente delle VM siano conformi ai criteri.

- \[Anteprima\]: Controlla le macchine virtuali Windows in cui la validità minima della password non è impostata su 70 giorni
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows in cui la validità massima della password non è impostata su 70 giorni
- \[Anteprima\]: Controlla le macchine virtuali Windows in cui la lunghezza minima della password non è limitata a 14 caratteri
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows che non limitano la lunghezza minima della password a 14 caratteri
- \[Anteprima\]: Controlla le macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti
- \[Anteprima\]: Distribuisci i requisiti per controllare le macchine virtuali Windows che consentono il riutilizzo delle 24 password precedenti

## <a name="103-and-1054-audit-generation"></a>10.3 and 10.5.4 Generazione di controllo

Questo progetto consente di assicurarsi che gli eventi di sistema vengano registrati assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano le impostazioni dei log nelle risorse di Azure.
I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. I log di Azure si basano sulla sincronizzazione degli orologi interni per creare un record correlato al tempo degli eventi nelle risorse.

- È consigliabile abilitare il controllo nelle impostazioni di Sicurezza dei dati avanzata in SQL Server
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL
- Distribuisci il controllo nei server SQL
- È consigliabile eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager
- È consigliabile eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>12.3.6 e 12.3.7 Sicurezza delle informazioni

Questo progetto consente di gestire e controllare la rete assegnando definizioni di [Criteri di Azure](../../../policy/overview.md) che controllano i percorsi di rete accettabili e i prodotti aziendali approvati e autorizzati per l'ambiente. Queste definizioni sono personalizzabili in ogni azienda tramite i parametri inclusi all'interno di ogni criterio.

- Percorsi consentiti
- Località consentite per i gruppi di risorse

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping dei controlli del progetto PCI-DSS v3.2.1, vedere gli articoli seguenti per informazioni generali e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto PCI-DSS v3.2.1 - Panoramica](./index.md)
> [Progetto PCI-DSS v3.2.1 - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).