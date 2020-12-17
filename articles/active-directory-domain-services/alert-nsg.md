---
title: Risolvere gli avvisi del gruppo di sicurezza di rete in Azure AD DS | Microsoft Docs
description: Informazioni su come risolvere i problemi e risolvere gli avvisi di configurazione del gruppo di sicurezza di rete per Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 58cdd025587823f7eb702164c965ab622a7325d3
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97615648"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Problemi noti: avvisi di configurazione di rete in Azure Active Directory Domain Services

Per consentire alle applicazioni e ai servizi di comunicare correttamente con un dominio gestito di Azure Active Directory Domain Services (Azure AD DS), è necessario aprire porte di rete specifiche per consentire il flusso del traffico. In Azure è possibile controllare il flusso di traffico usando i gruppi di sicurezza di rete. Lo stato di integrità di un dominio gestito di Azure AD DS Mostra un avviso se le regole del gruppo di sicurezza di rete richieste non sono presenti.

Questo articolo aiuta a comprendere e risolvere gli avvisi comuni per i problemi di configurazione dei gruppi di sicurezza di rete.

## <a name="alert-aadds104-network-error"></a>Avviso AADDS104: errore di rete

### <a name="alert-message"></a>Messaggio di avviso

*Microsoft non è in grado di raggiungere i controller di dominio per questo dominio gestito. Questo problema può verificarsi se un gruppo di sicurezza di rete (NSG) configurato nella rete virtuale blocca l'accesso al dominio gestito. Un altro motivo possibile è se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.*

Le regole del gruppo di sicurezza di rete non valide sono le cause più comuni degli errori di rete per Azure AD DS. Il gruppo di sicurezza di rete per la rete virtuale deve consentire l'accesso a porte e protocolli specifici. Se queste porte sono bloccate, la piattaforma Azure non è in grado di monitorare o aggiornare il dominio gestito. Anche la sincronizzazione tra la directory di Azure AD e Azure AD DS ha un effetto. Assicurarsi di lasciare aperte le porte predefinite per evitare interruzioni del servizio.

## <a name="default-security-rules"></a>Regole di sicurezza predefinite

Le seguenti regole di sicurezza predefinite in ingresso e in uscita vengono applicate al gruppo di sicurezza di rete per un dominio gestito. Queste regole mantengono Azure AD DS sicuro e consentono alla piattaforma Azure di monitorare, gestire e aggiornare il dominio gestito.

### <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| Priorità | Nome | Porta | Protocollo | Source (Sorgente) | Destination | Azione |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Qualsiasi | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Qualsiasi | Nega<sup>1</sup> |
| 65000    | AllVnetInBound | Qualsiasi | Qualsiasi | VirtualNetwork | VirtualNetwork | Consenti |
| 65001    | AllowAzureLoadBalancerInBound | Qualsiasi | Qualsiasi | AzureLoadBalancer | Qualsiasi | Allow |
| 65500    | DenyAllInBound | Qualsiasi | Qualsiasi | Qualsiasi | Qualsiasi | Nega |
<sup>1</sup> Facoltativo per il debug. Consenti quando necessario per la risoluzione dei problemi avanzata.

> [!NOTE]
> È anche possibile che si disponga di una regola aggiuntiva che consente il traffico in ingresso se si [Configura LDAP sicuro][configure-ldaps]. Questa regola aggiuntiva è obbligatoria per la comunicazione LDAPs corretta.

### <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| Priorità | Nome | Porta | Protocollo | Source (Sorgente) | Destination | Azione |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Qualsiasi | Qualsiasi | VirtualNetwork | VirtualNetwork | Consenti |
| 65001    | AllowAzureLoadBalancerOutBound | Qualsiasi | Qualsiasi |  Qualsiasi | Internet | Consenti |
| 65500    | DenyAllOutBound | Qualsiasi | Qualsiasi | Qualsiasi | Qualsiasi | Nega |

>[!NOTE]
> Per Azure AD DS è necessario l'accesso in uscita senza restrizioni dalla rete virtuale. Non è consigliabile creare regole aggiuntive che limitino l'accesso in uscita per la rete virtuale.

## <a name="verify-and-edit-existing-security-rules"></a>Verificare e modificare le regole di sicurezza esistenti

Per verificare le regole di sicurezza esistenti e verificare che le porte predefinite siano aperte, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **gruppi di sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*.
1. Nella pagina **Panoramica** vengono visualizzate le regole di sicurezza in ingresso e in uscita esistenti.

    Esaminare le regole in ingresso e in uscita e confrontare con l'elenco delle regole necessarie nella sezione precedente. Se necessario, selezionare e quindi eliminare eventuali regole personalizzate che bloccano il traffico necessario. Se mancano le regole necessarie, aggiungere una regola nella sezione successiva.

    Dopo l'aggiunta o l'eliminazione di regole per consentire il traffico necessario, l'integrità del dominio gestito si aggiorna automaticamente entro due ore e rimuove l'avviso.

### <a name="add-a-security-rule"></a>Aggiungere una regola di sicurezza

Per aggiungere una regola di sicurezza mancante, completare i passaggi seguenti:

1. Nella portale di Azure cercare e selezionare **gruppi di sicurezza di rete**.
1. Scegliere il gruppo di sicurezza di rete associato al dominio gestito, ad esempio *AADDS-contoso.com-NSG*.
1. In **Impostazioni** nel pannello a sinistra fare clic su *regole di sicurezza in ingresso* o *regole di sicurezza* in uscita a seconda della regola che è necessario aggiungere.
1. Selezionare **Aggiungi**, quindi creare la regola necessaria in base alla porta, al protocollo, alla direzione e così via. Quando si è pronti, fare clic su **OK**.

Sono necessari alcuni istanti per aggiungere la regola di sicurezza e visualizzarla nell'elenco.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta di supporto tecnico di Azure][azure-support] per richiedere ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
