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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76705633"
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
| provider |stringa |Consente di cercare criteri e chiavi simmetriche. Se viene usata la distribuzione delle chiavi Microsoft per la distribuzione di licenze Widevine, questo parametro viene ignorato. |
| policy_name |stringa |Nome di un criterio precedentemente registrato. Facoltativo. |
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
| content_key_specs track_type |stringa |Nome di un tipo di traccia. Se nella richiesta di licenza è specificato un valore content_key_specs, assicurarsi di specificare esplicitamente tutti i tipi di traccia. In caso contrario, non possono essere riprodotti gli ultimi 10 secondi. |
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
| policy_overrides&#46;renewal_server_url |stringa |Tutte le richieste di heartbeat (rinnovo) per la licenza sono inoltrate all'URL specificato. Questo campo viene usato solo se can_renew è true. |
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
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
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
