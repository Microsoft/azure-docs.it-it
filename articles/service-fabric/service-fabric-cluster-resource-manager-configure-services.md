---
title: Specificare le metriche e le impostazioni di selezione host
description: Informazioni sul servizio di Service Fabric, specificando metriche, vincoli di posizionamento e altri criteri di posizionamento.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610098"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurazione delle impostazioni di Cluster Resource Manager per i servizi Service Fabric
Cluster Resource Manager di Service Fabric consente un controllo con granularità fine sulle regole che disciplinano ogni singolo servizio denominato. Ogni servizio denominato può specificare regole per l'allocazione nel cluster. Ogni servizio denominato può anche definire il set di metriche di cui generare un report, incluso il relativo livello di importanza per tale servizio. La configurazione dei servizi prevede tre diverse attività:

1. Configurazione dei vincoli di posizionamento
2. Configurazione delle metriche
3. Configurazione dei criteri di posizionamento avanzati e di altre regole (meno comune)

## <a name="placement-constraints"></a>Vincoli di posizionamento
I vincoli di posizionamento vengono usati per controllare su quali nodi del cluster un servizio può effettivamente essere eseguito. Un'istanza del servizio denominato o tutti i servizi di un determinato tipo sono in genere vincolati all'esecuzione in un tipo di nodo specifico. I vincoli di posizionamento sono estendibili. È possibile definire qualsiasi set di proprietà per ogni tipo di nodo e quindi selezionarle con i vincoli durante la creazione di servizi. È inoltre possibile modificare i vincoli di posizionamento di un servizio mentre è in esecuzione. Ciò consente di rispondere alle modifiche del cluster o ai requisiti del servizio. Le proprietà di un determinato nodo possono essere aggiornate anche in modo dinamico nel cluster. Altre informazioni sui vincoli di posizionamento e sulla loro configurazione sono disponibili in [questo articolo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metriche
Le metriche sono il set di risorse necessarie per uno specifico servizio denominato. La configurazione delle metriche di un servizio include informazioni relative alla quantità della risorsa che ogni replica con stato o istanza senza stato usa per impostazione predefinita. Le metriche includono anche un peso che ne indica l'importanza del bilanciamento per il servizio, nel caso di compromessi necessari.

## <a name="advanced-placement-rules"></a>Regole di posizionamento avanzate
Sono disponibili altri tipi di regole di posizionamento che sono utili negli scenari meno comuni. Di seguito sono riportati alcuni esempi:
- Vincoli che sono di ausilio con i cluster geograficamente distribuiti
- Determinate architetture di applicazioni

Altre regole di posizionamento sono configurate tramite correlazioni o criteri.

## <a name="next-steps"></a>Passaggi successivi
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)
- L'affinità è un modo di configurare i servizi. Non è un'operazione frequente, ma se necessario sono disponibili altre informazioni [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Sono disponibili molte regole di posizionamento diverse da configurare nel servizio per gestire scenari aggiuntivi. È possibile trovare informazioni sui vari criteri di posizionamento [qui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Partire dall'inizio e vedere l' [introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni, vedere questo articolo sulla [Descrizione di un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
