---
title: Impostazioni dei criteri per il Web Application Firewall con lo sportello anteriore di Azure
description: Informazioni sul Web Application Firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75932607"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Impostazioni dei criteri per il Web Application Firewall in Azure front door

Un criterio di Web Application Firewall (WAF) consente di controllare l'accesso alle applicazioni Web tramite un set di regole personalizzate e gestite. Il nome del criterio WAF deve essere univoco. Se si tenta di usare un nome esistente, verrà visualizzato un errore di convalida. Sono disponibili più impostazioni a livello di criteri che si applicano a tutte le regole specificate per il criterio, come descritto in questo articolo.

## <a name="waf-state"></a>Stato WAF

Un criterio WAF per lo sportello anteriore può trovarsi in uno dei due stati seguenti:
- **Abilitato:** Quando un criterio è abilitato, WAF controlla attivamente le richieste in ingresso e accetta le azioni corrispondenti secondo le definizioni delle regole
- **Disabled:** -quando un criterio è disabilitato, l'ispezione WAF viene sospesa. Le richieste in ingresso ignoreranno WAF e vengono inviate ai back-end in base al routing della porta anteriore.

## <a name="waf-mode"></a>Modalità WAF

I criteri di WAF possono essere configurati per l'esecuzione nelle due modalità seguenti:

- **Modalità di rilevamento** Quando viene eseguita in modalità di rilevamento, WAF non esegue alcuna azione diversa da monitoraggio e registrazione della richiesta e della relativa regola WAF corrispondente ai log di WAF. Abilitare la registrazione della diagnostica per lo sportello anteriore (quando si usa il portale, per ottenere questo risultato, passare alla sezione **diagnostica** nella portale di Azure).

- **Modalità di prevenzione** Se configurato per l'esecuzione in modalità di prevenzione, WAF esegue l'azione specificata se una richiesta corrisponde a una regola. Tutte le richieste corrispondenti vengono anche registrate nei log di WAF.

## <a name="waf-response-for-blocked-requests"></a>Risposta WAF per le richieste bloccate

Per impostazione predefinita, quando WAF blocca una richiesta a causa di una regola corrispondente, restituisce un codice di stato 403 con **il messaggio di richiesta è bloccato** . Per la registrazione viene restituita anche una stringa di riferimento.

È possibile definire un codice di stato della risposta personalizzato e un messaggio di risposta quando una richiesta viene bloccata da WAF. Sono supportati i codici di stato personalizzati seguenti:

- 200 OK
- 403 non consentito
- 405 metodo non consentito
- 406 non accettabile
- 429 numero eccessivo di richieste

Il codice di stato della risposta personalizzata e il messaggio di risposta è un'impostazione a livello di criteri. Una volta configurata, tutte le richieste bloccate ottengono lo stesso stato di risposta personalizzato e il messaggio di risposta.

## <a name="uri-for-redirect-action"></a>URI per azione di Reindirizzamento

È necessario definire un URI per il reindirizzamento delle richieste a se l'azione di **Reindirizzamento** è selezionata per le regole contenute in un criterio WAF. Questo URI di reindirizzamento deve essere un sito HTTP (S) valido e, una volta configurata, tutte le richieste regole di corrispondenza con un'azione di reindirizzamento verranno reindirizzate al sito specificato.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come definire [risposte personalizzate](waf-front-door-configure-custom-response-code.md) WAF
