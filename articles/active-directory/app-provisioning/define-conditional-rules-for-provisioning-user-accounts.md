---
title: Eseguire il provisioning delle app con filtri di ambito | Microsoft Docs
description: Informazioni su come usare i filtri di ambito per evitare che venga eseguito il provisioning degli oggetti inclusi nelle app che supportano il provisioning automatico degli utenti se un oggetto non soddisfa i requisiti aziendali.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.openlocfilehash: 71c2e3a83c3d63d375935294a25a369ca7e54d80
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593745"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito
Questo articolo spiega come usare i filtri di ambito per definire regole basate su attributi per determinare gli utenti per i quali viene eseguito il provisioning per un'applicazione.

## <a name="scoping-filter-use-cases"></a>Casi d'uso dei filtri di ambito

Un filtro di ambito consente ad Azure Active Directory (Azure AD) di eseguire il provisioning del servizio per includere o escludere gli utenti che dispongono di un attributo che corrisponde a un valore specifico. Ad esempio, quando si esegue il provisioning di utenti di Azure AD in un'applicazione SaaS usata da un team di vendita, è possibile specificare che solo gli utenti con un attributo "Reparto" di "Vendite" devono essere inclusi nell'ambito del provisioning.

I filtri di ambito possono essere usati in modo diverso a seconda del tipo di connettore di provisioning:

* **Provisioning in uscita da Azure AD ad applicazioni SaaS**. Quando Azure AD è il sistema di origine, le [assegnazioni di utenti e gruppi](../manage-apps/assign-user-or-group-access-portal.md) sono il metodo più comune per determinare quali utenti sono inclusi nell'ambito per il provisioning. Queste assegnazioni vengono usate anche per l'abilitazione dell'accesso Single Sign-On e offrono un unico metodo per gestire l'accesso e il provisioning. I filtri di ambito possono essere usati facoltativamente, oltre o in sostituzione delle assegnazioni, per filtrare gli utenti in base ai valori di attributo.

    >[!TIP]
    > È possibile disabilitare il provisioning in base alle assegnazioni per un'applicazione aziendale modificando le impostazioni nel menu [Ambito](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) nelle impostazioni di provisioning su **Sincronizza tutti gli utenti e i gruppi**. L'uso di questa opzione insieme ai filtri di ambito basati su attributi offre prestazioni più veloci rispetto all'uso di assegnazioni basate su gruppo.  

* **Provisioning in ingresso dalle applicazioni di Gestione connessione ibrida ad Azure AD e Active Directory**. Quando il sistema di origine è un'[applicazione di Gestione connessione ibrida come Workday](../saas-apps/workday-tutorial.md), i filtri di ambito sono il metodo principale per determinare gli utenti che devono eseguire il provisioning dall'applicazione Gestione connessione ibrida ad Active Directory o Azure AD.

Per impostazione predefinita, i connettori di provisioning di Azure AD non dispongono di filtri di ambito basati su attributi configurati. 

## <a name="scoping-filter-construction"></a>Creazione di un filtro di ambito

Un filtro di ambito è costituito da una o più *clausole*. Le clausole determinano gli utenti che verranno restituiti dal filtro di ambito valutando gli attributi di ogni utente. Ad esempio, se una clausola richiede che l'attributo "Stato" di un utente sia uguale a "Italia", nell'applicazione viene eseguito il provisioning solo degli utenti italiani. 

Una sola clausola definisce una sola condizione per un singolo valore dell'attributo. Se vengono create più clausole in un solo filtro di ambito, queste vengono valutate insieme con la logica "AND". Ciò significa che tutte le clausole devono restituire "true" affinché si esegua il provisioning di un utente.

Infine, è possibile creare più filtri di ambito per una singola applicazione. Se sono presenti più filtri di ambito, questi vengono valutati insieme con la logica "OR". Ciò significa che se tutte le clausole in un qualsiasi filtro di ambito configurato restituiscono "true", per l'utente viene eseguito il provisioning.

Ogni utente o gruppo elaborato dal servizio di provisioning di Azure AD viene sempre valutato singolarmente in base a ogni filtro di ambito.

Vedere ad esempio il filtro di ambito seguente:

![Filtro per la definizione dell'ambito](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

In base a questo filtro di ambito, gli utenti devono soddisfare i criteri seguenti per poterne effettuare il provisioning:

* Devono trovarsi a New York.
* Devono lavorare nel reparto Engineering.
* L'ID del dipendente aziendale deve essere compreso tra 1.000.000 e 2.000.000.
* La posizione non deve essere null o vuota.

## <a name="create-scoping-filters"></a>Creare filtri di ambito
I filtri di ambito sono configurati come parte dei mapping degli attributi per ogni connettore di provisioning dell'utente di Azure AD. La procedura seguente presuppone che sia già stato configurato il provisioning automatico per [una delle applicazioni supportate](../saas-apps/tutorial-list.md) e che si aggiunga un filtro di ambito per tale applicazione.

### <a name="create-a-scoping-filter"></a>Creare un filtro di ambito
1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.

2. Selezionare l'applicazione per cui è stato configurato il provisioning automatico, ad esempio "ServiceNow".

3. Selezionare la scheda **provisioning** .

4. Nella sezione **Mapping** selezionare il mapping per cui si vuole configurare un filtro di ambito, ad esempio "Synchronize Azure Active Directory Users to ServiceNow" (Sincronizzare gli utenti di Azure Active Directory in ServiceNow).

5. Selezionare il menu **Ambito dell'oggetto di origine**.

6. Selezionare **Aggiungi filtro di ambito**.

7. Definire una clausola selezionando un'origine **Nome attributo**, un **Operatore** e un **Valore dell'attributo** da confrontare. Sono supportati gli operatori seguenti:

   a. **È uguale**a. La clausola restituisce "true" se l'attributo valutato corrisponde esattamente al valore della stringa di input (con distinzione tra maiuscole e minuscole).

   b. **è**diverso da. La clausola restituisce "true" se l'attributo valutato non corrisponde al valore della stringa di input (con distinzione tra maiuscole e minuscole).

   c. **IS TRUE** (È VERO). La clausola restituisce "true" se l'attributo valutato contiene un valore booleano true.

   d. **IS FALSE** (È FALSO). La clausola restituisce "true" se l'attributo valutato contiene un valore booleano false.

   e. **è null**. La clausola restituisce "true" se l'attributo valutato è vuoto.

   f. **IS NOT NULL** (NON È NULL). La clausola restituisce "true" se l'attributo valutato non è vuoto.

   g. **REGEX MATCH** (CORRISPONDENZA REGEX). La clausola restituisce "true" se l'attributo valutato corrisponde a un modello di espressione regolare. Ad esempio: ([1-9][0-9]) corrisponde a qualsiasi numero compreso tra 10 e 99.

   h. **NOT REGEX MATCH** (NESSUNA CORRISPONDENZA REGEX). La clausola restituisce "true" se l'attributo valutato non corrisponde a un modello di espressione regolare.
   
   i. **Greater_Than.** La clausola restituisce "true" se l'attributo valutato è maggiore del valore. Il valore specificato nel filtro di ambito deve essere un numero intero e l'attributo dell'utente deve essere un numero intero [0, 1, 2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** La clausola restituisce "true" se l'attributo valutato è maggiore o uguale al valore. Il valore specificato nel filtro di ambito deve essere un numero intero e l'attributo dell'utente deve essere un numero intero [0, 1, 2,...]. 
   
   k. **Include.** La clausola restituisce "true" se l'attributo valutato contiene il valore stringa (con distinzione tra maiuscole e minuscole), come descritto [qui](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - Il filtro IsMemberOf non è attualmente supportato.
> - Gli attributi multivalore non supportano EQUALs e NOT EQUALs

9. Facoltativamente, ripetere i passaggi 7 e 8 per aggiungere altre clausole di ambito.

10. In **Titolo filtro di ambito** aggiungere un nome per il filtro di ambito.

11. Selezionare **OK**.

12. Selezionare di nuovo **OK** nella schermata **Filtri di ambito**. Facoltativamente, ripetere i passaggi da 6 a 11 per aggiungere un altro filtro di ambito.

13. Selezionare **Salva** nella schermata **Mapping attributi**. 

>[!IMPORTANT] 
> Il salvataggio di un nuovo filtro di ambito attiva una nuova sincronizzazione completa per l'applicazione, in cui tutti gli utenti del sistema di origine vengono nuovamente valutati rispetto al nuovo filtro di ambito. Se un utente nell'applicazione era precedentemente incluso nell'ambito per il provisioning, ma non rientra più nell'ambito, l'account verrà disabilitato o ne verrà eseguito il deprovisioning nell'applicazione. Per eseguire l'override di questo comportamento predefinito, fare riferimento a [Ignora eliminazione per gli account utente che non rientrano nell'ambito](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Filtri di ambito comuni
| Target Attribute| Operatore | valore | Descrizione|
|----|----|----|----|
|userPrincipalName|CORRISPONDENZA REGEX|.\*@domain.com |Tutti gli utenti con userPrincipal con il dominio @domain.com saranno nell'ambito per il provisioning|
|userPrincipalName|NON CORRISPONDENZA REGEX|.\*@domain.com|Tutti gli utenti con userPrincipal che possiede il @domain.com dominio saranno fuori dall'ambito per il provisioning|
|department|EQUALS|sales|Tutti gli utenti del reparto vendite rientrano nell'ambito del provisioning|
|workerID|CORRISPONDENZA REGEX|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Tutti i dipendenti con workerIDs compreso tra 1 milione e 2 milioni rientrano nell'ambito del provisioning.|

## <a name="related-articles"></a>Articoli correlati
* [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS](../app-provisioning/user-provisioning.md)
* [Personalizzare i mapping degli attributi per il provisioning degli utenti](../app-provisioning/customize-application-attributes.md)
* [Scrivere espressioni per il mapping degli attributi](functions-for-customizing-application-data.md)
* [Notifiche relative al provisioning dell'account](../app-provisioning/user-provisioning.md)
* [Usare SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Elenco delle esercitazioni su come integrare le app SaaS](../saas-apps/tutorial-list.md)

