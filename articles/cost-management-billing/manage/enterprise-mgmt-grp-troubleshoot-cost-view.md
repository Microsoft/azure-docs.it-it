---
title: Risolvere i problemi delle visualizzazioni dei costi aziendali di Azure
description: Informazioni su come risolvere i problemi con le visualizzazioni dei costi aziendali nel portale di Azure.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d78621697d53f429624dc22a8e91ba4367dcfd18
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093972"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Risolvere i problemi delle visualizzazioni dei costi aziendali

Nelle registrazioni Enterprise sono disponibili varie impostazioni che potrebbero impedire agli utenti di visualizzare i costi all'interno della registrazione.  Queste impostazioni vengono gestite dall'amministratore delle registrazioni. In alternativa, se la registrazione non è stata acquistata direttamente tramite Microsoft, le impostazioni vengono gestite dal partner.  Questo articolo illustra queste impostazioni e l'impatto che hanno sulla registrazione. Queste impostazioni sono indipendenti dai ruoli di Azure.

## <a name="enable-access-to-costs"></a>Abilitare l'accesso ai costi

Viene visualizzato un messaggio Non autorizzato o in cui è indicato che *le visualizzazioni dei costi sono disabilitate nella registrazione* quando si cercano le informazioni sui costi?
![Screenshot che mostra il messaggio "non autorizzato" nel campo del costo corrente per la sottoscrizione.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Il problema può dipendere da una delle cause seguenti:

1. Azure è stato acquistato tramite un partner Enterprise, il quale non ha ancora rilasciato i prezzi. Contattare il partner per eseguire l'aggiornamento dell'impostazione dei prezzi in [Enterprise Portal](https://ea.azure.com).
2. I clienti diretti con Contratto Enterprise hanno altre due possibilità:
    * Si è un proprietario di un account e l'amministratore delle registrazioni ha disabilitato l'impostazione di **visualizzazione dei costi per i proprietari degli account**.  
    * Si è un amministratore di reparto e l'amministratore delle registrazioni ha disabilitato l'impostazione di **visualizzazione dei costi per gli amministratori di reparto**.
    * Contattare l'amministratore delle registrazioni per ottenere l'accesso. L'amministratore delle registrazioni può aggiornare le impostazioni nell'[Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Screenshot che mostra le impostazioni dell'Enterprise Portal per la visualizzazione degli addebiti.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>La risorsa non è disponibile.

Se viene visualizzato un messaggio di errore del tipo **Questa risorsa non è disponibile** quando si prova ad accedere a una sottoscrizione o a un gruppo di gestione, non si dispone del ruolo corretto per visualizzare questo elemento.  

![Screenshot con il messaggio indicante che la risorsa non è disponibile.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Chiedere l'accesso all'amministratore del gruppo di gestione o della sottoscrizione di Azure. Per altre informazioni, vedere [assegnare i ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi
- In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
