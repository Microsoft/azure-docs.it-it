---
title: Esempio di progetto DoD Impact Level 4
description: Procedura di distribuzione per l'esempio di progetto DoD Impact Level 4, inclusi i dettagli dei parametri degli artefatti del progetto.
ms.date: 06/30/2020
ms.topic: sample
ms.openlocfilehash: bb2599399284b2be5a9a7f88ef1d0244812ec4b2
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044975"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Distribuire l'esempio di progetto DoD Impact Level 4

Per distribuire l'esempio di progetto Azure Blueprints Department of Defense Impact Level 4 (DoD IL4), seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **DoD Impact Level 4** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto DoD Impact Level 4.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti che costituiscono l'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

## <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non allineata ai controlli DoD Impact Level 4.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dall'esempio di progetto DoD Impact Level 4". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

## <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

## <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Percorsi consentiti|Assegnazione dei criteri|Località consentite|Questi criteri consentono di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. Usare per imporre requisiti di conformità geografica.|
|Località consentite per i gruppi di risorse|Assegnazione dei criteri |Località consentite|Questi criteri consentono di limitare le località in cui l'organizzazione può creare gruppi di risorse. Usare per imporre requisiti di conformità geografica.|
|Distribuisci il controllo nei server SQL|Assegnazione di criteri|Valore in giorni del periodo di conservazione (0 indica conservazione illimitata)|Giorni di conservazione (facoltativo, 180 giorni se non è specificato alcun valore)|
|Distribuisci il controllo nei server SQL|Assegnazione di criteri|Nome gruppo di risorse per l'account di archiviazione per il controllo di SQL Server|Il controllo esegue la scrittura degli eventi del database in un log di controllo nell'account di archiviazione di Azure. Verrà creato un account di archiviazione in ogni area in cui viene creato un server SQL che verrà condiviso da tutti i server di tale area. Importante: per il corretto funzionamento del controllo, non eliminare o rinominare il gruppo di risorse o gli account di archiviazione.|
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete|Assegnazione di criteri|Prefisso dell'account di archiviazione per la diagnostica del gruppo di sicurezza di rete|Questo prefisso verrà combinato con la posizione del gruppo di sicurezza di rete per formare il nome dell'account di archiviazione creato.|
|Distribuire le impostazioni di diagnostica per i gruppi di sicurezza di rete|Assegnazione di criteri|Nome del gruppo di risorse per l'account di archiviazione per la diagnostica del gruppo di sicurezza di rete (deve esistere)|Il gruppo di risorse in cui verrà creato l'account di archiviazione. Questo gruppo di risorse deve già esistere.|
|Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux|Assegnazione di criteri|Area di lavoro Log Analytics per i set di scalabilità di macchine virtuali Linux|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Area di lavoro Log Analytics per VM Linux|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|Distribuisci l'agente di Log Analytics per le macchine virtuali Linux|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Linux da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows|Assegnazione di criteri|Area di lavoro Log Analytics per i set di scalabilità di macchine virtuali|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Area di lavoro Log Analytics per VM Windows|Se quest'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere manualmente le autorizzazioni di "Collaboratore di Log Analytics" (o simile) all'ID entità di sicurezza dell'assegnazione dei criteri.|
|Distribuisci l'agente di Log Analytics per le macchine virtuali Windows|Assegnazione di criteri|Facoltativo: elenco delle immagini delle macchine virtuali che supportano il sistema operativo Windows da aggiungere all'ambito|Una matrice vuota può essere usata per indicare l'assenza di parametri facoltativi: \[\]|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Membri che devono essere inclusi nel gruppo Administrators locale|Elenco delimitato da punto e virgola con i membri da escludere dal gruppo locale Administrators, ad esempio Administrator; utente1; utente2|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Membri che devono essere esclusi nel gruppo Administrators locale|Elenco delimitato da punto e virgola con i membri da includere nel gruppo locale Administrators. ad esempio Administrator; utente1; utente2|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica|Elenco dei tipi di risorse per il controllo, se l'impostazione dei log di diagnostica non è abilitata. I valori accettabili sono disponibili negli [schemi dei log di diagnostica di Monitoraggio di Azure](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|ID dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate|Si tratta dell'ID (GUID) dell'area di lavoro di Log Analytics per cui le macchine virtuali devono essere configurate.|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Il backup con ridondanza geografica a lungo termine deve essere abilitato per i database SQL di Azure|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'archiviazione con ridondanza geografica deve essere abilitata per gli account di archiviazione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Il backup con ridondanza geografica deve essere abilitato per i database di Azure per PostgreSQL|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'applicazione Web deve essere accessibile solo tramite HTTPS|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'app per le funzioni deve essere accessibile solo tramite HTTPS|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli account deprecati devono essere rimossi dalla sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|CORS non deve consentire a tutte le risorse di accedere all'applicazione Web|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|
|\[Anteprima\]: DoD Impact Level 4|Assegnazione di criteri|L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione|Per informazioni sugli effetti dei criteri, vedere [Informazioni sugli effetti di Criteri di Azure](../../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato i passaggi per distribuire l'esempio di progetto DoD Impact Level 4, vedere gli articoli seguenti per informazioni sul progetto e sul mapping dei controlli:

> [!div class="nextstepaction"]
> [Progetto DoD Impact Level 4 - Panoramica](./index.md)
> [Progetto DoD Impact Level 4 - Mapping dei controlli](./control-mapping.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
