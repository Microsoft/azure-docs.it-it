---
title: Domande frequenti relative ai problemi di distribuzione per Servizi cloud di Microsoft Azure| Microsoft Docs
description: Questo articolo elenca le domande frequenti relative alla distribuzione per Servizi cloud di Microsoft Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896478"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemi di distribuzione per servizi cloud di Azure (versione classica): domande frequenti

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).
Questo articolo include le domande frequenti relative ai problemi di distribuzione per [Servizi cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Perché la distribuzione di un servizio cloud nello slot di staging talvolta non riesce e si verifica un errore di allocazione delle risorse se c'è già una distribuzione esistente nello slot di produzione?
Se un servizio cloud ha una distribuzione in uno slot, il servizio è aggiunto a un cluster specifico. Ciò significa che se c'è già una distribuzione nello slot di produzione, una nuova distribuzione di staging può essere allocata solo nello stesso cluster dello slot di produzione.

Gli errori di allocazione si verificano quando il cluster in cui si trova il servizio cloud non ha risorse fisiche di calcolo sufficienti per soddisfare la richiesta di distribuzione.

Per informazioni sull'attenuazione di questi errori di allocazione, vedere [errore di allocazione del servizio cloud: soluzioni](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Perché il ridimensionamento di una distribuzione dei servizi cloud causa a volte un errore di allocazione?
Quando un servizio cloud viene distribuito, in genere viene aggiunto a un cluster specifico. Ciò significa che il ridimensionamento di un servizio cloud esistente comporta l'allocazione di nuove istanze nello stesso cluster. Se la capacità del cluster è quasi stata raggiunta oppure se le dimensioni o il tipo di VM desiderati non sono disponibili, la richiesta potrebbe avere esito negativo.

Per informazioni sull'attenuazione di questi errori di allocazione, vedere [errore di allocazione del servizio cloud: soluzioni](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Perché la distribuzione di un servizio cloud in un gruppo di affinità causa a volte un errore di allocazione?
Una nuova distribuzione in un servizio cloud vuoto può essere allocata dall'infrastruttura in qualsiasi cluster dell'area, a meno che il servizio cloud sia aggiunto a un gruppo di affinità. Le distribuzioni allo stesso gruppo di affinità verranno eseguite nello stesso cluster. Se la capacità del cluster è quasi stata raggiunta, la richiesta potrebbe avere esito negativo.

Per informazioni sull'attenuazione di questi errori di allocazione, vedere [errore di allocazione del servizio cloud: soluzioni](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Perché la modifica delle dimensioni della VM o l'aggiunta di una nuova VM a un servizio cloud esistente causa talvolta un errore di allocazione?
I cluster in un Data Center possono avere configurazioni diverse dei tipi di computer, ad esempio una serie, serie AV2, serie D, serie dv2, serie G, serie H e così via. Non tutti i cluster hanno però necessariamente tutti i tipi di VM. Se, ad esempio, si prova ad aggiungere una VM serie D a un servizio cloud che è già stato distribuito in un cluster con solo la serie A, si verifica un errore di allocazione. Questo si verifica anche se si cerca di modificare le dimensioni di SKU delle VM (ad esempio passando dalla serie A alla serie D).

Per informazioni sull'attenuazione di questi errori di allocazione, vedere [errore di allocazione del servizio cloud: soluzioni](cloud-services-allocation-failures.md#solutions).

Per verificare le dimensioni disponibili nella propria area, vedere [Microsoft Azure: Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Perché la distribuzione di un servizio cloud a volte non riesce a causa di limiti/quote/vincoli nella sottoscrizione o nel servizio?
La distribuzione di un servizio cloud potrebbe non riuscire se le risorse che devono essere allocate superano la quota predefinita o massima consentita per il servizio a livello di area o di data center. Per altre informazioni, vedere [Limiti relativi a Servizi cloud](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

È anche possibile tenere traccia dell'utilizzo e della quota correnti per la sottoscrizione nel portale: portale di Azure => subscriptions => \<appropriate subscription>   => "Usage + quota".

Le informazioni relative all'utilizzo e al consumo di risorse possono anche essere recuperate tramite le API di fatturazione di Azure. Vedere [Panoramica dell'API utilizzo di Azure](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Come è possibile modificare le dimensioni di una VM di un servizio cloud distribuito senza eseguire di nuovo la distribuzione?
Non è possibile modificare le dimensioni di una VM di un servizio cloud distribuito senza eseguire di nuovo la distribuzione. Le dimensioni della VM sono integrate nel file CSDEF, che può essere aggiornato solo eseguendo di nuovo la distribuzione.

Per altre informazioni, vedere [Come aggiornare un servizio cloud](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Perché non riesco a distribuire i Servizi Cloud tramite le API Gestione dei servizi o PowerShell quando uso l'account di archiviazione di Azure Resource Manager? 

Poiché il servizio cloud è una risorsa classica non direttamente compatibile con il modello di Azure Resource Manager, non è possibile associarla ai Azure Resource Manager account di archiviazione. Di seguito sono riportate alcune opzioni: 

- Distribuzione tramite l'API REST.

    Quando si esegue la distribuzione tramite l'API REST di Gestione dei servizi, è possibile risolvere la limitazione specificando un URL SAS nell'archiviazione BLOB che funziona con l'account classico e con l'account di archiviazione di Azure Resource Manager. Altre informazioni sulla proprietà 'PackageUrl' sono reperibili [qui](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Distribuzione tramite il [portale di Azure](https://portal.azure.com).

    Questa operazione funzionerà dal [portale di Azure](https://portal.azure.com) perché la chiamata passa attraverso un proxy/shim che consente la comunicazione tra Azure Resource Manager e le risorse classiche. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Perché il portale di Azure richiede di specificare un account di archiviazione per la distribuzione?

Nel portale classico il pacchetto viene caricato direttamente nel livello API di gestione, pertanto il livello API deve inserire temporaneamente il pacchetto in un account di archiviazione interna.  Questo processo causa problemi di prestazioni e scalabilità perché il livello API non è stato progettato come servizio di caricamento file.  Nel portale di Azure (modello di distribuzione Resource Manager), il passaggio intermedio di caricamento nel livello API è stato tralasciato, per distribuzioni più veloci e affidabili.

Per quanto riguarda i costi, sono molto limitati ed è possibile usare nuovamente lo stesso account di archiviazione in tutte le distribuzioni. È possibile usare il [calcolatore dei costi di archiviazione](https://azure.microsoft.com/pricing/calculator/#storage1) per determinare i costi relativi al caricamento del pacchetto del servizio (CSPKG), al download del CSPKG e quindi all'eliminazione del CSPKG.
