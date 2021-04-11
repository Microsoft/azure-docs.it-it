---
title: Convertire le configurazioni in risorse composite per State Configuration di Automazione di Azure
description: Questo articolo descrive come convertire le configurazioni in risorse composite per State Configuration di Automazione di Azure.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563573"
---
# <a name="convert-configurations-to-composite-resources"></a>Convertire le configurazioni in risorse composite

> Si applica a: Windows PowerShell 5.1

Dopo aver iniziato a creare configurazioni, è possibile creare rapidamente "scenari" per la gestione di gruppi di impostazioni.
Alcuni esempi potrebbero essere:

- Creare un server Web
- Creare un server DNS
- Creare un server di SharePoint
- Configurare un cluster SQL
- Gestire le impostazioni del firewall
- Gestire le impostazioni per le password

Se si è interessati a condividere questo lavoro con altri utenti, l'opzione migliore consiste nel creare un pacchetto della configurazione come [risorsa composita](/powershell/scripting/dsc/resources/authoringresourcecomposite).
La creazione di risorse composite per la prima volta può risultare impegnativa.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-compositeresource"></a>Progetto della community: CompositeResource

Per risolvere questo problema, è stata creata una soluzione gestita dalla community denominata [CompositeResource](https://github.com/microsoft/compositeresource).

CompositeResource automatizza il processo di creazione di un nuovo modulo dalla configurazione.
Si inizia con il [dot sourcing](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) dello script di configurazione nella workstation (o nel server di compilazione) in modo che venga caricato in memoria.
Quindi, invece di eseguire la configurazione per generare un file MOF, usare la funzione fornita dal modulo CompositeResource per automatizzare una conversione.
Il cmdlet caricherà il contenuto della configurazione, otterrà l'elenco dei parametri e genererà un nuovo modulo con tutti gli elementi necessari.

Dopo aver generato un modulo, è possibile incrementare la versione e aggiungere le note sulla versione ogni volta che si apportano modifiche e lo si pubblica nel [repository PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo) personale.

Dopo aver creato un modulo di risorsa composita contenente la configurazione (o più configurazioni), è possibile usarli nell' [esperienza di creazione componibile](./compose-configurationwithcompositeresources.md) in Azure oppure aggiungerli agli script di [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e [caricare i file MOF in automazione di Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrare quindi i server da [locale](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) o [in Azure](./automation-dsc-onboarding.md#enable-azure-vms) per eseguire il pull delle configurazioni.
L'aggiornamento più recente del progetto ha pubblicato anche [runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) per Automazione di Azure per automatizzare il processo di importazione delle configurazioni da PowerShell Gallery.

Per provare ad automatizzare la creazione di risorse composite per DSC, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) e scaricare la soluzione oppure fare clic su "Project Site" (Sito del progetto) per visualizzare la [documentazione](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su PowerShell DSC, vedere [Panoramica della configurazione di Desired State Configuration per Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- In [Risorse DSC](/powershell/scripting/dsc/resources/resources) sono disponibili informazioni sulle risorse di PowerShell DSC.
- Per altri dettagli sulla configurazione di Configuration Manager locale, vedere [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig).
