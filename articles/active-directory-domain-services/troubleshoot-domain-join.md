---
title: Risolvere i problemi di aggiunta al dominio con Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni quando si tenta di aggiungere un dominio a una macchina virtuale o di connettere un'applicazione a Azure Active Directory Domain Services e non è possibile connettersi o eseguire l'autenticazione al dominio gestito.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618604"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Risolvere i problemi di aggiunta al dominio con un Azure Active Directory Domain Services dominio gestito

Quando si tenta di aggiungere una macchina virtuale (VM) o di connettere un'applicazione a un dominio gestito di Azure Active Directory Domain Services (Azure AD DS), è possibile che venga ricevuto un errore che non è possibile eseguire. Per risolvere i problemi relativi all'aggiunta a un dominio, esaminare i punti seguenti in cui si è verificato un problema:

* Se non si riceve una richiesta di autenticazione, la macchina virtuale o l'applicazione non è in grado di connettersi al dominio gestito di Azure AD DS.
    * Iniziare a risolvere [i problemi di connettività per l'aggiunta al dominio](#connectivity-issues-for-domain-join).
* Se viene visualizzato un errore durante l'autenticazione, la connessione al dominio gestito ha esito positivo.
    * Iniziare a risolvere i [problemi relativi alle credenziali durante l'aggiunta al dominio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemi di connettività per l'aggiunta a un dominio

Se la macchina virtuale non riesce a trovare il dominio gestito, in genere è presente una connessione di rete o un problema di configurazione. Esaminare le seguenti procedure di risoluzione dei problemi per individuare e risolvere il problema:

1. Assicurarsi che la macchina virtuale sia connessa alla stessa rete virtuale o con peering come dominio gestito. In caso contrario, la macchina virtuale non riesce a trovare e a connettersi al dominio per potersi unire.
    * Se la VM non è connessa alla stessa rete virtuale, verificare che il peering di rete virtuale o la connessione VPN sia *attiva* o *connessa* per consentire il flusso del traffico correttamente.
1. Provare a eseguire il ping del dominio usando il nome di dominio del dominio gestito, ad esempio `ping aaddscontoso.com` .
    * Se la risposta al ping ha esito negativo, provare a effettuare il ping degli indirizzi IP per il dominio visualizzato nella pagina panoramica nel portale per il dominio gestito, ad esempio `ping 10.0.0.4` .
    * Se è possibile effettuare correttamente il ping dell'indirizzo IP ma non del dominio, il DNS potrebbe non essere configurato correttamente. Assicurarsi di aver [configurato i server DNS del dominio gestito per la rete virtuale][configure-dns].
1. Provare a scaricare la cache del resolver DNS nella macchina virtuale, ad esempio `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Configurazione del gruppo di sicurezza di rete (NSG)

Quando si crea un dominio gestito, viene creato anche un gruppo di sicurezza di rete con le regole appropriate per l'operazione di dominio riuscita. Se si modificano o si creano regole di gruppo di sicurezza di rete aggiuntive, è possibile bloccare involontariamente le porte necessarie per Azure AD DS per fornire servizi di connessione e autenticazione. Queste regole del gruppo di sicurezza di rete possono causare problemi, ad esempio il completamento della sincronizzazione delle password, gli utenti che non sono in grado di eseguire l'accesso o problemi di aggiunta al dominio.

Se continuano a verificarsi problemi di connessione, esaminare i passaggi seguenti per la risoluzione dei problemi:

1. Verificare lo stato di integrità del dominio gestito nel portale di Azure. Se è presente un avviso per *AADDS001*, una regola del gruppo di sicurezza di rete blocca l'accesso.
1. Esaminare le [porte richieste e le regole del gruppo di sicurezza di rete][network-ports]. Assicurarsi che nessuna regola del gruppo di sicurezza di rete applicata alla VM o alla rete virtuale da cui si sta eseguendo la connessione blocchi queste porte di rete.
1. Una volta risolti i problemi di configurazione dei gruppi di sicurezza di rete, l'avviso *AADDS001* scompare dalla pagina di integrità in circa 2 ore. Con la connettività di rete ora disponibile, provare a aggiungere di nuovo il dominio alla macchina virtuale.

## <a name="credentials-related-issues-during-domain-join"></a>Problemi relativi alle credenziali durante l'aggiunta a un dominio

Se viene visualizzata una finestra di dialogo in cui vengono richieste le credenziali per l'aggiunta al dominio gestito, la macchina virtuale è in grado di connettersi al dominio tramite la rete virtuale di Azure. Il processo di aggiunta al dominio non riesce a eseguire l'autenticazione al dominio o all'autorizzazione per completare il processo di aggiunta al dominio usando le credenziali fornite da.

Per risolvere i problemi relativi alle credenziali, esaminare i passaggi seguenti per la risoluzione dei problemi:

1. Provare a usare il formato UPN per specificare le credenziali, ad esempio `dee@contoso.onmicrosoft.com`. Verificare che il nome UPN sia configurato correttamente in Azure AD.
    * Il *sAMAccountName* per l'account può essere generato automaticamente se sono presenti più utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo. Pertanto, il formato di *sAMAccountName* per l'account può essere diverso da quello previsto o da usare nel dominio locale.
1. Provare a usare le credenziali per un account utente che fa parte del dominio gestito per aggiungere le macchine virtuali al dominio gestito.
1. Assicurarsi di aver abilitato la [sincronizzazione delle password][enable-password-sync] e di avere atteso il tempo sufficiente per il completamento della sincronizzazione iniziale delle password.

## <a name="next-steps"></a>Passaggi successivi

Per una conoscenza più approfondita dei processi di Active Directory come parte dell'operazione di aggiunta al dominio, vedere [problemi di join e di autenticazione][join-authentication-issues].

Se si verificano ancora problemi di aggiunta della macchina virtuale al dominio gestito, [vedere la guida e aprire un ticket di supporto per Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
