---
title: Panoramica dell'esempio di progetto Azure Security Benchmark
description: Panoramica dell'esempio di progetto Azure Security Benchmark. Questo esempio di progetto consente ai clienti di valutare specifici controlli.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 58e8c3972b0f05e5b0a3f15da0dfd3eae301ad31
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386993"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Esempio di progetto Azure Security Benchmark

L'esempio Azure Security benchmark Blueprint fornisce Guard di governance tramite [criteri di Azure](../../policy/overview.md) che consentono di valutare i controlli specifici di benchmark di [sicurezza di Azure V1](../../../security/benchmarks/overview.md) . Il progetto aiuta i clienti a distribuire un set di base di criteri per qualsiasi architettura distribuita in Azure che deve implementare i controlli di Azure Security Benchmark.

## <a name="control-mapping"></a>Mapping dei controlli

Il [mapping dei controlli di Criteri di Azure](../../policy/samples/azure-security-benchmark.md) fornisce informazioni dettagliate sulle definizioni di criteri incluse in questo progetto e sul relativo mapping ai **domini di conformità** e ai **controlli** di Azure Security Benchmark. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire l'esempio di progetto Azure Security Benchmark di Azure Blueprints, seguire questa procedura:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio **Azure Security benchmark V1** Blueprint in _altri esempi_ e selezionare il nome per selezionare questo esempio.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: specificare un nome per la copia dell'esempio di progetto Azure Security Benchmark.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti inclusi nell'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma le modifiche potrebbero renderla non allineata rispetto a quanto consigliato per Azure Security Benchmark.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica note** specificare ad esempio "Prima versione pubblicata dell'esempio di progetto Azure Security Benchmark". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

### <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

### <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco di utenti esclusi dal gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con i membri da escludere dal gruppo locale Administrators, ad esempio Administrator; utente1; utente2|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco di utenti che devono essere inclusi nel gruppo di Administrators della macchina virtuale Windows|Elenco delimitato da punto e virgola con i membri da includere nel gruppo locale Administrators. ad esempio Administrator; utente1; utente2|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco dei *soli* utenti che devono essere inclusi nel gruppo Administrators delle macchine virtuali Windows|Elenco delimitato da punto e virgola con tutti i membri previsti del gruppo locale di Administrators, ad esempio Administrator; utente1; utente2|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco di aree in cui deve essere abilitato Network Watcher|Per visualizzare un elenco completo delle aree, usare Get-AzLocation|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Rete virtuale a cui devono essere connesse le macchine virtuali|Esempio: /subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Gateway di rete che devono essere usati dalle reti virtuali|Esempio: /subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco degli ID delle aree di lavoro a cui devono connettersi gli agenti di Log Analytics|Un elenco di ID di aree di lavoro separati da punto e virgola a cui deve essere connesso l'agente di Log Analytics|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco dei tipi di risorsa per cui è necessario abilitare i log di diagnostica|Controlla l'impostazione di diagnostica per i tipi di risorse selezionati|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Versione di PHP più recente|Versione di PHP più recente supportata per Servizi app|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Versione di Java più recente|Versione di Java più recente supportata per Servizi app|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Versione di Python per Windows più recente|Versione di Python più recente supportata per Servizi app|
|Controlla le raccomandazioni per Azure Security Benchmark e distribuisce le estensioni di VM di supporto specifiche|Assegnazione di criteri|Versione di Python per Linux più recente|Versione di Python più recente supportata per Servizi app|

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).