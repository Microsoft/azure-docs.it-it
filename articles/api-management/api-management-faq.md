---
title: Domande frequenti su Gestione API di Azure | Microsoft Docs
description: Risposte alle domande frequenti, modelli e procedure consigliate in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335980"
---
# <a name="azure-api-management-faqs"></a>Domande frequenti su Gestione API di Azure
Risposte alle domande comuni, modelli e procedure consigliate per Gestione API di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Contatti
* [Come si rivolge una domanda al team di Gestione API di Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Domande frequenti
* [Che cosa significa se una funzionalità è in anteprima?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Come si protegge la connessione tra il gateway di Gestione API e i servizi back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Come si copia l'istanza del servizio Gestione API in una nuova istanza?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [È possibile gestire l'istanza di Gestione API a livello di codice?](#can-i-manage-my-api-management-instance-programmatically)
* [Come si aggiunge un utente al gruppo di amministratori?](#how-do-i-add-a-user-to-the-administrators-group)
* [Perché il criterio da aggiungere non è disponibile nell'editor dei criteri?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Come si configurano più ambienti in una sola API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [È possibile usare SOAP con Gestione API?](#can-i-use-soap-with-api-management)
* [È possibile configurare un server di autorizzazione OAuth 2.0 con la sicurezza AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Quale metodo di routing usa Gestione API nelle distribuzioni in più posizioni geografiche?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [È possibile usare un modello di Azure Resource Manager per creare un'istanza del servizio Gestione API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [È possibile utilizzare un certificato TLS/SSL autofirmato per un back-end?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Perché si verifica un errore di autenticazione quando si tenta di clonare un repository GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Gestione API funziona con ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Perché è necessaria una subnet dedicata nelle reti virtuali di Resource Manager quando Gestione API viene distribuita in tali reti?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Qual è la dimensione minima necessaria della subnet quando si distribuisce Gestione API in una rete virtuale?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [È possibile spostare un servizio Gestione API da una sottoscrizione a un'altra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Esistono restrizioni o problemi noti relativi all'importazione di questa API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Come si rivolge una domanda al team di Gestione API di Microsoft Azure?
È possibile contattare Microsoft in uno dei modi seguenti:

* Pubblicare le domande sul [forum MSDN di Gestione API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Inviare un messaggio di posta elettronica all'indirizzo <mailto:apimgmt@microsoft.com>.
* Inviare una richiesta di funzionalità nel [forum dei commenti e suggerimenti su Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Che cosa significa se una funzionalità è in anteprima?
Quando una funzionalità è in anteprima, significa che si stanno raccogliendo attivamente commenti e suggerimenti dagli utenti sulla funzionalità. Una funzionalità in anteprima è completa dal punto di vista funzionale, ma è possibile che venga apportata una modifica di rilievo in risposta ai commenti e suggerimenti dei clienti. È consigliabile non far dipendere l'ambiente di produzione da una funzionalità in anteprima. Per inviare commenti e suggerimenti sulle funzionalità in anteprima, usare una delle opzioni di contatto elencate in [Come si rivolge una domanda al team di Gestione API di Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Come si protegge la connessione tra il gateway di Gestione API e i servizi back-end?
Esistono diverse opzioni per proteggere la connessione tra il gateway di Gestione API e i servizi back-end. È possibile:

* Usare l'autenticazione HTTP di base. Per altre informazioni, vedere [Importare e pubblicare la prima API](import-and-publish.md).
* Usare l'autenticazione reciproca TLS come descritto in Come proteggere i [servizi back-end usando l'autenticazione](api-management-howto-mutual-certificates.md)del certificato client in Gestione API di Azure.
* Usare gli elenchi di IP consentiti nel servizio back-end. In tutti i livelli di Gestione API, ad eccezione del livello Consumo, l'indirizzo IP del gateway rimane costante, con alcune avvertenze descritte [nell'articolo della documentazione IP.](api-management-howto-ip-addresses.md)
* Connettere l'istanza di Gestione API a una rete virtuale di Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Come si copia l'istanza del servizio Gestione API in una nuova istanza?
Esistono diverse opzioni per copiare un'istanza di Gestione API in una nuova istanza. È possibile:

* Usare la funzione di backup e ripristino di Gestione API. Per altre informazioni, vedere [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Creare una propria funzionalità di backup e ripristino usando l'[API REST di Gestione API](/rest/api/apimanagement/). Usare l'API REST per salvare e ripristinare le entità dall'istanza del servizio desiderata.
* Scaricare la configurazione del servizio usando Git e quindi caricarla in una nuova istanza. Per altre informazioni, vedere [Come salvare e configurare la configurazione del servizio Gestione API tramite Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>È possibile gestire l'istanza di Gestione API a livello di codice?
Sì, è possibile gestire Gestione API a livello di codice usando:

* L'[API REST di Gestione API](/rest/api/apimanagement/).
* [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* I cmdlet di PowerShell per la [distribuzione del servizio](https://docs.microsoft.com/powershell/module/wds) e per la [gestione del servizio](https://docs.microsoft.com/powershell/azure/servicemanagement/overview).

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Come si aggiunge un utente al gruppo di amministratori?
Ecco come è possibile aggiungere un utente al gruppo di amministratori:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Passare al gruppo di risorse con l'istanza di Gestione API che si vuole aggiornare.
3. In Gestione API assegnare il ruolo **Collaboratore del servizio di gestione API** all'utente.

Ora il nuovo collaboratore aggiunto può usare i [cmdlet](https://docs.microsoft.com/powershell/azure/overview) di Azure PowerShell. Ecco come accedere come amministratore:

1. Usare il cmdlet `Connect-AzAccount` per accedere.
2. Impostare il contesto sulla sottoscrizione che contiene il servizio usando `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Ottenere un l'URL Single Sign-On usando `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Usare l'URL per accedere al portale di amministrazione.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Perché il criterio da aggiungere non è disponibile nell'editor dei criteri?
Se il criterio che si vuole aggiungere è in grigio o ombreggiato nell'editor dei criteri, assicurarsi che l'ambito del criterio sia corretto. Ogni istruzione di criterio è progettata per essere usata in ambiti e sezioni dei criteri specifici. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, vedere la sezione sull'utilizzo in [API Management policies](/azure/api-management/api-management-policies) (Criteri di Gestione API).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Come si configurano più ambienti in una sola API?
Per configurare più ambienti, ad esempio un ambiente di test e un ambiente di produzione, in una sola API, esistono due opzioni. È possibile:

* Ospitare API diverse nello stesso tenant.
* Ospitare le stesse API in tenant diversi.

### <a name="can-i-use-soap-with-api-management"></a>È possibile usare SOAP con Gestione API?
Ora è disponibile il supporto per il [pass-through SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/). Gli amministratori possono importare il file WSDL del servizio SOAP e Gestione API di Azure creerà un front-end SOAP. Per i servizi SOAP sono disponibili la documentazione del portale per sviluppatori, la console di test, i criteri e l'analisi.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>È possibile configurare un server di autorizzazione OAuth 2.0 con la sicurezza AD FS?
Per informazioni su come configurare un server di autorizzazione OAuth 2.0 con la sicurezza di Active Directory Federation Services (AD FS), vedere [Using ADFS in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/) (Uso di AD FS in Gestione API).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Quale metodo di routing usa Gestione API nelle distribuzioni in più posizioni geografiche?
Gestione API usa il [metodo di routing del traffico relativo alle prestazioni](../traffic-manager/traffic-manager-routing-methods.md#performance) nelle distribuzioni in più posizioni geografiche. Il traffico in ingresso viene instradato al gateway API più vicino. Quando un'area diventa offline, il traffico in ingresso viene automaticamente indirizzato al gateway successivo più vicino. Altre informazioni sui metodi di routing sono disponibili in [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>È possibile usare un modello di Azure Resource Manager per creare un'istanza del servizio Gestione API?
Sì. Vedere Modelli di avvio rapido del servizio di gestione API di [Azure.See](https://aka.ms/apimtemplate) the Azure API Management Service quickstart templates.

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>È possibile utilizzare un certificato TLS/SSL autofirmato per un back-end?
Sì. Questa operazione può essere eseguita tramite PowerShell o eseguendo direttamente l'invio all'API. La convalida della catena di certificati verrà quindi disabilitata e si consentirà l'utilizzo di certificati autofirmati o firmati privatamente per le comunicazioni da Gestione API ai servizi back-end.

#### <a name="powershell-method"></a>Metodo PowerShell ####
Utilizzare [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) i cmdlet di PowerShell (per i nuovi back-end) o [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (per quelli back-end esistenti) e impostare il `-SkipCertificateChainValidation` parametro su `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metodo di aggiornamento API diretto ####
1. Creare un'entità di [back-end](/rest/api/apimanagement/) usando Gestione API.     
2. Impostare la proprietà **skipCertificateChainValidation** su **true**.     
3. Se non si vuole più consentire certificati autofirmati, eliminare l'entità di back-end o impostare la proprietà **skipCertificateChainValidation** su **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Perché si ottiene un errore di autenticazione quando si cerca di clonare un repository GIT?
Se si usa Git Credential Manager o se si vuole clonare un repository Git usando Visual Studio, potrebbe verificarsi un problema noto con la finestra di dialogo Credenziali di Windows. Nella finestra di dialogo il limite per la lunghezza della password è di 127 caratteri e la password generata da Microsoft viene troncata. Microsoft sta lavorando per abbreviare la password. Per ora, per la clonazione del repository Git, usare GIT Bash.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestione API funziona con ExpressRoute?
Sì. Gestione API funziona con ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Perché è necessaria una subnet dedicata nelle reti virtuali di Resource Manager quando Gestione API viene distribuita in tali reti?
Il requisito di una subnet dedicata per Gestione API deriva dal fatto che è costruita su un modello di distribuzione classico (livello PAAS V1). Anche se è possibile eseguire la distribuzione in una rete virtuale (livello V2) di Resource Manager, è necessario considerare le conseguenze. Il modello di distribuzione classica in Azure non è strettamente associato con il modello di Resource Manager e quindi se si crea una risorsa nel livello V2, il livello V1 non è a conoscenza del livello V2 e questo può causare problemi, ad esempio la Gestione API prova a usare un indirizzo IP già assegnato a una scheda di rete (nel livello V2).
Per altre informazioni sulla differenza tra modelli classici e quelli di Resource Manager in Azure, vedere la [differenza nei modelli di distribuzione](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Qual è la dimensione minima necessaria della subnet quando si distribuisce Gestione API in una rete virtuale?
La dimensione minima della subnet necessaria per distribuire Gestione API è [/29](../virtual-network/virtual-networks-faq.md#configuration), ovvero la dimensione minima della subnet supportata da Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>È possibile spostare un servizio Gestione API da una sottoscrizione a un'altra?
Sì. Per informazioni, vedere [Move resources to a new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Esistono restrizioni o problemi noti relativi all'importazione di questa API?
[Problemi noti e limitazioni](api-management-api-import-restrictions.md) per i formati Open API (Swagger), WSDL e WADL.
