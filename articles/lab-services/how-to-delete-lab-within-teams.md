---
title: Eliminare un Lab Azure Lab Services dai team
description: Informazioni su come eliminare un Azure Lab Services Lab dai team.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433955"
---
# <a name="delete-labs-within-teams"></a>Elimina Lab all'interno di Team

Questo articolo illustra come eliminare un Lab dall'app **Azure Lab Services** .

## <a name="prerequisites"></a>Prerequisiti

* [Creare un account di Lab Services](tutorial-setup-lab-account.md#create-a-lab-account) nella portale di Azure.
* [Per iniziare, creare un Lab di Lab Services in teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Eliminare lab

Un Lab creato in teams può essere eliminato nel [sito Web di Lab Services](https://labs.azure.com) eliminando direttamente il Lab, come descritto in [Manage Labs in Azure Lab Services](how-to-manage-classroom-labs.md). 

L'eliminazione del Lab viene attivata anche quando il team viene eliminato. Se il team in cui viene creato il Lab viene eliminato, Lab verrebbe eliminato automaticamente 24 ore dopo l'attivazione della sincronizzazione automatica degli elenchi di utenti. 

> [!IMPORTANT]
> L'eliminazione della scheda o la disinstallazione dell'app non comporterà l'eliminazione del Lab. 

Se la scheda viene eliminata, gli utenti nell'elenco di appartenenza al team potranno comunque accedere alle macchine virtuali nel [sito Web di Lab Services](https://labs.azure.com) , a meno che l'eliminazione del Lab non venga attivata in modo esplicito eliminando il Lab nel sito Web o eliminando il team. 

## <a name="next-steps"></a>Passaggi successivi

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Gestire gli elenchi degli utenti del Lab nei team](how-to-manage-user-lists-within-teams.md)
- [Gestisci il pool di macchine virtuali del Lab in team](how-to-manage-vm-pool-within-teams.md)
- [Creare e gestire pianificazioni Lab all'interno di Team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale all'interno di teams-visualizzazione studente](how-to-access-vm-for-students-within-teams.md)

