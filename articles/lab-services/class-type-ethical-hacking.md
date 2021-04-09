---
title: Configurare un Lab di hacking etico con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab usando Azure Lab Services per insegnare un hacking etico.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97616423"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Configurare un Lab per insegnare una classe di hacking etico

Questo articolo illustra come configurare una classe incentrata sul lato forense dell'hacking etico. I test di penetrazione, una pratica usata dalla community di hacking etico, si verificano quando un utente tenta di accedere al sistema o alla rete per dimostrare le vulnerabilità che possono essere sfruttate da un utente malintenzionato.

In un corso di hacking etico gli studenti possono apprendere tecniche moderne per la difesa dalle vulnerabilità. Ogni studente ottiene una macchina virtuale host Windows Server con due macchine virtuali annidate, una con l'immagine [Metasploitable3](https://github.com/rapid7/metasploitable3) e l'altra con l'immagine [Kali Linux](https://www.kali.org/). La macchina virtuale Metasploitable viene usata a scopo di exploit, mentre la macchina virtuale Kali fornisce l'accesso agli strumenti necessari per eseguire attività forensi.

Questo articolo è costituito da due sezioni principali. La prima sezione illustra come creare il Lab della classe. La seconda sezione illustra come creare il computer modello con la virtualizzazione annidata abilitata e con gli strumenti e le immagini necessari. In questo caso, un'immagine Metasploitable e un'immagine di Kali Linux in un computer in cui è abilitato Hyper-V per ospitare le immagini.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services o usare un account esistente. Vedere l'esercitazione seguente per la creazione di un nuovo account Lab: [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo Lab e quindi applicare le impostazioni seguenti:

| Dimensioni della macchina virtuale | Immagine |
| -------------------- | ----- |
| Media (virtualizzazione annidata) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Computer modello

Una volta creato il modello, avviare il computer e connettersi al computer per completare le tre attività principali seguenti.

1. Configurare il computer per la virtualizzazione nidificata. Abilita tutte le funzionalità di Windows appropriate, ad esempio Hyper-V, e configura la rete per le immagini Hyper-V in modo che sia in grado di comunicare tra loro e con Internet.
2. Configurare l'immagine di [Kali](https://www.kali.org/) Linux. Kali è una distribuzione Linux che include strumenti per i test di penetrazione e il controllo della sicurezza.
3. Configurare l'immagine di Metasploitable. Per questo esempio verrà usata l'immagine di [Metasploitable3](https://github.com/rapid7/metasploitable3) . Questa immagine viene creata per avere intenzionalmente vulnerabilità di sicurezza.

Nella parte restante di questo articolo vengono illustrati i passaggi manuali per completare le attività descritte in precedenza.  In alternativa, è possibile eseguire gli script [Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) e gli [script di hacking etico dei servizi Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparare il computer modello per la virtualizzazione nidificata

Seguire le istruzioni per [abilitare la virtualizzazione annidata](how-to-enable-nested-virtualization-template-vm.md) per preparare la macchina virtuale modello per la virtualizzazione nidificata.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurare una macchina virtuale annidata con l'immagine di Kali Linux

Kali è una distribuzione Linux che include strumenti per i test di penetrazione e il controllo della sicurezza.

1. Scaricare l'immagine dalla [protezione offensiva immagini della macchina virtuale Kali Linux](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Ricordare il nome utente e la password predefiniti indicati nella pagina di download.
    1. Scaricare l'immagine di **Kali Linux VMware 64-bit (7z)** per VMware.
    1. Estrarre il file con estensione 7z.  Se non si dispone già di 7 zip, scaricarlo da [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Ricordare la posizione della cartella estratta, che sarà necessaria in un secondo momento.
1. Convertire il file VMDK Estratto in un file VHDX in modo da poter usare il file VHDX con Hyper-V. Sono disponibili diversi strumenti per convertire le immagini VMware in immagini Hyper-V.  Verrà usato il [convertitore STARWIND V2V](https://www.starwindsoftware.com/starwind-v2v-converter).  Per eseguire il download, vedere la [pagina di download di STARWIND V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Avviare **STARWIND V2V Converter**.
    1. Nella pagina **selezionare il percorso dell'immagine da convertire** scegliere **file locale**.  Selezionare **Avanti**.
    1. Nella pagina **immagine di origine** passare a e selezionare il file vmdk di Kali Linux Estratto nel passaggio precedente per l'impostazione **nome file** .  Il file sarà nel formato Kali-Linux-{Version}-VMware-amd64. vmdk.  Selezionare **Avanti**.
    1. Nel **selezionare il percorso dell'immagine di destinazione** scegliere **file locale**.  Selezionare **Avanti**.
    1. Nella pagina **selezione formato immagine di destinazione** scegliere **VHD/VHDX**.  Selezionare **Avanti**.
    1. Nella pagina **Seleziona opzione per formato immagine VHD/VHDX** scegliere **immagine espandibile VHDX**.  Selezionare **Avanti**.
    1. Nella pagina **Seleziona nome file di destinazione** accettare il nome file predefinito.  Selezionare **Converti**.
    1. Nella pagina **conversione** attendere che l'immagine venga convertita.  Questa operazione può richiedere alcuni minuti.  Selezionare **fine** al termine della conversione.
1. Creare una nuova macchina virtuale Hyper-V.
    1. Aprire la console **di gestione di Hyper-V**.
    1. Scegliere **azione**  ->  **nuova**  ->  **macchina virtuale**.
    1. Nella pagina **prima di iniziare** della **procedura guidata nuova macchina virtuale** Selezionare **Avanti**.
    1. Nella pagina **impostazione nome e percorso** immettere **Kali-Linux** come **nome** e fare clic su **Avanti**.
    1. Nella pagina **specifica generazione** accettare le impostazioni predefinite e fare clic su **Avanti**.
    1. Nella pagina **assegnazione memoria** immettere **2048 MB** per la memoria di **avvio** e fare clic su **Avanti**.
    1. Nella pagina **Configura rete** lasciare la connessione **non connessa**. La scheda di rete verrà configurata in un secondo momento.
    1. Nella pagina **Connessione disco rigido virtuale** selezionare **Usa un disco rigido virtuale esistente**. Passare al percorso del file **Kali-Linux-{Version}-VMware-amd64. vmdk** creato nel passaggio precedente e selezionare **Avanti**.
    1. Nella pagina **completamento della creazione guidata macchina virtuale** , fare clic su **fine**.
    1. Una volta creata la macchina virtuale, selezionarla nella console di gestione di Hyper-V. Non accendere ancora il computer.  
    1. Scegliere   ->  **Impostazioni** azione.
    1. Nella finestra di dialogo **impostazioni per Kali-Linux** per selezionare **Aggiungi hardware**.
    1. Selezionare **scheda di rete legacy** e selezionare **Aggiungi**.
    1. Nella pagina **scheda di rete legacy** selezionare **LabServicesSwitch** per l'impostazione del **Commuter virtuale** e quindi fare clic su **OK**. LabServicesSwitch è stato creato durante la preparazione del computer modello per Hyper-V nella sezione **preparare il modello per la virtualizzazione nidificata** .
    1. È ora possibile usare l'immagine Kali-Linux. Dalla console di **gestione di Hyper-V** fare clic su **azione**  ->  **Start**, quindi scegliere **azione**  ->  **Connetti** per connettersi alla macchina virtuale.  Il nome utente predefinito è **Kali** e la password è **Kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configurare una macchina virtuale annidata con l'immagine Metasploitable  

L'immagine Rapid7 Metasploitable è un'immagine configurata appositamente con vulnerabilità di sicurezza. Questa immagine verrà usata per testare e individuare i problemi. Le istruzioni seguenti illustrano come usare un'immagine Metasploitable creata in precedenza. Tuttavia, se è necessaria una versione più recente dell'immagine Metasploitable, vedere [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Scaricare l'immagine di Metasploitable.
    1. Passare a [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Compilare il modulo per scaricare l'immagine e selezionare il pulsante **Submit (Invia** ).
    2. Selezionare il pulsante **Scarica Metasploitable Now** .
    3. Quando viene scaricato il file zip, Estrai il file zip e ricorda il percorso del file Metasploitable. vmdk.
1. Convertire il file VMDK Estratto in un file VHDX in modo da poter usare il file VHDX con Hyper-V. Sono disponibili diversi strumenti per convertire le immagini VMware in immagini Hyper-V.  Si userà nuovamente [STARWIND V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter) .  Per eseguire il download, vedere la [pagina di download di STARWIND V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Avviare **STARWIND V2V Converter**.
    1. Nella pagina **selezionare il percorso dell'immagine da convertire** scegliere **file locale**.  Selezionare **Avanti**.
    1. Nella pagina **immagine di origine** passare a e selezionare il Metasploitable. vmdk Estratto nel passaggio precedente per l'impostazione **nome file** .  Selezionare **Avanti**.
    1. Nel **selezionare il percorso dell'immagine di destinazione** scegliere **file locale**.  Selezionare **Avanti**.
    1. Nella pagina **selezione formato immagine di destinazione** scegliere **VHD/VHDX**.  Selezionare **Avanti**.
    1. Nella pagina **Seleziona opzione per formato immagine VHD/VHDX** scegliere **immagine espandibile VHDX**.  Selezionare **Avanti**.
    1. Nella pagina **Seleziona nome file di destinazione** accettare il nome file predefinito.  Selezionare **Converti**.
    1. Nella pagina **conversione** attendere che l'immagine venga convertita.  Questa operazione può richiedere alcuni minuti.  Selezionare **fine** al termine della conversione.
1. Creare una nuova macchina virtuale Hyper-V.
    1. Aprire la console **di gestione di Hyper-V**.
    1. Scegliere **azione**  ->  **nuova**  ->  **macchina virtuale**.
    1. Nella pagina **prima di iniziare** della **procedura guidata nuova macchina virtuale** Selezionare **Avanti**.
    1. Nella pagina **impostazione nome e percorso** immettere **Metasploitable** per **nome** e quindi fare clic su **Avanti**.

        ![Creazione guidata nuova immagine di macchina virtuale](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Nella pagina **specifica generazione** accettare le impostazioni predefinite e fare clic su **Avanti**.
    1. Nella pagina **assegnazione memoria** immettere **512 MB** per la memoria di **avvio** e fare clic su **Avanti**.

        ![Pagina Assegnazione memoria](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Nella pagina **Configura rete** lasciare la connessione **non connessa**. La scheda di rete verrà configurata in un secondo momento.
    1. Nella pagina **Connessione disco rigido virtuale** selezionare **Usa un disco rigido virtuale esistente**. Passare al percorso del file **metasploitable. vhdx** creato nel passaggio precedente e selezionare **Avanti**.

        ![Pagina Connetti disco rete virtuale](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Nella pagina **completamento della creazione guidata macchina virtuale** , fare clic su **fine**.
    1. Una volta creata la macchina virtuale, selezionarla nella console di gestione di Hyper-V. Non accendere ancora il computer.  
    1. Scegliere   ->  **Impostazioni** azione.
    1. Nella finestra di dialogo **impostazioni per Metasploitable** per selezionare **Aggiungi hardware**.
    1. Selezionare **scheda di rete legacy** e selezionare **Aggiungi**.

        ![Pagina scheda di rete](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Nella pagina **scheda di rete legacy** selezionare **LabServicesSwitch** per l'impostazione del **Commuter virtuale** e quindi fare clic su **OK**. LabServicesSwitch è stato creato durante la preparazione del computer modello per Hyper-V nella sezione **preparare il modello per la virtualizzazione nidificata** .

        ![Pagina scheda di rete legacy](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. L'immagine Metasploitable è ora pronta per l'uso. Dalla console di **gestione di Hyper-V** fare clic su **azione**  ->  **Start**, quindi scegliere **azione**  ->  **Connetti** per connettersi alla macchina virtuale.  Il nome utente predefinito è **msfadmin** e la password è **msfadmin**.

Il modello è stato aggiornato e presenta le immagini necessarie per una classe di test di penetrazione etica, un'immagine con strumenti per eseguire il test di penetrazione e un'altra immagine con vulnerabilità di sicurezza da individuare. È ora possibile pubblicare l'immagine modello nella classe. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.

## <a name="cost"></a>Costi  

Se si vuole stimare il costo di questo lab, è possibile usare l'esempio seguente:

Per una classe di 25 studenti con 20 ore di lezioni pianificate e 10 ore di quota per compiti o assegnazioni, il prezzo del lab sarà dato dalla formula seguente:

25 studenti \* (20 + 10) ore \* 55 unità Lab \* 0,01 USD all'ora = 412,50 USD

>[!IMPORTANT]
>La stima dei costi è solo a scopo esemplificativo. Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusione

Questo articolo illustra la procedura per creare un Lab per la classe di hacker etici. Sono inclusi i passaggi per configurare la virtualizzazione annidata per la creazione di due macchine virtuali all'interno della macchina virtuale host per il testing.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Inviare i collegamenti di registrazione agli studenti tramite posta elettronica](how-to-configure-student-usage.md#send-invitations-to-users).
