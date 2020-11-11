---
title: Domande frequenti sugli hub di notifica di Azure | Microsoft Docs
description: Domande frequenti sulla progettazione e l'implementazione di soluzioni in hub di notifica di Azure.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: notifica push, notifiche push, notifiche push iOS, notifiche push android, push ios, push android
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 9d476b1db645ed1f91b62fcf11464f7077a8fb3c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491427"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notifiche push sicure con Hub di notifica di Azure - Domande frequenti

## <a name="general"></a>Generale

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Che cos'è la struttura di risorse di Hub di notifica?

Hub di notifica di Azure dispone di due livelli di risorse: hub e spazi dei nomi. Un hub è una singola risorsa push in grado di contenere le informazioni push multipiattaforma di un'app. Uno spazio dei nomi è una raccolta di hub in un'area. Il mapping consigliato abbina uno spazio dei nomi con un'unica app. In uno spazio dei nomi è possibile configurare un hub di produzione che funzioni con l'app di produzione, un hub di test che funzioni con l'app di test e così via.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Qual è il modello di prezzo di Hub di notifica?

Per informazioni dettagliate e aggiornate sui prezzi, vedere la pagina [Prezzi di Hub di notifica]. Hub di notifica viene fatturato a livello di spazio dei nomi. (Per la definizione di uno spazio dei nomi, vedere "Che cos'è la struttura di risorse di Hub di notifica?") Hub di notifica offre tre livelli:

* **Gratuito** : questo livello è un buon punto di partenza per esplorare le funzionalità push. Non è consigliabile per le applicazioni di produzione. Si ottengono 500 dispositivi e 1 milione di push inclusi per ogni spazio dei nomi al mese, senza garanzia di Contratto di servizio.
* **Basic** : questo livello, come il livello Standard, è consigliato per piccole applicazioni di produzione. Si ottengono 200.000 dispositivi e 10 milioni di push inclusi per ogni spazio dei nomi al mese come baseline.
* **Standard** : livello consigliato per le applicazioni di produzione di medie o grandi dimensioni. Si ottengono 10 milioni di dispositivi e 10 milioni di push inclusi per ogni spazio dei nomi al mese come baseline. Include dati di telemetria avanzata. Vengono forniti dati aggiuntivi sullo stato di push.

Funzionalità del livello Standard:

* **Telemetria avanzata** : è possibile usare Hub di notifica per la telemetria per messaggio per tenere traccia delle richieste push e i commenti di Platform Notification System per il debug.
* **Multi-tenant** : è possibile usare le credenziali Platform Notification System a livello di spazio dei nomi. Questa opzione consente di suddividere facilmente i tenant in hub all'interno del medesimo spazio dei nomi.
* **Push pianificati** : è possibile le notifiche da inviare.
* **Operazioni bulk** : Abilita la funzionalità di esportazione/importazione delle registrazioni come descritto nel documento relativo all' [esportazione/importazione delle registrazioni] .

### <a name="what-is-the-notification-hubs-sla"></a>Qual è il contratto di servizio di Hub di notifica?

Per i livelli Basic e Standard di Hub di notifica, le applicazioni correttamente configurate possono inviare notifiche push o eseguire operazioni di gestione delle registrazioni almeno per il 99,9% del tempo. Per altre informazioni sul Contratto di servizio, visitare la pagina del [contratto di servizio per Hub di notifica](https://azure.microsoft.com/support/legal/sla/notification-hubs/).

> [!NOTE]
> Poiché le notifiche push dipendono da sistemi di notifica della piattaforma di terze parti, ad esempio il servizio di notifica push di Apple (APNs) e Firebase Cloud Messaging (FCM) di Google, non esiste alcuna garanzia del contratto di servizio per la consegna di tali messaggi. Dopo che Hub di notifica ha inviato i batch a Platform Notification System, come garantito dal contratto di servizio, è Platform Notification System a dover recapitare i push, senza garanzia del contratto di servizio.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Come eseguire l'aggiornamento o il downgrade di un hub o di uno spazio dei nomi a un livello diverso?

Passare a **[portale di Azure]**  >  **spazi dei nomi di hub di notifica** o hub di **notifica**. Selezionare la risorsa che si desidera aggiornare, quindi passare al **piano tariffario**. Tenere presenti i requisiti seguenti:

* Il piano tariffario aggiornato si applica a *tutti* gli hub nello spazio dei nomi in uso.
* Se il conteggio dei dispositivi supera il limite del livello a cui si passa, è necessario eliminare i dispositivi prima effettuare il downgrade.

## <a name="design-and-development"></a>Progettazione e sviluppo

### <a name="which-server-side-platforms-do-you-support"></a>Quali piattaforme sul lato server sono supportate?

Gli SDK server sono disponibili per .NET, Java, Node.js, PHP e Python. Le API di Hub di notifica si basano su interfacce REST, pertanto è possibile lavorare direttamente con le API REST se si usano piattaforme diverse o se non si desidera una dipendenza aggiuntiva. Per altre informazioni, vedere la pagina sulle [API REST di Hub di notifica].

### <a name="which-client-platforms-do-you-support"></a>Quali piattaforme client sono supportate?

Sono supportate le notifiche push [per iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Novell [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Per ulteriori informazioni, vedere la pagina relativa alle [esercitazioni introduzione di hub di notifica](ios-sdk-get-started.md) .

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Sono supportate le notifiche via SMS, messaggi di posta elettronica o Web?

Hub di notifica invia notifiche ai dispositivi che eseguono app per dispositivi mobili. Non offre funzionalità di posta elettronica o SMS. Hub di notifica non fornisce neanche un servizio predefinito per il recapito di notifiche push all'interno del browser. I clienti possono implementare questa funzionalità mediante SignalR su piattaforme sul lato server supportate. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Quanti dispositivi sono supportati se si inviano notifiche push tramite Hub di notifica?

Per informazioni dettagliate sul numero di dispositivi supportati, vedere la pagina [Prezzi di Hub di notifica].

Se è necessario il supporto per più di 10 milioni dispositivi registrati, è necessario partizionare i dispositivi in più spazi dei nomi.

### <a name="how-many-push-notifications-can-i-send-out"></a>Quante notifiche push si può inviare?

A seconda del piano selezionato, Hub di notifica di Azure passa automaticamente a un piano superiore in base al numero di notifiche inviate nel sistema.

> [!NOTE]
> Il costo complessivo dell'utilizzo può aumentare in base al numero di notifiche push inviate. È importante conoscere i limiti dei piani descritti nella pagina [Prezzi di Hub di notifica].

I clienti usano Hub di notifica per inviare milioni di notifiche push ogni giorno. Non è necessario eseguire alcuna operazione particolare per ridimensionare la copertura delle notifiche push, purché si usi Hub di notifica di Azure.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Quanto tempo occorre perché le notifiche push inviate raggiungano il dispositivo?

In uno scenario di utilizzo normale, dove il carico in ingresso è piuttosto coerente e uniforme, Hub di notifica di Azure è in grado di elaborare almeno *1 milione di invii di notifiche push al minuto*. Questa velocità può variare in base al numero di tag, alla natura delle trasmissioni in ingresso e ad altri fattori esterni.

Nell'intervallo di tempo stimato per il recapito, il servizio calcola le destinazioni per piattaforma e instrada i messaggi al Push Notification Service (PNS) in base ai tag/espressioni tag registrati. È responsabilità del PNS inviare le notifiche al dispositivo.

Il PNS non garantisce alcun Contratto di servizio per il recapito delle notifiche. Tuttavia, la maggior parte delle notifiche push vengono recapitate ai dispositivi di destinazione entro pochi minuti (in genere entro 10 minuti) dal momento in cui vengono inviati a Hub di notifica. Alcune notifiche potrebbero richiedere più tempo.

> [!NOTE]
> Hub di notifica di Azure include anche un criterio per l'eliminazione delle notifiche che non vengono recapitate al PNS entro 30 minuti. Questo ritardo può verificarsi per diversi motivi, più comunemente perché il PNS sta rallentando l'applicazione.

### <a name="is-there-any-latency-guarantee"></a>La latenza è garantita?

Data la natura delle modifiche push, che vengono recapitate da un PNS esterno specifico di una piattaforma, non esiste alcuna garanzia di latenza. In genere, la maggior parte delle notifiche push viene recapitata entro pochi minuti.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Quali sono gli aspetti da considerare per la progettazione di una soluzione con spazi dei nomi e hub di notifica?

#### <a name="mobile-appenvironment"></a>Ambiente/app per dispositivi mobili

* Usare un hub di notifica per ogni app per dispositivi mobili per ogni ambiente.
* In uno scenario multi-tenant, ogni tenant dovrebbe avere un hub separato.
* Non condividere mai lo stesso hub di notifica fra ambienti di produzione e test. Ciò potrebbe causare problemi durante l'invio delle notifiche. Apple offre endpoint Sandbox e Production Push, che hanno credenziali separate.
* Per impostazione predefinita, è possibile inviare notifiche ai dispositivi registrati tramite il portale di Azure o il componente integrato di Azure in Visual Studio. La soglia è impostata su 10 dispositivi, che vengono selezionati in modo casuale dal pool di registrazione.

> [!NOTE]
> Se l'hub è stato originariamente configurato con un certificato Sandbox Apple e successivamente è stato riconfigurato per usare un certificato di produzione Apple, i token del dispositivo originale non sono validi. I token non validi determinano la mancata riuscita del push. Separare l'ambiente di test da quello di produzione e usare hub diversi per ambienti diversi.

#### <a name="pns-credentials"></a>Credenziali PNS

Quando un'app per dispositivi mobili viene registrata con il portale per sviluppatori della piattaforma (ad esempio Apple o Google), vengono inviati un identificatore app e i token di sicurezza. Il back-end di app fornisce questi token al PNS della piattaforma, in modo che possano essere inviate notifiche push ai dispositivi. I token di sicurezza possono essere sotto forma di certificati (ad esempio Apple iOS o Windows Phone) o chiavi di sicurezza (ad esempio Google Android o Windows). Devono essere configurati in hub di notifica. La configurazione viene eseguita in genere a livello di hub di notifica, ma può essere eseguita anche a livello di spazio dei nomi in uno scenario multi-tenant.

#### <a name="namespaces"></a>Spazi dei nomi

Gli spazi dei nomi possono essere usati anche per il raggruppamento di distribuzione. Possono essere usati anche per rappresentare tutti gli hub di notifica per tutti i tenant della stessa app in uno scenario multi-tenant.

#### <a name="geo-distribution"></a>Distribuzione geografica

La distribuzione geografica non è sempre fondamentale negli scenari di notifiche push. Varie PNS (ad esempio, APNs o FCM) che forniscono notifiche push ai dispositivi non sono distribuite in modo uniforme.

Se si ha un'applicazione che viene usata a livello globale, è possibile creare hub in vari spazi dei nomi tramite il servizio Hub di notifica in diverse aree di Azure nel mondo.

> [!NOTE]
> Tuttavia non si consiglia di farlo, perché in questo modo aumenta il costo di gestione, in particolare per le registrazioni. Si consiglia di farlo solo se vi è l'esigenza esplicita.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Le registrazioni devono essere eseguite dal back-end dell'app o direttamente tramite i dispositivi client?

Le registrazioni dal back-end dell'app sono utili quando è necessario autenticare i client prima di creare la registrazione. Sono utili anche quando è necessario creare o modificare tag dal back-end dell'app in base alla logica dell'applicazione. Per altre informazioni, vedere le pagine [Registrazione dal back-end dell'app] e [Guida alla registrazione del back-end 2].

### <a name="what-is-the-push-notification-delivery-security-model"></a>Qual è il modello di sicurezza del recapito di notifiche push?

Hub di notifica di Azure usa un modello di sicurezza basato sulla [firma di accesso condiviso](../storage/common/storage-sas-overview.md). È possibile usare i token di firma di accesso condiviso a livello di spazio dei nomi radice o a livello di hub di notifica granulare. I token di firma di accesso condiviso possono essere impostati in modo da seguire regole di autorizzazione diverse, ad esempio per inviare le autorizzazioni di messaggi o per ascoltare le autorizzazioni di notifica. Per altre informazioni, vedere il documento sul [modello di sicurezza di Hub di notifica].

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Come si gestisce un payload sensibile nelle notifiche push?

Tutte le notifiche vengono recapitate ai dispositivi di destinazione dal PNS della piattaforma. Quando una notifica viene inviata a Hub di notifica di Azure, viene elaborata e passata al rispettivo PNS.

Tutte le connessioni, dal mittente a Hub notifiche di Azure al PNS, usano HTTPS.

> [!NOTE]
> Hub di notifica di Azure non registra il payload dei messaggi.

Per l'invio di payload sensibili è consigliabile usare un modello push sicuro. Il mittente invia una notifica ping con un identificatore di messaggio al dispositivo senza il payload sensibile. Quando l'app sul dispositivo riceve il payload, l'applicazione chiama un'API sicura direttamente per recuperare i dettagli del messaggio. Per informazioni di guida su come implementare questo modello, vedere la pagina sull'[esercitazione relativa al push sicuro di Hub di notifica].

## <a name="operations"></a>Operazioni

### <a name="what-support-is-provided-for-disaster-recovery"></a>Quale supporto è fornito per il ripristino di emergenza?

È fornita la copertura del ripristino di emergenza dei metadati (nome di Hub di notifica, stringa di connessione e altre informazioni fondamentali) sul lato del servizio. Quando viene attivato uno scenario di ripristino di emergenza, i dati delle registrazioni sono l' *unico segmento* dell'infrastruttura di Hub di notifica che andrà perso. È necessario implementare una soluzione per ripopolare questi dati nel nuovo hub dopo il ripristino:

1. Creare un hub di notifica secondario in un controller di dominio diverso. È consigliabile crearne uno dall'inizio per proteggersi da un evento di ripristino di emergenza che potrebbe interferire con le capacità di gestione. È anche possibile crearne uno al momento dell'evento di ripristino di emergenza.

2. Lasciare sincronizzato l'hub di notifica secondario con l'hub di notifica primario usando una delle opzioni seguenti:

   * Usare un back-end dell'app che crea e aggiorna contemporaneamente le installazioni in entrambi gli hub di notifica. Le installazioni consentono di specificare un identificatore univoco del dispositivo, rendendolo più adatto per lo scenario di replica. Per ulteriori informazioni, vedere questo [esempio di codice](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Usare un back-end di app che ottiene regolarmente un dump delle registrazioni dall'hub di notifica primario come backup. Esso potrà quindi eseguire un inserimento di massa nell'hub di notifica secondario.

L'hub di notifica secondario può finire con installazioni/registrazioni scadute. Quando viene eseguito il push di un handle scaduto, hub di notifica pulisce automaticamente il record di installazione/registrazione associato in base alla risposta ricevuta dal server PNS. Per pulire i record scaduti da un hub di notifica secondario, aggiungere la logica personalizzata che elabora il feedback da ogni invio. Quindi, impostare la scadenza dell'installazione/registrazione nell'hub di notifica secondario.

Se non si ha un back-end, all'avvio dell'app nei dispositivi di destinazione viene eseguita una nuova registrazione nell'hub di notifica secondario. Alla fine l'hub di notifica secondario avrà tutti i dispositivi attivi registrati.

Vi sarà un periodo di tempo in cui i dispositivi con app non aperte non riceveranno le notifiche.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Tutti i dati vengono archiviati in formato crittografato?

Hub di notifica di Azure crittografa tutti i dati dei clienti inattivi, ad eccezione dei tag di registrazione. Per questo motivo, è consigliabile non archiviare dati personali o riservati usando i tag.

### <a name="is-there-audit-log-capability"></a>È disponibile una funzionalità di log di controllo?

Sì. Tutte le operazioni di gestione di Hub di notifica aggiornano il log attività di Azure, esposto nel [portale di Azure]. Il log attività di Azure offre informazioni approfondite sulle operazioni eseguite sulle risorse nelle sottoscrizioni. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. È anche possibile comprendere lo stato delle operazioni e altre proprietà pertinenti. Tuttavia, il log attività non include operazioni di lettura (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

### <a name="what-troubleshooting-capabilities-are-available"></a>Quali funzionalità di risoluzione dei problemi sono disponibili?

Hub di notifica di Azure fornisce numerose funzionalità per la risoluzione dei problemi, in particolare nello scenario frequente delle notifiche eliminate. Per informazioni dettagliate, vedere il white paper sulla [risoluzione dei problemi relativi a Hub di notifica].

### <a name="what-telemetry-features-are-available"></a>Sono disponibili le funzionalità di telemetria?

Hub di notifica di Azure consente la visualizzazione dei dati di telemetria nel [portale di Azure]. Per informazioni dettagliate sulle metriche, vedere la pagina relativa alle [metriche di Hub di notifica].

È anche possibile accedere alle metriche a livello di codice. Per altre informazioni, vedere gli articoli seguenti:

- [Recuperare le metriche di monitoraggio di Azure con .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). In questo esempio vengono utilizzati il nome utente e la password. Per usare un certificato, eseguire l'overload del metodo FromServicePrincipal per fornire un certificato, come illustrato in [questo esempio](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Recupero di metriche e log attività per una risorsa](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Procedura dettagliata sull'API REST di Monitoraggio di Azure](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Le notifiche riuscite significano semplicemente che le notifiche push sono state recapitate al PNS esterno, ad esempio APNs per iOS e macOS o FCM per i dispositivi Android. È responsabilità del PNS inviare le notifiche ai dispositivi di destinazione. In genere il PNS non espone le metriche di recapito a terze parti.  

[Azure portal]: https://portal.azure.com
[Prezzi di Hub di notifica]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[API REST di Hub di notifica]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Registrazione dal back-end dell'app]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Guida alla registrazione del back-end 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Modello di sicurezza di Hub di notifica]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Esercitazione sul push sicuro di Hub di notifica]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Risoluzione dei problemi di Hub di notifica]: ./notification-hubs-push-notification-fixer.md
[Metriche di Hub di notifica]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[esportazione/importazione di registrazioni]: ./export-modify-registrations-bulk.md
[Azure portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
