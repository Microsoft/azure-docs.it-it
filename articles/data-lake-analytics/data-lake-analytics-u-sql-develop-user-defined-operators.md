---
title: Sviluppare operatori U-SQL definiti dall'utente-Azure Data Lake Analytics
description: Informazioni su come sviluppare operatori definiti dall'utente da usare più volte nei processi di Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219990"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Sviluppare operatori U-SQL definiti dall'utente (UDO)
Questo articolo descrive come sviluppare operatori definiti dall'utente per elaborare i dati in un processo U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definire e usare un operatore definito dall'utente in U-SQL

### <a name="to-create-and-submit-a-u-sql-job"></a>Per creare e inviare un processo di U-SQL

1. In Visual Studio selezionare **File > Nuovo > Progetto > U-SQL Project** (Progetto U-SQL).
2. Fare clic su **OK**. Visual Studio crea una soluzione con un file Script.usql.
3. In **Esplora soluzioni** espandere Script.usql e fare doppio clic su **Script.usql.cs**.
4. Incollare il codice seguente nel file:

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. Aprire **Script.usql** e incollare lo script U-SQL seguente:

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Specificare l'account di Analisi Data Lake, il database e lo schema.
7. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi scegliere **Build Script** (Compila script).
8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Script.usql** e quindi scegliere **Submit Script** (Invia script).
9. Se non ci si è ancora connessi alla sottoscrizione di Azure, verrà richiesto di immettere le credenziali dell'account Azure.
10. Fare clic su **Submit** (Invia). Al termine della procedura di invio, nella finestra dei risultati saranno disponibili i risultati dell'operazione di invio e il collegamento al processo.
11. Per visualizzare lo stato attuale del processo, fare clic sul pulsante **Aggiorna** per aggiornare la schermata.

### <a name="to-see-the-output"></a>Per visualizzare l'output

1. Da **Esplora server** espandere **Azure**, quindi **Data Lake Analytics**, l'account Data Lake Analytics e infine **Account di archiviazione**. Fare clic con il pulsante destro del mouse su Archivio predefinito e quindi scegliere **Esplora**.

2. Espandere esempi, espandere gli output e quindi fare doppio clic su **Drivers.csv**.

## <a name="next-steps"></a>Passaggi successivi

* [Extending U-SQL Expressions with User-Code](/u-sql/concepts/extending-u-sql-expressions-with-user-code) (Estensione delle espressioni U-SQL con il codice utente)
* [Utilizzare gli strumenti Data Lake per Visual Studio per lo sviluppo di applicazioni U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
