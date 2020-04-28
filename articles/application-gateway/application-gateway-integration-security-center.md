---
title: Integrazione del gateway applicazione con il Centro sicurezza di Azure | Microsoft Docs
description: Questa pagina include informazioni sull'integrazione del gateway applicazione nel Centro sicurezza di Azure.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277197"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Panoramica dell'integrazione tra il gateway applicazione e il Centro sicurezza di Azure

Informazioni sulla protezione delle risorse dell'applicazione Web tramite il gateway applicazione e il Centro sicurezza di Azure. Il web application firewall (WAF) del gateway applicazione è integrato nel [Centro sicurezza](../security-center/security-center-intro.md) per offrire alle applicazioni Web non protette dell'ambiente una visualizzazione semplice per prevenire, rilevare e rispondere alle minacce.

## <a name="overview"></a>Panoramica

Il WAF del gateway applicazione è una raccomandazione del Centro sicurezza per la protezione delle applicazioni Web da attacchi e vulnerabilità. Le risorse abilitate per il Web non protette da WAF sono visualizzate nel Centro sicurezza come raccomandazioni con gravità elevata. Le raccomandazioni per i web application firewall sono visualizzate nella pagina **Panoramica** in **Applicazioni**.

![integrazione con il Centro sicurezza][1]

Se si fa clic su una raccomandazione relativa a web application firewall, viene aperta una nuova pagina che visualizza i dettagli della raccomandazione.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Aggiungere un web application firewall a una risorsa esistente

Passare a **tutti i servizi** > **sicurezza e identità** > **Centro sicurezza** e nel **Centro sicurezza-Panoramica**, fare clic su **applicazioni**. La tabella in **Centro sicurezza PC - Applicazioni** contiene un elenco delle applicazioni rilevate dal Centro sicurezza nella sottoscrizione.

![applicazioni Web][3]

Se si fa clic su un'applicazione Web con un problema critico, viene visualizzata la pagina **Integrità della sicurezza dell'applicazione**. Nell'immagine seguente è illustrata l'applicazione Web che non è protetta da un web application firewall. 

![risorse Web non protette][2]

Fare clic su **Aggiungi un web application firewall** in **Raccomandazioni** per aprire la pagina **Aggiungi un web application firewall**.

Se esiste alcun gateway applicazione o se ne vuole creare uno nuovo, fare clic su **Crea nuovo** e in **Nuovo web application firewall** fare clic su **Microsoft - Gateway applicazione**. Vengono descritti i passaggi per la creazione di un gateway applicazione. A questo punto, l'applicazione Web viene aggiunta come risorsa protetta e il Centro sicurezza rileva che la risorsa è protetta da un web application firewall. La risorsa non viene aggiunta come membro del pool back-end.

Se è presente un gateway applicazione, è possibile selezionarlo in **Usa la soluzione esistente**

![Pagina per aggiungere un web application firewall][4]

L'aggiunta di un'applicazione Web a un gateway applicazione tramite il Centro sicurezza non implica l'aggiunta della risorsa come membro del pool back-end. Questa operazione deve essere eseguita direttamente nella risorsa del gateway applicazione.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Aggiungere una risorsa a un web application firewall esistente

Passare a **tutti i servizi** > **sicurezza e identità** > **Centro sicurezza** e nel **Centro sicurezza-Panoramica**fare clic su **soluzioni partner**. I gateway applicazione compatibili con il Centro sicurezza esistenti sono visualizzati nella pagina **Soluzioni partner**.

![soluzioni partner][7]

Fare clic su **Collega app** per aprire **Collega applicazioni** nel quale sarà possibile selezionare le applicazioni esistenti. Scegliere le applicazioni da proteggere e fare clic su **OK**. Questa operazione non aggiunge l'applicazione Web al pool back-end del gateway applicazione. ma imposta la risorsa come risorsa protetta in modo che il Centro sicurezza possa rilevarla. Per aggiungere la risorsa come membro del pool back-end, è necessario eseguire questa operazione nel gateway applicazione: dalla pagina corrente fare clic su **Console della soluzione** per passare alla risorsa del gateway applicazione nella quale è possibile aggiungere l'applicazione Web al pool back-end.

![applicazioni di soluzioni partner][6]

## <a name="finalize-configuration"></a>Completare la configurazione

Il Centro sicurezza rileva le applicazioni aggiunte a un gateway applicazione come risorse protette.  Il Centro sicurezza monitora l'integrità della risorsa e verifica che sia protetta da un gateway applicazione. Il passaggio successivo consiste nell'aggiungere l'IP privato, l'IP pubblico o la scheda di interfaccia di rete della macchina virtuale al pool back-end del gateway applicazione. Prima del completamento del passaggio, viene visualizzata la raccomandazione aggiuntiva **Finalizza la protezione dell'applicazione** fino a quando la risorsa non viene aggiunta.

![Pagina per aggiungere un web application firewall][5]

## <a name="security-alerts"></a>Avvisi di sicurezza

Nel centro sicurezza passare a **rilevamento** > **avvisi di sicurezza**.  Vengono visualizzati gli avvisi WAF per i gateway applicazione. Gli avvisi sono suddivisi per regola WAF.

![avvisi di sicurezza][8]

La selezione di una regola fornirà un elenco di avvisi per la regola WAF specifica. Ogni avviso visualizza dettagli aggiuntivi sull'elemento individuato. I dettagli includono un collegamento al gateway applicazione.
 
![dettagli dell'avviso][9]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare il web application firewall in un gateway applicazione esistente, vedere [Creare o aggiornare un gateway applicazione di Azure con web application firewall](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png