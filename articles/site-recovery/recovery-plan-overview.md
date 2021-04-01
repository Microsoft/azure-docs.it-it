---
title: Informazioni sui piani di ripristino in Azure Site Recovery
description: Informazioni sui piani di ripristino in Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343915"
---
# <a name="about-recovery-plans"></a>Informazioni sui piani di ripristino

Questo articolo fornisce una panoramica dei piani di ripristino in [Azure Site Recovery](site-recovery-overview.md).

Un piano di ripristino raccoglie i computer in gruppi di ripristino ai fini del failover. Un piano di ripristino consente di definire un processo di ripristino sistematico creando piccole unità indipendenti che è possibile usare per il failover. Un'unità rappresenta in genere un'app nell'ambiente.

- Un piano di ripristino definisce la modalità di failover dei computer e la sequenza di avvio dopo il failover.
- I piani di ripristino possono essere usati sia per il failover che per il failback da Azure.
- È possibile aggiungere fino a 100 istanze protette a un piano di ripristino.
- È possibile personalizzare un piano aggiungendovi un ordine, istruzioni e attività.
- Dopo aver definito un piano, è possibile eseguire un failover per esso.
- È possibile fare riferimento ai computer in più piani di ripristino, in cui i piani successivi ignorano la distribuzione o l'avvio di un computer se è stato distribuito in precedenza con un altro piano di ripristino.



### <a name="why-use-a-recovery-plan"></a>Vantaggi dell'uso di un piano di ripristino

Usare i piani di ripristino per:

* Modellare un'app in base alle relative dipendenze.
* Automatizzare le attività di ripristino per ridurre l'obiettivo del tempo di ripristino (RTO).
* Verificare di essere pronti per la migrazione o il ripristino di emergenza, assicurandosi che le app facciano parte di un piano di ripristino.
* Eseguire failover di test nei piani di ripristino per assicurarsi che il ripristino di emergenza o la migrazione funzioni come previsto.


## <a name="model-apps"></a>Modellare le app 
È possibile pianificare e creare un gruppo di ripristino per acquisire le proprietà specifiche delle app. Considerare, ad esempio, una tipica applicazione a tre livelli, con un back-end SQL, middleware e un front-end Web. In genere, si personalizza il piano di ripristino in modo che i computer in ogni livello si avviino nell'ordine corretto dopo il failover.

- Il back-end SQL deve essere avviato per primo, seguito dal middleware e successivamente dal front-end Web.
- Questo ordine di avvio assicura che l'app sia in funzione nel momento in cui viene avviato l'ultimo computer.
- Questo ordine assicura che quando il middleware viene avviato e tenta di connettersi al livello di SQL Server, il livello di SQL Server è già in esecuzione. 
- Tale ordine aiuta anche ad assicurare che il server front-end venga avviato per ultimo, in modo che gli utenti finali non si connettano all'URL dell'app prima che tutti i componenti siano operativi e che l'app sia pronta per accettare le richieste.

Per creare questo ordine, aggiungere gruppi al gruppo di ripristino e aggiungere computer ai gruppi.
- Se l'ordine è specificato, viene usata la sequenziazione. Le azioni vengono eseguite in parallelo nel modo appropriato, per migliorare il ripristino dell'applicazione RTO.
- Il failover dei computer che fanno parte di un unico gruppo avviene in parallelo.
- Il failover dei computer in gruppi diversi avviene in base all'ordine dei gruppi, quindi i computer del gruppo 2 iniziano il failover solo dopo il failover e l'avvio di tutti i computer del gruppo 1.

    ![Piano di ripristino di esempio](./media/recovery-plan-overview/rp.png)

Con questa personalizzazione, ecco cosa accade quando si esegue un failover per il piano di ripristino: 

1. Un passaggio di arresto tenta di spegnere i computer locali. Nel caso di un failover di test, invece, il sito primario rimane in esecuzione. 
2. L'arresto attiva un failover parallelo di tutti i computer nel piano di ripristino.
3. Il failover prepara i dischi delle macchine virtuali usando i dati replicati.
4. I gruppi di avvio vengono eseguiti in ordine, avviando i computer in ogni gruppo. Viene eseguito prima il gruppo 1, quindi il gruppo 2 e, infine, gruppo 3. Se in un gruppo ci sono più computer, vengono avviati tutti in parallelo.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizzare le attività nei piani di ripristino

Il ripristino di applicazioni di grandi dimensioni può essere un'attività complessa. I passaggi manuali rendono il processo soggetto a errori e la persona che esegue il failover potrebbe non essere a conoscenza di tutti gli aspetti complessi delle app. È possibile usare un piano di ripristino per determinare l'ordine e automatizzare le azioni necessarie in ogni passaggio, usando i runbook di Automazione di Azure per il failover in Azure oppure gli script. Per le attività che non possono essere automatizzate, è possibile inserire pause per le azioni manuali nei piani di ripristino. Ci sono due tipi di attività che è possibile configurare:

* **Attività nella macchina virtuale di Azure dopo il failover**: quando si esegue il failover in Azure, è in genere necessario eseguire azioni in modo da potersi connettere alla macchina virtuale dopo il failover. Ad esempio: 
    * Creare un indirizzo IP pubblico nella macchina virtuale.
    * Assegnare un gruppo di sicurezza di rete alla scheda di rete della macchina virtuale di Azure.
    * Aggiungere un servizio di bilanciamento del carico a un set di disponibilità.
* **Attività nella macchina virtuale dopo il failover**: queste attività in genere riconfigurano l'app in esecuzione nel computer, in modo che continui a funzionare correttamente nel nuovo ambiente. Ad esempio:
    * Modificare la stringa di connessione del database all'interno del computer.
    * Modificare la configurazione o le regole del server Web.


### <a name="run-a-test-failover-on-recovery-plans"></a>Eseguire un failover di test nei piani di ripristino

È possibile usare un piano di ripristino per attivare un failover di test. Attenersi alle procedure consigliate seguenti:

- Completare sempre un failover di test su un'app prima di eseguire un failover completo. I failover di test aiutano a controllare se l'app diventa disponibile nel sito di ripristino.
- Se si verifica un problema, attivare una pulizia e quindi eseguire di nuovo il failover di test. 
- Eseguire un failover di test più volte fino a quando non si è certi che il ripristino dell'app avvenga senza problemi.
- Poiché ogni app è diversa, è necessario creare piani di ripristino personalizzati per ogni applicazione ed eseguire un failover di test per ognuno di essi.
- Le app e le relative dipendenze cambiano spesso. Per assicurarsi che i piani di ripristino siano aggiornati, eseguire un failover di test per ogni applicazione ogni trimestre.

    ![Schermata di un piano di ripristino di esempio di test in Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Guarda un video del piano di ripristino

Guarda un breve video di esempio che mostra un failover su un clic per un piano di ripristino per un'app WordPress a due livelli.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Passaggi successivi

- [Creare](site-recovery-create-recovery-plans.md) un piano di ripristino.
- [Eseguire](site-recovery-failover.md) i failover. 
