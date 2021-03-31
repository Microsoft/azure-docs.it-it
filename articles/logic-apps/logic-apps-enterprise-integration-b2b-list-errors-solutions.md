---
title: Soluzioni per errori e problemi comuni negli scenari B2B
description: Trovare soluzioni per errori e problemi comuni durante la risoluzione dei problemi relativi agli scenari B2B in app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86232560"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Errori B2B e soluzioni per le App per la logica di Azure

Questo articolo consente di risolvere gli errori che si possono verificare negli scenari con le app per la logica B2B e suggerisce le azioni appropriate per la correzione degli errori.

## <a name="agreement-resolution"></a>Risoluzione dell'accordo

### <a name="no-agreement-found"></a>Non sono stati trovati accordi 

**Descrizione dell'errore**: non è stato trovato alcun accordo con i parametri di risoluzione dell'accordo.

**Azione dell'utente**: il contratto deve essere aggiunto all'account di integrazione con le identità di business concordate. Le identità di business devono corrispondere agli ID dei messaggi di input.

### <a name="no-agreement-found-with-identities"></a>Non sono stati trovati accordi con le identità

**Descrizione dell'errore**: non sono stati trovati accordi con le identità:' AS2Identity '::' Partner1' and'AS2Identity '::' partner3'

**Azione dell'utente**: AS2-From o AS2-To non valido configurato per l'accordo. Correggere l'intestazione "AS2-From" o "AS2-To" del messaggio AS2 o l'accordo in modo da assicurare la corrispondenza degli ID AS2 presenti nelle intestazioni del messaggio AS2 con le configurazioni dell'accordo.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Intestazioni del messaggio AS2 mancanti  

**Descrizione errore**: intestazioni AS2 non valide. Una delle intestazioni "AS2-To" o "AS2-From" è vuota.

**Azione dell'utente**: è stato ricevuto un messaggio AS2 che non contiene il AS2-From o AS2-To o entrambe le intestazioni. Controllare le intestazioni AS2-From e AS2-To del messaggio AS2 e correggerle in base alla configurazione dell'accordo.

### <a name="missing-as2-message-body-and-headers"></a>Corpo e intestazioni del messaggio AS2 mancanti    

**Descrizione dell'errore**: il contenuto della richiesta è null o vuoto.

**Azione dell'utente**: è stato ricevuto un messaggio AS2 che non contiene il corpo del messaggio.

### <a name="as2-message-decryption-failure"></a>Errore di decrittografia del messaggio AS2

**Descrizione errore**: [elaborato/errore: decrittografia-non riuscita]

**Azione dell'utente**: aggiungere @base64ToBinary a AS2Message prima dell'invio al partner.

Ad esempio:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Errore di decrittografia della notifica sulla ricezione del messaggio

**Descrizione errore**: [elaborato/errore: decrittografia-non riuscita]

**Azione dell'utente**: aggiungere @base64ToBinary al messaggio MDN prima dell'invio al partner.

Ad esempio:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Certificato di firma mancante

**Descrizione errore**: il certificato di firma non è stato configurato per l'entità AS2. AS2-From: partner1 AS2-To: partner2

**Azione dell'utente**: configurare le impostazioni dell'accordo AS2 con il certificato corretto per la firma.

## <a name="x12-and-edifact"></a>X12 ed EDIFACT

### <a name="leading-or-trailing-space-found"></a>Trovato spazio iniziale o finale    

**Descrizione errore**: si è verificato un errore durante l'analisi. Il set di transazioni EDIFACT con ID ' 123456' contenuto nell'interscambio (senza gruppo) con ID ' 987654', ID mittente ' Partner1', ID ricevitore ' partner2' verrà sospeso con i seguenti errori: "trovato separatore finale"

**Azione dell'utente**: le impostazioni dell'accordo da configurare per consentire lo spazio finale e finale. Modificare le impostazioni dell'accordo in modo che consentano lo spazio iniziale e finale.

![consentire spazio](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Controllo duplicati abilitato nell'accordo

**Descrizione errore**: numero di controllo duplicato

**Azione dell'utente**: questo errore indica che il messaggio ricevuto contiene numeri di controllo duplicati. Correggere il numero di controllo e inviare nuovamente il messaggio.

### <a name="missing-schema-in-the-agreement"></a>Schema mancante nell'accordo

**Descrizione errore**: si è verificato un errore durante l'analisi. Il set di transazioni X12 con ID ' 564220001' contenuto nel gruppo funzionale con ID ' 56422', nell'interscambio con ID ' 000056422', ID mittente ' 12345678', ID ricevitore ' 87654321' verrà sospeso con i seguenti errori: "il messaggio presenta un tipo di documento sconosciuto e non è stato risolto in nessuno degli schemi esistenti configurati nell'accordo"

**Azione utente**: Configura schema nelle impostazioni dell'accordo.

### <a name="incorrect-schema-in-the-agreement"></a>Schema non corretto nell'accordo

**Descrizione dell'errore**: il messaggio presenta un tipo di documento sconosciuto e non è stato risolto in nessuno degli schemi esistenti configurati nell'accordo.

**Azione utente**: configurare lo schema corretto nelle impostazioni dell'accordo.

## <a name="flat-file"></a>File flat

### <a name="input-message-with-no-body"></a>Messaggio di input senza corpo

**Descrizione errore**: InvalidTemplate. Impossibile elaborare le espressioni del linguaggio del modello negli input dell'azione "Flat_File_Decoding", riga "1" e colonna "1902". Per la proprietà obbligatoria "content" è previsto un valore ma risulta null. Percorso ".'.

**Azione dell'utente**: questo errore indica che il messaggio di input non contiene un corpo.
