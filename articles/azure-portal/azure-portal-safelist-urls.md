---
title: Aggiungere gli URL del portale di Azure nell’elenco di indirizzi attendibili nel firewall o nel server proxy
description: Aggiungere questi URL al bypass del server proxy per comunicare con il portale di Azure e i relativi servizi
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745877"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Aggiungere gli URL del portale di Azure nell’elenco di indirizzi attendibili nel firewall o nel server proxy

È possibile configurare i dispositivi di sicurezza locali per ignorare le restrizioni di sicurezza per gli URL portale di Azure. Questa configurazione può migliorare le prestazioni e la connettività tra la rete locale o l'area estesa e il cloud di Azure.

Gli amministratori di rete spesso distribuiscono server proxy, firewall o altri dispositivi. Questi dispositivi consentono di proteggere e controllare il modo in cui gli utenti accedono a Internet. Le regole progettate per la protezione degli utenti possono a volte bloccare o rallentare il traffico Internet relativo alle aziende legittime. Questo traffico include le comunicazioni tra l'utente e Azure. Per ottimizzare la connettività tra la rete e il portale di Azure e i relativi servizi, è consigliabile aggiungere URL portale di Azure all'elenchi di sicurezza.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL di portale di Azure per il bypass del proxy

Gli endpoint URL per la portale di Azure di sicurezza sono specifici del cloud di Azure in cui viene distribuita l'organizzazione. Per consentire al traffico di rete a questi endpoint di ignorare le restrizioni, selezionare il cloud. Aggiungere quindi l'elenco di URL al server proxy o al firewall.

#### <a name="public-cloud"></a>[Cloud pubblico](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Cloud per enti pubblici statunitensi](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Cloud per enti pubblici Cina](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Il traffico verso questi endpoint usa le porte TCP standard per HTTP (80) e HTTPS (443).
>
>
