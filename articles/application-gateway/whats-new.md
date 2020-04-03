---
title: Novità del gateway applicazione di Azure
description: Informazioni sulle novità del gateway applicazione di Azure, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 1cfa2ab1d8bf096b1821f7473d9808801b47c547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371174"
---
# <a name="whats-new-in-azure-application-gateway"></a>Novità del gateway applicazione di Azure

Il gateway applicazione di Azure viene aggiornato regolarmente. Per rimanere aggiornati con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate

## <a name="new-features"></a>Nuove funzionalità

|Funzionalità  |Descrizione  |Data di aggiunta  |
|---------|---------|---------|
| Route definite dall'utente in v2 (anteprima) |Le route definite dall'utente sono ora supportate in alcuni scenari negli SKU del gateway applicazione v2. Per altre informazioni, vedere [Panoramica della configurazione del gateway applicazione](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Marzo 2020 |
|Modifiche ai cookie di affinità |Quando è abilitata l'affinità basata su cookie, oltre al cookie ApplicationGatewayAffinity esistente il gateway applicazione inserisce un altro cookie identico denominato *ApplicationGatewayAffinityCORS*. Ad *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi (*SameSite=None; Secure*) in modo che le sessioni permanenti vengano mantenute anche per richieste effettuate da origini diverse. Per altre informazioni, vedere [Affinità basata su cookie del gateway applicazione](configuration-overview.md#cookie-based-affinity). |Febbraio 2020 |
|Miglioramenti relativi al probe |Con i miglioramenti apportati ai probe personalizzati nello SKU del gateway applicazione v2, è stata semplificata la [configurazione del probe](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), sono stati resi più agevoli i [test di integrità del back-end su richiesta](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) e sono state aggiunte [altre informazioni di diagnostica](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) per facilitare la risoluzione dei problemi di integrità del back-end.  |Ottobre 2019 |
|Altre metriche |Sono state aggiunte le nuove metriche seguenti per facilitare il monitoraggio dello SKU del gateway applicazione v2: [Metriche temporali](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), stato della risposta back-end, byte ricevuti, byte inviati, protocollo TLS client e unità di calcolo correnti. Vedere [Metriche supportate dallo SKU del gateway applicazione V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto 2019 |
|Regole personalizzate di WAF |Il gateway applicazione WAF_v2 supporta ora la creazione di regole personalizzate. Vedere [Regole personalizzate del gateway applicazione](custom-waf-rules-overview.md). |Giugno 2019 |
|Ridimensionamento automatico, ridondanza delle zone, disponibilità generale del supporto di VIP statici |Disponibilità generale dello SKU v2, che supporta dimensionamento automatico, ridondanza di zona, miglioramento delle prestazioni, indirizzi VIP statici, Key Vault, riscrittura dell'intestazione. Vedere la [documentazione sul ridimensionamento automatico del gateway applicazione](application-gateway-autoscaling-zone-redundant.md). |Aprile 2019 |
|Integrazione di Key Vault |Il gateway applicazione supporta ora l'integrazione con Key Vault (in anteprima pubblica) per i certificati server associati a listener abilitati per HTTPS. Vedere [SSL termination with Key Vault certificates](key-vault-certs.md) (Terminazione SSL con certificati di Key Vault). |Aprile 2019 |
|CRUD/riscritture intestazioni     |È ora possibile riscrivere le intestazioni HTTP. Vedere [Esercitazione: Creare un gateway applicazione e riscrivere intestazioni HTTP](tutorial-http-header-rewrite-powershell.md).|Dicembre 2018|
|Configurazione di WAF ed elenco di esclusione     |Sono state aggiunte altre opzioni per configurare la soluzione WAF e ridurre i falsi positivi. Per altre informazioni, vedere [Limiti delle dimensioni di richiesta di Web application firewall ed elenchi di esclusione](application-gateway-waf-configuration.md).|Dicembre 2018|
|Ridimensionamento automatico, ridondanza delle zone, supporto di VIP statici      |Con la versione 2 dello SKU, sono stati introdotti diversi miglioramenti, come il ridimensionamento automatico, le prestazioni più elevate e altro ancora. Per altre informazioni, vedere [Cos'è il gateway applicazione di Azure?](overview.md)|Settembre 2018|
|Esaurimento delle connessioni     |Lo svuotamento delle connessioni consente di rimuovere correttamente i membri dai pool di back-end. Per altre informazioni, vedere [Svuotamento delle connessioni](features.md#connection-draining).|Settembre 2018|
|Pagine di errore personalizzate     |Con le pagine di errore personalizzate, è possibile creare una pagina di errore nello stesso formato delle altre parti dei siti Web. Per abilitare questa funzionalità, vedere[Creare pagine di errore personalizzate del gateway applicazione](custom-error.md).|Settembre 2018|
|Miglioramenti delle metriche     |Con le metriche migliorate, è possibile ottenere un quadro più completo dello stato del gateway applicazione. Per abilitare le metriche nel gateway applicazione, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).|Giugno 2018|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione di Azure, vedere [Cos'è il gateway applicazione di Azure?](overview.md)
