---
title: Microsoft Threat Modeling Tool versione 1/29/2019
titleSuffix: Azure
description: Leggere le note sulla versione per il Microsoft Threat Modeling Tool rilasciato il 1/29/2019. Le note includono le modifiche alle funzionalità e i problemi noti.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87539051"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Aggiornamento di Threat Modeling Tool versione 7.1.60126.1 - 29/01/2019

La versione 7.1.60126.1 di Microsoft Threat Modeling Tool è stata rilasciata il 29 gennaio 2019 e contiene le modifiche seguenti:

- La versione minima richiesta di .NET è stata aumentata a [4.7.1 .NET](https://go.microsoft.com/fwlink/?LinkId=863262).
- La versione minima richiesta di Windows è stata incrementata a [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) a causa della dipendenza .NET.
- Una funzionalità di attivazione/disattivazione della convalida del modello è stata aggiunta al menu Options (Opzioni) dello strumento.
- Sono stati aggiornati alcuni collegamenti nelle Proprietà della minaccia.
- Sono state apportate modifiche minime per l'esperienza utente alla schermata iniziale dello strumento.
- Threat Modeling Tool eredita ora le impostazioni di TLS dal sistema operativo host ed è supportato negli ambienti che richiedono TLS 1.2 o versione successiva.

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità

### <a name="model-validation-option"></a>Opzione di convalida del modello

In base al feedback dei clienti, allo strumento è stata aggiunta un'opzione per abilitare o disabilitare la convalida del modello. In precedenza, se il modello usava un singolo flusso di dati unidirezionali tra due oggetti, è possibile che sia stato ricevuto un messaggio di errore nel frame dei messaggi che informa: ObjectName richiede almeno uno "any". La disabilitazione della convalida del modello impedirà la visualizzazione di questi avvisi nella vista.

L'opzione per abilitare e disabilitare la convalida del modello è reperibile in File->Settings->Options menu (File ->Impostazioni->Menu delle opzioni). Il valore predefinito per questa impostazione è Disabilitato.

![Opzione di convalida del modello](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="known-issues"></a>Problemi noti

### <a name="unsupported-systems"></a>Sistemi non supportati

#### <a name="issue"></a>Problema

Gli utenti dei sistemi Windows 10 che non sono in grado di installare .NET 4.7.1 o versioni successive, ad esempio Windows 10 Enterprise LTSB (versione 1507), non potranno aprire lo strumento dopo l'aggiornamento. Queste versioni precedenti di Windows non sono più piattaforme supportate per Threat Modeling Tool e non dovrebbero installare l'ultimo aggiornamento.

#### <a name="workaround"></a>Soluzione alternativa

Gli utenti di Windows 10 Enterprise LTSB (versione 1507) che hanno installato la versione più recente di Threat Modeling Tool possono ripristinare la versione precedente tramite la finestra di dialogo di disinstallazione in Apps & Features (App e funzionalità).

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](threat-modeling-tool.md) e include le informazioni [sull'uso dello strumento](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
