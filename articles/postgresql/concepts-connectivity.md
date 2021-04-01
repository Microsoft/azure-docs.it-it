---
title: Gestire gli errori di connettività temporanei-database di Azure per PostgreSQL-server singolo
description: Informazioni su come gestire gli errori di connettività temporanei per database di Azure per PostgreSQL-server singolo.
keywords: connessione postgresql, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91704351"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Gestione degli errori di connettività temporanei per Database di Azure per PostgreSQL - Server singolo

Questo articolo descrive come gestire gli errori temporanei che si connettono al database di Azure per PostgreSQL.

## <a name="transient-errors"></a>Errori temporanei

Un errore temporaneo è un errore che si risolve in modo autonomo. Questi errori generalmente si manifestano come una connessione al server di database che viene interrotta. Inoltre non è possibile aprire nuove connessioni a un server. Possono verificarsi errori temporanei, ad esempio nel caso di un errore hardware o di rete. Un altro motivo potrebbe essere una nuova versione di un servizio PaaS che viene implementato. La maggior parte di questi eventi viene automaticamente attenuata dal sistema in meno di 60 secondi. Una procedura consigliata per la progettazione e lo sviluppo di applicazioni nel cloud è la previsione di errori temporanei, presupponendo che possano accadere in qualsiasi componente e in qualunque momento e che si disponga della logica appropriata per affrontare tali situazioni.

## <a name="handling-transient-errors"></a>Gestione degli errori temporanei

Gli errori temporanei devono essere gestiti usando la logica di ripetizione dei tentativi. Situazioni da considerare:

* Si verifica un errore quando si tenta di aprire una connessione
* Una connessione inattiva viene eliminata sul lato server. Se si prova a eseguire un comando non si riesce a eseguirlo
* Una connessione attiva che sta attualmente eseguendo un comando viene eliminata.

Il primo e il secondo caso sono abbastanza semplici da gestire. Provare ad aprire di nuovo la connessione. Quando l'operazione riesce, l'errore temporaneo è stato attenuato dal sistema. È possibile usare nuovamente il Database di Azure per PostgreSQL. È consigliabile attendere prima di riprovare a effettuare la connessione. Interrompere temporaneamente l'operazione se i tentativi iniziali hanno esito negativo. In questo modo il sistema può usare tutte le risorse disponibili per superare la situazione di errore. È consigliabile seguire questa procedura:

* Attendere 5 secondi prima di riprovare.
* Per ogni tentativo successivo, aumentare l'attesa in modo esponenziale, fino a 60 secondi.
* Impostare un numero massimo di tentativi nel punto in cui l'applicazione considera l'operazione non riuscita.

Quando una connessione con una transazione attiva ha esito negativo, la gestione corretta del ripristino è più difficile. Possono presentarsi due casi. Se la transazione era di sola lettura, è opportuno riaprire la connessione e riprovare la transazione. Se invece la transazione stava anche scrivendo nel database occorre stabilire se è stata sottoposta a rollback o se è riuscita prima che si verificasse l'errore temporaneo. In tal caso, è possibile che non sia stato ricevuto il riconoscimento del commit dal server di database.

Un modo per superare il problema consiste nel generare un ID univoco sul client usato per tutti i tentativi. Passare questo ID univoco come parte della transazione al server e archiviarlo in una colonna con un vincolo univoco. In questo modo è possibile riprovare a eseguire la transazione in tutta sicurezza. Il tentativo ha esito positivo se la transazione precedente è stata sottoposta a rollback e l'ID univoco generato dal client non esiste ancora nel sistema. Il tentativo ha esito negativo quando indica una violazione della chiave duplicata se l'ID univoco è stato già archiviato, perché la transazione precedente è stata completata correttamente.

Se il programma comunica con il Database di Azure per PostgreSQL tramite middleware di terze parti chiedere al fornitore se il middleware include la logica di ripetizione dei tentativi per errori temporanei.

Verificare di testare la logica di ripetizione dei tentativi. Ad esempio, provare a eseguire il codice mentre si ridimensionano le risorse di calcolo del server del Database di Azure per PostgreSQL. L'applicazione deve gestire senza problemi il breve tempo di inattività che si verifica durante questa operazione.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di connessione al Database di Azure per PostgreSQL](howto-troubleshoot-common-connection-issues.md)
