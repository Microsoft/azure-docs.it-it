---
title: Diagnostica delle transazioni in Azure Application Insights | Microsoft Docs
description: Diagnostica della transazioni end-to-end in Application Insights
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746098"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostica unificata delle transazioni tra componenti

L'esperienza di diagnostica unificata correla automaticamente i dati di telemetria lato server di tutti i componenti monitorati di Application Insights in un'unica vista. Non è importante se si hanno più risorse con chiavi di strumentazione separate. Application Insights rileva la relazione sottostante e consente di diagnosticare facilmente il componente, la dipendenza o l'eccezione dell'applicazione che ha causato un errore o un rallentamento nella transazione.

## <a name="what-is-a-component"></a>Cos'è un componente?

I componenti sono parti dell'applicazione distribuita o di microservizi, distribuibili autonomamente. Gli sviluppatori e i team delle operazioni hanno visibilità o accesso a livello di codice ai dati di telemetria generati dai componenti di queste applicazioni.

* I componenti sono diversi dalle dipendenze esterne "osservate" quali SQL, EventHub e così via, a cui il team o l'organizzazione potrebbero non avere accesso (codice o dati di telemetria).
* I componenti vengono eseguiti su diverse istanze di ruolo, server o contenitore.
* I componenti possono essere chiavi di strumentazione di Application Insights diverse tra loro (anche in caso di sottoscrizioni diverse) oppure ruoli diversi che creano report per una sola chiave di strumentazione di Application Insights. La nuova esperienza mostra i dettagli per tutti i componenti, indipendentemente dalla loro configurazione.

> [!NOTE]
> * **Mancano i collegamenti agli elementi correlati?** Tutti i dati di telemetria correlati sono inclusi nelle sezioni [superiore](#cross-component-transaction-chart) e [inferiore](#all-telemetry-with-this-operation-id) del lato sinistro. 

## <a name="transaction-diagnostics-experience"></a>Esperienza di diagnostica delle transazioni
Questa vista è composta da quattro parti principali: elenco risultati, un grafico delle transazioni tra componenti, un elenco temporale di tutti i dati di telemetria relativi a questa operazione e il riquadro dei dettagli relativi agli elementi di telemetria selezionati a sinistra.

![Parti principali](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Grafico delle transazioni tra componenti

Questo grafico offre una sequenza temporale con barre orizzontali per la durata delle richieste e delle dipendenze tra componenti. Anche le eccezioni raccolte vengono contrassegnate sulla sequenza temporale.

* La prima riga di questo grafico rappresenta il punto di ingresso, la richiesta in ingresso per il primo componente chiamato in questa transazione. La durata è il tempo totale impiegato per il completamento della transazione.
* Tutte le chiamate a dipendenze esterne sono rappresentate da semplici righe non comprimibili, con icone che indicano il tipo di dipendenza.
* Le chiamate ad altri componenti sono rappresentate da righe comprimibili. Ogni riga corrisponde a un'operazione specifica richiamata nel componente.
* Per impostazione predefinita, la richiesta, la dipendenza o l'eccezione selezionata viene visualizzata sul lato destro.
* Selezionare una riga qualsiasi per visualizzarne i relativi [dettagli a destra](#details-of-the-selected-telemetry). 

> [!NOTE]
> Le chiamate ad altri componenti hanno due righe: una riga rappresenta la chiamata in uscita (dipendenza) dal componente chiamante e l'altra riga corrisponde alla richiesta in ingresso al componente chiamato. L'icona iniziale e lo stile diverso delle barre della durata consentono di distinguere le chiamate tra loro.

## <a name="all-telemetry-with-this-operation-id"></a>Tutti i dati di telemetria per questo ID operazione

Questa sezione mostra una visualizzazione elenco semplice in una sequenza temporale di tutti i dati di telemetria relativi a questa transazione. Indica inoltre eventi personalizzati e le tracce che non sono visualizzate nel grafico delle transazioni. È possibile filtrare questo elenco per i dati di telemetria generati da un componente/una chiamata specifici. È possibile selezionare qualsiasi elemento di telemetria in questo elenco per visualizzarne i corrispondenti [dettagli sulla destra](#details-of-the-selected-telemetry).

![Sequenza temporale di tutti i dati di telemetria](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Dettagli dei dati di telemetria selezionati

Questo riquadro comprimibile mostra i dettagli di qualsiasi elemento selezionato dal grafico delle transazioni o dall'elenco. "Show all" (Mostra tutto) elenca tutti gli attributi standard raccolti. Gli attributi personalizzati vengono elencati separatamente sotto il set di attributi standard. Fare clic su "..." sotto la finestra di analisi dello stack per visualizzare un'opzione per copiare la traccia. "Open profiler traces" (Apri tracce profiler) o "Apri snapshot di debug" mostrano la diagnostica a livello di codice nei riquadri dei dettagli corrispondenti.

![Dettagli dell'eccezione](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Search Results

Questo riquadro comprimibile mostra altri risultati che soddisfano i criteri del filtro. Fare clic su qualsiasi risultato per aggiornare i rispettivi dettagli delle 3 sezioni elencate in precedenza. Tentiamo di trovare gli esempi con le maggiori possibilità di ottenere dettagli disponibili da tutti i componenti, anche se il campionamento è attivo in ognuno di essi. Questi vengono visualizzati come esempi "consigliati".

![Search Results](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler e Snapshot Debugger

[Application Insights Profiler](./profiler.md) o [Snapshot Debugger](snapshot-debugger.md) consentono di eseguire la diagnostica a livello di codice dei problemi di prestazioni e degli errori. Grazie a questa esperienza è possibile visualizzare le tracce del profiler o gli snapshot di un componente con un solo clic.

Se non è stato possibile ottenere il funzionamento del profiler, contattare **serviceprofilerhelp \@ Microsoft.com**

Se non è stato possibile ottenere Snapshot Debugger funzionante, contattare **snapshothelp \@ Microsoft.com**

![Integrazione profiler](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Domande frequenti

*Nel grafico viene visualizzato un solo componente, mentre gli altri vengono visualizzati solo come dipendenze esterne senza mostrare alcun dettaglio sulle operazioni eseguite all'interno di tali componenti.*

Possibili motivi:

* Gli altri componenti sono instrumentati con Application Insights?
* Usano l'SDK di Application Insights più recente e stabile?
* Se questi componenti sono separati Application Insights risorse, si ha l' [accesso](resources-roles-access-control.md) necessario se si dispone dell'accesso e i componenti sono instrumentati con gli sdk di Application Insights più recenti, è possibile comunicare tramite il canale di commenti e suggerimenti in alto a destra.

*Vengono visualizzate righe duplicate per le dipendenze. È previsto?*

In questa fase la chiamata di dipendenza in uscita viene mostrata separatamente dalla richiesta in ingresso. In genere, le due chiamate hanno lo stesso aspetto, cambia solo il valore della durata a causa del round trip della rete. L'icona iniziale e lo stile diverso delle barre della durata consentono di distinguere le chiamate tra loro. Questa organizzazione dei dati crea confusione? Inviare i propri commenti.

*Perché ci sono degli sfasamenti di orario tra le diverse istanze del componente?*

Le sequenze temporali vengono modificate in base agli sfasamenti di orario nel grafico delle transazioni. È possibile visualizzare i timestamp esatti nel riquadro dei dettagli o tramite Analytics.

*Perché nella nuova esperienza manca la maggior parte delle query degli elementi correlati?*

Questo si verifica per motivi strutturali. Tutti gli elementi correlati in tutti i componenti sono già disponibili a sinistra (nelle sezioni in alto e in basso). La nuova esperienza presenta due elementi correlati che non sono riportati a sinistra: tutti i dati di telemetria generati nei cinque minuti precedenti e successivi a questo evento e la sequenza temporale dell'utente.

