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
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058944"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Risolvere i problemi di connettività SMTP in uscita in Azure

A partire dal 15 novembre 2017, i messaggi di posta elettronica in uscita che vengono inviati direttamente a domini esterni, ad esempio outlook.com e gmail.com, da una macchina virtuale vengono resi disponibili solo a determinati tipi di sottoscrizione in Microsoft Azure. Le connessioni SMTP in uscita che usano la porta TCP 25 sono state bloccate. La porta 25 viene usata principalmente per il recapito di posta elettronica non autenticato.

Questo diverso comportamento si applica solo alle nuove sottoscrizioni e alle nuove distribuzioni a partire dal 15 novembre 2017.

## <a name="recommended-method-of-sending-email"></a>Metodo consigliato per l'invio di posta elettronica
Per inviare posta elettronica da macchine virtuali di Azure o da servizi app di Azure è consigliabile usare servizi di inoltro SMTP autenticato, che si connettono in genere tramite la porta TCP 587 o 443 ma supportano anche altre porte. Questi servizi vengono usati per mantenere la reputazione dell'indirizzo IP o del dominio in modo da ridurre al minimo la possibilità che provider di posta elettronica di terze parti rifiutino il messaggio. Tra questi servizi di inoltro SMTP è incluso [SendGrid](https://sendgrid.com/partners/azure/). Nel sistema locale può anche essere in esecuzione un servizio di inoltro SMTP sicuro che è possibile usare.

In Azure non sono previste restrizioni relative all'uso di questi servizi di recapito di posta elettronica, indipendentemente dal tipo di sottoscrizione.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Per gli utenti di Azure con contratto Enterprise Agreement, non vi sono variazioni riguardo alla capacità tecnica di inviare posta elettronica senza usare un servizio di inoltro autenticato. Gli utenti con contratto Enterprise Agreement, sia nuovi che esistenti, possono tentare il recapito di posta elettronica in uscita direttamente dalle macchine virtuali di Azure a provider di posta elettronica esterni, senza restrizioni da parte della piattaforma Azure. Anche se non è garantito che i provider di posta elettronica accettino posta elettronica in ingresso da un utente qualsiasi, i tentativi di recapito non verranno bloccati dalla piattaforma Azure per le macchine virtuali nell'ambito di sottoscrizioni Enterprise Agreement. Sarà necessario collaborare direttamente con i provider di posta elettronica per risolvere eventuali errori di recapito di messaggi o di filtro di posta indesiderata che interessano tali provider.

## <a name="pay-as-you-go"></a>Pagamento in base al consumo
Se si è eseguita l'iscrizione a offerte di sottoscrizione con pagamento in base al consumo o Microsoft Partner Network prima del 15 novembre 2017, non vi saranno variazioni riguardo alla capacità tecnica di tentare il recapito di posta elettronica in uscita. Si continuerà ad avere la possibilità di tentare il recapito di posta elettronica in uscita dalle macchine virtuali di Azure nell'ambito di queste sottoscrizioni direttamente a provider di posta elettronica esterni, senza restrizioni da parte della piattaforma Azure. Anche in questo caso, non è garantito che i provider di posta elettronica accettino posta elettronica in ingresso da un utente qualsiasi e gli utenti dovranno collaborare direttamente con i provider di posta elettronica per risolvere eventuali errori di recapito di messaggi o di filtro di posta indesiderata che interessano tali provider.

Per le sottoscrizioni con pagamento in base al consumo o Microsoft Partner Network create dopo il 15 novembre 2017, saranno presenti restrizioni tecniche che bloccano la posta elettronica inviata direttamente dalle macchine virtuali nell'ambito di tali sottoscrizioni. Se si vuole avere la possibilità di inviare posta elettronica direttamente dalle macchine virtuali di Azure a provider di posta elettronica esterni, senza inoltro SMTP autenticato, è possibile inviare una richiesta per la rimozione della restrizione. Le richieste verranno esaminate e approvate a discrezione di Microsoft e saranno soddisfatte solo in seguito a ulteriori controlli anti-frode. Per effettuare una richiesta, aprire un caso di supporto utilizzando il tipo di problema seguente: **Technical** > **Virtual Network** > **Connectivity** > **Cannot send email (SMTP/Port 25)**. Assicurarsi di aggiungere i dettagli sul motivo per cui la propria distribuzione deve inviare messaggi direttamente a provider di posta elettronica anziché usare un servizio di inoltro autenticato.

Dopo l'esenzione di una sottoscrizione con pagamento in base al popup o di Microsoft Partner Network e l'opzione "Le macchine virtuali sono state arrestate" & 'Avviato' dal portale di Azure, tutte le macchine virtuali all'interno di tale sottoscrizione verranno esentate in futuro. L'esenzione è applicabile solo alla sottoscrizione richiesta e si applica solo al traffico della macchina virtuale instradato direttamente a Internet. Il traffico della porta di routing 25 tramite i servizi di Azure PaaS, ad esempio Firewall di [Azure,](https://azure.microsoft.com/services/azure-firewall/) non è supportato.

> [!NOTE]
> Microsoft si riserva il diritto di revocare questa esenzione nel caso in cui venga accertata una violazione delle condizioni del servizio.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark e versione di valutazione gratuita
Se si è creata una sottoscrizione a MSDN, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsorship, Azure per studenti, a una versione di valutazione gratuita o a una qualsiasi versione di Visual Studio dopo il 15 novembre 2017, verranno applicate restrizioni tecniche che bloccano la posta elettronica inviata dalle macchine virtuali nell'ambito di tali sottoscrizioni direttamente ai provider di posta elettronica. Le restrizioni vengono applicate allo scopo di evitare casi di uso improprio. Non verranno accettate richieste per rimuovere questa restrizione.

Se si usano questi tipi di sottoscrizione, si consiglia di utilizzare i servizi di inoltro SMTP, come descritto in precedenza in questo articolo o modificare il tipo di sottoscrizione.

## <a name="cloud-service-provider-csp"></a>Provider di servizi cloud

Se si usano risorse di Azure tramite CSP, è possibile richiedere al CSP di creare una richiesta di esenzione per sblocco con Microsoft per conto dell'utente se non è possibile usare un inoltro SMTP sicuro.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se è ancora necessaria assistenza, [contattare](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) il supporto tecnico per risolvere rapidamente il problema utilizzando il tipo di problema seguente: Tipo di problema di **gestione della sottoscrizione:** richiesta di abilitare il flusso di **posta elettronica della porta 25**.
