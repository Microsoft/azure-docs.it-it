---
title: Sviluppo collaborativo distribuito di risorse Azure DevTest Labs
description: Fornisce le procedure consigliate per la configurazione di un ambiente di sviluppo distribuito e collaborativo per lo sviluppo di risorse DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: caf4bd13f2ec9c45db392a027db269b492cbd802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550077"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Procedure consigliate per lo sviluppo di risorse Azure DevTest Labs distribuite e collaborative
Lo sviluppo collaborativo distribuito consente a diversi team o persone di sviluppare e gestire una codebase. Per avere esito positivo, il processo di sviluppo dipende dalla possibilità di creare, condividere e integrare le informazioni. Questo principio di sviluppo chiave può essere utilizzato in Azure DevTest Labs. Sono disponibili diversi tipi di risorse all'interno di un Lab, comunemente distribuiti tra diversi Lab all'interno di un'azienda. I diversi tipi di risorse sono incentrati su due aree:

- Risorse archiviate internamente nel Lab (basate su Lab)
- Risorse archiviate in repository [esterni connessi al Lab](devtest-lab-add-artifact-repo.md) (basato sul repository di codice). 

Questo documento descrive alcune procedure consigliate che consentono la collaborazione e la distribuzione tra più team garantendo al tempo stesso personalizzazione e qualità a tutti i livelli.

## <a name="lab-based-resources"></a>Risorse basate su Lab

### <a name="custom-virtual-machine-images"></a>Immagini di macchine virtuali personalizzate
È possibile disporre di un'origine comune di immagini personalizzate che vengono distribuite nei Lab su base notturna. Per informazioni dettagliate, vedere [Image Factory](image-factory-create.md).    

### <a name="formulas"></a>Formule
Le [formule](devtest-lab-manage-formulas.md) sono specifiche del Lab e non hanno un meccanismo di distribuzione. I membri del Lab eseguono tutte le attività di sviluppo delle formule. 

## <a name="code-repository-based-resources"></a>Risorse basate sul repository di codice
Sono disponibili due funzionalità diverse basate su repository di codice, elementi e ambienti. In questo articolo vengono esaminate le funzionalità e viene illustrato come configurare in modo più efficace i repository e il flusso di lavoro per consentire la personalizzazione degli elementi e degli ambienti disponibili a livello dell'organizzazione o del team.  Questo flusso di lavoro è basato sulla strategia standard di [diramazione del controllo del codice sorgente](/azure/devops/repos/tfvc/branching-strategies-with-tfvc). 

### <a name="key-concepts"></a>Concetti chiave
Le informazioni di origine per gli artefatti includono metadati, script. Le informazioni di origine per gli ambienti includono metadati e modelli di Gestione risorse con tutti i file di supporto, ad esempio script di PowerShell, script DSC, file zip e così via.  

### <a name="repository-structure"></a>Struttura del repository  
La configurazione più comune per il controllo del codice sorgente (SCC) consiste nel configurare una struttura a più livelli per l'archiviazione di file di codice (Gestione risorse modelli, metadati e script) usati nei Lab. In particolare, è possibile creare repository diversi per archiviare le risorse gestite dai diversi livelli aziendali:   

- Risorse a livello di azienda.
- Risorse business unit/Division
- Risorse specifiche del team.

Ognuno di questi livelli è collegato a un repository diverso in cui il ramo principale deve essere della qualità di produzione. I [rami](/azure/devops/repos/git/git-branching-guidance) in ogni repository sono per lo sviluppo di tali risorse specifiche (elementi o modelli). Questa struttura è perfettamente allineata a DevTest Labs, in quanto è possibile connettere facilmente più repository e più rami contemporaneamente ai Lab dell'organizzazione. Il nome del repository è incluso nell'interfaccia utente (UI) per evitare confusione quando sono presenti nomi, descrizioni e server di pubblicazione identici.
     
Il diagramma seguente mostra due repository: un repository aziendale gestito dalla divisione IT e un repository di divisione gestito dalla divisione R&D.

![Un ambiente di sviluppo distribuibile e collaborativo di esempio](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Questa struttura a più livelli consente lo sviluppo mantenendo un livello di qualità superiore nel ramo principale, mentre la presenza di più repository connessi a un Lab consente una maggiore flessibilità.

## <a name="next-steps"></a>Passaggi successivi    
Vedere gli articoli seguenti:

- Aggiungere un repository a un Lab usando il [portale di Azure](devtest-lab-add-artifact-repo.md) o tramite il [modello di gestione risorse di Azure](add-artifact-repository.md)
- [Elementi di DevTest Labs](devtest-lab-artifact-author.md)
- [Ambienti DevTest Labs](devtest-lab-create-environment-from-arm.md).
