---
title: Proteggere l'accesso ai dati
description: Proteggere l'accesso a input, output, trigger basati su richiesta, cronologia di esecuzione, attività di gestione e accesso ad altre risorse in App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/08/2020
ms.openlocfilehash: cdaa054559be9db52eeef6f3aaa0f86ccf84206f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922943"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteggere l'accesso e i dati in App per la logica di Azure

App per la logica di Azure si basa su [archiviazione di Azure](../storage/index.yml) per archiviare e [crittografare automaticamente i dati](../security/fundamentals/encryption-atrest.md)inattivi. Questa crittografia protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, archiviazione di Azure usa chiavi gestite da Microsoft per crittografare i dati. Per altre informazioni, vedere [Crittografia di Archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).

Per controllare ulteriormente l'accesso e proteggere i dati sensibili in app per la logica di Azure, è possibile configurare una sicurezza aggiuntiva in queste aree:

* [Accesso per le chiamate in ingresso a trigger basati su richiesta](#secure-inbound-requests)
* [Accesso alle operazioni di app per la logica](#secure-operations)
* [Accesso agli input e agli output della cronologia di esecuzione](#secure-run-history)
* [Accesso agli input dei parametri](#secure-action-parameters)
* [Accesso per le chiamate in uscita ad altri servizi e sistemi](#secure-outbound-requests)
* [Blocca la creazione di connessioni per connettori specifici](#block-connections)
* [Linee guida sull'isolamento per le app per la logica](#isolation-logic-apps)
* [Baseline della sicurezza di Azure per app per la logica di Azure](../logic-apps/security-baseline.md)

Per ulteriori informazioni sulla sicurezza in Azure, vedere gli argomenti seguenti:

* [Panoramica della crittografia di Azure](../security/fundamentals/encryption-overview.md)
* [Crittografia dei dati inattivi di Azure](../security/fundamentals/encryption-atrest.md)
* [Azure Security Benchmark](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Accesso per le chiamate in ingresso a trigger basati su richiesta

Le chiamate in ingresso che un'app per la logica riceve tramite un trigger basato su richiesta, come il trigger di [richiesta](../connectors/connectors-native-reqres.md) o il trigger [http webhook](../connectors/connectors-native-webhook.md) , supportano la crittografia e sono protette con [Transport Layer Security (TLS) 1,2 almeno](https://en.wikipedia.org/wiki/Transport_Layer_Security), noto in precedenza come Secure Sockets Layer (SSL). App per la logica applica questa versione quando riceve una chiamata in ingresso al trigger di richiesta o un callback al trigger o all'azione del webhook HTTP. Se si verificano errori di handshake TLS, assicurarsi di usare TLS 1.2. Per altre informazioni, vedere [Risoluzione del problema relativo a TLS 1.0](/security/solving-tls1-problem).

Le chiamate in ingresso supportano questi pacchetti di crittografia:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Ecco altri modi in cui è possibile limitare l'accesso ai trigger che ricevono chiamate in ingresso all'app per la logica in modo che solo i client autorizzati possano chiamare l'app per la logica:

* [Generare firme di accesso condiviso (SAS)](#sas)
* [Abilitare Azure Active Directory Open Authentication (Azure AD OAuth)](#enable-oauth)
* [Esporre l'app per la logica con gestione API di Azure](#azure-api-management)
* [Limitare gli indirizzi IP in ingresso](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generare firme di accesso condiviso (SAS)

Ogni endpoint di richiesta a un'app per la logica ha una [firma di accesso condiviso (Shared Access Signature - SAS)](/rest/api/storageservices/constructing-a-service-sas) come parte dell'URL, che segue il formato seguente:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Ogni URL contiene i parametri di query `sp`, `sv`e `sig` come descritto nella tabella seguente:

| Query parameter (Parametro di query) | Descrizione |
|-----------------|-------------|
| `sp` | Specifica le autorizzazioni per i metodi HTTP consentiti. |
| `sv` | Specifica la versione usata per generare la firma. |
| `sig` | Specifica la firma da usare per l'autenticazione dell'accesso al trigger. La firma viene generata attraverso l'algoritmo SHA256 con una chiave di accesso privata in tutti i percorsi e le proprietà dell'URL. La chiave non viene mai esposta né pubblicata, inoltre continua a essere crittografata e archiviata nell'ambito dell'app per la logica. L'app per la logica autorizza solo i trigger che contengono una firma valida creata con la chiave privata. |
|||

Le chiamate in ingresso a un endpoint di richiesta possono usare un solo schema di autorizzazione, ovvero SAS o [Azure Active Directory autenticazione aperta](#enable-oauth). Sebbene l'utilizzo di uno schema non disabilita l'altro schema, l'utilizzo di entrambi gli schemi contemporaneamente causa un errore, in quanto il servizio non conosce lo schema da scegliere.

Per altre informazioni sulla protezione dell'accesso con SAS, vedere le sezioni seguenti in questo argomento:

* [Per rigenerare le chiavi di accesso](#access-keys)
* [Creare URL di callback in scadenza](#expiring-urls)
* [Creare URL con una chiave primaria o secondaria](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

È possibile generare in qualsiasi momento una nuova chiave di accesso protetta tramite il portale di Azure o API REST. Tutti gli URL generati in precedenza usando la chiave precedente verranno invalidati e non avranno più l'autorizzazione per attivare l'app per la logica. Per accedere agli URL recuperati dopo la rigenerazione, è necessaria la nuova chiave di accesso.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica che contiene la chiave che si desidera rigenerare.

1. Nel menu di app per la logica in **Impostazioni**, selezionare **Chiave di accesso**.

1. Scegliere la chiave da rigenerare e completare il processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Creare URL di callback in scadenza

Se si condivide l'URL dell'endpoint per un trigger di richiesta con altre parti è possibile generare gli URL di callback che usano chiavi specifiche e hanno delle date di scadenza. In questo modo è possibile aggiornare le chiavi senza problemi o limitare l'accesso per attivare l'app per la logica sulla base di un determinato lasso di tempo. Per specificare una data di scadenza per un URL usare l'[API REST delle app per la logica](/rest/api/logic/workflowtriggers), ad esempio:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la `NotAfter`proprietà usando una stringa di data JSON. Questa proprietà restituisce un URL di callback valido solo fino a data e ora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Creare URL con una chiave privata primaria o secondaria

Quando si generano URL di callback per i trigger basati su richieste, è possibile specificare la chiave da usare per firmare l'URL. Per generare un URL firmato da una chiave specifica usare l'[API REST delle app per la logica](/rest/api/logic/workflowtriggers), ad esempio:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Nel corpo includere la proprietà `KeyType` come `Primary` o `Secondary`. Questa proprietà restituisce un URL firmato dalla chiave di sicurezza specificata.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Abilitare Azure Active Directory Open Authentication (Azure AD OAuth)

Per le chiamate in ingresso a un endpoint creato da un trigger basato su richiesta, è possibile abilitare [Azure Active Directory Open Authentication (Azure ad OAuth)](../active-directory/develop/index.yml) definendo o aggiungendo un criterio di autorizzazione per l'app per la logica. In questo modo, le chiamate in ingresso usano i [token di accesso](../active-directory/develop/access-tokens.md) OAuth per l'autorizzazione.

Quando l'app per la logica riceve una richiesta in ingresso che include un token di accesso OAuth, il servizio app per la logica di Azure Confronta le attestazioni del token con le attestazioni specificate da ciascun criterio di autorizzazione. Se esiste una corrispondenza tra le attestazioni del token e tutte le attestazioni in almeno un criterio, l'autorizzazione ha esito positivo per la richiesta in ingresso. Il token può avere più attestazioni rispetto al numero specificato dal criterio di autorizzazione.

Prima di abilitare Azure AD OAuth, esaminare le considerazioni seguenti:

* Una chiamata in ingresso all'endpoint della richiesta può usare un solo schema di autorizzazione, Azure AD OAuth o la [firma di accesso condiviso (SAS)](#sas). Sebbene l'uso di uno schema non disabilita l'altro schema, l'uso di entrambi gli schemi contemporaneamente causa un errore perché il servizio app per la logica non conosce lo schema da scegliere.

* Per Azure AD token di accesso OAuth sono supportati solo gli schemi di autorizzazione di [tipo Bearer](../active-directory/develop/active-directory-v2-protocols.md#tokens) , il che significa che l' `Authorization` intestazione per il token di accesso deve specificare il `Bearer` tipo.

* L'app per la logica è limitata a un numero massimo di criteri di autorizzazione. Ogni criterio di autorizzazione ha anche un numero massimo di [attestazioni](../active-directory/develop/developer-glossary.md#claim). Per altre informazioni, vedere [Limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Un criterio di autorizzazione deve includere almeno l'attestazione dell' **autorità emittente** , che ha un valore che inizia con `https://sts.windows.net/` o `https://login.microsoftonline.com/` (OAuth v2) come ID autorità di certificazione Azure ad.

  Si supponga, ad esempio, che l'app per la logica disponga di un criterio di autorizzazione che richiede due tipi di attestazione, **audience** e **emittente**. Questa [sezione di payload](../active-directory/develop/access-tokens.md#payload-claims) di esempio per un token di accesso decodificato include entrambi i tipi di attestazione in cui `aud` è il valore **audience** e `iss` è il valore dell' **autorità emittente** :

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>Definire i criteri di autorizzazione in portale di Azure

Per abilitare Azure AD OAuth per l'app per la logica nel portale di Azure, seguire questa procedura per aggiungere uno o più criteri di autorizzazione all'app per la logica:

1. Nel [portale di Azure](https://portal.microsoft.com) individuare e aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Autorizzazione**. Dopo l'apertura del riquadro Autorizzazione, selezionare **Aggiungi criteri**.

   ![Selezionare "Autorizzazione" > "Aggiungi criteri"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Fornire informazioni sui criteri di autorizzazione specificando i [tipi di attestazione](../active-directory/develop/developer-glossary.md#claim) e i valori previsti dall'app per la logica nel token di accesso presentato da ogni chiamata in ingresso al trigger di richiesta:

   ![Fornire le informazioni per i criteri di autorizzazione](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome del criterio** | Sì | Il nome da usare per il criterio di autorizzazione |
   | **Richieste** | Sì | I tipi di attestazione e i valori accettati dall'app per la logica dalle chiamate in ingresso. Il valore dell'attestazione è limitato a un [numero massimo di caratteri](logic-apps-limits-and-config.md#authentication-limits). Ecco i tipi di attestazione disponibili: <p><p>- **Autorità di certificazione** <br>- **Destinatari** <br>- **Oggetto** <br>- **ID JWT** (ID token Web JSON) <p><p>Come minimo, l'elenco di **attestazioni** deve includere l'attestazione dell' **autorità emittente** , che ha un valore che inizia con `https://sts.windows.net/` o `https://login.microsoftonline.com/` come ID autorità di certificazione Azure ad. Per altre informazioni su questi tipi di attestazione, vedere [Attestazioni nei token di sicurezza Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). È anche possibile specificare il proprio tipo di attestazione e il proprio valore. |
   |||

1. Per aggiungere un'altra attestazione, selezionare una delle opzioni seguenti:

   * Per aggiungere un altro tipo di attestazione, selezionare **Aggiungi attestazione standard**, selezionare il tipo di attestazione e specificare il valore dell'attestazione.

   * Per aggiungere un'attestazione personalizzata, selezionare **Aggiungi attestazione personalizzata**, quindi specificare il valore dell'attestazione personalizzata.

1. Per aggiungere un altro criterio di autorizzazione, selezionare **Aggiungi criteri**. Ripetere i passaggi precedenti per configurare i criteri.

1. Al termine, selezionare **Salva**.

1. Per includere l' `Authorization` intestazione dal token di accesso negli output dei trigger basati su richiesta, vedere [includere l'intestazione ' Authorization ' negli output del trigger di richiesta](#include-auth-header).

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>Definire i criteri di autorizzazione nel modello di Azure Resource Manager

Per abilitare Azure AD OAuth nel modello ARM per la distribuzione dell'app per la logica, seguire questa procedura e la sintassi seguente:

1. Nella `properties` sezione relativa alla [definizione di risorsa dell'app](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)per la logica aggiungere un `accessControl` oggetto, se non ne esiste alcuno, che contiene un `triggers` oggetto.

   Per altre informazioni sull' `accessControl` oggetto, vedere [limitare gli intervalli di indirizzi IP in ingresso nel modello di Azure Resource Manager](#restrict-inbound-ip-template) e [riferimenti ai modelli di flussi di lavoro Microsoft. logici](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. Nell' `triggers` oggetto aggiungere un oggetto contenente `openAuthenticationPolicies` l' `policies` oggetto in cui si definiscono uno o più criteri di autorizzazione.

1. Fornire un nome per i criteri di autorizzazione, impostare il tipo di criteri su `AAD` e includere una `claims` matrice in cui si specificano uno o più tipi di attestazione.

   Come minimo, la `claims` matrice deve includere il tipo di attestazione dell'autorità di certificazione in cui si imposta la proprietà dell'attestazione `name` su `iss` e impostare `value` su per iniziare con `https://sts.windows.net/` o `https://login.microsoftonline.com/` come ID emittente Azure ad. Per altre informazioni su questi tipi di attestazione, vedere [Attestazioni nei token di sicurezza Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). È anche possibile specificare il proprio tipo di attestazione e il proprio valore.

1. Per includere l' `Authorization` intestazione dal token di accesso negli output dei trigger basati su richiesta, vedere [includere l'intestazione ' Authorization ' negli output del trigger di richiesta](#include-auth-header).

Ecco la sintassi da seguire:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Includi intestazione ' Authorization ' negli output del trigger di richiesta

Per le app per la logica che [abilitano l'autenticazione Azure Active Directory Open (Azure ad OAuth)](#enable-oauth) per autorizzare le chiamate in ingresso ad accedere ai trigger basati su richiesta, è possibile abilitare gli output del trigger di richiesta o del webhook http per includere l' `Authorization` intestazione dal token di accesso OAuth. Nella definizione JSON sottostante del trigger aggiungere e impostare la `operationOptions` proprietà su `IncludeAuthorizationHeadersInOutputs` . Di seguito è riportato un esempio per il trigger di richiesta:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Riferimento allo schema per i tipi di trigger e di azione-trigger di richiesta](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Riferimento allo schema per i tipi di trigger e di azione-trigger HTTP webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Riferimento allo schema per i tipi di trigger e azione-opzioni operazione](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Esporre l'app per la logica con gestione API di Azure

Per aggiungere altri [protocolli di autenticazione](../active-directory/develop/authentication-vs-authorization.md) all'app per la logica, provare a usare il servizio [gestione API di Azure](../api-management/api-management-key-concepts.md) . Questo servizio consente di esporre l'app per la logica come API e offre monitoraggio avanzato, sicurezza, criteri e documentazione per ogni endpoint. Gestione API può esporre un endpoint pubblico o privato per l'app per la logica. Per autorizzare l'accesso a questo endpoint, è possibile usare Azure AD OAuth, un [certificato client](#client-certificate-authentication)o altri standard di sicurezza per autorizzare l'accesso a tale endpoint. Quando Gestione API riceve una richiesta, il servizio invia la richiesta all'app per la logica, eseguendo anche eventuali trasformazioni necessarie e restrizioni. Per consentire solo a gestione API di chiamare l'app per la logica, è possibile [limitare gli indirizzi IP in ingresso dell'app per la logica](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Limitare gli indirizzi IP in ingresso

Oltre alla firma di accesso condiviso (SAS) è possibile che l'utente voglia limitare client specifici che possono chiamare l'app per la logica. Ad esempio, se si gestisce l'endpoint di richiesta usando [gestione API di Azure](../api-management/api-management-key-concepts.md), è possibile limitare l'app per la logica ad accettare le richieste solo dall'indirizzo IP per l' [istanza del servizio gestione API creata](../api-management/get-started-create-service-instance.md).

> [!NOTE]
> Indipendentemente dagli indirizzi IP specificati, è comunque possibile eseguire un'app per la logica con un trigger basato su richiesta tramite l' [API REST di app per la logica: trigger del flusso di lavoro-eseguire](/rest/api/logic/workflowtriggers/run) la richiesta o usando gestione API. Tuttavia, in questo caso potrebbe essere richiesta [l'autenticazione](../active-directory/develop/authentication-vs-authorization.md) all'API REST di Azure. Tutti gli eventi vengono visualizzati nel log di controllo di Azure. Assicurarsi di impostare i criteri di controllo di accesso di conseguenza.

<a name="restrict-inbound-ip-portal"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Limitare gli intervalli di indirizzi IP in ingresso in portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. Nella sezione **configurazione di controllo di accesso** , in **indirizzi IP in ingresso consentiti**, scegliere il percorso per lo scenario:

   * Per rendere l'app per la logica richiamabile solo come app per la logica annidata usando l'azione predefinita app per la logica di [Azure](../logic-apps/logic-apps-http-endpoint.md), selezionare **solo altre app** per la logica, che funziona *solo* quando si usa l'azione app per la logica di **Azure** per chiamare l'app per la logica nidificata.
   
     Questa opzione scrive una matrice vuota nella risorsa dell'app per la logica e richiede che solo le chiamate da app per la logica padre che usano l'azione predefinita app per la logica di **Azure** possano attivare l'app per la logica nidificata.

   * Per fare in modo che l'app per la logica richiamabile solo come app annidata usando l'azione HTTP, selezionare **intervalli IP specifici**, *non* **solo altre app** per la logica. Quando viene visualizzata la casella **intervalli IP per trigger** , immettere gli [indirizzi IP in uscita](../logic-apps/logic-apps-limits-and-config.md#outbound)dell'app per la logica padre. Un intervallo IP valido usa i formati seguenti: x. x.x. x */x* o *x. x.* x. x-x. x. x. x.
   
     > [!NOTE]
     > Se si usa l'opzione **solo altre app** per la logica e l'azione http per chiamare l'app per la logica nidificata, la chiamata viene bloccata e viene ricevuto un errore "401 non autorizzato".
        
   * Per gli scenari in cui si desidera limitare le chiamate in ingresso da altri IP, quando viene visualizzata la casella **intervalli IP per trigger** , specificare gli intervalli di indirizzi IP accettati dal trigger. Un intervallo IP valido usa i formati seguenti: x. x.x. x */x* o *x. x.* x. x-x. x. x. x.

1. Facoltativamente, in **limitare le chiamate per ottenere i messaggi di input e di output dalla cronologia di esecuzione agli indirizzi IP forniti**, è possibile specificare gli intervalli di indirizzi IP per le chiamate in ingresso che possono accedere ai messaggi di input e di output nella cronologia di esecuzione.

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli di indirizzi IP in ingresso nel modello di Azure Resource Manager

Se si [automatizza la distribuzione per le app per la logica usando modelli di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile specificare gli intervalli di indirizzi IP in ingresso consentiti nella definizione di risorsa dell'app per la logica usando la `accessControl` sezione. In questa sezione usare le `triggers` sezioni, `actions` e facoltative in `contents` base alle esigenze, includendo la `allowedCallerIpAddresses` sezione con la `addressRange` proprietà e impostando il valore della proprietà sull'intervallo di indirizzi IP consentiti nel formato x. x.x. x */x* o *x.* x. x. x-x. x.x. x.

* Se l'app per la logica nidificata usa l' **unica opzione altre** app per la logica, che consente le chiamate in ingresso solo da altre app per la logica che usano l'azione app per la logica di Azure, impostare la `addressRange` proprietà su una matrice vuota (**[]**).

* Se l'app per la logica nidificata usa l'opzione **intervalli IP specifici** per altre chiamate in ingresso, ad esempio altre app per la logica che usano l'azione http, impostare la `addressRange` proprietà sull'intervallo di indirizzi IP consentiti.

Questo esempio illustra una definizione di risorsa per un'app per la logica annidata che consente chiamate in ingresso solo da app per la logica che usano l'azione predefinita app per la logica di Azure:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": []
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Questo esempio illustra una definizione di risorsa per un'app per la logica annidata che consente chiamate in ingresso da app per la logica che usano l'azione HTTP:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Accesso alle operazioni di app per la logica

È possibile consentire solo a utenti o gruppi specifici di eseguire attività specifiche, ad esempio la gestione, la modifica e la visualizzazione di app per la logica. Per controllare le autorizzazioni, usare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md) in modo da poter assegnare ruoli personalizzati o predefiniti ai membri nella sottoscrizione di Azure:

* [Collaboratore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-contributor): consente di gestire le app per la logica, ma non di modificarne l'accesso.

* [Operatore per app per la logica](../role-based-access-control/built-in-roles.md#logic-app-operator): consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

Per impedire la modifica o l'eliminazione delle app per la logica da parte di altri utenti è inoltre possibile usare i [blocchi per le risorse di Azure](../azure-resource-manager/management/lock-resources.md). Questa funzionalità impedisce ad altri utenti di modificare o eliminare le risorse di produzione.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Accesso ai dati della cronologia di esecuzione

Durante l'esecuzione di un'app per la logica, tutti i dati vengono [crittografati durante il transito](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) tramite Transport Layer Security (TLS) e sono [inattivi](../security/fundamentals/encryption-atrest.md). Al termine dell'esecuzione dell'app per la logica, è possibile visualizzare la cronologia dell'esecuzione, inclusi i passaggi eseguiti con lo stato, la durata, gli input e gli output per ogni azione. Questo approfondimento fornisce informazioni dettagliate sulle modalità di esecuzione dell'app per la logica e su dove è possibile iniziare la risoluzione dei problemi che si verificano.

Quando si visualizza la cronologia di esecuzione di un'app per la logica, App per la logica autentica l'accesso e fornisce i collegamenti agli input e agli output delle richieste e delle risposte per ogni esecuzione. Tuttavia, per le azioni che gestiscono password, segreti, chiavi o altre informazioni riservate, è opportuno impedire ad altri utenti di visualizzare e accedere a tali dati. Ad esempio, se l'app per la logica ottiene un segreto da [Azure Key Vault](../key-vault/general/overview.md) da usare per l'autenticazione di un'azione HTTP, occorre nascondere tale segreto in modo che non venga visualizzato.

Per controllare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, sono disponibili le opzioni seguenti:

* [Limitare l'accesso in base all'indirizzo IP](#restrict-ip).

  Questa opzione consente di proteggere l'accesso alla cronologia di esecuzione in base alle richieste provenienti da un intervallo di indirizzi IP specifico.

* [Proteggere i dati nella cronologia di esecuzione usando l'offuscamento](#obfuscate).

  In molti trigger e azioni è possibile proteggere gli input, gli output o entrambi nella cronologia di esecuzione di un'app per la logica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Limitare l'accesso in base all'intervallo di indirizzi IP

È possibile limitare l'accesso agli input e agli output nella cronologia di esecuzione dell'app per la logica, in modo che solo le richieste provenienti da intervalli di indirizzi IP specifici possano visualizzare tali dati. Ad esempio, per impedire a tutti gli utenti di accedere a input e output, specificare un intervallo di indirizzi IP come `0.0.0.0-0.0.0.0`. Questa restrizione può essere rimossa solo da una persona con autorizzazioni di amministratore che fornisce l'accesso "just-in-time" ai dati dell'app per la logica. È possibile specificare gli intervalli di indirizzi IP da limitare usando il portale di Azure o in un modello di Azure Resource Manager usato per la distribuzione dell'app per la logica.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Limitare gli intervalli di indirizzi IP in portale di Azure

1. Nel portale di Azure aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**.

1. In **Configurazione del controllo di accesso** > **Indirizzi IP in ingresso consentiti** selezionare **Intervalli IP specifici**.

1. In **Intervalli IP per i contenuti**, specificare gli intervalli di indirizzi IP che possono accedere al contenuto da input e output.

   Un intervallo IP valido usa questi formati: *x.x.x.x/x* o *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Limitare gli intervalli di indirizzi IP nel modello di Azure Resource Manager

Se si [automatizza la distribuzione per le app per la logica usando i modelli di Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile specificare gli intervalli IP usando la sezione `accessControl` con la sezione `contents` nella definizione di risorsa dell'app per la logica, ad esempio:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Proteggere i dati nella cronologia di esecuzione usando l'offuscamento

Molti trigger e azioni hanno impostazioni per la protezione degli input, degli output o di entrambi dalla cronologia di esecuzione di un'app per la logica. Prima di usare queste impostazioni per facilitare la protezione dei dati, [tenere conto di queste considerazioni](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteggere gli input e gli output nella finestra di progettazione

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

   ![App per la logica aperta nella finestra di progettazione di app per la logica](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Nel trigger o nell'azione in cui si desidera proteggere i dati sensibili, selezionare il pulsante con i puntini di sospensione ( **...** ), quindi selezionare **Impostazioni** .

   ![Aprire impostazioni trigger o azione](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Attivare **Input protetti**, **Output protetti** o entrambi. Al termine, fare clic su **Fine**.

   ![Attivare "Input protetti" o "Output protetti"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   L'azione o il trigger ora mostra un'icona a forma di lucchetto nella barra del titolo.

   ![La barra del titolo azione o trigger mostra l'icona a forma di lucchetto](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Anche i token che rappresentano output protetti dalle azioni precedenti mostrano le icone a forma di lucchetto. Ad esempio, quando si seleziona un output di questo tipo dall'elenco di contenuto dinamico da usare in un'azione, il token mostra un'icona a forma di lucchetto.

   ![Selezionare il token per l'output protetto](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare la cronologia per tale esecuzione.

   1. Nel riquadro **Panoramica** dell'app per la logica selezionare l'esecuzione che si desidera visualizzare.

   1. Nel riquadro **Esecuzione dell'app per la logica**, espandere le azioni che si desiderano esaminare.

      Se si è scelto di nascondere gli input e gli output, questi valori vengono ora nascosti.

      ![Input e agli output nascosti nella cronologia di esecuzione](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteggere gli input e gli output nella visualizzazione codice

Nel trigger sottostante o nella definizione di azione aggiungere o aggiornare la matrice `runtimeConfiguration.secureData.properties` con uno o entrambi i valori seguenti:

* `"inputs"`: protegge gli input nella cronologia di esecuzione.
* `"outputs"`: protegge gli output nella cronologia di esecuzione.

Di seguito sono riportate alcune [considerazioni da esaminare](#obfuscation-considerations) quando si usano queste impostazioni per proteggere questi dati.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Considerazioni sulla protezione di input e output

* Quando si oscurano gli input o gli output in un trigger o un'azione, le app per la logica non inviano i dati protetti ad Azure Log Analytics. Non è inoltre possibile aggiungere [proprietà rilevate](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a tale trigger o azione per il monitoraggio.

* [L'API app per la logica per la gestione della cronologia del flusso di lavoro](/rest/api/logic/) non restituisce output protetti.

* Per proteggere gli output da un'azione che nasconde gli input o nasconde in modo esplicito gli output, attivare manualmente **gli output protetti** in tale azione.

* Assicurarsi di attivare gli **input protetti** o gli **output protetti** nelle azioni downstream in cui si prevede che la cronologia di esecuzione nasconda i dati.

  **Impostazione degli output protetti**

  Quando si attivano manualmente gli **output protetti** in un trigger o un'azione, app per la logica nasconde questi output nella cronologia di esecuzione. Se un'azione downstream usa in modo esplicito questi output protetti come input, app per la logica nasconde gli input dell'azione nella cronologia di esecuzione, ma *non abilita* l'impostazione degli **input protetti** dell'azione.

  ![Output protetti come input e effetto downstream sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Le azioni Compose, Parse JSON e Response hanno solo l'impostazione degli **input protetti**. Se attivata, l'impostazione nasconde anche gli output di tali azioni. Se queste azioni usano in modo esplicito gli output protetti upstream come input, le app per la logica nascondono gli input e gli output di tali azioni, ma *non abilitano* l'impostazione degli **input protetti** delle azioni. Se un'azione downstream usa in modo esplicito gli output nascosti delle azioni Compose, Parse JSON o Response come input, le app per la logica *non nascondono gli input o gli output dell'azione downstream*.

  ![Output protetti come input con effetto downstream su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Impostazione degli input protetti**

  Quando si attivano manualmente gli **input protetti** in un trigger o un'azione, app per la logica nasconde questi input nella cronologia di esecuzione. Se un'azione downstream usa in modo esplicito gli output visibili da tale trigger o azione come input, l'app per la logica nasconde gli input dell'azione downstream nella cronologia di esecuzione, ma *non abilita* gli **input protetti** in questa azione e non nasconde gli output di questa azione.

  ![Input protetti e effetto downstream sulla maggior parte delle azioni](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se le azioni Compose, Parse JSON e Response usano in modo esplicito gli output visibili del trigger o dell'azione con gli input protetti, app per la logica nasconde gli input e gli output di queste azioni, ma *non abilita* l'impostazione degli **input protetti** dell'azione. Se un'azione downstream usa in modo esplicito gli output nascosti delle azioni Compose, Parse JSON o Response come input, le app per la logica *non nascondono gli input o gli output dell'azione downstream*.

  ![Input protetti e effetto downstream su azioni specifiche](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Accesso agli input dei parametri

Se si esegue la distribuzione in ambienti diversi, provare a parametrizzare i valori nella definizione del flusso di lavoro che variano in base a tali ambienti. In questo modo, è possibile evitare i dati hardcoded usando un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) per distribuire l'app per la logica, proteggere i dati sensibili definendo parametri protetti e passare i dati come input distinti tramite i [parametri del modello](../azure-resource-manager/templates/template-parameters.md) usando un [file di parametri](../azure-resource-manager/templates/parameter-files.md).

Ad esempio, per autenticare azioni HTTP con [Azure Active Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth), è possibile definire e nascondere i parametri che accettano l'ID client e il segreto client usati per l'autenticazione. Per definire questi parametri nell'app per la logica, usare la `parameters` sezione nella definizione del flusso di lavoro dell'app per la logica e il modello di Resource Manager per la distribuzione. Per proteggere i valori di parametro che non si vogliono mostrare quando si modifica l'app per la logica o si visualizza la cronologia di esecuzione, definire i parametri usando il tipo `securestring` o `secureobject`e usare la codifica in base alle esigenze. I parametri con questo tipo non vengono restituiti con la definizione della risorsa e non sono accessibili quando si visualizza la risorsa dopo la distribuzione. Per accedere a questi valori di parametro durante la fase di esecuzione, usare l'espressione `@parameters('<parameter-name>')` all'interno della definizione del flusso di lavoro. Questa espressione viene valutata solo in fase di esecuzione e viene descritta dal [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se si usa un parametro nelle intestazioni o nel corpo della richiesta, tale parametro potrebbe essere visibile quando si visualizza la cronologia di esecuzione dell'app per la logica e la richiesta HTTP in uscita. Assicurarsi anche di configurare di conseguenza i criteri di accesso ai contenuti.
> È possibile anche usare [l'offuscamento](#obfuscate) per nascondere gli input e gli output nella cronologia di esecuzione. Le intestazioni di autorizzazione non sono mai visibili tramite input o output. Se quindi viene usato un segreto, questo non sarà recuperabile.

Per altre informazioni, vedere le sezioni in questo argomento:

* [Proteggere i parametri nelle definizioni del flusso di lavoro](#secure-parameters-workflow)
* [Proteggere i dati nella cronologia di esecuzione usando l'offuscamento](#obfuscate)

Se si [automatizza la distribuzione per le app per la logica usando i modelli di Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile definire i [parametri del modello](../azure-resource-manager/templates/template-parameters.md) protetti, che vengono valutati in fase di distribuzione, usando i tipi `securestring` e `secureobject`. Per definire i parametri del modello, usare la sezione `parameters` di primo livello del modello, che è separata e diversa dalla sezione `parameters` della definizione del flusso di lavoro. Per specificare i valori per i parametri del modello, usare un [file di parametro](../azure-resource-manager/templates/parameter-files.md) separato.

Ad esempio, se si usano i segreti, è possibile definire e usare i parametri di modello protetti che recuperano i segreti da [Azure Key Vault](../key-vault/general/overview.md) in fase di distribuzione. Quindi è possibile fare riferimento all'insieme di credenziali delle chiavi e alla chiave privata nel file dei parametri. Per altre informazioni, vedere gli argomenti seguenti:

* [Passare i valori sensibili alla distribuzione usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Proteggere i parametri nei modelli di Azure Resource Manager](#secure-parameters-deployment-template) più avanti in questo argomento

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteggere i parametri nelle definizioni del flusso di lavoro

Per proteggere le informazioni riservate nella definizione del flusso di lavoro dell'app per la logica, usare i parametri protetti per fare in modo che queste informazioni non siano visibili dopo il salvataggio dell'app per la logica. Si supponga, ad esempio, che un'azione HTTP richieda l'autenticazione di base, che usa un nome utente e una password. Nella definizione del flusso di lavoro, la sezione `parameters` definisce i parametri `basicAuthPasswordParam` e `basicAuthUsernameParam` usando il tipo `securestring`. La definizione dell'azione fa quindi riferimento a questi parametri nella sezione `authentication`.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Proteggere i parametri nei modelli di Azure Resource Manager

Un [modello di Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)per un'app per la logica ha diverse sezioni `parameters`. Per proteggere password, chiavi, segreti e altre informazioni riservate, definire parametri protetti a livello di modello e di definizione del flusso di lavoro usando il tipo `securestring` o `secureobject`. È quindi possibile archiviare questi valori in [Azure Key Vault](../key-vault/general/overview.md) e usare il [file di parametri](../azure-resource-manager/templates/parameter-files.md) per fare riferimento all'insieme di credenziali delle chiavi e al segreto. Il modello recupera quindi tali informazioni in fase di distribuzione. Per altre informazioni vedere [Passare i valori sensibili alla distribuzione usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Ecco altre informazioni su queste sezioni `parameters`:

* Al livello principale del modello, una sezione `parameters` definisce i parametri per i valori usati dal modello in fase di *distribuzione*. Questi valori, ad esempio, possono includere stringhe di connessione per un ambiente di distribuzione specifico. È quindi possibile archiviare questi valori in un [file di parametro](../azure-resource-manager/templates/parameter-files.md) separato, rendendo più semplice la modifica di questi valori.

* All'interno della definizione di risorsa dell'app per la logica, ma all'esterno della definizione del flusso di lavoro, una sezione `parameters` specifica i valori per i parametri della definizione del flusso di lavoro. In questa sezione è possibile assegnare questi valori usando espressioni di modello che fanno riferimento ai parametri del modello. Queste espressioni vengono valutate in fase di distribuzione.

* All'interno della definizione del flusso di lavoro, una sezione `parameters` definisce i parametri che l'app per la logica usa in fase di esecuzione. È quindi possibile fare riferimento a questi parametri nel flusso di lavoro dell'app per la logica usando le espressioni di definizione del flusso di lavoro, che vengono valutate in fase di esecuzione.

Questo modello di esempio ha più definizioni di parametro protette che usano il tipo `securestring`:

| Nome parametro | Descrizione |
|----------------|-------------|
| `TemplatePasswordParam` | Parametro di modello che accetta una password che viene quindi passata al parametro `basicAuthPasswordParam` della definizione del flusso di lavoro |
| `TemplateUsernameParam` | Parametro di modello che accetta un nome utente che viene quindi passato al parametro `basicAuthUserNameParam` della definizione del flusso di lavoro |
| `basicAuthPasswordParam` | Parametro di definizione del flusso di lavoro che accetta la password per l'autenticazione di base in un'azione HTTP |
| `basicAuthUserNameParam` | Parametro di definizione del flusso di lavoro che accetta il nome utente per l'autenticazione di base in un'azione HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Accesso per le chiamate in uscita ad altri servizi e sistemi

In base alla funzionalità dell'endpoint di destinazione, le chiamate in uscita inviate dal [trigger http o dall'azione http](../connectors/connectors-native-http.md)supportano la crittografia e sono protette con [Transport Layer Security (TLS) 1,0, 1,1 o 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), precedentemente noto come Secure Sockets Layer (SSL). App per la logica negozia con l'endpoint di destinazione usando la versione più alta possibile supportata. Se, ad esempio, l'endpoint di destinazione supporta 1,2, il trigger o l'azione HTTP utilizzerà prima 1,2. In caso contrario, il connettore usa la versione successiva più elevata supportata.

Di seguito sono riportate informazioni sui certificati autofirmati TLS/SSL:

* Per le app per la logica nell'ambiente Azure multi-tenant globale, il connettore HTTP non consente i certificati TLS/SSL autofirmati. Se l'app per la logica effettua una chiamata HTTP a un server e presenta un certificato autofirmato TLS/SSL, la chiamata HTTP ha esito negativo e viene `TrustFailure` visualizzato un errore.

* Per le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), il connettore HTTP consente i certificati autofirmati per gli handshake TLS/SSL. Tuttavia, è necessario innanzitutto [abilitare il supporto dei certificati autofirmati](../logic-apps/create-integration-service-environment-rest-api.md#request-body) per un ISE esistente o un nuovo ISE usando l'API REST di app per la logica e installare il certificato pubblico nel `TrustedRoot` percorso.

Ecco altri modi per proteggere gli endpoint che gestiscono le chiamate inviate dall'app per la logica:

* [Aggiungere l'autenticazione alle richieste in uscita](#add-authentication-outbound).

  Quando si usa il trigger o l'azione HTTP per inviare chiamate in uscita, è possibile aggiungere l'autenticazione alla richiesta inviata dall'app per la logica. È ad esempio possibile selezionare i tipi di autenticazione seguenti:

  * [Autenticazione di base](#basic-authentication)

  * [Autenticazione con certificato client](#client-certificate-authentication)

  * [Autenticazione Active Directory OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticazione identità gestita](#managed-identity-authentication)

* Limitazione dell'accesso dagli indirizzi IP delle app per la logica.

  Tutte le chiamate agli endpoint da app per la logica provengono da indirizzi IP specifici designati basati sulle aree delle app per la logica. È possibile aggiungere un filtro che accetti le richieste solo da tali indirizzi IP. Per un elenco di questi indirizzi IP, vedere [Limiti e configurazione delle app per la logica](logic-apps-limits-and-config.md#configuration).

* Migliorare la sicurezza per le connessioni ai sistemi locali.

  Le app per la logica offrono integrazione con tali servizi per fornire una comunicazione locale più sicura e affidabile.

  * Gateway dati locale

    Molti dei connettori gestiti in App per la logica di Azure offrono una connettività sicura a sistemi locali, tra cui File System, SQL, SharePoint, DB2. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni su [come funziona il gateway dati locale](logic-apps-gateway-install.md#gateway-cloud-service).

  * Connettersi attraverso Gestione API di Azure

    [Gestione API di Azure](../api-management/api-management-key-concepts.md) offre opzioni di connessione locali, ad esempio la rete privata virtuale da sito a sito e l'integrazione [ExpressRoute](../expressroute/expressroute-introduction.md) per il proxy protetto e la comunicazione con i sistemi locali. Se si dispone di un'API che fornisce l'accesso al sistema locale ed è stata esposta tale API creando un'istanza del [servizio gestione API](../api-management/get-started-create-service-instance.md), è possibile chiamare tale API nel flusso di lavoro dell'app per la logica selezionando il trigger o l'azione di gestione API incorporata nella finestra di progettazione dell'app per la logica.

    > [!NOTE]
    > Il connettore Mostra solo i servizi di gestione API in cui si hanno le autorizzazioni per la visualizzazione e la connessione, ma non Mostra i servizi di gestione API basati sul consumo.

    1. Nella finestra di progettazione dell'app per la logica immettere `api management` nella casella di ricerca. Scegliere il passaggio a seconda che si stia aggiungendo un trigger o un'azione:<p>

       * Se si aggiunge un trigger, che è sempre il primo passaggio del flusso di lavoro, selezionare **scegliere un trigger di gestione API di Azure**.

       * Se si sta aggiungendo un'azione, selezionare **scegliere un'azione di gestione API di Azure**.

       In questo esempio viene aggiunto un trigger:

       ![Aggiungere un trigger di gestione API di Azure](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Selezionare l'istanza del servizio gestione API creata in precedenza.

       ![Selezionare l'istanza del servizio gestione API](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Selezionare la chiamata API da usare.

       ![Seleziona API esistente](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Aggiunta dell'autenticazione alle chiamate in uscita

Gli endpoint HTTP e HTTPS supportano vari tipi di autenticazione. In alcuni trigger e azioni usati per inviare chiamate in uscita o richieste a questi endpoint, è possibile specificare un tipo di autenticazione. Nella finestra di progettazione dell'app per la logica, i trigger e le azioni che supportano la scelta di un tipo di autenticazione hanno una proprietà di **autenticazione** . Questa proprietà, tuttavia, potrebbe non essere sempre visualizzata per impostazione predefinita. In questi casi, nel trigger o nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare **autenticazione**.

> [!IMPORTANT]
> Per proteggere le informazioni riservate gestite dall'app per la logica, usare i parametri protetti e codificare i dati in modo necessario.
> Per altre informazioni sull'uso e sulla protezione dei parametri, vedere [Accesso agli input dei parametri](#secure-action-parameters).

Questa tabella identifica i tipi di autenticazione disponibili per i trigger e le azioni in cui è possibile selezionare un tipo di autenticazione:

| Tipo di autenticazione | Trigger e azioni supportati |
|---------------------|--------------------------------|
| [Base](#basic-authentication) | Gestione API di Azure, servizio app di Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certificato client](#client-certificate-authentication) | Gestione API di Azure, servizio app di Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Gestione API di Azure, servizi app di Azure, Funzioni di Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Raw](#raw-authentication) | Gestione API di Azure, servizi app di Azure, Funzioni di Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Identità gestita](#managed-identity-authentication) | Gestione API di Azure, servizi app Azure, funzioni di Azure, HTTP, webhook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticazione di base

Se è disponibile l'opzione [Basic](../active-directory-b2c/secure-rest-api.md), specificare i valori delle proprietà seguenti:

| Proprietà (progettazione) | Proprietà (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | Basic | Tipo di autenticazione da usare |
| **Nome utente** | `username` | Sì | <*user-name*>| Il nome utente per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
| **Password** | `password` | Sì | <*password*> | La password per l'autenticazione dell'accesso all'endpoint del servizio di destinazione |
||||||

Quando si usano i [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager per l'automazione della distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica l'autenticazione `type` come `Basic` e usa la [funzione dei parametri()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autenticazione con certificato client

Se è disponibile l'opzione [Certificato client](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md), specificare i valori delle proprietà seguenti:

| Proprietà (progettazione) | Proprietà (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | **Certificato client** <br>o <br>`ClientCertificate` | Tipo di autenticazione da usare. È possibile gestire i certificati con [gestione API di Azure](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Nota**: i connettori personalizzati non supportano l'autenticazione basata su certificati per le chiamate in ingresso e in uscita. |
| **Pfx** | `pfx` | Sì | <*encoded-pfx-file-content*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) <p><p>Per convertire il file PFX in formato con codifica Base64, è possibile usare PowerShell attenendosi alla procedura seguente: <p>1. Salvare il contenuto del certificato in una variabile: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Convertire il contenuto del certificato usando la funzione `ToBase64String()` e salvare il contenuto in un file di testo: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Password** | `password`| No | <*password-for-pfx-file*> | Password per accedere al file PFX. |
|||||

Quando si usano i [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) per l'automazione della distribuzione, è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica l'autenticazione `type` come `ClientCertificate` e usa la [funzione dei parametri()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Per altre informazioni sulla protezione dei servizi tramite l'autenticazione del certificato client, vedere gli argomenti seguenti:

* [Migliorare la sicurezza per le API usando l'autenticazione con certificati client in Gestione API di Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Migliorare la sicurezza dei servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Migliorare la sicurezza per il servizio RESTful usando certificati client](../active-directory-b2c/secure-rest-api.md)
* [Credenziali del certificato per l'autenticazione dell'applicazione](../active-directory/develop/active-directory-certificate-credentials.md)
* [Usare un certificato TLS/SSL nel codice nel Servizio app di Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory Open Authentication

Sui trigger di richiesta, è possibile usare [Azure Active Directory autenticazione Open (Azure ad OAuth)](../active-directory/develop/index.yml)per autenticare le chiamate in ingresso dopo aver [configurato Azure ad i criteri di autorizzazione](#enable-oauth) per l'app per la logica. Per tutti gli altri trigger e azioni che forniscono il tipo di autenticazione **Active Directory OAuth** da selezionare, specificare i valori delle proprietà seguenti:

| Proprietà (progettazione) | Proprietà (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | **Active Directory OAuth** <br>o <br>`ActiveDirectoryOAuth` | Tipo di autenticazione da usare. App per la logica segue attualmente il [protocollo di OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Authority** | `authority` | No | <*URL-for-authority-token-issuer*> | URL dell'autorità che fornisce il token di accesso. Per impostazione predefinita, questo valore è `https://login.windows.net`. |
| **Tenant** | `tenant` | Sì | <*tenant-ID*> | L'ID tenant per il tenant di Azure AD |
| **Destinatari** | `audience` | Sì | <*resource-to-authorize*> | La risorsa che si vuole usare per l'autorizzazione, ad esempio `https://management.core.windows.net/` |
| **ID client** | `clientId` | Sì | <*client-ID*> | L'ID client per l'app richiedente l'autorizzazione |
| **Tipo di credenziali** | `credentialType` | Sì | Certificato <br>o <br>Segreto | Il tipo di credenziale che il client usa per la richiesta di autorizzazione. Questa proprietà e questo valore non vengono visualizzati nella definizione sottostante dell'app per la logica, ma determinano le proprietà che vengono visualizzate per il tipo di credenziale selezionato. |
| **Segreto** | `secret` | Sì, ma solo per il tipo di credenziale "Segreto" | <*client-secret*> | Il segreto client per la richiesta dell'autorizzazione |
| **Pfx** | `pfx` | Sì, ma solo per il tipo di credenziale "Certificato" | <*encoded-pfx-file-content*> | Contenuto con codifica base64 del file di scambio di informazioni personali (PFX, Personal Information Exchange) |
| **Password** | `password` | Sì, ma solo per il tipo di credenziale "Certificato" | <*password-for-pfx-file*> | Password per accedere al file PFX. |
|||||

Quando si usano i [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) per l'automazione della distribuzione, è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica l'autenticazione `type` come `ActiveDirectoryOAuth`, il tipo di credenziali come `Secret` e usa la [funzione dei parametri()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Autenticazione con dati non elaborati

Se l'opzione **Raw** è disponibile, è possibile usare questo tipo di autenticazione quando è necessario usare gli [schemi di autenticazione](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) che non seguono il [protocollo OAuth 2.0](https://oauth.net/2/). Con questo tipo, si crea manualmente il valore dell'intestazione di autorizzazione inviato con la richiesta in uscita e si specifica il valore dell'intestazione nel trigger o nell'azione.

Ecco ad esempio un'intestazione di esempio per una richiesta HTTPS che segue il protocollo [OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Nel trigger o nell'azione che supporta l'autenticazione con dati non elaborati specificare i valori delle proprietà seguenti:

| Proprietà (progettazione) | Proprietà (JSON) | Obbligatoria | valore | Descrizione |
|---------------------|-----------------|----------|-------|-------------|
| **autenticazione** | `type` | Sì | Raw | Tipo di autenticazione da usare |
| **Valore** | `value` | Sì | <*authorization-header-value*> | Valore dell'intestazione di autorizzazione da usare per l'autenticazione |
||||||

Quando si usano i [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) per l'automazione della distribuzione, è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica l'autenticazione `type` come `Raw` e usa la [funzione dei parametri()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Autenticazione identità gestita

Se l'opzione [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) è disponibile in un' [azione o un trigger specifico](#add-authentication-outbound), l'app per la logica può usare l'identità assegnata dal sistema o una *singola* identità assegnata dall'utente creata manualmente per autenticare l'accesso ad altre risorse protette da Azure Active Directory (Azure ad) senza accesso. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti. Sono disponibili altre informazioni sui [servizi di Azure che supportano le identità gestite per l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Prima che l'app per la logica possa usare un'identità gestita, seguire la procedura descritta in [Autenticare l'accesso alle risorse di Azure usando identità gestite in App per la logica di Azure](../logic-apps/create-managed-service-identity.md). Questa procedura abilita l'identità gestita nell'app per la logica e imposta l'accesso dell'identità sulla risorsa di destinazione.

1. Prima che una funzione di Azure possa usare un'identità gestita, [abilitare l'autenticazione per le funzioni di Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. Nel trigger o nell'azione in cui si vuole usare l'identità gestita specificare i valori delle proprietà seguenti:

   | Proprietà (progettazione) | Proprietà (JSON) | Obbligatoria | valore | Descrizione |
   |---------------------|-----------------|----------|-------|-------------|
   | **autenticazione** | `type` | Sì | **Identità gestita** <br>o <br>`ManagedServiceIdentity` | Tipo di autenticazione da usare |
   | **Identità gestita** | `identity` | Sì | * **Identità gestita assegnata dal sistema** <br>o <br>`SystemAssigned` <p><p>* <*user-assigned-identity-name*> | L'identità gestita da usare |
   | **Destinatari** | `audience` | Sì | <*target-resource-ID*> | L'ID risorsa per la risorsa di destinazione a cui si vuole accedere. <p>Ad esempio, `https://storage.azure.com/` rende i [token di accesso](../active-directory/develop/access-tokens.md) per l'autenticazione validi per tutti gli account di archiviazione. Tuttavia, è anche possibile specificare un URL del servizio radice, ad esempio `https://fabrikamstorageaccount.blob.core.windows.net` per un account di archiviazione specifico. <p>**Nota**: la proprietà **Destinatari** potrebbe essere nascosta in alcuni trigger o azioni. Per fare in modo che la proprietà venga visualizzata, nel trigger o nell'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare **Destinatari**. <p><p>**Importante**: assicurarsi che questo ID risorsa di destinazione *corrisponda esattamente* al valore previsto da Azure AD, incluse le eventuali barre finali necessarie. Quindi, l'ID della risorsa `https://storage.azure.com/` per tutti gli account di archiviazione BLOB di Azure richiede una barra finale. Tuttavia, l'ID della risorsa per un account di archiviazione specifico non richiede una barra finale. Per trovare questi ID risorsa, vedere [Servizi di Azure che supportano Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Quando si usano i [parametri protetti](#secure-action-parameters) per gestire e proteggere le informazioni riservate, ad esempio in un [modello di Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) per l'automazione della distribuzione, è possibile usare le espressioni per accedere a questi valori di parametro in fase di esecuzione. Questa definizione di azione HTTP di esempio specifica l'autenticazione `type` come `ManagedServiceIdentity` e usa la [funzione dei parametri()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) per ottenere i valori dei parametri:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blocca la creazione di connessioni

Se l'organizzazione non consente la connessione a risorse specifiche usando i connettori in app per la logica di Azure, è possibile [bloccare la funzionalità per creare le connessioni](../logic-apps/block-connections-connectors.md) per connettori specifici nei flussi di lavoro delle app per la logica usando [criteri di Azure](../governance/policy/overview.md). Per altre informazioni, vedere [bloccare le connessioni create da connettori specifici in app](../logic-apps/block-connections-connectors.md)per la logica di Azure.

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Linee guida sull'isolamento per le app per la logica

È possibile usare le app per la logica di Azure in [Azure per enti pubblici](../azure-government/documentation-government-welcome.md) per supportare tutti i livelli di interesse nelle aree descritte dal materiale sussidiario di [isolamento di Azure Government Impact Level 5](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) e dalla [Guida ai requisiti di sicurezza del reparto US Department of Defense (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Per soddisfare questi requisiti, app per la logica supporta la possibilità di creare ed eseguire flussi di lavoro in un ambiente con risorse dedicate, in modo da ridurre l'impatto sulle prestazioni di altri tenant di Azure nelle app per la logica ed evitare di condividere le risorse di elaborazione con altri tenant.

* Per eseguire il proprio codice o eseguire la trasformazione XML, [creare e chiamare una funzione di Azure](../logic-apps/logic-apps-azure-functions.md), anziché usare la [funzionalità di codice inline](../logic-apps/logic-apps-add-run-inline-code.md) o fornire gli [assembly da usare rispettivamente come Maps](../logic-apps/logic-apps-enterprise-integration-maps.md). Inoltre, configurare l'ambiente host per l'app per le funzioni in modo che soddisfi i requisiti di isolamento.

  Ad esempio, per soddisfare i requisiti del livello di influenza 5, creare l'app per le funzioni con il [piano di servizio app](../azure-functions/functions-scale.md#app-service-plan) usando il piano [tariffario **isolato**](../app-service/overview-hosting-plans.md) insieme a un [ambiente del servizio app (ASE)](../app-service/environment/intro.md) che usa anche il piano tariffario **isolato** . In questo ambiente, le app per le funzioni vengono eseguite in macchine virtuali di Azure dedicate e in reti virtuali di Azure dedicate, che garantiscono l'isolamento della rete oltre l'isolamento di calcolo per le app e le funzionalità di scalabilità orizzontale. Per altre informazioni, vedere [linee guida sull'isolamento di Azure Government Impact Level 5-funzioni di Azure](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Per altre informazioni, vedere gli argomenti seguenti:<p>

  * [Piani di servizio app Azure](../app-service/overview-hosting-plans.md)
  * [Opzioni di rete di Funzioni di Azure](../azure-functions/functions-networking-options.md)
  * [Host dedicati di Azure per le macchine virtuali](../virtual-machines/dedicated-hosts.md)
  * [Isolamento della macchina virtuale in Azure](../virtual-machines/isolation.md)
  * [Distribuire servizi di Azure dedicati in reti virtuali](../virtual-network/virtual-network-for-azure-services.md)

* Per creare app per la logica che vengono eseguite su risorse dedicate e possono accedere alle risorse protette da una rete virtuale di Azure, è possibile creare un [ambiente del servizio di integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Alcune reti virtuali di Azure usano endpoint privati ([collegamento privato di Azure](../private-link/private-link-overview.md)) per fornire l'accesso ai servizi PaaS di Azure, ad esempio archiviazione di azure, Azure Cosmos DB o il database SQL di Azure, i servizi dei partner o i servizi dei clienti ospitati in Azure. Se le app per la logica necessitano dell'accesso alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di ISE.

  * Per un maggiore controllo sulle chiavi di crittografia usate da archiviazione di Azure, è possibile configurare, usare e gestire la propria chiave usando [Azure Key Vault](../key-vault/general/overview.md). Questa funzionalità è nota anche come "Bring Your Own Key" (BYOK) e la chiave è detta "chiave gestita dal cliente". Per altre informazioni, vedere [configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure](../logic-apps/customer-managed-keys-integration-service-environment.md).

Per altre informazioni, vedere gli argomenti seguenti:

* [Isolamento nel cloud pubblico di Azure](../security/fundamentals/isolation-choices.md)
* [Sicurezza per app IaaS altamente riservate in Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Passaggi successivi

* [Baseline della sicurezza di Azure per app per la logica di Azure](../logic-apps/security-baseline.md)
* [Automatizzare la distribuzione per le app per la logica di Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitorare le app per la logica](../logic-apps/monitor-logic-apps-log-analytics.md)
