---
title: Sintassi di azione SQL della regola di sottoscrizione del bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce un riferimento per la sintassi dell'azione della regola SQL. Le azioni vengono scritte nella sintassi basata sul linguaggio SQL eseguita su un messaggio.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: f7b8cdfcccc22508b98a42391d2a0ef9955232d0
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742684"
---
# <a name="subscription-rule-sql-action-syntax"></a>Sintassi di azione SQL della regola di sottoscrizione

Un' *azione SQL* viene utilizzata per modificare i metadati del messaggio dopo che un messaggio è stato selezionato da un filtro di una regola di sottoscrizione. Si tratta di un'espressione di testo che si basa su un subset dello standard SQL-92. Le espressioni di azione vengono usate con l' `sqlExpression` elemento della proprietà "Action" di un bus di servizio `Rule` in un [modello di Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md), l'argomento del comando dell'interfaccia della riga di comando di Azure `az servicebus topic subscription rule create` [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) e diverse funzioni SDK che consentono di gestire le regole di sottoscrizione.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argomenti  
  
-   `<scope>` è una stringa facoltativa che indica l'ambito di `<property_name>`. I valori validi sono `sys` o `user`. Il valore `sys` indica l'ambito del sistema. In questo caso, `<property_name>` sarà il nome di una proprietà pubblica della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica l'ambito dell'utente. In questo caso, `<property_name>` sarà una chiave del dizionario della [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` l'ambito è l'ambito predefinito se `<scope>` non è specificato.  
  
### <a name="remarks"></a>Osservazioni  

Un tentativo di accedere a una proprietà di sistema inesistente è un errore, mentre un tentativo di accedere a una proprietà utente inesistente non è un errore. Una proprietà utente inesistente viene invece valutata internamente come valore sconosciuto. Un valore sconosciuto viene gestito in modo speciale durante la valutazione degli operatori.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argomenti  
 `<regular_identifier>` è una stringa rappresentata dall'espressione regolare seguente:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 L'espressione corrisponde a qualsiasi stringa che inizia con una lettera seguita da uno o più caratteri di sottolineatura/lettere/cifre.  
  
 `[:IsLetter:]` indica qualsiasi carattere Unicode classificato come lettera Unicode. `System.Char.IsLetter(c)` restituisce `true` se `c` è una lettera Unicode.  
  
 `[:IsDigit:]` indica qualsiasi carattere Unicode classificato come cifra decimale. `System.Char.IsDigit(c)` restituisce `true` se `c` è una cifra Unicode.  
  
 Un `<regular_identifier>` non può essere una parola chiave riservata.  
  
 `<delimited_identifier>` è qualsiasi stringa racchiusa tra parentesi quadra aperta e parentesi quadra chiusa ([]). Una parentesi quadra chiusa è rappresentata con due parentesi quadre chiuse. Di seguito sono riportati esempi di `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` è qualsiasi stringa racchiusa tra virgolette doppie. Le virgolette doppie nell'identificatore sono rappresentate con due virgolette doppie. Non è consigliabile usare gli identificatori delimitati perché può essere facilmente confuso con una costante di stringa. Usare se possibile un identificatore delimitato. Di seguito è riportato un esempio di `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Osservazioni
  
 `<pattern>` deve essere un'espressione valutata come stringa. Viene usato come modello per l'operatore LIKE      e può contenere i caratteri jolly seguenti.  
  
-   `%`: qualsiasi stringa di zero o più caratteri.  
  
-   `_`: qualsiasi carattere singolo.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Osservazioni
  
 `<escape_char>` deve essere un'espressione valutata come stringa di lunghezza 1. Viene usato come carattere di escape per l'operatore LIKE.  
  
 Ad esempio, `property LIKE 'ABC\%' ESCAPE '\'` corrisponde a `ABC%` anziché a una stringa che inizia con `ABC`.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argomenti  
  
-   `<integer_constant>` è una stringa di numeri che non sono racchiusi tra virgolette e non contengono punti decimali. I valori sono archiviati internamente come `System.Int64` e seguono lo stesso intervallo.  
  
     Di seguito sono riportati esempi di costanti di tipo long:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` è una stringa di numeri che non sono racchiusi tra virgolette e contengono una virgola decimale. I valori sono archiviati internamente come `System.Double` e seguono lo stesso intervallo e la stessa precisione.  
  
     In una versione futura, questo numero potrebbe essere archiviato in un tipo di dati diverso per supportare la semantica esatta dei numeri, pertanto non è consigliabile fare affidamento sul fatto che il tipo di dati sottostante è `System.Double` per `<decimal_constant>` .  
  
     Di seguito sono riportati esempi di costanti decimali:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` è un numero scritto in notazione scientifica. I valori sono archiviati internamente come `System.Double` e seguono lo stesso intervallo e la stessa precisione. Di seguito sono riportati esempi di costanti numeriche approssimative:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Osservazioni
  
Le costanti booleane sono rappresentate dalle parole chiave `TRUE` e `FALSE`. I valori sono archiviati come `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Osservazioni
  
Le costanti di tipo stringa sono racchiuse tra virgolette singole e includono qualsiasi carattere Unicode valido. Le virgolette singole incorporate in una costante di tipo stringa sono rappresentate con due virgolette singole.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Osservazioni  

La `newid()` funzione restituisce un oggetto `System.Guid` generato dal `System.Guid.NewGuid()` metodo.  
  
La funzione `property(name)` restituisce il valore della proprietà a cui viene fatto riferimento con `name`. Il valore di `name` può essere qualsiasi espressione valida che restituisce un valore stringa.  

[!INCLUDE [service-bus-filter-examples](../../includes/service-bus-filter-examples.md)]
  
## <a name="considerations"></a>Considerazioni

- L'azione SET viene usata per creare una nuova proprietà o aggiornare il valore di una proprietà esistente.
- L'azione REMOVE viene usata per rimuovere una proprietà.
- SET esegue la conversione implicita, se possibile, quando il tipo dell'espressione e il tipo della proprietà esistente sono diversi.
- Se viene fatto riferimento a proprietà di sistema inesistenti, l'azione ha esito negativo.
- L'azione non ha esito negativo se è stato fatto riferimento a proprietà utente inesistenti.
- Una proprietà utente inesistente viene valutata internamente come valore sconosciuto, seguendo la stessa semantica di [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) nella valutazione degli operatori.

## <a name="next-steps"></a>Passaggi successivi

- [Classe SQLRuleAction (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe SQLRuleAction (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [Classe SqlRuleAction (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)