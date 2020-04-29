---
title: Che cos'è la sicurezza a livello di colonna per la sinapsi di Azure?
description: La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella di database in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo, semplificando la progettazione e la codifica della sicurezza nell'applicazione e consentendo di implementare restrizioni per l'accesso alle colonne.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687931"
---
# <a name="column-level-security"></a>Sicurezza a livello di colonna

La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Poiché questo video è stato pubblicato, la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) è diventata disponibile per le sinapsi di Azure.

La sicurezza a livello di colonna semplifica la progettazione e la codifica della sicurezza nell'applicazione, consentendo di limitare l'accesso alle colonne per proteggere i dati sensibili. Assicurando, ad esempio, che utenti specifici possano accedere solo determinate colonne di una tabella relative al loro reparto. La logica di restrizione dell'accesso si trova sul livello del database e non su un altro livello applicazione lontano dai dati. Il database applica le restrizioni di accesso ogni volta che si tenta di accedere ai dati da qualsiasi livello. Questa restrizione rende la sicurezza più affidabile e affidabile riducendo la superficie di attacco del sistema di sicurezza globale. Inoltre, la sicurezza a livello di colonna Elimina la necessità di introdurre viste per filtrare le colonne per l'imposizione delle restrizioni di accesso agli utenti.

È possibile implementare la sicurezza a livello di colonna con l'istruzione T-SQL [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . Con questo meccanismo sono supportate sia l'autenticazione SQL che quella di Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintassi

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come limitare `TestUser` l'accesso alla `SSN` colonna della `Membership` tabella:

Creare `Membership` una tabella con la colonna SSN usata per archiviare i numeri di previdenza sociale:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Consente `TestUser` di accedere a tutte le colonne ad eccezione della colonna SSN, che include i dati sensibili:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Le query eseguite `TestUser` come avranno esito negativo se includono la colonna SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Modalità di utilizzo comuni

Di seguito sono riportati alcuni esempi di come viene utilizzata la sicurezza a livello di colonna:

- Una società di servizi finanziari consente solo agli account manager di accedere ai numeri di previdenza sociale (SSN), ai numeri di telefono e ad altre informazioni personali (PII).
- Un Health care provider consente solo a medici e infermieri di accedere a record medici sensibili, evitando al tempo stesso ai membri del reparto di fatturazione di visualizzare questi dati.
