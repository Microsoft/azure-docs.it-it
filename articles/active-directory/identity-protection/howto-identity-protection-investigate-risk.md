---
title: Esaminare Azure Active Directory Identity Protection di rischio
description: Informazioni su come analizzare gli utenti, i rilevamenti e gli accessi a rischio in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253481"
---
# <a name="how-to-investigate-risk"></a>Procedura: analizzare i rischi

Identity Protection offre alle organizzazioni tre report che possono usare per analizzare i rischi di identità nel proprio ambiente. Questi report sono gli **utenti a rischio, gli** **accessi a rischio**e i **rilevamenti dei rischi**. L'analisi degli eventi è fondamentale per comprendere meglio e identificare eventuali punti deboli nella strategia di sicurezza.

Tutti e tre i report consentono il download degli eventi in. Formato CSV per un'ulteriore analisi al di fuori della portale di Azure. I report utenti a rischio e accessi a rischio consentono di scaricare le voci 2500 più recenti, mentre il rapporto sui rilevamenti dei rischi consente di scaricare i record 5000 più recenti.

Le organizzazioni possono sfruttare i vantaggi delle integrazioni dell'API Microsoft Graph per aggregare i dati con altre origini a cui possono accedere come organizzazione.

I tre rapporti sono disponibili nella **portale di Azure** > **Azure Active Directory** > **sicurezza**.

## <a name="navigating-the-reports"></a>Esplorazione dei report

Ogni report viene avviato con un elenco di tutti i rilevamenti relativi al periodo indicato nella parte superiore del report. Ogni report consente l'aggiunta o la rimozione di colonne in base alle preferenze dell'amministratore. Gli amministratori possono scegliere di scaricare i dati in. CSV o. Formato JSON. I report possono essere filtrati utilizzando i filtri nella parte superiore del report.

Selezionando le singole voci è possibile abilitare voci aggiuntive nella parte superiore del report, ad esempio la possibilità di confermare un accesso come compromesso o sicuro, confermare un utente come compromesso oppure ignorare il rischio utente.

Selezionando singole voci, viene espansa una finestra dei dettagli sotto i rilevamenti. La visualizzazione dettagli consente agli amministratori di analizzare ed eseguire azioni su ogni rilevamento. 

![Esempio di report di Identity Protection che Mostra gli accessi a rischio e i dettagli](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Utenti a rischio

Con le informazioni fornite dal rapporto utenti a rischio, gli amministratori possono trovare:

- Quali utenti sono a rischio, sono stati risolti i rischi o sono stati rilevati rischi?
- Dettagli sui rilevamenti
- Cronologia di tutti gli accessi a rischio
- Cronologia rischio
 
Gli amministratori possono quindi scegliere di intervenire su questi eventi. Gli amministratori possono scegliere di:

- Reimpostare la password utente
- Conferma compromissione utente
- Ignorare il rischio utente
- Blocca l'accesso dell'utente
- Approfondire l'uso di Azure ATP

## <a name="risky-sign-ins"></a>Accessi a rischio

Il report degli accessi a rischio contiene dati filtrabili per un massimo di 30 giorni (1 mese).

Con le informazioni fornite dal report degli accessi a rischio, gli amministratori possono trovare:

- Quali accessi sono classificati come a rischio, sono stati confermati compromessi, sono stati confermati, sono stati rilasciati e risolti.
- Livelli di rischio in tempo reale e aggregati associati ai tentativi di accesso.
- Tipi di rilevamento attivati
- Criteri di accesso condizionale applicati
- Dettagli dell'autenticazione a più fattori
- Informazioni sul dispositivo
- Informazioni sull'applicazione
- Informazioni sulla posizione

Gli amministratori possono quindi scegliere di intervenire su questi eventi. Gli amministratori possono scegliere di:

- Confermare la compromissione dell'accesso
- Conferma l'accesso sicuro

## <a name="risk-detections"></a>Rilevamenti dei rischi

Il report rilevamento rischi contiene dati filtrabili per un massimo di 90 giorni (3 mesi).

Con le informazioni fornite dal rapporto sui rilevamenti dei rischi, gli amministratori possono trovare:

- Informazioni su ogni rilevamento dei rischi, incluso il tipo.
- Altri rischi attivati allo stesso tempo
- Percorso del tentativo di accesso
- Per altri dettagli, fare collegamento a Microsoft Cloud App Security (MCAS).

Gli amministratori possono quindi scegliere di tornare al report dei rischi o degli accessi dell'utente per eseguire azioni in base alle informazioni raccolte.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)

- [Abilitare l'accesso e i criteri di rischio utente](howto-identity-protection-configure-risk-policies.md)
