---
title: Panoramica degli avvisi classici in monitoraggio di Azure
description: Gli avvisi classici sono in fase di deprecazione. Gli avvisi consentono di monitorare le metriche, gli eventi e i log delle risorse di Azure e, successivamente, inviare una notifica quando una condizione specificata viene soddisfatta.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: c7dfbd60b7a668b849c3150bb16e40285c345634
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015350"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi classici in Microsoft Azure?

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [avvisi delle metriche near Real time più recenti e una nuova esperienza di avvisi](./alerts-overview.md). Gli avvisi classici vengono [ritirati](./monitoring-classic-retirement.md), anche se ancora in uso limitato per le risorse che non supportano ancora i nuovi avvisi.
>

Gli avvisi consentono di configurare le condizioni sui dati e la notifica di quando le condizioni corrispondono ai dati di monitoraggio più recenti.

## <a name="old-and-new-alerting-capabilities"></a>Funzionalità per gli avvisi obsolete e nuove

In precedenza Monitoraggio di Azure, Application Insights, Log Analytics e Integrità dei servizi di Azure avevano funzionalità di avviso separate. Con il tempo Azure ha migliorato e combinato sia l'interfaccia utente che i diversi metodi di invio degli avvisi. Il consolidamento è ancora in corso.

È possibile visualizzare gli avvisi classici solo nella schermata dell'utente degli avvisi classici nel portale di Azure. Questa schermata viene visualizzata usando il pulsante **Visualizza avvisi classici** sulla schermata degli avvisi. 

 ![Opzioni di avviso nel portale di Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

La nuova esperienza utente degli avvisi offre i vantaggi seguenti rispetto all'esperienza degli avvisi in versione classica:
- **Migliore sistema di notifica**: tutti gli avvisi più recenti usano gruppi di azioni, ovvero gruppi di azioni e notifiche con nome che possono essere riusati in più avvisi. Al contrario, gli avvisi delle metriche classici e gli avvisi di Log Analytics di tipo precedente non usano gruppi di azioni.
- **Esperienza di creazione unificata** : tutti gli avvisi per le metriche, i log e i log attività in monitoraggio di Azure, Log Analytics e Application Insights sono in un'unica posizione.
- **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: è ora possibile visualizzare anche gli avvisi di Log Analytics attivati nella sottoscrizione. In precedenza, era necessario un portale separato.
- **Separazione tra avvisi attivati e regole di avviso**: viene fatta distinzione tra regole di avviso (definizioni delle condizioni che attivano gli avvisi) e avvisi attivati (istanze di attivazione delle regole di avviso) e sono pertanto disponibili visualizzazioni separate per le attività operative e la configurazione.
- **Flusso di lavoro migliore**: la nuova esperienza di creazione di avvisi guida l'utente nel processo di configurazione di una regola di avviso, rendendo più semplice l'identificazione delle condizioni per cui è opportuno ricevere un avviso.
- **Consolidamento degli avvisi intelligenti** e **impostazione dello stato dell'avviso**: gli avvisi più recenti includono funzionalità di raggruppamento automatico che mostrano avvisi simili tra loro per ridurre l'overload dell'interfaccia utente. 

Gli avvisi delle metriche più recenti presentano i seguenti vantaggi rispetto agli avvisi delle metriche classici:
- **Latenza migliorata**: gli avvisi delle metriche più recenti possono essere eseguiti con una frequenza di un minuto. Gli avvisi metrica meno recenti sono sempre eseguibili con una frequenza di 5 minuti. Gli avvisi più recenti presentano un ritardo più breve tra il verificarsi del problema e la notifica o l'azione (da 3 a 5 minuti). Gli avvisi precedenti vanno da 5 a 15 minuti a seconda del tipo.  Gli avvisi relativi ai log presentano in genere un ritardo di 10-15 minuti a causa del tempo necessario per inserire i log, ma i metodi di elaborazione più recenti stanno riducendo questo tempo. 
- **Supporto delle metriche multidimensionali**: è possibile inviare un avviso per le metriche multidimensionali, in modo da monitorare un segmento interessante della metrica.
- **Maggiore controllo delle condizioni delle metriche**: è possibile definire regole di avviso più dettagliate. Gli avvisi più recenti supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche**: è possibile monitorare più metriche (attualmente un massimo di due) con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Migliore sistema di notifica**: tutti gli avvisi più recenti usano [gruppi di azioni](./action-groups.md), ovvero gruppi di azioni e notifiche con nome che possono essere riusati in più avvisi.  Al contrario, gli avvisi delle metriche classici e gli avvisi di Log Analytics di tipo precedente non usano gruppi di azioni. 
- **Metriche dai log** (anteprima pubblica): i dati di log diretti a Log Analytics possono essere ora estratti in metriche di Monitoraggio di Azure e quindi essere oggetto di avvisi come le altre metriche. Vedere [Avvisi (versione classica)]() per la terminologia degli avvisi classici. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Avvisi classici sui dati di Monitoraggio di Azure
Sono disponibili due tipi di avvisi classici, vale a dire gli avvisi delle metriche e gli avvisi del log attività.

* **Avvisi delle metriche classici**: questo avviso si attiva quando il valore di una data metrica supera una soglia assegnata. L'avviso genera una notifica quando tale soglia viene superata e la condizione di avviso è soddisfatta. A questo punto, l'avviso è considerato "attivato". Genera un'altra notifica quando viene "risolto", ovvero quando il valore rientra nella soglia e la condizione non è più soddisfatta.

* **Avvisi classici del log attività**: un avviso di log in streaming che si attiva per una voce di evento del log attività corrispondente ai criteri di filtro. Questi avvisi hanno solo lo stato "Attivato". Il motore degli avvisi applica semplicemente i criteri di filtro a tutti i nuovi eventi. Non esegue una ricerca per trovare voci meno recenti. Questi avvisi possono notificare quando si verifica un nuovo evento imprevisto relativo all'integrità dei servizi o quando un utente o un'applicazione esegue un'operazione nella sottoscrizione, ad esempio "Elimina macchina virtuale."

Per i dati dei log delle risorse disponibili tramite monitoraggio di Azure, indirizzare i dati in Log Analytics e usare un avviso di query di log. Log Analytics ora usa il [nuovo metodo di avviso](./alerts-overview.md) 

Il diagramma seguente riepiloga le origini dei dati in Monitoraggio di Azure e, a livello concettuale, i modi per avvisare di tali dati.

![Avvisi illustrati](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Tassonomia degli avvisi (versione classica)
Azure usa i termini seguenti per descrivere gli avvisi classici e le relative funzioni:
* **Avviso**: una definizione di criteri (una o più regole o condizioni) che vengono attivati quando vengono soddisfatti.
* **Attivo**: lo stato di quando vengono soddisfatti i criteri definiti da un avviso classico.
* **Risolto**: lo stato di quando i criteri definiti da un avviso classico non sono più soddisfatti dopo esserlo stati precedentemente.
* **Notifica**: l'azione eseguita quando un avviso classico diventa attivo.
* **Azione**: una chiamata specifica inviata a un ricevitore di una notifica (ad esempio, l'invio di un messaggio di posta elettronica a un indirizzo o la pubblicazione in un URL del webhook). Le notifiche possono in genere attivare più azioni.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Come si riceve una notifica da un avviso classico di Monitoraggio di Azure?
Gli avvisi di Azure nelle versioni precedenti da servizi diversi usano metodi di notifica predefiniti propri. 

Monitoraggio di Azure ha creato un raggruppamento di notifiche riutilizzabili denominato *gruppi di azioni*. I gruppi di azioni specificano un set di destinatari di una notifica. Ogni volta che viene attivato un avviso che fa riferimento al gruppo di azioni, tutti i destinatari ricevono la notifica. I gruppi di azioni consentono di riutilizzare un raggruppamento di destinatari, ad esempio un elenco di tecnici su richiesta, in molti oggetti avviso. I gruppi di azioni supportano la notifica, oltre ad altre azioni, tramite la registrazione a un URL di webhook, agli indirizzi di posta elettronica e ai numeri SMS.  Per altre informazioni, vedere [Gruppi di azioni](./action-groups.md). 

Gli avvisi classici precedenti del log attività usano i gruppi di azioni.

Non usano invece i gruppi di azioni gli avvisi delle metriche precedenti. È comunque possibile configurare le azioni seguenti: 
- Inviare notifiche tramite posta elettronica all'amministratore del servizio, ai co-amministratori e/o ad altri indirizzi di posta elettronica specificati.
- Richiamare un webhook, che consente di avviare altre azioni di automazione.

I webhook consentono l'automazione e la correzione, ad esempio, usando:
- Runbook di Automazione di Azure
- Funzione di Azure
- App per la logica di Azure
- un servizio di terze parti

## <a name="next-steps"></a>Passaggi successivi
Ottenere informazioni sulle regole degli avvisi e sulla relativa configurazione usando:

* Altre informazioni sulle [metriche](data-platform.md)
* Configurare gli [avvisi delle metriche classici tramite il portale di Azure](alerts-classic-portal.md)
* Configurare gli [avvisi delle metriche classici per PowerShell](alerts-classic-portal.md)
* Configurare gli [avvisi delle metriche classici per l'interfaccia della riga di comando](alerts-classic-portal.md)
* Configurare gli [avvisi delle metriche classici per l'API REST del Monitoraggio di Azure](/rest/api/monitor/alertrules)
* Altre informazioni sul [log attività](platform-logs-overview.md)
* Configurare [gli avvisi del log attività tramite il portale di Azure](activity-log-alerts.md)
* Configurare [gli avvisi del log attività tramite Resource Manager](alerts-activity-log.md)
* Esaminare lo [schema webhook degli avvisi del log attività](activity-log-alerts-webhook.md)
* Altre informazioni sui [gruppi di azioni](action-groups.md)
* Configurare [avvisi più recenti](alerts-metric.md)

