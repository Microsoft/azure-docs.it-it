---
title: Esempi di PowerShell
description: Esempi di Azure PowerShell relativi ad alcuni degli scenari comuni di Servizio app. Informazioni su come automatizzare le attività di gestione o distribuzione di Servizio app.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: a1577d42de9a4452467a448a0de5cd5f9575a55f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169426"
---
# <a name="powershell-samples-for-azure-app-service"></a>Esempi di PowerShell per Servizio app di Azure

La tabella seguente include i collegamenti agli script PowerShell compilati tramite Azure PowerShell.

| Script | Descrizione |
|-|-|
|**Creare un'app**||
| [Creare un'app con la distribuzione da GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app che esegue il pull del codice da GitHub. |
| [Creare un'app con distribuzione continua da GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app che distribuisce in modo continuo il codice da GitHub. |
| [Creare un'app e distribuire il codice da FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e carica i file da una directory locale tramite FTP. |
| [Creare un'app e distribuire il codice da un archivio Git locale](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e configura il push del codice da un repository Git locale. |
| [Creare un'app e distribuire il codice in un ambiente di gestione temporanea](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
|  [Creare un'app ed esporla con un endpoint privato](./scripts/powershell-deploy-private-endpoint.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app con un endpoint privato. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'app](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app ed esegue il mapping di un nome di dominio personalizzato a essa. |
| [Associare un certificato TLS/SSL personalizzato a un'app](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e vi associa il certificato TLS/SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'app manualmente](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e la ridimensiona su 2 istanze. |
| [Ridimensionare un'app a livello globale con un'architettura a disponibilità elevata](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea due app del servizio app in due aree geografiche diverse e le rende disponibili tramite un singolo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Connettere un'app a un database SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e un database nel database SQL di Azure e quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'app a un account di archiviazione](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e un account di archiviazione e quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
|**Eseguire il backup e il ripristino delle app**||
| [Eseguire il backup di un'app](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e un backup singolo per essa. |
| [Creare un backup pianificato per un'app](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e un backup pianificato per essa. |
| [Eliminare un backup per un'app](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina un backup esistente per un'app. |
| [Ripristinare un'app dal backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app da un backup precedentemente completato. |
| [Ripristinare un backup tra sottoscrizioni](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app Web da un backup in un'altra sottoscrizione. |
|**Monitorare un'app**||
| [Monitorare un'app con i log del server Web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app, abilita la creazione di log per essa e scarica i log nel computer locale. |
| | |
