---
title: Configurare un lab focalizzato sull'apprendimento avanzato usando Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare lo script della shell in Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257760"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurare un lab incentrato sul Deep Learning nell'elaborazione del linguaggio naturale con Azure Lab Services
Questo articolo illustra come configurare un lab focalizzato sull'apprendimento avanzato nell'elaborazione del linguaggio naturale (PNL) con Azure Lab Services. L'elaborazione del linguaggio naturale è una forma di intelligenza artificiale che dota i computer di strumenti di traduzione, riconoscimento vocale e altre funzionalità di comprensione del linguaggio.  

Gli studenti che frequentano una classe di elaborazione del linguaggio naturale lavorano su una macchina virtuale Linux per apprendere come applicare gli algoritmi di rete neurale al fine di sviluppare modelli di Deep Learning usati per l'analisi del linguaggio umano scritto. 

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo Lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Quando si dispone di una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services o usare un account lab esistente. Vedere l'esercitazione seguente per la creazione di un nuovo account Lab: [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).
 
Dopo aver creato l'account Lab, abilitare le impostazioni seguenti nell'account Lab: 

| Impostazione dell'account Lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine Data Science Virtual Machine per Linux (Ubuntu) per l'uso nell'account Lab.  Per istruzioni, vedere l'articolo seguente: [specificare le immagini del Marketplace disponibili per gli autori del Lab](specify-marketplace-images.md). | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo Lab e applicare le impostazioni seguenti:

| Impostazioni Lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni delle macchine virtuali (VM) | Small GPU (Compute). Queste dimensioni sono ideali per applicazioni a elevato utilizzo di calcolo e di rete, come l'intelligenza artificiale e l'apprendimento avanzato. |
| Immagine VM | [Data Science Virtual Machine per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Questa immagine fornisce Framework e strumenti per Deep learning per Machine Learning e data science. Per visualizzare l'elenco completo degli strumenti installati in questa immagine, vedere l'articolo seguente: [cosa è incluso in DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Abilita connessione Desktop remoto | Abilita. <p>L'abilitazione di questa impostazione consentirà agli insegnanti e agli studenti di connettersi alle macchine virtuali (VM) utilizzando Desktop remoto (RDP).</p><p>**Importante**: il protocollo RDP è già installato e configurato nell'immagine Data Science Virtual Machine per Linux. Di conseguenza, insegnanti e studenti possono connettersi alle macchine virtuali tramite RDP senza ulteriori passaggi. Inoltre, se è necessario connettersi al desktop con interfaccia grafica, in questa immagine è già installato il [Server x2go](https://wiki.x2go.org/doku.php/doc:newtox2go) nella macchina virtuale. Gli studenti devono installare il client di X2Go nei computer locali e devono usare il client per la connessione. Per ulteriori informazioni, vedere le guide seguenti: <ul><li>[Come accedere alla macchina virtuale per l'analisi scientifica dei dati per Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Connettersi alla macchina virtuale modello per installare i pacchetti RDP e GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

L'immagine Data Science Virtual Machine per Linux fornisce i Framework e gli strumenti di apprendimento avanzato necessari per questo tipo di classe. Di conseguenza, dopo la creazione del computer modello, non è necessario personalizzarlo ulteriormente. Può essere pubblicata per gli studenti da usare. Selezionare il pulsante **pubblica** nella pagina modello per pubblicare il modello nel Lab.  

## <a name="cost"></a>Costi
Se si desidera stimare il costo di questo Lab, è possibile utilizzare l'esempio seguente: 

Per una classe di 25 studenti con 20 ore di tempo di classe pianificata e 10 ore di quota per il lavoro o le assegnazioni, il prezzo per il Lab sarà di-25 studenti * (20 + 10) ore * 139 unità Lab * 0,01 USD all'ora = 1042,5 USD

Per ulteriori informazioni sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni
Questo articolo illustra la procedura per creare un Lab per la classe di elaborazione del linguaggio naturale. È possibile usare una configurazione simile per altre classi di apprendimento avanzato.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi Lab:

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users). 

