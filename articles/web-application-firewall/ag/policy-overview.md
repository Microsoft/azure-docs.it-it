---
title: Panoramica dei criteri di Web Application Firewall (WAF) di Azure
description: Questo articolo è una panoramica dei criteri globali, per sito e URI di Web Application Firewall (WAF).
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518737"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Panoramica dei criteri di Web Application Firewall (WAF) di Azure

I criteri del Web Application Firewall contengono tutte le configurazioni e le impostazioni di WAF. Sono incluse le esclusioni, le regole personalizzate, le regole gestite e così via. Questi criteri vengono quindi associati a un gateway applicazione (globale), a un listener (per sito) o a una regola basata sul percorso (per URI) per rendere effettive.

Non esiste alcun limite al numero di criteri che è possibile creare. Quando si crea un criterio, è necessario associarlo a un gateway applicazione per renderlo effettivo. Può essere associato a qualsiasi combinazione di gateway applicazione, listener e regole basate sul percorso.

## <a name="global-waf-policy"></a>Criteri WAF globali

Quando si associa un criterio WAF a livello globale, ogni sito dietro il WAF del gateway applicazione viene protetto con le stesse regole gestite, regole personalizzate, esclusioni e qualsiasi altra impostazione configurata.

Se si vuole applicare un singolo criterio a tutti i siti, è possibile associare i criteri al gateway applicazione. Per altre informazioni, vedere [creare criteri di Web Application Firewall per il gateway applicazione](create-waf-policy-ag.md) per creare e applicare un criterio WAF usando il portale di Azure. 

## <a name="per-site-waf-policy"></a>Criteri WAF per sito

Con i criteri WAF per sito, è possibile proteggere più siti con esigenze di sicurezza diverse dietro un unico WAF usando i criteri per sito. Ad esempio, se dietro il WAF ci sono cinque siti, è possibile avere cinque criteri WAF distinti, uno per ogni listener, per personalizzare le esclusioni, le regole personalizzate, i set di regole gestite e tutte le altre impostazioni WAF per ogni sito.

Supponiamo che al gateway applicazione sia stato applicato un criterio globale. È necessario applicare quindi un criterio diverso a un listener in quel gateway applicazione. I criteri del listener ora diventano effettivi solo per quel listener. I criteri globali del gateway applicazione si applicano comunque a tutti gli altri listener e alle regole basate sul percorso a cui non è stato assegnato un criterio specifico.

## <a name="per-uri-policy"></a>Criteri per URI

Per una maggiore personalizzazione fino al livello URI, è possibile associare un criterio WAF a una regola basata sul percorso. Se sono presenti alcune pagine all'interno di un singolo sito che richiedono criteri diversi, è possibile apportare modifiche ai criteri di WAF che influiscono solo su un determinato URI. Questo potrebbe essere applicabile a una pagina di pagamento o di accesso o a qualsiasi altro URI che necessita di un criterio WAF ancora più specifico rispetto agli altri siti dietro la WAF.

Come per i criteri di WAF per sito, i criteri più specifici eseguono l'override di quelli meno specifici. Ciò significa che un criterio per URI in una mappa di percorso URL esegue l'override di tutti i criteri di WAF per sito o globali.

### <a name="example"></a>Esempio

Supponiamo di avere tre siti: contoso.com, fabrikam.com e adatum.com tutti dietro lo stesso gateway applicazione. Si vuole applicare un WAF a tutti e tre i siti, ma è necessario aggiungere sicurezza con adatum.com perché i clienti visitano, sfogliano e acquistano i prodotti.

È possibile applicare un criterio globale a WAF, con alcune impostazioni di base, esclusioni o regole personalizzate, se necessario, per arrestare alcuni falsi positivi dal blocco del traffico. In questo caso, non è necessario disporre di regole SQL injection globali in esecuzione perché fabrikam.com e contoso.com sono pagine statiche senza back-end SQL. Quindi, è possibile disabilitare tali regole nei criteri globali.

Questo criterio globale è adatto per contoso.com e fabrikam.com, ma è necessario prestare maggiore attenzione con adatum.com in cui vengono gestite le informazioni di accesso e i pagamenti. È possibile applicare un criterio per sito al listener Adatum e lasciare le regole SQL in esecuzione. Si supponga anche che sia presente un cookie che blocca il traffico, quindi è possibile creare un'esclusione per il cookie per arrestare il falso positivo. 

L'URI di adatum.com/payments è il punto in cui è necessario prestare attenzione. Applicare quindi un altro criterio a tale URI e lasciare tutte le regole abilitate e rimuovere anche tutte le esclusioni.

In questo esempio sono presenti criteri globali che si applicano a due siti. Si dispone di un criterio per sito che si applica a un sito e quindi a un criterio per URI che si applica a una regola specifica basata sul percorso. Per questo esempio, vedere [configurare i criteri di WAF per sito usando Azure PowerShell](per-site-policies.md) per il corrispondente PowerShell.

## <a name="existing-waf-configurations"></a>Configurazioni WAF esistenti

Tutte le nuove impostazioni WAF del Web Application Firewall (regole personalizzate, configurazioni del set di regole gestite, esclusioni e così via) esistono in un criterio WAF. Se si dispone di un WAF esistente, queste impostazioni potrebbero ancora esistere nella configurazione di WAF. Per ulteriori informazioni sul passaggio al nuovo criterio WAF, [eseguire la migrazione di WAF config a un criterio WAF](./migrate-policy.md). 


## <a name="next-steps"></a>Passaggi successivi

- [Creare criteri per sito e per URI usando Azure PowerShell](per-site-policies.md).
