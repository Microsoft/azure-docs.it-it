---
title: Tipi di conflitto e criteri di risoluzione dei conflitti in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e i criteri di risoluzione dei conflitti in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441978"
---
# <a name="conflict-types-and-resolution-policies"></a>Tipi di conflitto e criteri di risoluzione dei conflitti

I conflitti e i criteri di risoluzione dei conflitti sono applicabili se l'account Azure Cosmos DB è configurato con più aree di scrittura.

Per gli account Cosmos di Azure configurati con più aree di scrittura, possono verificarsi conflitti di aggiornamento quando i writer aggiornano contemporaneamente lo stesso elemento in più aree. I conflitti di aggiornamento possono essere dei tre tipi seguenti:

* **Conflitti**di inserimento: questi conflitti possono verificarsi quando un'applicazione inserisce contemporaneamente due o più elementi con lo stesso indice univoco in due o più aree. Ad esempio, questo conflitto potrebbe verificarsi con una proprietà ID.

* **Sostituzione conflitti**: questi conflitti possono verificarsi quando un'applicazione aggiorna lo stesso elemento contemporaneamente in due o più aree.

* **Elimina conflitti**: questi conflitti possono verificarsi quando un'applicazione elimina contemporaneamente un elemento in un'area e lo aggiorna in un'altra area.

## <a name="conflict-resolution-policies"></a>Criteri di risoluzione dei conflitti

Azure Cosmos DB offre un meccanismo flessibile basato su criteri per risolvere i conflitti di scrittura. È possibile scegliere tra due criteri di risoluzione dei conflitti in un contenitore Cosmos di Azure:You can select from two conflict resolution policies on an Azure Cosmos container:

* **Last Write Wins (LWW):** questo criterio di risoluzione, per impostazione predefinita, utilizza una proprietà timestamp definita dal sistema. Si basa sul protocollo di sincronizzazione dell'ora dell'orologio. Se si usa l'API SQL, è possibile specificare qualsiasi altra proprietà numerica personalizzata (ad esempio, la propria nozione di timestamp) da utilizzare per la risoluzione dei conflitti. Una proprietà numerica personalizzata viene anche definita percorso di risoluzione dei *conflitti*. 

  Se due o più elementi sono in conflitto su operazioni di inserimento o sostituzione, l'elemento con il valore massimo per il percorso di risoluzione dei conflitti diventa il vincitore. Il sistema determina il vincitore se più elementi hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti. Tutte le aree convergono sicuramente in un singolo vincitore e si ritrovano con la stessa versione dell'elemento di cui si esegue il commit. Quando sono coinvolti i conflitti di eliminazione, la versione eliminata prevale sempre sui conflitti di inserimento o sostituzione. Questo risultato si verifica indipendentemente dal valore del percorso di risoluzione dei conflitti.

  > [!NOTE]
  > Ultime vincite di scrittura è il `_ts` criterio di risoluzione dei conflitti predefinito e utilizza il timestamp per le API seguenti: SQL, MongoDB, Cassandra, Gremlin e Table. La proprietà numerica personalizzata è disponibile solo per l'API SQL.

  Per altre informazioni, vedere gli [esempi d'uso dei criteri di risoluzione dei conflitti LWW](how-to-manage-conflicts.md).

* **Personalizzato:** questo criterio di risoluzione è progettato per la semantica definita dall'applicazione per la riconciliazione dei conflitti. Quando si impostano questi criteri nel contenitore Cosmos di Azure, è inoltre necessario registrare una stored procedure di *tipo merge.* Questa procedura viene richiamata automaticamente quando vengono rilevati conflitti in una transazione di database sul server. Il sistema fornisce esattamente una volta la garanzia per l'esecuzione di una routine di tipo merge come parte del protocollo di impegno.  

  Se si configura il contenitore con l'opzione di risoluzione personalizzata e non si riesce a registrare una procedura di unione nel contenitore o la procedura di unione genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel *feed dei conflitti.* L'applicazione deve quindi risolvere manualmente i conflitti nel feed dei conflitti. Per altre informazioni, vedere gli [esempi di utilizzo dei criteri di risoluzione personalizzati e del feed dei conflitti](how-to-manage-conflicts.md).

  > [!NOTE]
  > Il criterio personalizzato per la risoluzione dei conflitti è disponibile solo per gli account API SQL.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare i criteri di risoluzione dei conflitti:Learn how to configure conflict resolution policies:

* [Come configurare più master nelle applicazioni](how-to-multi-master.md)
* [Come gestire i criteri di risoluzione dei conflitti](how-to-manage-conflicts.md)
* [Come leggere dal feed conflitti](how-to-manage-conflicts.md#read-from-conflict-feed)
