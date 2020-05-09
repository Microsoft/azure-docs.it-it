---
title: Risolvere i problemi di connettività SMTP in uscita in Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi di connettività SMTP in uscita in Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: e5a878c8108706c4a3a175c920708beeeaa4aa12
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801751"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Risolvere i problemi di connettività SMTP in uscita in Azure
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=027fa865-2329-23de-3740-cfbe35359302" target='_blank'>Iniziare</a></span><span class="has-padding-small">a risolvere rapidamente il problema usando l'agente virtuale per eseguire la <b>diagnostica automatica.</b> </span> Informativa <sub>sulla privacy</sub> <span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <div align="right"></div></a></span></p>
A partire dal 15 novembre 2017, i messaggi di posta elettronica in uscita che vengono inviati direttamente a domini esterni, ad esempio outlook.com e gmail.com, da una macchina virtuale vengono resi disponibili solo a determinati tipi di sottoscrizione in Microsoft Azure. Le connessioni SMTP in uscita che usano la porta TCP 25 sono state bloccate. La porta 25 viene usata principalmente per il recapito di posta elettronica non autenticato.

Questo diverso comportamento si applica solo alle nuove sottoscrizioni e alle nuove distribuzioni a partire dal 15 novembre 2017.

## <a name="recommended-method-of-sending-email"></a>Metodo consigliato per l'invio di posta elettronica
Per inviare posta elettronica da macchine virtuali di Azure o da servizi app di Azure è consigliabile usare servizi di inoltro SMTP autenticato, che si connettono in genere tramite la porta TCP 587 o 443 ma supportano anche altre porte. Questi servizi vengono usati per mantenere la reputazione dell'indirizzo IP o del dominio in modo da ridurre al minimo la possibilità che provider di posta elettronica di terze parti rifiutino il messaggio. Tra questi servizi di inoltro SMTP è incluso [SendGrid](https://sendgrid.com/partners/azure/). Nel sistema locale può anche essere in esecuzione un servizio di inoltro SMTP sicuro che è possibile usare.

In Azure non sono previste restrizioni relative all'uso di questi servizi di recapito di posta elettronica, indipendentemente dal tipo di sottoscrizione.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Per gli utenti di Azure con contratto Enterprise Agreement, non vi sono variazioni riguardo alla capacità tecnica di inviare posta elettronica senza usare un servizio di inoltro autenticato. Gli utenti con contratto Enterprise Agreement, sia nuovi che esistenti, possono tentare il recapito di posta elettronica in uscita direttamente dalle macchine virtuali di Azure a provider di posta elettronica esterni, senza restrizioni da parte della piattaforma Azure. Anche se non è garantito che i provider di posta elettronica accettino posta elettronica in ingresso da un utente qualsiasi, i tentativi di recapito non verranno bloccati dalla piattaforma Azure per le macchine virtuali nell'ambito di sottoscrizioni Enterprise Agreement. Sarà necessario collaborare direttamente con i provider di posta elettronica per risolvere eventuali errori di recapito di messaggi o di filtro di posta indesiderata che interessano tali provider.

## <a name="pay-as-you-go"></a>Pagamento in base al consumo
Se si è eseguita l'iscrizione a offerte di sottoscrizione con pagamento in base al consumo o Microsoft Partner Network prima del 15 novembre 2017, non vi saranno variazioni riguardo alla capacità tecnica di tentare il recapito di posta elettronica in uscita. Si continuerà ad avere la possibilità di tentare il recapito di posta elettronica in uscita dalle macchine virtuali di Azure nell'ambito di queste sottoscrizioni direttamente a provider di posta elettronica esterni, senza restrizioni da parte della piattaforma Azure. Anche in questo caso, non è garantito che i provider di posta elettronica accettino posta elettronica in ingresso da un utente qualsiasi e gli utenti dovranno collaborare direttamente con i provider di posta elettronica per risolvere eventuali errori di recapito di messaggi o di filtro di posta indesiderata che interessano tali provider.

Per le sottoscrizioni con pagamento in base al consumo o Microsoft Partner Network create dopo il 15 novembre 2017, saranno previste restrizioni tecniche che bloccano la posta elettronica inviata direttamente dalle macchine virtuali all'interno di tali sottoscrizioni. Se si vuole avere la possibilità di inviare posta elettronica direttamente dalle macchine virtuali di Azure a provider di posta elettronica esterni, senza inoltro SMTP autenticato, è possibile inviare una richiesta per la rimozione della restrizione. Le richieste verranno esaminate e approvate a discrezione di Microsoft e verranno concesse solo dopo ulteriori controlli anti-frode. Per eseguire una richiesta, aprire un caso di supporto usando il tipo di problema seguente: la**connettività** > di**rete** > virtuale **tecnica** > **non può inviare messaggi di posta elettronica (SMTP/porta 25)**. Assicurarsi di aggiungere i dettagli sul motivo per cui la propria distribuzione deve inviare messaggi direttamente a provider di posta elettronica anziché usare un servizio di inoltro autenticato.

Dopo che una sottoscrizione con pagamento in base al consumo o Microsoft Partner Network è stata esentata e che le macchine virtuali sono state arrestate &' avviato ' dal portale di Azure, tutte le macchine virtuali all'interno di tale sottoscrizione verranno esentate in futuro. L'esenzione è applicabile solo alla sottoscrizione richiesta e si applica solo al traffico delle macchine virtuali indirizzato direttamente a Internet. Il traffico della porta 25 di routing tramite i servizi PaaS di Azure, ad esempio il [firewall di Azure](https://azure.microsoft.com/services/azure-firewall/) , non è supportato.

> [!NOTE]
> Microsoft si riserva il diritto di revocare questa esenzione nel caso in cui venga accertata una violazione delle condizioni del servizio.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark e versione di valutazione gratuita
Se è stato creato un abbonamento MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure Student, una versione di valutazione gratuita o una sottoscrizione di Visual Studio dopo il 15 novembre 2017, saranno previste restrizioni tecniche che bloccano la posta elettronica inviata dalle macchine virtuali all'interno di tali sottoscrizioni direttamente ai provider di posta elettronica. Le restrizioni vengono applicate allo scopo di evitare casi di uso improprio. Non verranno accettate richieste per rimuovere questa restrizione.

Se si usano questi tipi di sottoscrizione, è consigliabile usare i servizi di inoltro SMTP, come descritto in precedenza in questo articolo o modificare il tipo di sottoscrizione.

## <a name="cloud-service-provider-csp"></a>Provider di servizi cloud

Se si usano risorse di Azure tramite CSP, è possibile richiedere al CSP di creare una richiesta di esenzione da sbloccare con Microsoft per conto dell'utente se non è possibile usare un inoltro SMTP sicuro.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se è ancora necessaria assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema usando il tipo di problema: **gestione delle sottoscrizioni** tipo di problema: **richiesta di abilitazione del flusso di posta elettronica della porta 25**.
