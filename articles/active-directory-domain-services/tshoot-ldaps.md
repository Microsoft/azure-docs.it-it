---
title: Risolvere i problemi relativi a LDAP sicuro in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi a LDAP sicuro (LDAPs) per un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: c339893c12af74b5ecadb182b9aecb8000df65b9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86222956"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Risolvere i problemi di connettività LDAP sicura a un Azure Active Directory Domain Services dominio gestito

Le applicazioni e i servizi che usano LDAP (Lightweight Directory Access Protocol) per la comunicazione con Azure Active Directory Domain Services (Azure AD DS) possono essere [configurati per l'uso di LDAP sicuro](tutorial-configure-ldaps.md). Per il corretto funzionamento di LDAP sicuro, è necessario aprire un certificato appropriato e le porte di rete necessarie.

Questo articolo consente di risolvere i problemi relativi all'accesso LDAP sicuro in Azure AD DS.

## <a name="common-connection-issues"></a>Problemi di connessione comuni

Se si verificano problemi durante la connessione a un dominio gestito di Azure AD DS usando Secure LDAP, vedere la procedura seguente per la risoluzione dei problemi. Dopo ogni passaggio di risoluzione dei problemi, provare a connettersi di nuovo al dominio gestito:

* La catena di autorità di certificazione del certificato LDAP sicuro deve essere considerata attendibile nel client. È possibile aggiungere l'autorità di certificazione radice (CA) all'archivio certificati radice attendibili nel client per stabilire la relazione di trust.
    * Assicurarsi [di esportare e applicare il certificato ai computer client][client-cert].
* Verificare che il certificato LDAP sicuro per il dominio gestito abbia il nome DNS nell'attributo *Subject* o nome *alternativo soggetto* .
    * Esaminare i [requisiti dei certificati LDAP protetti][certs-prereqs] e creare un certificato sostitutivo, se necessario.
* Verificare che il client LDAP, ad esempio *ldp.exe* si connetta all'endpoint LDAP sicuro usando un nome DNS e non l'indirizzo IP.
    * Il certificato applicato al dominio gestito non include gli indirizzi IP del servizio, ma solo i nomi DNS.
* Controllare il nome DNS a cui si connette il client LDAP. È necessario risolvere l'indirizzo IP pubblico per l'accesso LDAP sicuro nel dominio gestito.
    * Se il nome DNS viene risolto nell'indirizzo IP interno, aggiornare il record DNS per risolvere l'indirizzo IP esterno.
* Per la connettività esterna, il gruppo di sicurezza di rete deve includere una regola che consenta il traffico sulla porta TCP 636 da Internet.
    * Se è possibile connettersi al dominio gestito usando l'accesso LDAP sicuro dalle risorse direttamente connesse alla rete virtuale ma non alle connessioni esterne, assicurarsi [di creare una regola del gruppo di sicurezza di rete per consentire il traffico LDAP sicuro][ldaps-nsg].

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta di supporto tecnico di Azure][azure-support] per richiedere ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
