---
title: Panoramica del modello di licenza di servizi multimediali di Azure V3 con Widevine
description: Questo argomento offre una panoramica di un modello di licenza Widevine usato per configurare le licenze Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2020
ms.author: juliako
ms.openlocfilehash: f614bd7f00587c5bdc0e7bc3e4ec737985da328b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996994"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Panoramica del modello di licenza di servizi multimediali V3 con Widevine

Servizi multimediali di Azure consente di crittografare il contenuto con **Google Widevine**. Servizi multimediali offre inoltre un servizio per la distribuzione delle licenze Widevine. Le API di Servizi multimediali di Azure consentono di configurare licenze Google Widevine. Quando un lettore tenta di riprodurre contenuto protetto da Widevine, viene inviata una richiesta al servizio di distribuzione di licenze per ottenere la licenza. Se il servizio di licenza approva la richiesta, genera la licenza. Viene inviata al client e usata per decrittografare e riprodurre il contenuto specificato.

Una richiesta per la licenza Widevine è formattata come messaggio JSON.  

>[!NOTE]
> È possibile creare un messaggio vuoto senza alcun valore, semplicemente "{}". Quindi viene creato un modello di licenza con le impostazioni predefinite. Il valore predefinito funziona nella maggior parte dei casi. Gli scenari di distribuzione di licenze basati su Microsoft devono sempre usare i valori predefiniti. Se è necessario impostare i valori di "provider" e "content_id", un provider deve corrispondere alle credenziali di Widevine.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage":<renew with usage>
       }
    }

## <a name="json-message"></a>Messaggio JSON

| Nome | valore | Descrizione |
| --- | --- | --- |
| payload |Stringa con codifica Base64 |Richiesta di licenza inviata da un client. |
| content_id |Stringa con codifica Base64 |Identificatore usato per derivare l'ID chiave e la chiave simmetrica per ogni content_key_specs.track_type. |
| provider |string |Consente di cercare criteri e chiavi simmetriche. Se viene usata la distribuzione delle chiavi Microsoft per la distribuzione di licenze Widevine, questo parametro viene ignorato. |
| policy_name |string |Nome di un criterio precedentemente registrato. Facoltativa. |
| allowed_track_types |enum |SD_ONLY o SD_HD. Consente di controllare le chiavi simmetriche incluse in una licenza. |
| content_key_specs |Matrice di strutture JSON. Vedere la sezione "Specifiche della chiave simmetrica".  |Controllo più granulare delle chiavi simmetriche da restituire. Per altre informazioni, vedere la sezione "Specifiche della chiave simmetrica". È possibile specificare solo uno dei valori allowed_track_types e content_key_specs. |
| use_policy_overrides_exclusively |Booleano: true o false |Usare gli attributi di criteri specificati in policy_overrides e omettere tutti i criteri memorizzati in precedenza. |
| policy_overrides |Struttura JSON, vedere la sezione "Override dei criteri". |Impostazioni di criteri per questa licenza.  Nel caso in cui per questo asset esistano già criteri predefiniti, vengono usati questi valori specificati. |
| session_init |Struttura JSON, vedere la sezione "Inizializzazione della sessione". |Dati facoltativi vengono passati alla licenza. |
| parse_only |Booleano: true o false |La richiesta di licenza viene analizzata, ma la licenza non viene generata. Nella risposta, tuttavia, vengono restituiti i valori componenti la richiesta di licenza. |

## <a name="content-key-specs"></a>Specifiche della chiave simmetrica
Se esiste già un criterio preesistente, non è necessario specificare alcun valore nella specifica della chiave simmetrica. Il criterio preesistente associato a questo contenuto viene usato per determinare la protezione dell'output, ad esempio High-bandwidth Digital protezione del contenuto (HDCP) e Copy General Management System (CGMS). Se i criteri pre-esistenti non sono registrati con il server di licenze Widevine, il provider di contenuti può inserire i valori nella richiesta di licenza.   

Ogni valore content_key_specs deve essere specificato per tutte le singole tracce, indipendentemente dall'opzione use_policy_overrides_exclusively. 

| Nome | valore | Descrizione |
| --- | --- | --- |
| content_key_specs track_type |string |Nome di un tipo di traccia. Se nella richiesta di licenza è specificato un valore content_key_specs, assicurarsi di specificare esplicitamente tutti i tipi di traccia. In caso contrario, non possono essere riprodotti gli ultimi 10 secondi. |
| content_key_specs  <br/> security_level |uint32 |Definisce i requisiti di affidabilità client per la riproduzione. <br/> - È richiesta una soluzione di crittografia white box basata su software. <br/> - Sono necessari una soluzione di crittografia software e un decodificatore offuscato. <br/> - Il materiale della chiave e le operazioni di crittografia devono essere eseguiti all'interno di un ambiente di esecuzione affidabile basato su hardware. <br/> - Le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.  <br/> - Le operazioni di crittografia, decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware. |
| content_key_specs <br/> required_output_protection.hdc |Stringa, uno di HDCP_NONE, HDCP_V1, HDCP_V2 |Indica se è necessario il protocollo HDCP. |
| content_key_specs <br/>Key |Base64<br/>codifica Base64 |Chiave simmetrica da usare per questa traccia. Se specificato, il track_type o key_id è obbligatorio. Il provider di contenuti può usare questa opzione per inserire la chiave simmetrica della traccia ed evitare quindi che sia il server di licenze Widevine a generare o cercare una chiave. |
| content_key_specs.key_id |Stringa binaria con codifica Base64, 16 byte |Identificatore univoco della chiave. |

## <a name="policy-overrides"></a>Override dei criteri
| Nome | valore | Descrizione |
| --- | --- | --- |
| policy_overrides&#46;can_play |Booleano: true o false |Indica che è consentita la riproduzione del contenuto. L'impostazione predefinita è false. |
| policy_overrides&#46;can_persist |Booleano: true o false |Indica che la licenza può essere mantenuta in una memoria non volatile per l'uso offline. L'impostazione predefinita è false. |
| policy_overrides&#46;can_renew |Booleano: true o false |Indica che è consentito il rinnovo della licenza. Se true, è possibile estendere la durata della licenza mediante heartbeat. L'impostazione predefinita è false. |
| policy_overrides&#46;license_duration_seconds |int64 |Indica la finestra temporale di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Indica la finestra temporale in cui è consentita la riproduzione. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |La finestra temporale di visualizzazione a partire dall'inizio della riproduzione nell'ambito del periodo di validità della licenza. Il valore 0 indica che non esiste alcun limite di durata. Il valore predefinito è 0. |
| policy_overrides&#46;renewal_server_url |string |Tutte le richieste di heartbeat (rinnovo) per la licenza sono inoltrate all'URL specificato. Questo campo viene usato solo se can_renew è true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Numero di secondi prima che venga eseguito il primo tentativo di rinnovo, a partire dal valore license_start_time. Questo campo viene usato solo se can_renew è true. Il valore predefinito è 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Specifica l'intervallo di tempo, espresso in secondi, tra richieste di rinnovo della licenza consecutive, in caso di errore. Questo campo viene usato solo se can_renew è true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |La finestra temporale nella quale può continuare la riproduzione mentre vengono eseguiti nuovi tentativi di rinnovo, ancora con esito negativo a causa di problemi di back-end con il server licenze. Il valore 0 indica che non esiste alcun limite di durata. Questo campo viene usato solo se can_renew è true. |
| policy_overrides&#46;renew_with_usage |Booleano: true o false |Indica che la licenza viene inviata per il rinnovo nel momento in cui si inizia a usarla. Questo campo viene usato solo se can_renew è true. |

## <a name="session-initialization"></a>Inizializzazione della sessione
| Nome | valore | Descrizione |
| --- | --- | --- |
| provider_session_token |Stringa con codifica Base64 |Questo token di sessione viene restituito alla licenza e persiste nei rinnovi successivi, ma non in altre sessioni. |
| provider_client_token |Stringa con codifica Base64 |Token client da restituire nella risposta di licenza. Se la richiesta di licenza contiene un token client, questo valore viene ignorato. Il token client persiste anche in altre sessioni della licenza. |
| override_provider_client_token |Booleano: true o false |Se false e la richiesta di licenza contiene un token client, usare il token della richiesta anche se nella struttura è stato specificato un token client. Se true, usare sempre il token specificato nella struttura. |

## <a name="configure-your-widevine-license-with-net"></a>Configurare licenze Widevine con .NET 

Servizi multimediali fornisce una classe che consente di configurare una licenza Widevine. Per creare la licenza, trasmettere JSON a [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Per configurare il modello è possibile:

### <a name="directly-construct-a-json-string"></a>Costruire direttamente una stringa JSON

Questo metodo può essere soggetto a errori. Si consiglia di usare altri metodi, descritti in [Definire le classi necessarie e serializzare in JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Definire le classi necessarie e serializzare in JSON

#### <a name="define-classes"></a>Definire le classi

Nell'esempio seguente viene illustrato un esempio di definizioni di classi che eseguono il mapping allo schema JSON di Widevine. È possibile creare istanze di classi prima della loro serializzazione in stringa JSON.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Configurare la licenza

Usare le classi definite nella sezione precedente per creare un JSON che viene usato per configurare [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

Scoprire come [proteggere con DRM](protect-with-drm.md)
