---
title: Trasferimenti di Azure Enterprise
description: Descrive i trasferimenti di Azure EA
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943686"
---
# <a name="azure-enterprise-transfers"></a>Trasferimenti di Azure Enterprise

Questo articolo offre una panoramica dei trasferimenti aziendali.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Trasferire un account aziendale in una nuova registrazione

Un trasferimento di account sposta il proprietario dell'account da una registrazione a un'altra. Tutte le sottoscrizioni correlate del proprietario dell'account vengono spostate nella registrazione di destinazione. Usare un trasferimento di account se si hanno più registrazioni attive e si vogliono spostare solo determinati proprietari di account.

Questa sezione è unicamente a scopo informativo perché l'azione non può essere eseguita da un amministratore dell'organizzazione. Per trasferire un account aziendale in una nuova registrazione, è necessaria una richiesta di supporto.

Quando si trasferisce un account aziendale in una nuova registrazione, tenere presente quanto segue:

- Vengono trasferiti solo gli account specificati nella richiesta. Se si selezionano tutti gli account, vengono trasferiti tutti.
- La registrazione di origine mantiene il proprio stato come attiva o estesa. È possibile continuare a usarla fino alla scadenza.

### <a name="prerequisites"></a>Prerequisiti

Quando si richiede il trasferimento di un account, fornire le informazioni seguenti:

- Numero della registrazione di destinazione, nome dell'account e indirizzo di posta elettronica del proprietario dell'account da trasferire
- Per la registrazione di origine, numero di registrazione e account da trasferire

Altri punti da tenere presenti prima del trasferimento di un account:

- Per la registrazione di destinazione e di origine è necessaria l'approvazione di un amministratore EA
- Se il trasferimento di un account non soddisfa le proprie esigenze, prendere in considerazione un trasferimento di registrazione.
- Con il trasferimento vengono trasferiti tutti i servizi e le sottoscrizioni correlati agli account specifici.
- Al termine del trasferimento, l'account trasferito appare inattivo nella registrazione di origine e attivo in quella di destinazione.
- L'account mostra la data di fine corrispondente alla data del trasferimento effettivo nella registrazione di origine e come data di inizio nella registrazione di destinazione.
- Qualsiasi utilizzo che si verifica per l'account prima della data del trasferimento effettivo rimane nella registrazione di origine.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Trasferire la registrazione EA in una nuova registrazione

Il trasferimento di una registrazione si prende in considerazione quando:

- Il periodo di pagamento anticipato di una registrazione corrente è terminato.
- Una registrazione è nello stato di scadenza/estensione e viene negoziato un nuovo contratto.
- Si hanno più registrazioni e si vogliono combinare tutti gli account e la fatturazione in una singola registrazione.

Questa sezione è unicamente a scopo informativo perché l'azione non può essere eseguita da un amministratore dell'organizzazione. È necessaria una richiesta di supporto per trasferire una registrazione Enterprise a una nuova, a meno che la registrazione non sia idonea per il [trasferimento automatico](#auto-enrollment-transfer).

Quando si richiede il trasferimento di un'intera registrazione EA in un'altra registrazione, si verificano le azioni seguenti:

- Tutti i servizi di Azure, le sottoscrizioni, gli account, i reparti e l'intera struttura di registrazione, inclusi tutti gli amministratori del reparto EA, vengono trasferiti in una nuova registrazione di destinazione.
- Lo stato della registrazione viene impostato su _Trasferito_. La registrazione trasferita è disponibile solo per la creazione di report cronologici sull'utilizzo.
- Non è possibile aggiungere ruoli o sottoscrizioni a una registrazione trasferita. Lo stato trasferito impedisce un maggiore utilizzo per la registrazione.
- Qualsiasi saldo del pagamento anticipato di Azure rimanente nel contratto va perso, inclusi i periodi futuri.
-    Se la registrazione da cui si sta effettuando il trasferimento prevede l'acquisto di RI, la tariffa di acquisto del RI rimarrà nella registrazione di origine, tuttavia tutti i vantaggi di RI verranno trasferiti tra loro per l'uso nella nuova registrazione.
-    Il prezzo di acquisto monouso del Marketplace ed eventuali tariffe fisse mensili già addebitate alla registrazione precedente non vengono trasferiti alla nuova registrazione. Verranno trasferiti gli addebiti per il Marketplace basati sul consumo.

### <a name="effective-transfer-date"></a>Data di validità del trasferimento

La data di validità del trasferimento può essere una data corrispondente o successiva alla data di inizio della registrazione di destinazione.

L'utilizzo della registrazione di origine viene addebitato in base al pagamento anticipato o come eccedenza. L'utilizzo che si verifica dopo la data di trasferimento effettiva viene trasferito alla nuova registrazione e addebitato.

### <a name="prerequisites"></a>Prerequisiti

Quando si richiede il trasferimento di una registrazione, fornire le informazioni seguenti:

- Per la registrazione di origine, numero di registrazione.
- Per la registrazione di destinazione, numero della registrazione in cui eseguire il trasferimento.
- Come data di validità del trasferimento della registrazione, una data corrispondente o successiva alla data di inizio della registrazione di destinazione. La data scelta non può influire sull'utilizzo di alcuna fattura in eccedenza già eseguita.

Altri punti da tenere presenti prima del trasferimento di una registrazione:

- È necessaria l'approvazione degli amministratori EA sia della registrazione di destinazione che di quella di origine.
- Se il trasferimento di una registrazione non soddisfa le proprie esigenze, prendere in considerazione un trasferimento di account.
- Lo stato della registrazione di origine verrà aggiornato a Trasferito e sarà disponibile solo per la creazione di report cronologici sull'utilizzo.

### <a name="auto-enrollment-transfer"></a>Trasferimento di registrazione automatica

Si potrebbe notare che una registrazione ha lo stato **trasferito** , anche se non è stato inviato un ticket di supporto per richiedere un trasferimento di registrazione. Lo stato **trasferito** risulta dal processo di trasferimento di registrazione automatica. Affinché il processo di trasferimento automatico venga eseguito durante la frase di rinnovo, è necessario includere alcuni elementi nel nuovo contratto:

- Numero di registrazione precedente (deve esistere nel portale EA)
- La data di scadenza del numero di registrazione precedente è un giorno prima della data di inizio effettiva del nuovo contratto
- Il nuovo contratto ha un ordine prepagato di Azure fatturato con una data corrente o è obsoleto
- La nuova registrazione viene creata nel portale EA

Se non sono presenti dati di utilizzo mancanti nel portale EA tra la registrazione precedente e la nuova registrazione, non è necessario creare un ticket di supporto per il trasferimento.

### <a name="azure-prepayment"></a>Pagamento anticipato di Azure

Il pagamento anticipato di Azure non è trasferibile tra registrazioni. I saldi del pagamento anticipato di Azure sono vincolati contrattualmente alla registrazione in cui è stato ordinato. Il pagamento anticipato di Azure non viene trasferito come parte del processo di trasferimento dell'account o della registrazione.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Nessun servizio interessato dai trasferimenti di account e registrazioni

Durante il trasferimento di un account o una registrazione non si verifica alcun tempo di inattività. Il trasferimento può essere completato nello stesso giorno della richiesta, se vengono fornite tutte le informazioni necessarie.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Trasferimento di una sottoscrizione Enterprise a una sottoscrizione con pagamento in base al consumo

Per trasferire una sottoscrizione Enterprise a una sottoscrizione individuale con pagamento in base al consumo è necessario creare una nuova richiesta di supporto in Azure Enterprise Portal. Per creare una richiesta di supporto selezionare **+ Nuova richiesta di supporto** nell'area **Guida e supporto tecnico**.

## <a name="change-azure-subscription-or-account-ownership"></a>Cambiare la proprietà dell'account o della sottoscrizione di Azure

Azure EA Portal può trasferire le sottoscrizioni da un proprietario dell'account a un altro. Per altre informazioni, vedere [Cambiare la proprietà dell'account o della sottoscrizione di Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Effetti del trasferimento della sottoscrizione

Quando una sottoscrizione di Azure viene trasferita in un account nello stesso tenant di Azure Active Directory, tutti gli utenti, i gruppi e le entità servizio con il [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) per gestire le risorse conservano l'accesso.

Per visualizzare gli utenti con autorizzazioni di controllo degli accessi in base al ruolo alla sottoscrizione:

1. Nel portale di Azure aprire **Sottoscrizioni**.
2. Selezionare la sottoscrizione da visualizzare e quindi selezionare **Controllo di accesso (IAM)** .
3. Selezionare le **Assegnazioni di ruoli**. La pagina Assegnazioni di ruolo elenca tutti gli utenti con autorizzazioni di controllo degli accessi in base al ruolo alla sottoscrizione.

Se la sottoscrizione viene trasferita a un account in un altro tenant di Azure AD, tutti gli utenti, i gruppi e le entità servizio che disponevano del [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) per gestire le risorse _perdono_ l'accesso. Anche senza le autorizzazioni di controllo degli accessi in base al ruolo, l'accesso alla sottoscrizione potrebbe essere disponibile tramite meccanismi di sicurezza, tra cui:

- Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../../cloud-services/cloud-services-certs-create.md).
- Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../../storage/common/storage-account-overview.md).
- Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se il destinatario deve limitare l'accesso alle risorse di Azure, è consigliabile che esegua l'aggiornamento degli eventuali segreti associati al servizio. La maggior parte delle risorse può essere aggiornate usando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Tutte le risorse** dal menu Hub.
3. Selezionare la risorsa.
4. Nella pagina della risorsa selezionare **Impostazioni** per visualizzare e aggiornare i segreti esistenti.

## <a name="next-steps"></a>Passaggi successivi

- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](ea-portal-troubleshoot.md).