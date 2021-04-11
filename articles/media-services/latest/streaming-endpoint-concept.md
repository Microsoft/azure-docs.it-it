---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campi obbligatori. Scopri di più su aka.ms/skyeye/meta.
title: endpoint di streaming (Origin): Descrizione di servizi multimediali di Azure: informazioni sugli endpoint di streaming (Origin), un servizio di creazione dinamica dei pacchetti e di streaming che fornisce contenuti direttamente a un'app lettore client o a una rete per la distribuzione di contenuti (CDN). Servizi: Media-Services documentationcenter:'' Author: IngridAtMicrosoft Manager: FEMila Editor:''

ms. Service: Media-Services ms. workload: ms. Topic: Conceptual ms. Date: 02/13/2020 ms. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Endpoint di streaming (Origin) in servizi multimediali di Azure

In Servizi multimediali di Microsoft Azure, un [endpoint di streaming](/rest/api/media/streamingendpoints) rappresenta un servizio di creazione e creazione di pacchetti dinamici (just-in-Time) in grado di fornire contenuti live e on demand direttamente a un'app per giocatori client usando uno dei protocolli di streaming media comuni (HLS o Dash). Inoltre, l' **endpoint di streaming** fornisce la crittografia dinamica (just-in-Time) a DRM leader del settore. 

Quando si crea un account di Servizi multimediali viene creato un endpoint di streaming **predefinito** nello stato Arrestato. È possibile creare altri endpoint di streaming con l'account (vedere [quote e limiti](limits-quotas-constraints-reference.md)).

> [!NOTE]
> Per avviare lo streaming di video, è necessario avviare l **'endpoint di streaming** da cui si vuole trasmettere il video.
>
> Viene addebitato solo quando l'endpoint di streaming è nello stato in esecuzione.

Assicurarsi di rivedere anche l'argomento [Dynamic Packaging](encode-dynamic-packaging-concept.md) . 

## <a name="naming-convention"></a>Convenzione di denominazione

Il formato del nome host dell'URL di streaming è: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , dove `servicename` = il nome dell'endpoint di streaming o il nome dell'evento Live.

Quando si usa l'endpoint di streaming predefinito, `servicename` viene omesso, quindi l'URL è: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Limitazioni

* Il nome dell'endpoint di streaming ha un valore massimo di 24 caratteri.
* Il nome deve seguire questo modello [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Tipi

Sono disponibili due tipi di **endpoint di streaming** : **standard** (anteprima) e **Premium**. Il tipo è definito in base al numero di unità di scala (`scaleUnits`) allocate per l'endpoint di streaming.

Il limite massimo di unità di streaming è in genere 10. Contattaci [qui](https://azure.microsoft.com/support/create-ticket/) per aumentare il limite per l'account.

La tabella seguente descrive i tipi:

|Tipo|Unità di scala|Descrizione|
|--------|--------|--------|  
|**Standard**|0|L'endpoint di streaming predefinito è un tipo **standard** , che può essere modificato nel tipo Premium regolando `scaleUnits` .|
|**Premium**|>0|**Premium** Gli endpoint di streaming sono adatti per carichi di lavoro avanzati e offrono una capacità di larghezza di banda dedicata e scalabile. Si passa a un tipo **Premium** regolando `scaleUnits` (unità di streaming). Il valore `scaleUnits` rappresenta la capacità di uscita dedicata acquistabile in incrementi di 200 Mbps. Quando si usa il tipo **Premium** , ogni unità abilitata fornisce ulteriore capacità di larghezza di banda per l'app. |

> [!NOTE]
> Per i clienti che desiderano distribuire contenuti a destinatari Internet di grandi dimensioni, è consigliabile abilitare la rete CDN nell'endpoint di streaming.

Per informazioni sul contratto di contratto, vedere [prezzi e contratto di contratto](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Confronto tra le tipologie di streaming

Funzionalità|Standard|Premium
---|---|---
Velocità effettiva |Fino a 600 Mbps e possono fornire una velocità effettiva molto più elevata quando si usa una rete CDN.|200 Mbps per unità di streaming (SU). Può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.
Rete CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Ogni giorno|Ogni giorno
Crittografia dinamica|Sì|Sì
creazione dinamica dei pacchetti|Sì|Sì
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|SUs aggiuntivo
Filtro IP/G20/host personalizzato <sup>1</sup>|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale.

<sup>1</sup> usato direttamente sull'endpoint di streaming quando la rete CDN non è abilitata nell'endpoint.<br/>

### <a name="versions"></a>Versioni

|Tipo|StreamingEndpointVersion|ScaleUnits|Rete CDN|Fatturazione|
|--------------|----------|-----------------|-----------------|-----------------|
|Classic|1.0|0|N/D|Gratuito|
|Endpoint di streaming standard (anteprima)|2.0|0|Sì|Paid|
|Unità di streaming Premium|1.0|>0|Sì|Paid|
|Unità di streaming Premium|2.0|>0|Sì|Paid|

### <a name="features"></a>Funzionalità

Funzionalità|Standard|Premium
---|---|---
Velocità effettiva |Fino a 600 Mbps e possono fornire una velocità effettiva molto più elevata quando si usa una rete CDN.|200 Mbps per unità di streaming (SU). Può fornire una velocità effettiva molto più elevata quando viene usata una rete CDN.
Rete CDN|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.|Rete CDN di Azure, rete CDN di terze parti o nessuna rete CDN.
Fatturazione con ripartizione proporzionale| Ogni giorno|Ogni giorno
Crittografia dinamica|Sì|Sì
creazione dinamica dei pacchetti|Sì|Sì
Scalabilità|Scalabilità automatica fino alla velocità effettiva di destinazione.|Unità di streaming aggiuntive.
Filtro IP/G20/host personalizzato <sup>1</sup>|Sì|Sì
Download progressivo|Sì|Sì
Uso consigliato |Consigliato per la maggior parte dei casi di streaming.|Uso professionale. 

<sup>1</sup> usato direttamente sull'endpoint di streaming quando la rete CDN non è abilitata nell'endpoint.<br/>

Per informazioni sul contratto di contratto, vedere [prezzi e contratto di contratto](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migrazione tra le tipologie

Da | A | Azione
---|---|---
Classico|Standard|È necessario fornire il consenso esplicito
Classic|Premium| Scalabilità (unità di streaming aggiuntive)
Standard/Premium|Classic|Non disponibile (se la versione dell'endpoint di streaming è 1.0. È consentito modificare la tipologia classica impostando Scaleunits su "0")
Standard (con/senza la rete CDN)|Premium con le stesse configurazioni|Consentito nello stato **avviato**. (tramite il Portale di Azure)
Premium (con/senza la rete CDN)|Standard con le stesse configurazioni|Consentito nello stato **avvio** (tramite il Portale di Azure)
Standard (con/senza la rete CDN)|Premium con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Premium (con/senza la rete CDN)|Standard con diverse configurazioni|Consentito nello stato **interrotto** (tramite il Portale di Azure). Non consentito nello stato di esecuzione.
Versione 1.0 con SU >=1 con la rete CDN|Standard/Premium con nessuna rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**.
Versione 1.0 con SU >=1 con la rete CDN|Standard con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della versione 1.0 e ne verrà creata e avviata una nuova.
Versione 1.0 con SU >=1 con la rete CDN|Premium con/senza la rete CDN|Consentito nello stato **interrotto**. Non consentito nello stato **avviato**. Verrà eliminata la rete CDN della tipologia classica e ne verrà creata e avviata una nuova.







## <a name="streaming-endpoint-properties"></a>Proprietà dell'endpoint di streaming

Questa sezione fornisce informazioni dettagliate su alcune delle proprietà dell'endpoint di streaming. Per esempi di come creare un nuovo endpoint di streaming e per le descrizioni di tutte le proprietà, vedere [Streaming Endpoint](/rest/api/media/streamingendpoints/create) (Endpoint di streaming).

- `accessControl`: Usato per configurare le seguenti impostazioni di sicurezza per questo endpoint di streaming: chiavi di autenticazione dell'intestazione della firma Akamai e indirizzi IP autorizzati a connettersi a questo endpoint. Questa proprietà può essere impostata solo quando `cdnEnabled` è impostato su false.

- `cdnEnabled`: Indica se l'integrazione della rete CDN di Azure per questo endpoint di streaming è abilitata (disabilitata per impostazione predefinita). Se `cdnEnabled` è impostata su true, vengono disabilitate le configurazioni seguenti: `customHostNames` e `accessControl`.

    Non tutti i data center supportano l'integrazione della rete CDN di Azure. Per verificare se l'integrazione della rete CDN di Azure data center è disponibile, seguire questa procedura:

  - Provare a impostare `cdnEnabled` su true.
  - Controllare il risultato restituito per un `HTTP Error Code 412` (PreconditionFailed) con un messaggio "Impossibile impostare la proprietà CdnEnabled dell'endpoint di streaming su true perché la funzionalità della rete CDN non è disponibile nell'area corrente".

    Se si riceve questo errore, il data center non lo supporta. Provare con un'altra data center.

- `cdnProfile`: Quando `cdnEnabled` è impostato su true, è anche possibile passare `cdnProfile` i valori. `cdnProfile` è il nome del profilo della rete CDN in cui verrà creato il punto di endpoint della rete CDN. È possibile fornire un cdnProfile esistente o usarne uno nuovo. Se il valore è NULL e `cdnEnabled` è impostata su true, viene usato il valore predefinito "AzureMediaStreamingPlatformCdnProfile". Se il `cdnProfile` fornito è già esistente, nel profilo viene creato un endpoint. Se il profilo non esiste, viene creato automaticamente un nuovo profilo.
- `cdnProvider`: Quando la rete CDN è abilitata, è anche possibile passare `cdnProvider` i valori. `cdnProvider` consente di controllare il provider usato. Sono attualmente supportati tre valori: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se non viene specificato alcun valore e `cdnEnabled` è true, viene usato "StandardVerizon", ovvero il valore predefinito.
- `crossSiteAccessPolicies`: Usato per specificare i criteri di accesso tra siti per diversi client. Per altre informazioni, vedere [Cross-domain policy file specification](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) (Specifica dei file di criteri tra domini) e [Making a Service Available Across Domain Boundaries](/previous-versions/azure/azure-services/gg185950(v=azure.100))(Disponibilità di un servizio tra confini di dominio). Le impostazioni si applicano solo ai Smooth Streaming.
- `customHostNames`: Usato per configurare un endpoint di streaming in modo da accettare il traffico indirizzato a un nome host personalizzato. Questa proprietà è valida per gli endpoint di streaming standard e Premium e può essere impostata quando `cdnEnabled` : false.

    La proprietà del nome di dominio deve essere confermata da servizi multimediali. Servizi multimediali verifica la proprietà del nome di dominio richiedendo un `CName` record contenente l'ID dell'account di servizi multimediali come componente da aggiungere al dominio in uso. Ad esempio, per usare "sports.contoso.com" come nome host personalizzato per l'endpoint di streaming, è necessario configurare un record per `<accountId>.contoso.com` che punti a uno dei nomi dell'host di verifica di Servizi multimediali. Il nome dell'host di verifica è costituito da `\<mediaservices-dns-zone>` verifydns.

    Di seguito sono riportate le zone DNS previste da usare nel record di verifica per diverse aree di Azure.
  
  - America del Nord, Europa, Singapore, Hong Kong SAR, Giappone:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Cina:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Ad esempio, un `CName` record che esegue il mapping di "945a4c4e-28ea-45CD-8ccb-a519f6b700ad.contoso.com" a "verifydns.Media.Azure.NET" dimostra che l'ID di servizi multimediali 945a4c4e-28ea-45CD-8ccb-a519f6b700ad ha la proprietà del dominio contoso.com, consentendo in questo modo di usare qualsiasi nome in contoso.com come nome host personalizzato per un endpoint di streaming con tale account. Per individuare il valore dell'ID di Servizi multimediali, passare al [portale di Azure](https://portal.azure.com/) e selezionare l'account di Servizi multimediali. L' **ID account** viene visualizzato in alto a destra nella pagina.

    Se viene effettuato un tentativo di impostare un nome host personalizzato senza una verifica appropriata del `CName` record, la risposta DNS avrà esito negativo e quindi verrà memorizzata nella cache per un certo periodo di tempo. Una volta definito il record corretto, potrebbe essere necessario del tempo prima che la risposta memorizzata nella cache venga nuovamente convalidata. A seconda del provider DNS per il dominio personalizzato, per riconvalidare il record sono necessari da pochi minuti a un'ora.

    Oltre all'oggetto `CName` mappato `<accountId>.<parent domain>` a `verifydns.<mediaservices-dns-zone>` , è necessario crearne un altro `CName` che esegue il mapping del nome host personalizzato (ad esempio, `sports.contoso.com` ) al nome host dell'endpoint di streaming di servizi multimediali (ad esempio, `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Gli endpoint di streaming che si trovano nella stessa data center non possono condividere lo stesso nome host personalizzato.

    Attualmente Servizi multimediali non supporta TLS con i domini personalizzati.

- `maxCacheAge` : Esegue l'override dell'intestazione di controllo della cache HTTP Max-Age predefinita impostata dall'endpoint di streaming nei frammenti di supporti e nei manifesti su richiesta. Il valore è impostato in secondi.
- `resourceState` -

    - Arrestato: stato iniziale di un endpoint di streaming dopo la creazione
    - Avvio: transizione allo stato in esecuzione
    - Running: è in grado di trasmettere il contenuto ai client
    - Ridimensionamento: aumento o riduzione delle unità di scala
    - Arresto in corso: transizione allo stato interrotto
    - Eliminazione di: verrà eliminato

- `scaleUnits`: Fornire capacità in uscita dedicata che può essere acquistata con incrementi di 200 Mbps. Se è necessario passare al tipo **Premium**, regolare `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Perché usare più endpoint di streaming?

Un singolo endpoint di streaming può trasmettere video sia live che su richiesta e la maggior parte dei clienti USA un solo endpoint di streaming. Questa sezione contiene alcuni esempi dei motivi per cui potrebbe essere necessario usare più endpoint di streaming.

* Ogni unità riservata supporta 200 Mbps di larghezza di banda. Se è necessario più di 2.000 Mbps di larghezza di banda (2 Gbps), è possibile usare il secondo endpoint di streaming e il bilanciamento del carico per ottenere una maggiore larghezza di banda.

    Tuttavia, la rete CDN è il modo migliore per ottenere la scalabilità orizzontale per il contenuto in streaming, ma se si distribuisce un contenuto così elevato che la rete CDN sta estraendo più di 2 Gbps, è possibile aggiungere altri endpoint di streaming (Origin). In questo caso è necessario distribuire gli URL del contenuto bilanciati tra i due endpoint di streaming. Questo approccio offre una migliore memorizzazione nella cache rispetto al tentativo di inviare richieste a ogni origine in modo casuale, ad esempio tramite Gestione traffico. 
    
    > [!TIP]
    > In genere, se la rete CDN sta effettuando il pull di più di 2 Gbps, un elemento potrebbe non essere configurato correttamente (ad esempio, nessuna schermatura delle origini).
    
* Bilanciamento del carico di diversi provider di rete CDN. Ad esempio, è possibile configurare l'endpoint di streaming predefinito per usare la rete CDN Verizon e crearne uno secondo per usare Akamai. Quindi, aggiungere un bilanciamento del carico tra i due per ottenere il bilanciamento del carico di rete CDN. 

    Tuttavia, i clienti spesso eseguono il bilanciamento del carico tra più provider della rete CDN usando un'unica origine.
* Streaming di contenuto misto: Live e video on demand. 

    I modelli di accesso per il contenuto Live e su richiesta sono molto diversi. Il contenuto Live tende a richiedere un numero elevato di richieste per lo stesso contenuto in una sola volta. Il contenuto del video su richiesta (contenuto dell'archivio della coda lunga per l'istanza) ha un basso utilizzo sullo stesso contenuto. Quindi, la memorizzazione nella cache funziona molto bene sul contenuto attivo, ma non anche sul contenuto a lungo termine.

    Si consideri uno scenario in cui i clienti stanno principalmente controllando i contenuti live, ma solo occasionalmente controllano i contenuti su richiesta e vengono serviti dallo stesso endpoint di streaming. Il basso utilizzo del contenuto su richiesta occupa lo spazio della cache che verrebbe salvato meglio per il contenuto Live. In questo scenario si consiglia di servire il contenuto Live da un endpoint di streaming e il contenuto della coda lunga da un altro endpoint di streaming. Ciò consentirà di migliorare le prestazioni del contenuto dell'evento Live.
    
## <a name="scaling-streaming-with-cdn"></a>Ridimensionamento dello streaming con la rete CDN

Vedere gli articoli seguenti:

- [Panoramica della rete CDN](../../cdn/cdn-overview.md)
- [Ridimensionamento dello streaming con la rete CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Porre domande e ottenere gli aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

[Creazione dinamica dei pacchetti](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli endpoint di streaming](manage-streaming-endpoints-howto.md)
