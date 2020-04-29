---
title: Configurazione basata su STIG da usare nella configurazione dello stato-automazione di Azure
description: Informazioni sulle configurazioni basate su STIG per la configurazione dello stato in automazione di Azure.
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6d257198fcae54b1214d77f6b905d876d2687f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585542"
---
# <a name="configuration-based-on-stig"></a>Configurazione basata su STIG

> Si applica a: Windows PowerShell 5,1

La creazione di contenuto di configurazione per la prima volta può risultare complessa.
In molti casi, l'obiettivo è quello di automatizzare la configurazione dei server che seguono una "linea di base" che si spera sia allineata a una raccomandazione di settore.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-powerstig"></a>Progetto della community: PowerSTIG

Un progetto della community denominato [PowerSTIG](https://github.com/microsoft/powerstig) mira a risolvere questo problema generando contenuti DSC basati su [informazioni pubbliche](https://public.cyber.mil/stigs/) fornite da Stig (Guida all'implementazione tecnica della sicurezza).

La gestione delle linee di base è più complessa di quanto non sia il suono.
Molte organizzazioni devono [documentare le eccezioni](https://github.com/microsoft/powerstig#powerstigdata) alle regole e gestire tali dati su larga scala.
PowerSTIG risolve il problema fornendo [risorse composite](https://github.com/microsoft/powerstig#powerstigdsc) per risolvere ogni area della configurazione anziché tentare di risolvere l'intero intervallo di impostazioni in un file di grandi dimensioni.

Una volta generate le configurazioni, è possibile usare gli [script di configurazione DSC](/powershell/scripting/dsc/configurations/configurations) per generare file MOF e [caricare i file MOF in automazione di Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Quindi registrare i server da [locale](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) o [in Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) per eseguire il pull delle configurazioni.

Per provare PowerSTIG, visitare il [PowerShell Gallery](https://www.powershellgallery.com) e scaricare la soluzione oppure fare clic su "Project Site" per visualizzare la [documentazione](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Risorse DSC](/powershell/scripting/dsc/resources/resources)
- [Configurazione della Configuration Manager locale](/powershell/scripting/dsc/managing-nodes/metaconfig)
