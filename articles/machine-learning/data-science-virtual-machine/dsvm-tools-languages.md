---
title: Lingue supportate
titleSuffix: Azure Data Science Virtual Machine
description: I linguaggi di programma supportati e gli strumenti correlati pre-installati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516431"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Linguaggi supportati dalla macchina virtuali per data science 

Il Data Science Virtual Machine (DSVM) è dotato di diversi linguaggi e strumenti di sviluppo predefiniti per la creazione di applicazioni di intelligenza artificiale (AI). Di seguito sono riportate alcune di quelle più importanti.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Category | Valore |
| ------------- | ------------- |
| Versioni del linguaggio supportate | Python 2,7 e 3,7 |
| Edizioni DSVM supportate      | Windows Server 2016     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | `conda`Vengono creati due ambienti globali: <br /> * L' `root` ambiente disponibile in `/anaconda/` è Python 3,7. <br/> * L' `python2` ambiente disponibile in `/anaconda/envs/python2` è Python 2,7.       |
| Collegamenti agli esempi      | Sono inclusi i notebook di Jupyter di esempio per Python.     |
| Strumenti correlati in DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Le build di Windows Server 2016 create prima del 2018 marzo contengono Python 3,5 e Python 2,7. Python 2,7 è l'ambiente **radice** conda e **py37** è l'ambiente Python 3,7.

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

* Eseguire al prompt dei comandi:

  Aprire un prompt dei comandi e usare uno dei metodi seguenti, a seconda della versione di Python che si vuole eseguire:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Usare in un IDE:

  Usare Python Tools for Visual Studio (PTVS), installato in Visual Studio Community Edition. Per impostazione predefinita, l'unico ambiente configurato automaticamente in PTVS è Python 3,6. 

    > [!NOTE]
    > Per puntare PTVS in Python 2,7, è necessario creare un ambiente personalizzato in PTVS. Per impostare questo percorso dell'ambiente in Visual Studio Community Edition, passare a **strumenti** strumenti  ->  **Python**  ->  **ambienti Python** e selezionare **+ personalizzato**. Impostare quindi il percorso su **c:\anaconda\envs\python2** e selezionare **rilevamento automatico**.

* Usare in Jupyter:

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come _Python [conda root]_ per Python 3,7 e _Python [conda ENV: python2]_ per Python 2,7.

* Installare i pacchetti Python:

  Gli ambienti Python predefiniti nella DSVM sono ambienti globali leggibili da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, attivare la radice o l'ambiente python2 usando il `activate` comando come amministratore. Quindi, è possibile usare un gestore pacchetti come `conda` o `pip` per installare o aggiornare i pacchetti.

## <a name="python-linux-edition"></a>Python (edizione Linux)

| Category | Valore |
| ------------- | ------------- |
| Versioni del linguaggio supportate | Python 2,7 e 3,5 |
| Edizioni DSVM supportate      | Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | `conda`Vengono creati due ambienti globali: <br /> * `root` l'ambiente disponibile in `/anaconda/` è Python 2,7. <br/> * `py35` l'ambiente disponibile in `/anaconda/envs/py35` è Python 3,5.       |
| Collegamenti agli esempi      | Sono inclusi i notebook di Jupyter di esempio per Python.     |
| Strumenti correlati in DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

* Eseguire in un terminale:

  Aprire il terminale ed eseguire una delle operazioni seguenti, a seconda della versione di Python che si vuole eseguire:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Usare in un IDE:

  Usare PyCharm, installato in Visual Studio Community Edition. 

* Usare in Jupyter:

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come **Python [conda root]** per Python 2,7 e **Python [conda ENV: PY35]** per l'ambiente Python 3,5. 

* Installare i pacchetti Python:

  Gli ambienti Python predefiniti nella macchina virtuale per data science sono ambienti globali leggibili da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, attivare la radice o l'ambiente PY35 usando il `source activate` comando come amministratore o come utente con autorizzazioni sudo. Quindi, è possibile usare un gestore pacchetti come `conda` o `pip` per installare o aggiornare i pacchetti.


## <a name="r"></a>R

| Category | Valore |
| ------------- | ------------- |
| Versioni del linguaggio supportate | Microsoft R Open 3. x (100% compatibile con CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (piattaforma R scalabile per l'azienda)|
| Edizioni DSVM supportate      | Linux, Windows     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Collegamenti agli esempi      | Sono inclusi i notebook di Jupyter di esempio per R.     |
| Strumenti correlati in DSVM      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

**Windows**:

* Eseguire al prompt dei comandi:

  Aprire un prompt dei comandi e digitare `R`.

* Usare in un IDE:

  Usare gli strumenti R Tools per Visual Studio (RTVS) installati in Visual Studio Community Edition o RStudio. Sono disponibili nel menu Start o come icona del desktop. 

* Usare in Jupyter

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come **r** per usare il kernel r Jupyter (IRKernel).

* Installare i pacchetti R:

  R viene installato in DSVM in un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire R usando uno dei metodi precedenti. Quindi, è possibile eseguire Gestione pacchetti R `install.packages()` per installare o aggiornare i pacchetti.

**Linux**:

* Esegui nel terminale:

  Aprire un terminale ed eseguire `R` .  

* Usare in un IDE:

  Usare RStudio, installato nella DSVM Linux.  

* Usare in Jupyter:

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come **r** per usare il kernel r Jupyter (IRKernel). 

* Installare i pacchetti R:

  R viene installato in DSVM in un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire R usando uno dei metodi precedenti. Quindi, è possibile eseguire Gestione pacchetti R `install.packages()` per installare o aggiornare i pacchetti.


## <a name="julia"></a>Julia

| Category | Valore |
| ------------- | ------------- |
| Versioni del linguaggio supportate | 0,6 |
| Edizioni DSVM supportate      | Linux, Windows     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Windows: installato in `C:\JuliaPro-VERSION`<br /> Linux: installato in `/opt/JuliaPro-VERSION`    |
| Collegamenti agli esempi      | Sono inclusi i notebook di Jupyter di esempio per Julia.     |
| Strumenti correlati in DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo    

**Windows**:

* Eseguire al prompt dei comandi

  Aprire un prompt dei comandi ed eseguire `julia` .
* Usare in un IDE:

  Usare `Juno` con l'IDE di Julia installato nella DSVM e disponibile come collegamento sul desktop.

* Usare in Jupyter:

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come **versione Julia**.

* Installare i pacchetti Julia:

  Il percorso predefinito di Julia è un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire Julia usando uno dei metodi precedenti. Quindi, è possibile eseguire i comandi di gestione pacchetti Julia come `Pkg.add()` per installare o aggiornare i pacchetti.


**Linux**:
* Eseguire in un terminale:

  Aprire un terminale ed eseguire `julia` .
* Usare in un IDE:

  Usare `Juno` con l'IDE di Julia installato nella DSVM e disponibile come collegamento al menu di un' **applicazione** .

* Usare in Jupyter:

  Aprire Jupyter e selezionare **nuovo** per creare un nuovo notebook. È possibile impostare il tipo di kernel come **versione Julia**.

* Installare i pacchetti Julia:

  Il percorso predefinito di Julia è un ambiente globale leggibile da tutti gli utenti. Ma solo gli amministratori possono scrivere e installare i pacchetti globali. Per installare i pacchetti nell'ambiente globale, eseguire Julia usando uno dei metodi precedenti. Quindi, è possibile eseguire i comandi di gestione pacchetti Julia come `Pkg.add()` per installare o aggiornare i pacchetti.

## <a name="other-languages"></a>Altri linguaggi

**C#**: disponibile in Windows e accessibile tramite Visual Studio Community Edition o in, in `Developer Command Prompt for Visual Studio` cui è possibile eseguire il `csc` comando.

**Java**: OpenJDK è disponibile sia per le edizioni Linux che Windows di DSVM ed è impostato sul percorso. Per usare Java, digitare il `javac` `java` comando o al prompt dei comandi in Windows o nella shell bash in Linux.

**Node.js**: Node.js è disponibile in entrambe le edizioni Linux e Windows di DSVM ed è impostato sul percorso. Per accedere Node.js, digitare il `node` `npm` comando o al prompt dei comandi in Windows o nella shell bash in Linux. In Windows, l'estensione di Visual Studio per gli strumenti di Node.js viene installata per fornire un IDE grafico per sviluppare l'applicazione Node.js.

**F #**: disponibile in Windows e accessibile tramite Visual Studio Community Edition o in un `Developer Command Prompt for Visual Studio` , in cui è possibile eseguire il `fsc` comando.
