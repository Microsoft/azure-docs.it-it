---
title: Sicurezza a livello di colonna per il pool SQL dedicato
description: La sicurezza a livello di colonna consente ai clienti di controllare l'accesso alle colonne della tabella di database in base al contesto di esecuzione o all'appartenenza ai gruppi dell'utente, semplificando la progettazione e la codifica della sicurezza nell'applicazione e consentendo di implementare restrizioni per l'accesso alle colonne.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: fb34051f7d4b24190806dde939c8cc6d9c2a4896
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679948"
---
# <a name="column-level-security"></a>Sicurezza a livello di colonna

Column-Level sicurezza consente ai clienti di controllare l'accesso alle colonne della tabella in base al contesto di esecuzione dell'utente o all'appartenenza al gruppo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Poiché questo video è stato pubblicato, la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) è diventata disponibile per il pool SQL dedicato nella sinapsi di Azure.

La sicurezza a livello di colonna semplifica la progettazione e la codifica della sicurezza nell'applicazione, consentendo di limitare l'accesso alle colonne per proteggere i dati sensibili. Assicurando, ad esempio, che utenti specifici possano accedere solo determinate colonne di una tabella relative al loro reparto. La logica di restrizione dell'accesso si trova sul livello del database e non su un altro livello applicazione lontano dai dati. Il database applica le restrizioni di accesso a ogni tentativo di accesso ai dati da qualsiasi livello. Questa restrizione rende la sicurezza più affidabile e solida, grazie alla riduzione della superficie di attacco del sistema di sicurezza generale. La sicurezza a livello di colonna elimina anche l'esigenza di introduzione di visualizzazioni per filtrare le colonne per imporre limitazioni di accesso agli utenti.

È possibile implementare la sicurezza a livello di colonna con l'istruzione T-SQL [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . Con questo meccanismo, sono supportate sia l'autenticazione SQL che la Azure Active Directory (Azure AD).

![Il diagramma mostra una tabella schematica con la prima colonna a capo di un lucchetto chiuso e le relative celle un colore arancione mentre le altre colonne sono celle bianche.](./media/column-level-security/cls.png)

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

L'esempio seguente illustra come escludere `TestUser`dall'accesso alla colonna `SSN` della tabella `Membership`:

Creare la tabella `Membership` con la colonna SSN usata per archiviare i numeri di previdenza sociale:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Consentire a `TestUser` di accedere a tutte le colonne a eccezione di SSN contenente dati sensibili:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Le query eseguite come `TestUser` avranno esito negativo se includeranno la colonna SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Modalità di utilizzo comuni

Di seguito sono riportati alcuni esempi di come viene utilizzata la sicurezza a livello di colonna:

- Una società di servizi finanziari consente solo ai responsabili degli account di accedere ai numeri di previdenza sociale (SSN) dei clienti, ai numeri di telefono e ad altri dati personali.
- Un fornitore di assistenza sanitaria consente solo a medici e infermieri di avere accesso a cartelle cliniche sensibili mentre impedisce ai membri del reparto contabilità di visualizzare questi dati.
