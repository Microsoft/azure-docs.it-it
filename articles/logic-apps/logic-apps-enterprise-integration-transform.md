---
title: Trasformare il codice XML tra i formati
description: Creare trasformazioni o mappe per convertire i dati XML tra i formati in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 38861ed941d49317e69bfab9afd17638a790bf7b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072429"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Creare mappe per trasformare i dati XML tra i formati in App per la logica di Azure con Enterprise Integration Pack

Il connettore di trasformazione di Enterprise Integration converte i dati da un formato a un altro. Ad esempio, si è ricevuto un messaggio contenente la data corrente nel formato YearMonthDay. È possibile usare una trasformazione per riformattare la data nel formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>Funzioni della trasformazione
Un'app per le API Transform, conosciuta anche come Map, consiste in uno schema XML di origine (input) e in uno schema XML di destinazione (output). È possibile usare diverse funzioni predefinite per contribuire a gestire o controllare i dati, incluse la modifica di stringhe, le operazioni Conditional Assignment, le espressioni aritmetiche, i formattatori di data/ora e persino i costrutti a ciclo continuo.

## <a name="how-to-create-a-transform"></a>Procedura: Creare una trasformazione
È possibile creare una mappa/trasformazione tramite l' [SDK di Enterprise Integration](https://aka.ms/vsmapsandschemas)di Visual Studio. Al termine della creazione e del testing della trasformazione, caricare la trasformazione nell'account di integrazione. 

## <a name="how-to-use-a-transform"></a>Procedura: Usare una trasformazione
Dopo aver caricato il file della trasformazione/mappa nell'account di integrazione, è possibile usarlo per creare un'app per la logica. L'app per la logica esegue le trasformazioni a ogni attivazione dell'app, con la relativa trasformazione del contenuto immesso.

**Di seguito è riportata la procedura per l'uso di una trasformazione**:

### <a name="prerequisites"></a>Prerequisiti

* Creare un account di integrazione e aggiungervi una mappa  

Una volta soddisfatti i requisiti, è ora di creare l'app per la logica:  

1. Creare un'app per la logica e [collegarla all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene la mappa.
2. Aggiungere un trigger **Richiesta** all'app per la logica.  
   ![Screenshot dell'elenco a discesa "Mostra API gestite da Microsoft" con il trigger di richiesta selezionato. L'elenco a discesa si trova in un'app per la logica creata con Visual Studio Enterprise Integration SDK.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Aggiungere l'azione **Trasforma XML** selezionando prima **Aggiungi un'azione**   
   ![Screenshot che mostra il pulsante "Aggiungi un'azione" selezionato nella schermata del trigger di richiesta.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Immettere la parola *trasforma* nella casella di ricerca per filtrare tutte le azioni e ottenere quella che si vuole usare.  
   ![Screenshot che illustra come cercare l'azione trasforma XML nell'elenco a discesa "Mostra API gestite da Microsoft" in modo che possa essere aggiunto al trigger di richiesta.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selezionare l'azione **Trasforma XML**   
6. Aggiungere il **CONTENUTO** XML da trasformare. È possibile usare tutti i dati XML ricevuti nella richiesta HTTP come **CONTENUTO**. In questo esempio selezionare il corpo della richiesta HTTP che ha attivato l'app per la logica.

   > [!NOTE]
   > Assicurarsi che il contenuto relativo all'azione **Trasforma XML** sia in formato XML. Se il contenuto non è in formato XML o in codifica Base64, è necessario specificare un'espressione che elabori il contenuto. In questo caso, è possibile usare [funzioni](logic-apps-workflow-definition-language.md#functions) come ```@base64ToBinary``` per decodificare il contenuto o ```@xml``` per elaborare il contenuto come XML.
 

7. Selezionare il nome della **MAPPA** che si vuole usare per eseguire la trasformazione. La mappa deve essere già presente nell'account di integrazione. In un passaggio precedente è stato concesso l'accesso dell'app per la logica all'account di integrazione che contiene la mappa.      
   ![Screenshot che mostra i campi del contenuto e della mappa nella schermata Transform XML per il trigger request.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Salvare il lavoro   
    ![Screenshot che mostra il pulsante Salva nella finestra di progettazione di app per la logica.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

A questo punto, la configurazione della mappa è completa. In un'applicazione reale è possibile archiviare i dati trasformati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della trasformazione a Salesforce. 

È ora possibile testare la trasformazione eseguendo una richiesta all'endpoint HTTP.  


## <a name="features-and-use-cases"></a>Funzionalità e casi d'uso
* La trasformazione creata in una mappa può essere semplice, come ad esempio la copia di un nome e di un indirizzo da un documento a un altro. In alternativa, è possibile creare trasformazioni più complesse usando operazioni di mapping predefinite.  
* Sono già disponibili più operazioni o funzioni di mapping, incluse stringhe, funzioni data/ora e così via.  
* È possibile eseguire una copia diretta dei dati tra gli schemi. Nel mapper incluso nell'SDK, questa operazione è semplice quanto tracciare una linea che colleghi gli elementi nello schema di origine con le relative controparti nello schema di destinazione.  
* Durante la creazione di una mappa viene visualizzata la relativa rappresentazione grafica, che mostra tutte le relazioni e i collegamenti creati dall'utente.
* Usare la funzione Mappa di test per aggiungere un messaggio XML di esempio. Con un semplice clic, è possibile testare la mappa creata e visualizzare l'output generato.  
* Caricare le mappe esistenti  
* Include il supporto per il formato XML.

## <a name="advanced-features"></a>Funzionalità avanzate

### <a name="reference-assembly-or-custom-code-from-maps"></a>Assembly di riferimento o codice personalizzato dalle mappe 
L'azione di trasformazione supporta anche le mappe o le trasformazioni con riferimento all'assembly esterno. Questa funzionalità abilita le chiamate a codice .NET personalizzato direttamente da mappe XSLT. Di seguito sono elencati i prerequisiti per l'uso dell'assembly nelle mappe.

* La mappa e l'assembly a cui si fa riferimento nella mappa devono essere [caricati nell'account di integrazione](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > La mappa e l'assembly devono essere caricati in un ordine specifico. È necessario caricare l'assembly prima della mappa che fa riferimento all'assembly.

* La mappa deve anche presentare questi attributi e una sezione CDATA contenente la chiamata al codice dell'assembly:

    * **nome** è il nome personalizzato dell'assembly.
    * **spazio dei nomi** è lo spazio dei nomi nell'assembly che include il codice personalizzato.

  Questo esempio mostra una mappa che fa riferimento a un assembly denominato "XslUtilitiesLib" e chiama il metodo `circumreference` dall'assembly.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Byte order mark
Per impostazione predefinita, la risposta ottenuta dalla trasformazione inizia con il Byte order mark (BOM). È possibile accedere a questa funzionalità solo quando si lavora nell'editor della visualizzazione Codice. Per disabilitare questa funzionalità, specificare `disableByteOrderMark` per la proprietà `transformOptions`:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni sulle Enterprise Integration Pack")  
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")  

