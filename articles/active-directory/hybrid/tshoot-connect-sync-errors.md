---
title: 'Azure AD Connect: Risoluzione dei problemi durante la sincronizzazione | Microsoft Docs'
description: Questo articolo descrive come risolvere i problemi che si verificano durante la sincronizzazione con Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd779c26cd523bbf33fa1be6c87f21b4415c152
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016419"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Risoluzione degli problemi durante la sincronizzazione
Gli errori potrebbero verificarsi durante la sincronizzazione dei dati dell'identità da Windows Server Active Directory (AD DS) ad Azure Active Directory (Azure AD). L'articolo offre una panoramica delle diverse tipologie di errori di sincronizzazione, di alcuni scenari possibili che causano tali errori e delle possibili soluzioni per correggere questi errori. In questo articolo vengono descritti soltanto gli errori più comuni.

 L'articolo presuppone che il lettore abbia sufficiente familiarità con [i basilari concetti di progettazione di Azure AD e Azure AD Connect](plan-connect-design-concepts.md).

Con la versione più recente di Azure AD Connect \( agosto 2016 o successiva \) , un report degli errori di sincronizzazione è disponibile nell' [portale di Azure](https://aka.ms/aadconnecthealth) come parte del Azure ad Connect Health per la sincronizzazione.

A partire dal 1 settembre 2016 la funzionalità [Azure Active Directory Duplicate Attribute Resiliency](how-to-connect-syncservice-duplicate-attribute-resiliency.md) (Resilienza degli attributi duplicati di Azure Active Directory) verrà abilitata per impostazione predefinita per tutti i *nuovi* tenant di Azure Active Directory. Nei prossimi mesi questa funzionalità verrà abilitata automaticamente per i tenant esistenti.

Azure AD Connect esegue tre tipi di operazioni dalle directory che mantiene sincronizzate: importazione, sincronizzazione ed esportazione. Gli errori possono verificarsi durante tutte le operazioni. L'articolo si concentra prevalentemente sugli errori che si verificano durante l'esportazione in Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Errori durante l'esportazione in Azure AD
La sezione seguente descrive le differenti tipologie di errori di sincronizzazione che possono verificarsi durante l'esportazione in Azure AD usando il connettore di Azure AD, identificabile dal formato del nome: "contoso.*onmicrosoft.com*".
Quando si verificano un errore durante l'esportazione in Azure AD significa che l'operazione di \(aggiunta, aggiornamento, eliminazione e così via\) tentata dal \(motore di sincronizzazione\) di Azure AD Connect in Azure Active Directory non è andata a buon fine.

![Panoramica degli errori di esportazione](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Errori di mancata corrispondenza dei dati
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrizione
* Quando il \(motore di sincronizzazione\) di Azure AD Connect invia il comando ad Azure Active Directory di aggiungere o aggiornare gli oggetti, Azure AD associa l'oggetto in ingresso usando l'attributo **sourceAnchor** all'attributo **immutableId** degli oggetti presenti in Azure AD. Tale corrispondenza viene detta **corrispondenza rigida**.
* Quando Azure AD **non trova** alcun oggetto che corrisponda all'attributo **immutableId** con l'attributo **sourceAnchor** dell'oggetto in ingresso, prima di eseguire il provisioning di un nuovo oggetto, esegue il fallback per usare gli attributi UserPrincipalName e ProxyAddresses per trovare una corrispondenza. Tale corrispondenza viene detta **corrispondenza flessibile**. La corrispondenza flessibile è pensata per associare gli oggetti già presenti in Azure AD (che sono distribuiti in Azure AD) ai nuovi oggetti aggiunti o aggiornati durante la sincronizzazione che rappresentano la stessa entità (utenti, gruppi) in locale.
* L'errore **InvalidSoftMatch** si verifica quando la corrispondenza rigida non trova oggetti corrispondenti **E** la corrispondenza flessibile trova un oggetto corrispondente, che però ha un differente valore dell'attributo *immutableId* rispetto all'attributo *SourceAnchor* dell'oggetto in ingresso, indicando che l'oggetto corrispondente è stato sincronizzato con un altro oggetto dall'Active Directory locale.

In altre parole, affinché la corrispondenza flessibile funzioni, l'oggetto con cui stabilire una corrispondenza di questo tipo non deve avere alcun valore per l'attributo *immutableId*. Se la corrispondenza rigida di un qualsiasi oggetto con l'attributo *immutableId* impostato con un valore non riesce, ma tale oggetto soddisfa i criteri della corrispondenza flessibile, l'operazione restituirà come risultato un errore di sincronizzazione di tipo InvalidSoftMatch.

Lo schema di Azure Active Directory non consente a due o più oggetti di avere lo stesso valore degli attributi seguenti, \)L'elenco è incompleto.\(

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> La funzionalità [Resilienza degli attributi duplicati di Azure Active Directory](how-to-connect-syncservice-duplicate-attribute-resiliency.md) viene anche implementata come comportamento predefinito di Azure Active Directory.  Ciò ridurrà il numero degli errori di sincronizzazione rilevati da Azure AD Connect (e da altri client di sincronizzazione), rendendo Azure AD più resiliente nella modalità di gestione degli attributi ProxyAddresses e UserPrincipalName duplicati che sono presenti negli ambienti AD locali. Questa funzionalità non risolve gli errori di duplicazione, pertanto c'è ancora bisogno di correggere i dati. Tuttavia, questa funzionalità consente il provisioning di nuovi oggetti per i quali, altrimenti, viene bloccato il provisioning a causa dei valori duplicati in Azure AD. Ciò riduce anche il numero di errori di sincronizzazione restituito al client di sincronizzazione.
> Se questa funzionalità è abilitata per il tenant, non si verificheranno gli errori di sincronizzazione InvalidSoftMatch rilevati durante il provisioning di nuovi oggetti.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Scenari di esempio per InvalidSoftMatch
1. Nell'Active Directory locale sono presenti due o più oggetti con lo stesso valore dell'attributo ProxyAddresses. ma il provisioning viene eseguito in Azure AD soltanto per un oggetto.
2. Nell'Active Directory locale sono presenti due o più oggetti con lo stesso valore dell'attributo userPrincipalName. ma il provisioning viene eseguito in Azure AD soltanto per un oggetto.
3. Un oggetto è stato aggiunto all'Active Directory locale con lo stesso valore dell'attributo ProxyAddresses a quello di un oggetto già presente in Azure Active Directory. Per l'oggetto aggiunto in locale non viene eseguito il provisioning in Azure Active Directory.
4. Un oggetto è stato aggiunto all'Active Directory locale con lo stesso valore dell'attributo userPrincipalName a quello di un account presente in Azure Active Directory. Per l'oggetto non viene eseguito il provisioning in Azure Active Directory.
5. Un account sincronizzato è stato spostato da Foresta A a Foresta B. Azure AD Connect (motore di sincronizzazione) stava usando l'attributo ObjectGUID per calcolare l'attributo SourceAnchor. Dopo lo spostamento della foresta, il valore dell'attributo SourceAnchor è differente. La sincronizzazione del nuovo oggetto (da Foresta B) con l'oggetto esistente in Azure AD non riesce.
6. Un oggetto sincronizzato è stato accidentalmente eliminato dall'Active Directory locale e un nuovo oggetto è stato creato in Active Directory per la stessa entità (ad esempio un utente) senza eliminare l'account in Azure Active Directory. La sincronizzazione del nuovo account con l'oggetto di Azure AD esistente non riesce.
7. Azure AD Connect è stato disinstallato e reinstallato. Durante la reinstallazione, come attributo SourceAnchor è stato scelto un attributo differente. La sincronizzazione di tutti gli oggetti già sincronizzati in precedenza è stata interrotta restituendo l'errore InvalidSoftMatch.

#### <a name="example-case"></a>Caso di esempio:
1. **Bob Smith** è un utente sincronizzato in Azure Active Directory dall'Active Directory locale di *contoso.com*
2. Il **userPrincipalName** di Bob Smith è impostato come **Bob \@ contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** è l'attributo **SourceAnchor** calcolato da Azure AD Connect usando l'attributo **objectGUID** di Bob Smith dall'Active Directory locale, che corrisponde all'attributo **immutableId** di Bob Smith in Azure Active Directory.
4. Bob dispone anche dei seguenti valori per l'attributo **proxyAddresses**:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
5. Un nuovo utente, **Bob Taylor**, viene aggiunto all'Active Directory locale.
6. Il **userPrincipalName** di Bob Taylor è impostato come **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** è l'attributo **sourceAnchor** calcolato da Azure AD Connect usando l'attributo **objectGUID** di Bob Taylor dall'Active Directory locale. L'oggetto di Bob Taylor NON è stato ancora sincronizzato ad Azure Active Directory.
8. Bob Taylor dispone dei valori seguenti per l'attributo proxyAddresses
   * SMTP: bobt@contoso.com
   * SMTP: bob.taylor@contoso.com
   * **SMTP: Bob \@ contoso.com**
9. Durante la sincronizzazione, Azure AD Connect riconoscerà l'aggiunta di Bob Taylor nell'Active Directory locale e chiederà ad Azure AD di apportare la stessa modifica.
10. Azure AD eseguirà prima una corrispondenza rigida, ossia eseguirà la ricerca per vedere se esiste un qualsiasi oggetto con l'attributo immutableId uguale a "abcdefghijkl0123456789==". La corrispondenza rigida avrà esito negativo se nessun altro oggetto in Azure AD avrà quell'attributo immutableId.
11. Azure AD tenterà quindi di eseguire la corrispondenza flessibile con Bob Taylor, ossia, eseguirà la ricerca per vedere se esiste un qualsiasi oggetto con l'attributo proxyAddresses uguale ai tre valori, compreso l'SMTP: bob@contoso.com
12. Azure AD troverà l'oggetto di Bob Smith che corrisponde ai criteri della corrispondenza flessibile. Tuttavia, il valore dell'attributo immutableId per questo oggetto è "abcdefghijklmnopqrstuv==", il quale indica che l'oggetto è stato sincronizzato da un altro oggetto presente nell'Active Directory locale. Di conseguenza, Azure AD non può eseguire la corrispondenza flessibile di questi oggetti e viene restituito l'errore di sincronizzazione **InvalidSoftMatch**.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Come correggere l'errore InvalidSoftMatch
La causa più comune dell'errore InvalidSoftMatch è che due oggetti con attributi SourceAnchor \(immutableId\) differenti hanno lo stesso valore per gli attributi ProxyAddresses e/o UserPrincipalName, i quali vengono usati durante il processo di corrispondenza flessibile in Azure AD. Per risolvere l'errore di corrispondenza flessibile

1. Identificare il valore duplicato degli attributi proxyAddresses, userPrincipalName o un altro valore di attributo che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. È necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica.

I report sugli errori di sincronizzazione all'interno di Azure AD Connect Health per la sincronizzazione sono aggiornati ogni 30 minuti e includono gli errori rilevati durante il tentativo di sincronizzazione più recente.

> [!NOTE]
> L'attributo immutableId, per definizione, non deve cambiare nel corso della durata dell'oggetto. Se non si è configurato Azure AD Connect tenendo in considerazione alcuni degli scenari riportati nell'elenco precedente, si potrebbe incorrere in una situazione in cui Azure AD Connect calcola un valore differente dell'attributo SourceAnchor per l'oggetto di Active Directory che rappresenta la stessa entità (stesso gruppo/utente/contatto e così via) che dispone di un oggetto di Azure AD esistente che si desidera continuare a usare.
>
>

#### <a name="related-articles"></a>Articoli correlati
* [Gli attributi duplicati o non validi impediscono la sincronizzazione della directory in Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrizione
Quando Azure AD tenta una corrispondenza flessibile tra due oggetti, è possibile che i due oggetti appartenenti a una "tipologia di oggetto" differente (ad esempio, utente, gruppo, contatto e così via) abbiano gli stessi valori per gli attributi usati per eseguire tale corrispondenza. Poiché la duplicazione di questi attributi non è consentita in Azure AD, l'operazione può portare alla restituzione di un errore di sincronizzazione "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Scenari di esempio per l'errore ObjectTypeMismatch
* In Microsoft 365 viene creato un gruppo di sicurezza abilitato alla posta elettronica. L'amministratore aggiunge un nuovo utente o un contatto in AD locale (che non è ancora sincronizzato con Azure AD) con lo stesso valore per l'attributo ProxyAddresses del gruppo di Microsoft 365.

#### <a name="example-case"></a>Caso di esempio
1. L'amministratore crea un nuovo gruppo di sicurezza abilitato alla posta elettronica in Microsoft 365 per il reparto fiscale e fornisce un indirizzo di posta elettronica come tax@contoso.com . A questo gruppo viene assegnato il valore dell'attributo ProxyAddresses di **SMTP: tax \@ contoso.com**
2. Un nuovo utente si aggiunge Contoso.com e viene creato un account per l'utente locale con il proxyAddress come **SMTP: tax \@ contoso.com**
3. Quando Azure AD Connect sincronizzerà il nuovo account utente, verrà restituito l'errore "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Come correggere l'errore ObjectTypeMismatch
La causa più comune dell'errore ObjectTypeMismatch è che due oggetti di tipo differente (utente, gruppo, contatto e così via) hanno lo stesso valore per l'attributo ProxyAddresses. Per correggere l'errore ObjectTypeMismatch:

1. Identificare il valore duplicato dell'attributo proxyAddresses (o su un altro attributo) che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. Si noti che è necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica. Il report sugli errori di sincronizzazione all'interno di Azure AD Connect Health per la sincronizzazione viene aggiornato ogni 30 minuti e include gli errori rilevati durante il tentativo di sincronizzazione più recente.

## <a name="duplicate-attributes"></a>Attributi duplicati
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrizione
Lo schema di Azure Active Directory non consente a due o più oggetti di avere lo stesso valore degli attributi seguenti, vale a dire che ogni oggetto in Azure AD è obbligato ad avere un valore univoco di questi attributi a una determinata istanza.

* ProxyAddresses
* UserPrincipalName

Se Azure AD Connect tenta di aggiungere un nuovo oggetto o di aggiornare un oggetto esistente con un valore degli attributi indicati sopra che è già stato assegnato a un altro oggetto in Azure Active Directory, l'operazione restituisce l'errore di sincronizzazione "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Scenari possibili:
1. Il valore duplicato viene assegnato a un oggetto già sincronizzato, che entra in conflitto con un altro oggetto sincronizzato.

#### <a name="example-case"></a>Caso di esempio:
1. **Bob Smith** è un utente sincronizzato in Azure Active Directory da un'Active Directory locale di contoso.com
2. Il **userPrincipalName** di Bob Smith in locale è impostato come **Bob \@ contoso.com**.
3. Bob dispone anche dei seguenti valori per l'attributo **proxyAddresses**:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
4. Un nuovo utente, **Bob Taylor**, viene aggiunto all'Active Directory locale.
5. Il **userPrincipalName** di Bob Taylor è impostato come **bobt \@ contoso.com**.
6. **Bob Taylor** dispone dei valori seguenti per l'attributo **proxyAddresses**: SMTP: bobt@contoso.com ii. SMTP: bob.taylor@contoso.com
7. L'oggetto di Bob Taylor viene sincronizzato correttamente con Azure AD.
8. L'amministratore ha deciso di aggiornare l'attributo **ProxyAddresses** di Bob Taylor con il seguente valore: i. **SMTP: Bob \@ contoso.com**
9. Azure AD tenterà di aggiornare l'oggetto di Bob Taylor in Azure AD con il valore indicato sopra, ma tale operazione avrà esito negativo poiché il valore di ProxyAddresses è già stato assegnato a Bob Smith; di conseguenza verrà restituito l'errore "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Come correggere l'errore AttributeValueMustBeUnique
La causa più comune dell'errore AttributeValueMustBeUnique è che due oggetti con attributi SourceAnchor \(immutableId\) differenti hanno lo stesso valore per gli attributi ProxyAddresses e/o UserPrincipalName. Per correggere l'errore AttributeValueMustBeUnique

1. Identificare il valore duplicato degli attributi proxyAddresses, userPrincipalName o un altro valore di attributo che ha causato l'errore. Identificare anche i due \(o più\) oggetti coinvolti nel conflitto. Il report generato da [Azure AD Connect Health per la sincronizzazione](https://aka.ms/aadchsyncerrors) consente di identificare i due oggetti.
2. Identificare l'oggetto che deve continuare ad avere il valore duplicato e quello che deve essere modificato.
3. Rimuovere il valore duplicato dall'oggetto che NON deve avere tale valore. Si noti che è necessario apportare la modifica nella directory in cui l'oggetto è originato. In alcuni casi, potrebbe essere necessario eliminare uno degli oggetti in conflitto.
4. Se si apporta la modifica nell'Active Directory locale, consentire ad Azure AD Connect di sincronizzare la modifica per correggere l'errore.

#### <a name="related-articles"></a>Articoli correlati
-[Gli attributi duplicati o non validi impediscono la sincronizzazione della directory in Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Errori di convalida dei dati
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrizione
Azure Active Directory applica varie restrizioni sui dati prima di consentire la scrittura dei dati nella directory al fine di garantire agli utenti finali di beneficiare delle migliori esperienze mentre usano le applicazioni che dipendono da questi dati.

#### <a name="scenarios"></a>Scenari
a. Il valore dell'attributo UserPrincipalName contiene caratteri non validi o non supportati.
b. L'attributo UserPrincipalName non è conforme al formato richiesto.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Come correggere l'errore IdentityDataValidationFailed
a. Assicurarsi che l'attributo userPrincipalName contenga caratteri supportati e rispetti il formato richiesto.

#### <a name="related-articles"></a>Articoli correlati
* [Preparare il provisioning degli utenti tramite la sincronizzazione della directory Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrizione
Questo è un caso che restituisce un errore di sincronizzazione **"FederatedDomainChangeError"** quando il suffisso dell'attributo UserPrincipalName di un utente viene modificato da un dominio federato a un altro dominio federato.

#### <a name="scenarios"></a>Scenari
Per un utente sincronizzato, il suffisso dell'attributo UserPrincipalName è stato modificato da un dominio federato a un altro dominio federato locale. Ad esempio, *userPrincipalName = bob \@ contoso.com* è stato modificato in *userPrincipalName = Bob \@ Fabrikam.com*.

#### <a name="example"></a>Esempio
1. Bob Smith, un account di Contoso.com, viene aggiunto come nuovo utente in Active Directory con l'attributo UserPrincipalName bob@contoso.com
2. Bob passa a una divisione diversa di Contoso.com denominata Fabrikam.com e i relativi UserPrincipalName vengono modificati in bob@fabrikam.com
3. Entrambi i domini di contoso.com e fabrikam.com sono domini federati con Azure Active Directory.
4. L'attributo userPrincipalName di Bob non viene aggiornato e si verifica l'errore di sincronizzazione "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Procedura di correzione
Se il suffisso UserPrincipalName di un utente è stato aggiornato da bob@**contoso.com** a Bob \@ **Fabrikam.com**, in cui sia **contoso.com** che **Fabrikam.com** sono **domini federati**, attenersi alla procedura seguente per correggere l'errore di sincronizzazione

1. Aggiornare l'attributo UserPrincipalName dell'utente in Azure AD da bob@contoso.com a bob@contoso.onmicrosoft.com. È possibile utilizzare il comando PowerShell seguente con il modulo di Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Consentire al ciclo di sincronizzazione successivo di eseguire un nuovo tentativo di sincronizzazione. Questa volta la sincronizzazione andrà a buon fine e permetterà di aggiornare l'attributo UserPrincipalName di Bob su bob@fabrikam.com, come previsto.

#### <a name="related-articles"></a>Articoli correlati
* [Le modifiche non vengono sincronizzate dallo strumento di sincronizzazione Azure Active Directory dopo la modifica dell'UPN di un account utente per l'utilizzo di un dominio federato diverso](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrizione
Quando un attributo supera il limite di dimensioni consentite, di lunghezza o di conteggio impostati dallo schema di Azure Active Directory, la sincronizzazione restituisce come risultato un errore di sincronizzazione di tipo **LargeObject** o **ExceededAllowedLength**. In genere questo errore si verifica per gli attributi seguenti:

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Scenari possibili
1. L'attributo userCertificate di Bob contiene troppi certificati assegnati a Bob, tra i quali possono esserci certificati scaduti o meno recenti. Il limite rigido consente 15 certificati. Per altre informazioni su come gestire gli errori di LargeObject con attributo userCertificate, vedere l'articolo [Handling LargeObject errors caused by userCertificate attribute](tshoot-connect-largeobjecterror-usercertificate.md) (Gestione degli errori di LargeObject causati dall'attributo userCertificate).
2. L'attributo userSMIMECertificate di Bob contiene troppi certificati assegnati a Bob, tra i quali possono esserci certificati scaduti o meno recenti. Il limite rigido consente 15 certificati.
3. L'attributo thumbnailPhoto di Bob impostato in Active Directory è troppo grande per essere sincronizzato in Azure AD.
4. Durante il popolamento automatico dell'attributo ProxyAddresses in Active Directory a un oggetto sono stati assegnati troppi elementi ProxyAddresses.

### <a name="how-to-fix"></a>Procedura di correzione
1. Verificare che l'attributo che ha causato l'errore non superi il limite consentito.

## <a name="existing-admin-role-conflict"></a>Conflitto tra ruoli di amministratore esistenti

### <a name="description"></a>Descrizione
Un **conflitto tra ruoli di amministratore esistenti** si verifica per un oggetto utente durante la sincronizzazione quando tale oggetto ha:

- autorizzazioni amministrative e
- lo stesso UserPrincipalName di un oggetto di Azure AD esistente

Azure AD Connect non è autorizzato a stabilire una corrispondenza flessibile di un oggetto utente in un'istanza locale di AD con un oggetto utente in Azure AD a cui è assegnato un ruolo amministrativo.  Per ulteriori informazioni, vedere [Azure ad popolamento userPrincipalName](plan-connect-userprincipalname.md)

![Amministratore esistente](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Procedura di correzione
Per risolvere il problema, eseguire questi passaggi:

1. Rimuovere l'account Azure AD (proprietario) da tutti i ruoli di amministratore. 
2. **Eliminare** definitivamente l'oggetto in quarantena nel cloud. 
3. Il ciclo di sincronizzazione successivo si occuperà della corrispondenza flessibile dell'utente locale con l'account cloud (poiché l'utente del cloud non è più una GA globale). 
4. Ripristinare le appartenenze ai ruoli per il proprietario. 

>[!NOTE]
>Dopo aver stabilito la corrispondenza flessibile tra i due oggetti, sarà possibile assegnare nuovamente il ruolo amministrativo all'oggetto utente esistente.

## <a name="related-links"></a>Collegamenti correlati
* [Individuare oggetti Active Directory in Centro di amministrazione di Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Come eseguire una query di Azure Active Directory per un oggetto usando Azure Active Directory PowerShell](/previous-versions/azure/jj151815(v=azure.100))