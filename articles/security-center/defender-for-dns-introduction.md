---
title: Azure Defender per DNS - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dffb505719e6778adfdd8e99f62790df9ebd615a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100696"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Introduzione ad Azure Defender per DNS

[DNS di Azure](../dns/dns-overview.md) è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.

Azure Defender per DNS offre un livello di protezione aggiuntivo per le risorse cloud grazie a:

- monitoraggio continuo di tutte le query DNS dalle risorse di Azure
- esecuzione di analisi di sicurezza avanzate per inviare avvisi sulle attività sospette

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prezzi:|**Azure Defender per DNS** viene fatturato come indicato nei [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quali sono i vantaggi di Azure Defender per DNS?

Azure Defender per DNS protegge da problemi quali:

- Esfiltrazione di dati dalle risorse di Azure tramite tunneling DNS
- Comunicazione del malware con il server di comando e controllo
- Comunicazione con domini dannosi come phishing e crypto mining
- Attacchi DNS: comunicazione con resolver DNS dannosi 

Per un elenco completo degli avvisi forniti da Azure Defender per DNS, vedere [Avvisi per DNS](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Dependencies

Azure Defender per DNS non usa agenti. 

Per proteggere il livello DNS, abilitare Azure Defender per DNS per ogni sottoscrizione, come descritto in [Abilitare Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per DNS. Per i materiali correlati, vedere l'articolo seguente: 

- Gli avvisi di sicurezza potrebbero essere generati dal Centro sicurezza o essere ricevuti dal Centro sicurezza da prodotti di sicurezza diversi. Per esportare tutti questi avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione degli avvisi in un sistema di informazioni di sicurezza e gestione degli eventi](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](enable-azure-defender.md)