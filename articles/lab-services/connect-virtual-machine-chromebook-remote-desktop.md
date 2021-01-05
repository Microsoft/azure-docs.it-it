---
title: Come connettersi a una macchina virtuale di Azure Lab Services da un Chromebook | Microsoft Docs
description: Informazioni su come connettersi da un Chromebook a una macchina virtuale in Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813225"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Connettersi a una macchina virtuale con Remote Desktop Protocol in un Chromebook

Questa sezione illustra come uno studente può connettersi a una macchina virtuale di un lab per le classi da un Chromebook usando Remote Desktop Protocol (RDP).

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Installare Desktop remoto Microsoft in un Chromebook

1. Aprire l'App Store nel Chromebook e cercare **Desktop remoto Microsoft**.

    ![Desktop remoto Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Installare la versione più recente di Desktop remoto Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Accedere alla macchina virtuale dal Chromebook usando RDP

1. Aprire il file **RDP** scaricato nel computer in cui è installato **Desktop remoto Microsoft**. Dovrebbe iniziare a connettersi alla macchina virtuale. 

    ![Connettersi alla macchina virtuale](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Quando viene richiesto, immettere la password.

    ![Screenshot che mostra la schermata di accesso in cui immettere il nome utente e la password.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Selezionare **Continua** se viene visualizzato il seguente avviso. 

    ![Avviso del certificato](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Si dovrebbe visualizzare il desktop della macchina virtuale a cui ci si connette.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione alle macchine virtuali Linux, vedere [Connettersi a macchine virtuali Linux](how-to-use-remote-desktop-linux-student.md)

