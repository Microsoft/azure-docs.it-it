---
title: Risolvere un errore di creazione del cluster di Azure Data ExplorerTroubleshoot failure of Azure Data Explorer cluster creation
description: Questo articolo descrive i passaggi di risoluzione dei problemi per la creazione di un cluster in Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562412"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Risoluzione dei problemi: Creazione del cluster non riuscita di Azure Data ExplorerTroubleshoot: Failed cluster creation of Azure Data Explorer

Nel caso improbabile che si verifichi un errore di creazione del cluster in Esplora dati di Azure, seguire questa procedura.

1. Assicurarsi di disporre delle autorizzazioni appropriate. Per creare un cluster è necessario essere un membro del ruolo *Collaboratore* oppure *Proprietario* della sottoscrizione di Azure. Se necessario rivolgersi all'amministratore della sottoscrizione per essere aggiunti al ruolo appropriato.

1. Assicurarsi che non ci siano errori di validazione relativi al nome del cluster inserito in **Creare cluster** nel portale di Azure.

1. Controllare il [dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/). Cercare lo stato di Esplora dati di Azure nell'area in cui si sta cercando di creare il cluster.

    Se lo stato non è **Buono** (segno di spunta verde), provare a creare il cluster una volta avvenuto un miglioramento dello stato.

1. Per richiedere ulteriore assistenza per la risoluzione del problema, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
