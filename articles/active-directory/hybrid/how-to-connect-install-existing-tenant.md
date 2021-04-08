---
title: 'Azure AD Connect: Quando è già presente Azure AD | Documentazione Microsoft'
description: Questo argomento illustra come usare Connect quando è presente un tenant Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93420530"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: quando si dispone di un tenant esistente
La maggior parte degli argomenti che illustrano come usare Azure AD Connect presuppongono che si inizi con un nuovo tenant di Azure AD che non contiene utenti o altri oggetti. Questo argomento è utile se tuttavia si inizia con un tenant di Azure AD che contiene utenti e altri oggetti e si vuole usare Connect.

## <a name="the-basics"></a>Nozioni di base
Un oggetto in Azure AD viene gestito nel cloud (Azure AD) o in locale. Per un singolo oggetto non è possibile gestire alcuni attributi in locale e altri attributi in Azure AD. Ogni oggetto ha un flag che indica dove viene gestito l'oggetto.

È possibile gestire alcuni utenti in locale e altri nel cloud. Uno scenario comune per questa configurazione è un'azienda con una combinazione di contabili e addetti alla vendita. I contabili hanno un account AD locale, a differenza degli addetti alla vendita che hanno un account in Azure AD. Alcuni utenti verranno gestiti in locale, alcuni in Azure AD.

Se si iniziano a gestire in Azure AD utenti che si trovano anche nell'istanza di AD locale e successivamente si vuole usare Connect, è necessario prendere in considerazione altri aspetti.

## <a name="sync-with-existing-users-in-azure-ad"></a>Eseguire la sincronizzazione con gli utenti esistenti in Azure AD
Quando si installa Azure AD Connect e si avvia la sincronizzazione, il servizio di sincronizzazione Azure AD (in Azure AD) esegue un controllo su ogni nuovo oggetto e tenta di trovare un oggetto esistente per la corrispondenza. Vengono usati tre attributi per questo processo: **userPrincipalName**, **proxyAddresses** e **sourceAnchor**/**immutableID**. Una corrispondenza per **userPrincipalName** e **proxyAddresses** è nota come **corrispondenza flessibile**. Una corrispondenza per **sourceAnchor** è nota come **corrispondenza rigida**. Per l'attributo **proxyAddresses** solo il valore con attributo **SMTP:**, ovvero l'indirizzo di posta elettronica principale, viene usato per la valutazione.

La corrispondenza viene valutata solo per i nuovi oggetti provenienti da Connect. Se si modifica un oggetto esistente in modo che corrisponda a uno di questi attributi, verrà visualizzato un errore.

Se Azure AD rileva un oggetto in cui i valori di attributo sono gli stessi per un oggetto proveniente da Connect che è già presente in Azure AD, l'oggetto in Azure AD verrà acquisito da Connect. L'oggetto precedentemente gestito nel cloud viene contrassegnato come gestito in locale. Tutti gli attributi in Azure AD con un valore in AD locale vengono sovrascritti con il valore locale.

> [!WARNING]
> Poiché tutti gli attributi in Azure AD verranno sovrascritti con il valore locale, accertarsi che i dati locali siano corretti. Se, ad esempio, si dispone solo di un indirizzo di posta elettronica gestito in Microsoft 365 e non è stato aggiornato in servizi di dominio Active Directory in locale, si perderanno tutti i valori Azure AD/Microsoft 365 non presenti in servizi di dominio Active Directory.

> [!IMPORTANT]
> Se si usa la sincronizzazione delle password, opzione sempre usata dalle impostazioni rapide, la password in Azure AD verrà sovrascritta con la password dell'istanza di AD locale. Se gli utenti sono abituati a gestire più password, è necessario informarli che dovranno usare la password locale dopo aver installato Connect.

È necessario prendere in considerazione le indicazioni della sezione precedente nella pianificazione. Se in Azure AD sono state apportate molte modifiche che non sono state applicate nell'istanza di Active Directory Domain Services locale, è necessario pianificare come popolare Active Directory Domain Services con i valori aggiornati prima di sincronizzare gli oggetti con Azure AD Connect.

Se gli oggetti sono stati associati con una corrispondenza flessibile, verrà aggiunto **sourceAnchor** all'oggetto in Azure AD e sarà quindi possibile usare una corrispondenza rigida in seguito.

>[!IMPORTANT]
> Microsoft sconsiglia fortemente la sincronizzazione di account locali con account amministrativi preesistenti in Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Differenza tra corrispondenza rigida e corrispondenza flessibile
Per una nuova installazione di Connect, non esiste alcuna differenza pratica tra corrispondenza flessibile e corrispondenza rigida. La differenza emerge in una situazione di ripristino di emergenza. Se si è perso il server con Azure AD Connect, è possibile reinstallare una nuova istanza senza perdere i dati. Un oggetto con un attributo sourceAnchor viene inviato a Connect durante l'installazione iniziale. La corrispondenza può quindi essere valutata dal client (Azure AD Connect), che per l'operazione impiega molto meno tempo rispetto ad Azure AD. Una corrispondenza rigida viene valutata sia da Connect che da Azure AD. Una corrispondenza flessibile viene valutata solo da Azure AD.

### <a name="other-objects-than-users"></a>Oggetti diversi dagli utenti
Per i gruppi abilitati alla posta e ai contatti è possibile eseguire la corrispondenza flessibile in base a proxyAddresses. La corrispondenza rigida non è applicabile poiché è possibile aggiornare solo sourceAnchor o immutableID (tramite PowerShell) solo per Utenti. Per i gruppi che non sono abilitati per la posta non c'è attualmente alcun supporto per la corrispondenza rigida o per la corrispondenza flessibile.

### <a name="admin-role-considerations"></a>Considerazioni sui ruoli di amministratore
Per impedire che gli utenti locali non attendibili corrispondano a un utente cloud con un ruolo di amministratore, Azure AD Connect non corrisponderà a oggetti utente locali con oggetti che dispongono di un ruolo di amministratore. Questo accade per impostazione predefinita. Per aggirare questo comportamento, è possibile eseguire le operazioni seguenti:

1.  Rimuovere i ruoli della directory dall'oggetto utente solo cloud.
2.  Se si è verificato un tentativo di sincronizzazione utente non riuscito, eliminare definitivamente l'oggetto in quarantena nel cloud.
3.  Attivare una sincronizzazione.
4.  Facoltativamente, aggiungere di nuovo i ruoli della directory all'oggetto utente nel cloud dopo che è stata eseguita la corrispondenza.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Creare una nuova istanza di Active Directory locale dai dati di Azure AD
Alcuni clienti iniziano con una soluzione solo cloud con Azure AD e non hanno un'istanza di AD locale. In un secondo momento intendono usare risorse locali e creare un'istanza di AD locale basata sui dati di Azure AD. Azure AD Connect non potrà essere utile per questo scenario. Non crea gli utenti locali e non può impostare in locale la stessa password di Azure AD.

Se l'unico motivo per cui si prevede di aggiungere un'istanza di AD locale è il supporto di LOB (app line-of-business), forse è opportuno considerare invece l'uso di [Azure AD Domain Services](../../active-directory-domain-services/index.yml).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
