---
title: Messaggi Encode X12
description: Convalidare le proprietà EDI e convertire messaggi con codifica XML con il codificatore di messaggi X12 in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792356"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Messaggi Encode X12 in App per la logica di Azure con Enterprise Integration Pack

Il connettore di messaggi Encode X12 convalida le proprietà EDI e specifiche del partner, converte i messaggi con codifica XML in set di transazioni EDI nell'interscambio e richiede un riconoscimento tecnico, funzionale o entrambi.
Per usare questo connettore, è necessario aggiungerlo a un trigger esistente nell'app per la logica.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) già definito e associato alla sottoscrizione di Azure. Per usare il connettore di messaggi Encode X12 è necessario un account di integrazione.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.
* Un [contratto X12](logic-apps-enterprise-integration-x12.md) già definito nell'account di integrazione.

## <a name="encode-x12-messages"></a>Messaggi Encode X12

1. [Creare un'app per la logica](quickstart-create-first-logic-app-workflow.md).

2. Il connettore di messaggi Encode X12 non dispone di trigger, pertanto è necessario aggiungerne uno per avviare l'app per la logica, ad esempio un trigger di richiesta. In Progettazione app per la logica aggiungere un trigger e un'azione all'app per la logica.

3.  Nella casella di ricerca, immettere "x12" come filtro. Selezionare **X12 - Codifica in un messaggio X12 in base al nome dell'accordo** o **X12 - Codifica in un messaggio X12 in base alle identità**.
   
    ![Cercare "X12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se non sono state create in precedenza le connessioni all'account di integrazione, a questo punto viene richiesto di creare la connessione. Denominare la connessione e selezionare l'account di integrazione al quale connettersi. 
   
    ![connessione all'account di integrazione](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Le proprietà con l'asterisco sono obbligatorie.

    | Proprietà | Dettagli |
    | --- | --- |
    | Nome connessione * |Immettere un nome per la connessione. |
    | Account di integrazione * |Immettere un nome per l'account di integrazione. Verificare che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

5.  Al termine, i dettagli della connessione dovrebbero essere simili a quelli dell'esempio seguente. Per completare la creazione della connessione, scegliere **Crea**.

    ![connessione all'account di integrazione creata](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    La connessione è stata creata.

    ![dettagli della connessione all'account di integrazione](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 Message by agreement name (Messaggio Encode X12 per nome contratto)

Se si sceglie di codificare i messaggi X12 in base al nome del contratto, aprire l'elenco **Nome dell'accordo X12** e digitare o selezionare il contratto X12 esistente. Immettere il messaggio XML da codificare.

![Immettere il nome del contratto X12 e il messaggio XML da codificare.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Encode X12 Message by identities (Messaggio Encode X12 per identità)

Se si sceglie di codificare i messaggi X12 in base alle identità, inserire l'identificatore del mittente, il qualificatore del mittente, l'identificatore del destinatario e il qualificatore del destinatario come configurati nel contratto X12. Selezionare il messaggio XML da codificare.
   
![Fornire le identità del mittente e del destinatario e selezionare il messaggio XML da codificare](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Dettagli di Encode X12

Il connettore Encode X12 esegue queste attività:

* Risoluzione del contratto associando le proprietà del contesto di mittente e del destinatario.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Un riconoscimento tecnico viene generato in seguito alla convalida dell'intestazione. Il riconoscimento tecnico segnala lo stato dell'elaborazione di un'intestazione e finale di interscambio in base all'indirizzo del ricevitore.
  * Un riconoscimento funzionale viene generato in seguito alla convalida del corpo. Il riconoscimento funzionale segnala ogni errore rilevato durante l'elaborazione del documento ricevuto.

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni sulle Enterprise Integration Pack") 

