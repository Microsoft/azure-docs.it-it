---
title: Codificare messaggi EDIFACT
description: Convalidare le proprietà EDI e generare codice XML con il codificatore di messaggi EDIFACT per App per la logica di Azure tramite Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82106526"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Messaggi Encode EDIFACT in App per la logica di Azure con Enterprise Integration Pack

Il connettore di messaggi Encode EDIFACT convalida le proprietà EDI e specifiche del partner e genera un documento XML per ogni set di transazioni e richiede un riconoscimento tecnico, funzionale o entrambi.
Per usare questo connettore, è necessario aggiungerlo a un trigger esistente nell'app per la logica.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Encode EDIFACT è necessario un account di integrazione. 
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto EDIFACT](logic-apps-enterprise-integration-edifact.md) già definito nell'account di integrazione.

## <a name="encode-edifact-messages"></a>Codificare messaggi EDIFACT

> [!IMPORTANT]
> Il connettore EDIFACT supporta solo caratteri UTF-8.
> Se l'output contiene caratteri imprevisti, controllare che i messaggi EDIFACT usino il set di caratteri UTF-8.

1. [Creare un'app per la logica](quickstart-create-first-logic-app-workflow.md).

2. Il connettore di messaggi Encode EDIFACT non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.  Nella casella di ricerca, digitare "EDIFACT" come filtro. Selezionare **Encode EDIFACT message by Agreement Name** o **Encode to EDIFACT message by Identities**.
   
    ![ricerca di EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi.

    ![creare connessione all'account di integrazione](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.  Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.

    ![dettagli della connessione all'account di integrazione](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    La connessione è stata creata.

    ![connessione all'account di integrazione creata](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message by agreement name

Se si sceglie di codificare i messaggi EDIFACT in base al nome del contratto, aprire l'elenco **Nome dell'accordo EDIFACT** e digitare o selezionare il nome del contratto EDIFACT. Immettere il messaggio XML da codificare.

![Immettere il nome del contratto EDIFACT e un messaggio XML da codificare](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message by identities

Se si sceglie di codificare i messaggi EDIFACT in base alle identità, inserire l'identificatore del mittente, il qualificatore del mittente, l'identificatore del destinatario e il qualificatore del destinatario come configurati nel contratto EDIFACT. Selezionare il messaggio XML da codificare.

![Fornire le identità del mittente e del destinatario e selezionare il messaggio XML da codificare](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Dettagli della codifica EDIFACT

Il connettore Encode EDIFACT esegue queste attività: 

* Risolvere il contratto associando il qualificatore e l'identificatore del mittente e il qualificatore e l'identificatore del ricevitore.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Genera un documento XML per ogni set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Come riconoscimento tecnico, il messaggio CONTRL indica la ricezione di un interscambio.
  * Come riconoscimento funzionale, il messaggio CONTRL indica l'accettazione o il rifiuto dell'interscambio, del gruppo o del messaggio ricevuto, con un elenco di errori o funzionalità non supportate.

## <a name="view-swagger-file"></a>Visualizzare il file Swagger
Per visualizzare i dettagli di Swagger per il connettore EDIFACT, vedere [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni sulle Enterprise Integration Pack") 

