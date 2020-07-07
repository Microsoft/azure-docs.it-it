---
title: Gestire ruoli del database e utenti in Azure Analysis Services | Microsoft Docs
description: Informazioni su come gestire ruoli del database e utenti in un server Analysis Services in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81454204"
---
# <a name="manage-database-roles-and-users"></a>Gestire ruoli del database e utenti

A livello di database modello, tutti gli utenti devono appartenere a un ruolo. I ruoli definiscono gli utenti con determinate autorizzazioni per il database modello. Qualsiasi gruppo di sicurezza o utente aggiunto a un ruolo deve avere un account in un tenant di Azure AD nella stessa sottoscrizione del server. 

La modalità di definizione dei ruoli varia a seconda dello strumento in uso, ma l'effetto è lo stesso.

Le autorizzazioni di ruoli includono:
*  **Amministratore**: utenti che dispongono di autorizzazioni complete per il database. I ruoli del database con autorizzazioni di amministratore sono diversi dagli amministratori di server.
*  **Elabora**: utenti che possono connettersi ed eseguire operazioni di elaborazione nel database e analizzare i dati del database modello.
*  **Lettura**: utenti che possono usare un'applicazione client per connettersi e analizzare i dati del database modello.

Quando si crea un progetto di modello tabulare, è possibile creare ruoli e aggiungere utenti o gruppi a tali ruoli usando Gestione ruoli in Visual Studio con Analysis Services progetti. Quando si esegue la distribuzione in un server, usare SQL Server Management Studio (SSMS), [Analysis Services cmdlet di PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)o TMSL ( [Tabular Model Scripting Language](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) ) per aggiungere o rimuovere ruoli e membri utente.

Quando si aggiunge un **gruppo di sicurezza**, utilizzare `obj:groupid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Per aggiungere o gestire ruoli e utenti in Visual Studio  
  
1.  In **Esplora modelli tabulari**fare clic con il pulsante destro del mouse su **ruoli**.  
  
2.  In **Gestione ruoli**fare clic su **nuovo**.  
  
3.  Digitare un nome per il ruolo.  
  
     Per impostazione predefinita, il nome del ruolo predefinito è numerato in modo incrementale per ogni nuovo ruolo. È consigliabile digitare un nome che identifichi con precisione il tipo di membro, ad esempio responsabili finanze o esperti di risorse umane.  
  
4.  Selezionare una delle seguenti autorizzazioni:  
  
    |Autorizzazione|Descrizione|  
    |----------------|-----------------|  
    |**Nessuno**|I membri non possono leggere o modificare lo schema del modello e non possono eseguire query sui dati.|  
    |**Lettura**|I membri possono eseguire query su dati, in base ai filtri di riga, ma non possono modificare lo schema del modello.|  
    |**Lettura ed elaborazione**|I membri possono eseguire query su dati in base ai filtri a livello di riga ed eseguire operazioni Elabora ed Elabora tutto, ma non possono modificare lo schema del modello.|  
    |**Processo**|I membri possono effettuare le operazioni relative alle opzioni Elabora ed Elabora tutto, Impossibile leggere o modificare lo schema del modello e non è possibile eseguire query sui dati.|  
    |**Amministratore**|I membri possono modificare lo schema del modello ed eseguire query su tutti i dati.|   
  
5.  Se il ruolo che si sta creando dispone delle autorizzazioni Lettura o Lettura ed elaborazione, è possibile aggiungere filtri di riga usando una formula DAX. Fare clic sulla scheda **Filtri di riga**, quindi selezionare una tabella, quindi scegliere il campo **Filtro DAX** e quindi digitare una formula DAX.
  
6.  Fare clic su **membri**  >  **Aggiungi esterno**.  
  
8.  In **Aggiungi membro esterno** immettere gli utenti o i gruppi nel tenant di Azure AD dall'indirizzo e-mail. Dopo avere fatto clic su OK e avere chiuso Gestione ruoli, i ruoli e i membri del ruolo vengono visualizzati in Esplora modelli tabulari. 
 
     ![Ruoli e utenti in Esplora modelli tabulari](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Eseguire la distribuzione in un server di Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Per aggiungere o gestire ruoli e utenti in SSMS

Per aggiungere ruoli e utenti a un database modello distribuito, è necessario connettersi al server come amministratore del server o già in un ruolo del database con autorizzazioni di amministratore.

1. In Explorer oggetto, fare clic con il pulsante destro del mouse su **ruoli**  >  **nuovo ruolo**.

2. In **Crea ruolo** immettere il nome di un ruolo e una descrizione.

3. Selezionare un'autorizzazione.

   |Autorizzazione|Descrizione|  
   |----------------|-----------------|  
   |**Controllo completo (amministratore)**|I membri possono modificare lo schema del modello, eseguire operazioni di elaborazione e query su tutti i dati.| 
   |**Elaborazione database**|I membri possono effettuare le operazioni relative alle opzioni Elabora ed Elabora tutto, ma non possono modificare lo schema del modello, né eseguire query sui dati.|  
   |**Lettura**|I membri possono eseguire query su dati, in base ai filtri di riga, ma non possono modificare lo schema del modello.|  
  
4. Fare clic su **Appartenenza**, quindi immettere un utente o un gruppo nel tenant di Azure AD dall'indirizzo e-mail.

     ![Add User](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se il ruolo che si sta creando dispone dell'autorizzazione Lettura, è possibile aggiungere filtri di riga usando una formula DAX. Fare clic su **Filtri di riga**, selezionare una tabella e quindi digitare una formula DAX nel campo **Filtro DAX**. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Per aggiungere ruoli e utenti usando uno script TMSL

È possibile eseguire uno script TMSL nella finestra XMLA in SSMS o usando PowerShell. Usare il comando [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) e l'oggetto [Ruoli](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl).

**Script di esempio del linguaggio di scripting del modello tabulare**

In questo esempio, un gruppo e un utente esterno B2B vengono aggiunti al ruolo analista con autorizzazioni Lettura per il database SalesBI. Sia il gruppo che l'utente esterno devono trovarsi nello stesso tenant di Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Per aggiungere ruoli e utenti usando PowerShell

Il modulo [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). I cmdlet seguenti vengono usati per la gestione di utenti e ruoli del database.
  
|Cmdlet|Descrizione|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Aggiunge un membro a un ruolo del database.| 
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Rimuove un membro da un ruolo del database.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Esegue uno script TMSL.|

## <a name="row-filters"></a>Filtri di riga  

I filtri di riga definiscono le righe di una tabella su cui i membri di uno specifico ruolo possono eseguire query. e vengono definiti per ogni tabella in un modello tramite formule DAX.  
  
I filtri di riga possono essere definiti solo per i ruoli con le autorizzazioni Lettura e Lettura ed elaborazione. Per impostazione predefinita, se non si definisce un filtro di riga per una determinata tabella, i membri possono eseguire query su tutte le righe della tabella a meno che non vengano applicati filtri incrociati da un'altra tabella.
  
 I filtri di riga richiedono una formula DAX, che deve restituire un valore TRUE/FALSE, per definire le righe su cui i membri del ruolo specifico possono eseguire query. Non è possibile eseguire query su righe non incluse nella formula DAX. Ad esempio, la tabella Customers con l'espressione dei filtri di riga seguente, *= Customers [Country] = "USA"*, i membri del ruolo Sales possono visualizzare solo i clienti negli Stati Uniti.  
  
I filtri di riga si applicano alle righe specificate e alle righe correlate. Quando una tabella dispone di più relazioni, tramite i filtri viene applicata la sicurezza alla relazione che è attiva. I filtri di riga vengono intersecati con altri filtri di riga definiti per le tabelle correlate, ad esempio:  
  
|Tabella|Espressione DAX|  
|-----------|--------------------|  
|Region|=Region[Country]="USA"|  
|ProductCategory|=ProductCategory[Name]="Bicycles"|  
|Transazioni|=Transactions[Year]=2016|  
  
 I membri possono eseguire query sulle righe di dati, in cui il cliente si trova negli Stati Uniti, la categoria del prodotto è biciclette e l'anno è il 2016. Gli utenti non possono eseguire query sulle transazioni all'esterno degli Stati Uniti, non relative alle biciclette o all'anno 2016, a meno che non siano membri di un altro ruolo che prevede queste autorizzazioni.
  
 È possibile usare il filtro, *=FALSE()*, per negare l'accesso a tutte le righe di un'intera tabella.

## <a name="next-steps"></a>Passaggi successivi

  [Gestire gli amministratori del server](analysis-services-server-admins.md)   
  [Gestire Azure Analysis Services con PowerShell](analysis-services-powershell.md)  
  [Riferimento al Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

