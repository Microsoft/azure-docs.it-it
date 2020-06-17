---
title: Confrontare i servizi basati su Active Directory in Azure | Microsoft Docs
description: In questa panoramica vengono confrontate le diverse offerte di gestione delle identità relativamente ad Active Directory Domain Services, Azure Active Directory e Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: 013b78e0e8ad47e98b1d192bfc48c5c4a4de0163
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84555135"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Confrontare soluzioni Active Directory Domain Services autogestite, Azure Active Directory e Azure Active Directory Domain Services gestite

Per offrire ad applicazioni, servizi o dispositivi l'accesso a un'identità centrale usando servizi basati su Active Directory in Azure, esistono tre modi comuni. Questa possibilità di scelta tra soluzioni di gestione delle identità consente di usare in modo flessibile la directory più appropriata per le esigenze dell'organizzazione. Se si gestiscono prevalentemente utenti solo cloud che eseguono dispositivi mobili, ad esempio, potrebbe non essere opportuno creare ed eseguire una propria soluzione di gestione delle identità Active Directory Domain Services. Si potrebbe invece usare semplicemente Azure Active Directory.

Nonostante condividano nome e tecnologia, le tre soluzioni di gestione delle identità basate su Active Directory sono progettate per offrire servizi che soddisfano esigenze diverse dei clienti. A livello generale, queste soluzioni di gestione delle identità offrono i set di funzionalità seguenti.

* **Active Directory Domain Services**: server LDAP (Lightweight Directory Access Protocol) di livello aziendale che offre funzionalità chiave come identità e autenticazione, gestione di oggetti computer, Criteri di gruppo e trust.
    * Active Directory Domain Services è un componente centrale in molte organizzazioni con un ambiente IT locale e offre funzionalità di base per l'autenticazione degli account utente e la gestione dei computer.
    * Per altre informazioni, vedere la [panoramica di Active Directory Domain Services nella documentazione di Windows Server][overview-adds].
* **Azure Active Directory (Azure AD)** : gestione di dispositivi mobili e identità basata sul cloud che offre servizi di autenticazione e account utente per risorse come Office 365, il portale di Azure o le applicazioni SaaS.
    * È possibile sincronizzare Azure AD con un ambiente Active Directory Domain Services locale per fornire una singola identità agli utenti che lavorano in modo nativo nel cloud.
    * Per altre informazioni su Azure AD, vedere [Informazioni su Azure Active Directory][whatis-azuread].
* **Azure Active Directory Domain Services (Azure AD DS)** : offre servizi di dominio gestiti con un sottoinsieme di funzionalità tradizionali di AD DS completamente compatibili, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM.
    * Azure AD DS si integra con Azure AD, che a sua volta è in grado di eseguire la sincronizzazione con un ambiente di AD DS locale. Questa funzionalità estende i casi d'uso di gestione centrale delle identità alle applicazioni Web tradizionali eseguite in Azure come parte di una strategia lift-and-shift.
    * Per altre informazioni sulla sincronizzazione con Azure AD e in locale, vedere [Sincronizzazione di oggetti e credenziali in un dominio gestito][synchronization].

Questo articolo di panoramica mette a confronto il modo in cui queste soluzioni di gestione delle identità possono interagire o essere usate in modo indipendente, a seconda delle esigenze dell'organizzazione.

> [!div class="nextstepaction"]
> [Per iniziare, creare un dominio gestito di Azure Active Directory Domain Services usando il portale di Azure][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS e soluzione Active Directory Domain Services autogestita

Se si usano applicazioni e servizi che devono accedere a meccanismi di autenticazione tradizionali come Kerberos o NTLM, è possibile offrire Active Directory Domain Services nel cloud in due modi:

* *Dominio gestito* creato con Azure Active Directory Domain Services. Microsoft crea e gestisce le risorse necessarie.
* Dominio *autogestito* creato e configurato usando risorse tradizionali come le macchine virtuali, il sistema operativo guest Windows Server e Active Directory Domain Services. Si continuerà quindi ad amministrare queste risorse.

Con Azure AD DS, i componenti di base del servizio vengono distribuiti e gestiti da Microsoft in un'esperienza di *dominio gestito*. Non si devono eseguire la distribuzione, la gestione, l'applicazione di patch e la protezione dell'infrastruttura di Active Directory Domain Services per componenti come le VM, il sistema operativo Windows Server o i controller di dominio.

Azure AD DS offre un subset ridotto di funzionalità rispetto a un ambiente Active Directory Domain Services autogestito tradizionale, eliminando così parte della complessità di progettazione e di gestione. Non è ad esempio necessario progettare e gestire foreste, domini, siti e collegamenti di replica di Active Directory. È comunque possibile [creare trust tra foreste tra Azure Active Directory Domain Services e gli ambienti locali (attualmente disponibile in anteprima)][create-forest-trust].

Per le applicazioni e i servizi eseguiti nel cloud che devono accedere a meccanismi di autenticazione tradizionali come Kerberos o NTLM, Azure AD DS offre un'esperienza di dominio gestito con carico amministrativo minimo. Per altre informazioni, vedere [Concetti relativi alla gestione di account utente, password e amministrazione in Azure Active Directory Domain Services][administration-concepts].

Quando si distribuisce e si esegue un ambiente Active Directory Domain Services autogestito, è necessario gestire tutti i componenti associati dell'infrastruttura e delle directory. Un ambiente Active Directory Domain Services autogestito comporta un carico di manutenzione aggiuntivo, ma consente di eseguire attività supplementari come estendere lo schema o creare trust tra foreste.

Di seguito sono illustrati i modelli di distribuzione comuni per un ambiente Active Directory Domain Services autogestito che fornisce identità ad applicazioni e servizi nel cloud.

* **Ambiente AD DS solo cloud autonomo**: le VM di Azure sono configurate come controller di dominio e viene creato un ambiente di AD DS solo cloud separato, non integrato con un ambiente Active Directory Domain Services locale. Per accedere alle VM nel cloud e amministrarle viene usato un diverso set di credenziali.
* **Distribuzione in una foresta di risorse**: le VM di Azure sono configurate come controller di dominio e viene creato un dominio di AD DS come parte di una foresta esistente. Viene quindi configurata una relazione di trust con un ambiente Active Directory Domain Services locale. È possibile aggiungere altre VM di Azure al dominio nella foresta di risorse nel cloud. L'autenticazione utente viene eseguire tramite una connessione VPN/ExpressRoute all'ambiente Active Directory Domain Services locale.
* **Estensione del dominio locale in Azure**: una rete virtuale di Azure si connette alla rete locale con una connessione VPN/ExpressRoute. Le VM di Azure si connettono alla rete virtuale di Azure e tramite questa possono essere aggiunte a un dominio nell'ambiente Active Directory Domain Services locale.
    * In alternativa, è possibile creare le VM di Azure e alzarle di livello al ruolo di controller di dominio di replica dal dominio di Active Directory Domain Services locale. Questi controller di dominio eseguono la replica tramite una connessione VPN/ExpressRoute all'ambiente Active Directory Domain Services locale. Il dominio di Active Directory Domain Services locale viene di fatto esteso in Azure.

La tabella seguente illustra alcune funzionalità che potrebbero essere necessarie per l'organizzazione e le differenze tra un dominio di Azure AD DS gestito e un dominio di Active Directory Domain Services autogestito:

| **Funzionalità** | **Azure AD DS** | **Active Directory Domain Services autogestito** |
| ----------- |:---------------:|:----------------------:|
| **Servizio gestito**                               | **&#x2713;** | **&#x2715;** |
| **Distribuzioni sicure**                            | **&#x2713;** | L'amministratore protegge la distribuzione |
| **Server DNS**                                    | **&#x2713;** (servizio gestito) |**&#x2713;** |
| **Privilegi di amministratore di dominio o dell'organizzazione** | **&#x2715;** | **&#x2713;** |
| **Aggiunta a un dominio**                                   | **&#x2713;** | **&#x2713;** |
| **Autenticazione di dominio con NTLM e Kerberos** | **&#x2713;** | **&#x2713;** |
| **Delega vincolata Kerberos**               | Basata sulle risorse | Basata sulle risorse e basata sull'account|
| **Struttura personalizzata per le unità organizzative**                           | **&#x2713;** | **&#x2713;** |
| **Criteri di gruppo**                                  | **&#x2713;** | **&#x2713;** |
| **Estensioni dello schema**                             | **&#x2715;** | **&#x2713;** |
| **Trust tra foreste/domini di AD**                     | **&#x2713;** (solo trust tra foreste in uscita unidirezionali) | **&#x2713;** |
| **LDAP sicuro (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **Lettura LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Scrittura LDAP**                                    | **&#x2713;** (nel dominio gestito) | **&#x2713;** |
| **Distribuzioni geograficamente distribuite**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS e Azure AD

Azure AD consente di gestire l'identità dei dispositivi usati dall'organizzazione e di controllare l'accesso alle risorse aziendali da tali dispositivi. Gli utenti possono anche registrare un dispositivo personale (modello Bring Your Own Device, BYOD) in Azure AD, assegnando così un'identità al dispositivo. Azure AD autentica quindi il dispositivo quando un utente esegue l'accesso ad Azure AD e lo usa per accedere a risorse protette. Il dispositivo può essere gestito con software MDM (Mobile Device Management) come Microsoft Intune. Questa possibilità di gestione consente di limitare l'accesso a risorse sensibili ai dispositivi gestiti e conformi ai criteri.

È possibile aggiungere ad Azure AD anche computer e portatili tradizionali. Questo meccanismo offre gli stessi vantaggi della registrazione di un dispositivo personale in Azure AD, ad esempio per consentire agli utenti di accedere al dispositivo con le credenziali aziendali.

I dispositivi aggiunti ad Azure AD offrono i vantaggi seguenti:

* Accesso Single Sign-On (SSO) alle applicazioni protette da Azure AD.
* Roaming conforme ai criteri dell'organizzazione per le impostazioni utente su più dispositivi.
* Accesso a Windows Store per le aziende con le credenziali aziendali.
* Windows Hello for Business.
* Accesso alle app e alle risorse solo tramite dispositivi conformi ai criteri aziendali.

I dispositivi possono essere aggiunti ad Azure AD con o senza una distribuzione ibrida che include un ambiente Active Directory Domain Services locale. La tabella seguente illustra i modelli comuni di proprietà dei dispositivi e come vengono in genere aggiunti a un dominio:

| **Tipo di dispositivo**                                        | **Piattaforme**             | **Meccanismo**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Dispositivi personali                                          | Windows 10, iOS, Android, macOS | Registrazione in Azure AD    |
| Dispositivo di proprietà dell'organizzazione non aggiunto a un'istanza locale di AD DS | Windows 10                       | Aggiunta ad Azure AD        |
| Dispositivo di proprietà dell'organizzazione aggiunto a un'istanza locale di AD DS  | Windows 10                       | Aggiunta a Azure AD ibrido |

In un dispositivo registrato o aggiunto ad Azure AD, l'autenticazione utente viene eseguita con protocolli moderni basati su OAuth/OpenID Connect. Questi protocolli, progettati per funzionare via Internet, sono ideali in scenari per dispositivi mobili in cui gli utenti accedono ovunque alle risorse aziendali.

Con dispositivi aggiunti ad Azure AD DS, le applicazioni possono usare i protocolli Kerberos e NTLM per l'autenticazione ed è quindi possibile supportare applicazioni legacy sottoposte a migrazione per l'esecuzione in VM di Azure come parte di una strategia lift-and-shift. La tabella seguente illustra le differenze nel modo in cui i dispositivi vengono rappresentati e possono eseguire l'autenticazione nella directory:

| **Aspetto**                      | **Aggiunta ad Azure AD**                                 | **Aggiunta ad Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Dispositivo controllato da            | Azure AD                                            | Dominio gestito di Azure AD DS                                                |
| Rappresentazione nella directory | Oggetti dispositivo nella directory di Azure AD            | Oggetti computer nel dominio gestito di Azure AD DS                        |
| Authentication                  | Protocolli basati su OAuth/OpenID Connect              | Protocolli Kerberos e NTLM                                               |
| Gestione                      | Software di gestione di dispositivi mobili (MDM), ad esempio Intune | Criteri di gruppo                                                              |
| Rete                      | Funziona attraverso Internet                             | Deve essere connesso o associato alla rete virtuale in cui è distribuito il dominio gestito |
| Ideale per...                    | Dispositivi mobili o desktop degli utenti finali                  | VM server distribuite in Azure                                              |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure AD DS, [creare un dominio gestito di Azure AD DS con il portale di Azure][tutorial-create].

Per altre informazioni, vedere [Concetti relativi alla gestione di account utente, password e amministrazione in Azure Active Directory Domain Services][administration-concepts] e [Sincronizzazione di oggetti e credenziali in un dominio gestito][synchronization].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
