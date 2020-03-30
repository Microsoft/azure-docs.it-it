---
title: Modelli di prodotto in Gestione API di Azure | Microsoft Docs
description: Informazioni su come personalizzare il contenuto delle pagine prodotto nel portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243926"
---
# <a name="product-templates-in-azure-api-management"></a>Modelli di prodotto in Gestione API di Azure

In Gestione API di Azure è possibile personalizzare le pagine del portale per sviluppatori usando un set di modelli che ne configurano il contenuto. La sintassi [DotLiquid](http://dotliquidmarkup.org/) usata insieme all'editor di propria scelta, ad esempio [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e a un set di [risorse stringa](api-management-template-resources.md#strings) localizzate, [risorse Glifo](api-management-template-resources.md#glyphs) e [controlli di pagina](api-management-page-controls.md) offre una grande flessibilità nella configurazione personalizzata del contenuto delle pagine attraverso questi modelli.  
  
 I modelli in questa sezione consentono di personalizzare il contenuto delle pagine prodotto del portale per sviluppatori.  
  
-   [Elenco dei prodotti](#ProductList)  
  
-   [Prodotto](#Product)  
  
> [!NOTE]
>  La documentazione seguente include alcuni modelli predefiniti di esempio. A causa dei continui miglioramenti che vengono apportati, questi modelli sono però soggetti a modifiche. È possibile visualizzare i modelli predefiniti direttamente nel portale per sviluppatori accedendo ai singoli modelli desiderati. Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a>Elenco prodotti  
 Il modello **Elenco prodotti** consente di personalizzare il corpo della pagina di elenco prodotti nel portale per sviluppatori.  
  
 ![Elenco di prodotti](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Controlli  
 Il modello `Product list` può usare i [controlli di pagina](api-management-page-controls.md) seguenti.  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [controllo di ricerca](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modello di dati  
  
|Proprietà|Type|Descrizione|  
|--------------|----------|-----------------|  
|Paging|Entità [Paging](api-management-template-data-model-reference.md#Paging).|Le informazioni di paging per la raccolta di prodotti.|  
|Filtri|Entità [Filtri](api-management-template-data-model-reference.md#Filtering).|Le informazioni dei filtri per la pagina di elenco proodtti.|  
|Products|Raccolta di entità [Prodotto](api-management-template-data-model-reference.md#Product).|I prodotti visibili all'utente corrente.|  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a>Prodotto  
 Il modello **Prodotto** consente di personalizzare il corpo della pagina prodotto nel portale per sviluppatori.  
  
 ![Pagina del prodotto del portale per sviluppatori](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Controlli  
 Il modello `Product list` può usare i [controlli di pagina](api-management-page-controls.md) seguenti.  
  
-   [pulsante di iscrizione](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Modello di dati  
  
|Proprietà|Type|Descrizione|  
|--------------|----------|-----------------|  
|Prodotto|[Prodotto](api-management-template-data-model-reference.md#Product)|Il prodotto specificato.|  
|IsDeveloperSubscribed|boolean|Indica se l'utente corrente è sottoscritto a questo prodotto.|  
|SubscriptionState|d'acquisto|Stato della sottoscrizione. Gli stati possibili sono elencati di seguito:<br /><br /> -   `0 - suspended`: la sottoscrizione è bloccata e il sottoscrittore non può chiamare le API del prodotto.<br />-   `1 - active`: la sottoscrizione è attiva.<br />-   `2 - expired`: la sottoscrizione ha raggiunto la data di scadenza ed è stata disattivata.<br />-   `3 - submitted`: la richiesta di sottoscrizione è stata eseguita dallo sviluppatore, ma non è ancora stata approvata o rifiutata.<br />-   `4 - rejected`: la richiesta di sottoscrizione è stata rifiutata da un amministratore.<br />-   `5 - cancelled`: la sottoscrizione è stata annullata dallo sviluppatore o dall'amministratore.|  
|Limiti|array|Questa proprietà è deprecata e non deve essere usata.|  
|DelegatedSubscriptionEnabled|boolean|Indica se sia abilitata la [delega](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) per questa sottoscrizione.|  
|DelegatedSubscriptionUrl|string|Se è abilitata la delega, indica l'URL della sottoscrizione delegata.|  
|IsAgreed|boolean|Se il prodotto presenta delle condizioni, indica se l'utente corrente le ha accettate.|  
|Sottoscrizioni|Raccolta di entità [Riepilogo della sottoscrizione](api-management-template-data-model-reference.md#SubscriptionSummary).|Le sottoscrizioni al prodotto.|  
|API|Raccolta di entità [API](api-management-template-data-model-reference.md#API).|Le API in questo prodotto.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Indica se l'utente corrente è idoneo per la sottoscrizione al prodotto per quanto riguarda il limite delle sottoscrizioni.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Indica se l'utente corrente è idoneo per la sottoscrizione al prodotto per quanto riguarda l'autorizzazione a effettuare più sottoscrizioni.|  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).
