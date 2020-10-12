---
title: Informazioni sulle notifiche per l'integrità del servizio.
description: Le notifiche sull'integrità del servizio consentono di visualizzare i messaggi sull'integrità del servizio pubblicati da Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86529013"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>Usare il portale di Azure per visualizzare le notifiche sull'integrità del servizio

Le notifiche di integrità del servizio sono pubblicate da Azure e contengono informazioni sulle risorse nella sottoscrizione. Queste notifiche sono una sottoclasse di eventi del log attività e sono disponibili anche nel log attività. Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Sono disponibili varie classi di notifiche sull'integrità del servizio:  

- **Azione richiesta:** Azure potrebbe notare un'anomalia nell'account e collaborare con l'utente per risolvere il problema. Azure invia una notifica con informazioni dettagliate sulle azioni da eseguire o sulla modalità di contatto del supporto tecnico o dell'assistenza di Azure.  
- **Evento imprevisto:** un evento che influisce sul servizio ha effetto su una o più risorse della sottoscrizione.  
- **Manutenzione:** un'attività di manutenzione pianificata che potrebbe avere effetto su una o più risorse della sottoscrizione.  
- **Informazioni:** potenziali ottimizzazioni che potrebbero migliorare l'uso delle risorse. 
- **Sicurezza:** informazioni urgenti relative alla sicurezza delle soluzioni in esecuzione in Azure.

Ogni notifica sull'integrità del servizio contiene informazioni dettagliate sull'ambito e sull'effetto sulle risorse. I dettagli includono:

Nome proprietà | Descrizione
-------- | -----------
channels | Uno dei valori seguenti: **Admin** o **Operation**.
correlationId | In genere un GUID in formato stringa. Gli eventi che appartengono alla stessa azione in genere condividono lo stesso correlationId.
eventDataId | Identificatore univoco di un evento.
eventName | Titolo di un evento.
livello | Livello di un evento.
resourceProviderName | Nome del provider di risorse della risorsa interessata.
resourceType| Tipo della risorsa interessata.
subStatus | In genere il codice di stato HTTP della chiamata REST corrispondente, ma può includere anche altre stringhe che descrivono uno stato secondario. Ad esempio: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504).
eventTimestamp | Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento.
submissionTimestamp | Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query.
subscriptionId | Sottoscrizione di Azure in cui è stato registrato l'evento.
status | Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: **Started**, **In Progress**, **Succeeded**, **Failed**, **Active** e **Resolved**.
operationName | Nome dell'operazione.
category | Questa proprietà è sempre **ServiceHealth**.
resourceId | ID risorsa della risorsa interessata.
Properties.title | Titolo della comunicazione localizzato. L'inglese è la lingua predefinita.
Properties.communication | Dettagli localizzati della comunicazione con markup HTML. L'inglese è la lingua predefinita.
Properties.incidentType | Uno dei valori seguenti: **ActionRequired**, **Informational**, **Incident**, **Maintenance**o **Security**.
Properties.trackingId | L'evento imprevisto a cui è associato l'evento. Usare questa proprietà per correlare gli eventi relativi a un evento imprevisto.
Properties.impactedServices | BLOB JSON preceduto da un carattere di escape che descrive i servizi e le aree su cui ha effetto l'evento imprevisto. La proprietà include un elenco di servizi, ognuno dei quali ha un **ServiceName** e un elenco delle aree interessate, ognuna con un nome di area **RegionName**.
Properties.defaultLanguageTitle | La comunicazione in inglese.
Properties.defaultLanguageContent | La comunicazione in inglese con markup HTML o come testo normale.
Properties.stage | I valori possibili di **Incident** e **Security** sono **Active,** **Resolved** o **RCA**. Per **ActionRequired** o **Informational** l'unico valore è **Active.** Per **la manutenzione** sono: **attivo**, **pianificato**, in **corso**, **annullato**, **ripianificato**, **risolto**o **completo**.
Properties.communicationId | La comunicazione a cui è associato l'evento.

### <a name="details-on-service-health-level-information"></a>Informazioni sui livelli di integrità dei servizi

**Azione necessaria** (properties.incidentType == ActionRequired)
- Informational: l'azione dell'amministratore è necessaria per impedire l'effetto sui servizi esistenti.
    
**Manutenzione** (properties.incidentType == Maintenance)
- Avviso-manutenzione di emergenza
- Informazioni-manutenzione pianificata standard

**Informazioni** (properties.incidentType == Information)
- Informational: l'amministratore potrebbe essere necessario per impedire l'effetto sui servizi esistenti.

**Sicurezza** (properties.incidentType == Security)
- Avviso: avvisi di sicurezza che influiscono sui servizi esistenti e che potrebbero richiedere l'intervento dell'amministratore.
- Avvisi informativi sulla sicurezza che influiscono sui servizi esistenti.

**Problemi relativi al servizio** (properties.incidentType == Incident)
- Errore: un grande numero di clienti ha problemi di ampia portata ad accedere a più servizi in più aree.
- Avviso: un sottoinsieme di clienti ha problemi ad accedere a servizi specifici e/o aree specifiche.
- Informativo: i problemi influiscono sulle operazioni di gestione e/o sulla latenza senza alcun impatto sulla disponibilità del servizio.
