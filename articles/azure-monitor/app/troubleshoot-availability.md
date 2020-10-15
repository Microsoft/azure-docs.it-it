---
title: Risolvere i problemi relativi ai test di disponibilità di applicazione Azure Insights
description: Risolvere i problemi dei test Web in applicazione Azure Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318148"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

Questo articolo consente di risolvere i problemi comuni che possono verificarsi quando si usa il monitoraggio della disponibilità.

## <a name="ssltls-errors"></a>Errori SSL/TLS

|Sintomo/messaggio di errore| Possibili cause|
|--------|------|
|Non è stato possibile creare il canale sicuro SSL/TLS  | Versione SSL. Sono supportati solo TLS 1,0, 1,1 e 1,2. **SSLv3 non è supportato.**
|Livello record TLSv 1.2: avviso (livello: irreversibile, Descrizione: MAC record non valido)| Per [ulteriori informazioni](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake), vedere thread stackexchange.
|L'URL che ha avuto esito negativo è la rete CDN (rete per la distribuzione di contenuti) | Il problema potrebbe essere causato da una configurazione errata della rete CDN |  

### <a name="possible-workaround"></a>Soluzione alternativa possibile

* Se gli URL in cui si verifica il problema sono sempre a risorse dipendenti, è consigliabile disabilitare le **richieste di analisi dipendenti** per il test Web.

## <a name="test-fails-only-from-certain-locations"></a>Il test ha esito negativo solo da determinate posizioni

|Sintomo/messaggio di errore| Possibili cause|
|----|---------|
|Tentativo di connessione non riuscito. risposta non corretta della parte connessa dopo un periodo di tempo  | Gli agenti di test in determinate posizioni sono bloccati da un firewall.|
|    |Il reindirizzamento di determinati indirizzi IP avviene tramite (bilanciamenti del carico, responsabili del traffico geografico, Azure Express Route). 
|    |Se si usa Azure ExpressRoute, esistono scenari in cui i pacchetti possono essere eliminati nei casi in cui [si verifica il routing asimmetrico](../../expressroute/expressroute-asymmetric-routing.md).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Errore di test con errore di violazione del protocollo

|Sintomo/messaggio di errore| Possibili cause| Possibili soluzioni |
|----|---------|-----|
|Il server ha eseguito il commit di una violazione del protocollo. Section = ResponseHeader detail = CR deve essere seguito da LF | Questo errore si verifica quando vengono rilevate intestazioni in formato non valido. In particolare, è possibile che alcune intestazioni non usino CRLF per indicare la fine della riga, violando la specifica HTTP. Application Insights impone questa specifica HTTP e non genera risposte con intestazioni in formato non valido.| a. Contattare il provider host del sito Web o il provider della rete CDN per correggere i server difettosi. <br> b. Se le richieste non riuscite sono risorse (ad esempio, file di stile, immagini, script), è possibile disabilitare l'analisi delle richieste dipendenti. Tenere presente che, se si esegue questa operazione, si perderà la possibilità di monitorare la disponibilità di tali file.

> [!NOTE]
> l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Domande comuni sulla risoluzione dei problemi

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Il sito sembra funzionare correttamente, ma i test segnalano errori. Perché Application Insights invia avvisi?

   * Il test ha **richieste di analisi dipendenti** abilitate? Ciò comporta un controllo rigoroso delle risorse, ad esempio script, immagini e così via. Questi tipi di errori potrebbero non essere evidenti in un browser. Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. Se uno di essi ha esito negativo, il test viene segnalato come non superato, anche se la pagina HTML principale viene caricata senza problemi. Per desensibilizzare il test a errori di questo tipo, è sufficiente deselezionare l'opzione analizza richieste dipendenti dalla configurazione di test.

   * Per ridurre le probabilità di rumore dai blip di rete temporanei e così via, verificare che sia selezionata l'opzione Abilita tentativi per la configurazione degli errori dei test. È anche possibile eseguire test da più posizioni e gestire la soglia delle regole di avviso di conseguenza per evitare che problemi specifici della posizione causino avvisi non dovuti.

   * Fare clic su uno qualsiasi dei punti rossi dell'esperienza di disponibilità oppure su qualsiasi errore di disponibilità presente in Esplora ricerche per visualizzare i dettagli del motivo per cui è stato segnalato l'errore. Il risultato del test, insieme ai dati di telemetria lato server correlati (se abilitati), dovrebbe consentire di comprendere perché il test non è riuscito. Le cause più comuni dei problemi temporanei sono problemi di connessione o di rete.

   * Controllare se si è verificato il timeout del test. I test vengono interrotti dopo 2 minuti. Se il ping o il test in più passaggi richiede più di 2 minuti, verrà segnalato come errore. È consigliabile suddividere il test in più test di durata più breve.

   * Valutare se sono stati segnalati errori in tutte le località e solo in alcune. Se gli errori sono stati segnalati solo in alcune località, potrebbero essere causati da errori di rete/CDN. Anche in questo caso, facendo clic sui puntini rossi dovrebbe essere possibile comprendere perché nella località sono stati segnalati errori.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Non si è ricevuto un messaggio di posta elettronica quando l'avviso è stato attivato o risolto o in entrambi i casi?

Controllare la configurazione degli avvisi classici per verificare se l'indirizzo di posta elettronica è inserito direttamente nell'elenco o se per la ricezione delle notifiche è configurata una lista di distribuzione che include tale indirizzo. In quest'ultimo caso, controllare la configurazione della lista di distribuzione per verificare che possa ricevere messaggi di posta elettronica esterni. Controllare anche se l'amministratore di posta elettronica ha eventualmente configurato criteri che possono causare questo problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Non si è ricevuto la notifica webhook?

Verificare che l'applicazione che riceve la notifica webhook sia disponibile e che riesca a elaborare le richieste di un webhook. Per altre informazioni, vedere [qui](../platform/alerts-log-webhook.md).

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Si ricevono 403 errori non consentiti, che cosa significa?

Questo errore indica che è necessario aggiungere eccezioni del firewall per consentire agli agenti di disponibilità di testare l'URL di destinazione. Per un elenco completo degli indirizzi IP di Agent da consentire, vedere l' [articolo eccezione IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Esito negativo intermittente dei test con un errore di violazione del protocollo?

Un errore di tipo "Violazione del protocollo... CR deve essere seguito da LF" indica un problema relativo al server o alle dipendenze. Questa situazione si verifica quando nella risposta vengono impostate intestazioni in formato non valido e può essere causata da servizi di bilanciamento del carico o reti per la distribuzione di contenuti. In particolare, è possibile che alcune intestazioni non usino CRLF per indicare la fine della riga, che viola la specifica HTTP e pertanto non riesce a eseguire la convalida a livello di WebRequest .NET. Controllare la risposta per individuare le intestazioni, che potrebbero essere in violazione.

> [!NOTE]
> l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Non vengono visualizzati dati di telemetria sul lato server correlati per diagnosticare gli errori dei test? *

Se Application Insights è configurato per l'applicazione lato server, il motivo può essere l'esecuzione del [campionamento](./sampling.md). Selezionare un risultato di disponibilità diverso.

### <a name="can-i-call-code-from-my-web-test"></a>È possibile chiamare codice da un test Web?

No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono diversi plug-in che potrebbero risultare utili.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Esiste una differenza tra "test Web" e "test di disponibilità"?

I due termini vengono usati in modo intercambiabile. Test di disponibilità è un termine più generico che include i singoli test di ping URL oltre ai test Web in più passaggi.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>È possibile usare test di disponibilità nel server interno protetto da un firewall?

   Esistono due possibili soluzioni:

   * Configurare il firewall per consentire richieste in ingresso dagli [indirizzi IP degli agenti di test Web](./ip-addresses.md).
   * Scrivere il proprio codice per testare periodicamente il server interno. Eseguire il codice come processo in background in un server di prova protetto da firewall. Il processo di test può inviare i risultati ad Application Insights tramite l'API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) nel pacchetto SDK di base. In questo modo è necessario testare il server per avere un accesso in uscita per l'endpoint di inserimento di Application Insights, ma questo rappresenta un minore rischio per la sicurezza rispetto all'alternativa di consentire le richieste in ingresso. I risultati verranno visualizzati nei pannelli dei test Web di disponibilità, anche se l'esperienza sarà leggermente semplificata rispetto a quanto disponibile per i test creati tramite il portale. I test di disponibilità personalizzati verranno visualizzati anche come risultati della disponibilità in analisi, ricerca e metriche.

### <a name="uploading-a-multi-step-web-test-fails"></a>Non è possibile caricare un test Web in più passi

Di seguito sono riportate alcune delle possibili cause:
   * È previsto un limite di dimensioni pari a 300 KB.
   * I cicli non sono supportati.
   * I riferimenti ad altri test Web non sono supportati.
   * Le origini dati non sono supportate.

### <a name="my-multi-step-test-doesnt-complete"></a>Il test in più passi non viene completato

È previsto un limite di 100 richieste per ogni test. Inoltre, il test viene arrestato se la durata dell'esecuzione è superiore a due minuti.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>È possibile eseguire un test con certificati client?

Non supportato attualmente.

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica solo agli avvisi classici e aiuterà a ottimizzare le notifiche di avviso assicurando che siano ricevute solo dai destinatari desiderati. Per capire meglio la differenza tra gli [avvisi classici](../platform/alerts-classic.overview.md) e la nuova esperienza di avviso fare riferimento all'[articolo Panoramica avvisi](../platform/alerts-overview.md). Per controllare la notifica di avviso nella nuova esperienza di avviso usare i [gruppi di azioni](../platform/action-groups.md).

* Consigliamo di usare destinatari specifici per le notifiche di avviso classiche.

* Per gli avvisi in caso di errori dalle posizioni X da Y, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con ruoli di amministratore o coamministratore.  Essenzialmente _tutti_ gli amministratori della _sottoscrizione_ riceveranno le notifiche.

* Per gli avvisi sulle metriche di disponibilità, l'opzione casella di controllo **bulk/Group** se abilitata, invia agli utenti i ruoli proprietario, collaboratore o lettore nella sottoscrizione. In effetti, _tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights rientrano nell'ambito e riceveranno le notifiche. 

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

Usare la nuova esperienza di avviso/avvisi quasi in tempo reale se si desidera inviare notifiche agli utenti in base ai ruoli. Con [gruppi di azioni](../platform/action-groups.md), è possibile configurare notifiche tramite posta elettronica agli utenti con ruolo proprietario, collaboratore o lettore (non combinati insieme come una singola opzione).

## <a name="next-steps"></a>Passaggi successivi

* [Test Web in più passaggi](availability-multistep.md)
* [Test ping URL](monitor-web-app-availability.md)

