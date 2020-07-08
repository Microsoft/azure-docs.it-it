---
title: Raccomandazioni di Endpoint Protection nei centri sicurezza di Azure
description: Il modo in cui le soluzioni di Endpoint Protection vengono individuate e identificate come integre.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78208543"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Valutazione e raccomandazioni di Endpoint Protection nel centro sicurezza di Azure

Il Centro sicurezza di Azure fornisce valutazioni dell'integrità delle versioni [supportate](security-center-services.md#endpoint-supported) delle soluzioni di Endpoint Protection. Questo articolo illustra gli scenari in cui il Centro sicurezza genera le due raccomandazioni seguenti:

* **Installare soluzioni Endpoint Protection nella macchina virtuale**
* **Risolvere i problemi di integrità della protezione degli endpoint nei computer**

## <a name="windows-defender"></a>Windows Defender

* Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) viene eseguito e il risultato è **AMServiceEnabled: false**

* Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) viene eseguito e si verifica una delle condizioni seguenti:

  * Una delle proprietà seguenti è false:

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Se una o entrambe le proprietà seguenti sono pari a 7 o più.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Il Centro sicurezza consiglia di **"installare soluzioni Endpoint Protection nella macchina virtuale"** durante l'importazione di **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** ed eseguendo **Get-MProtComputerStatus** risultati con **AMServiceEnabled = false**

* Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando **Get-MprotComputerStatus** viene eseguito e si verifica una delle condizioni seguenti:

    * Almeno una delle proprietà seguenti è false:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Se uno o entrambi gli aggiornamenti della firma seguenti sono maggiori o uguali a 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Tendenza Micro

* Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando uno dei seguenti controlli non viene soddisfatto:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** esiste
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** exists
    * Il file **dsa_query. cmd** si trova nella cartella di installazione
    * Esecuzione di **dsa_query. cmd** risultati con **Component. am. Mode: rilevato agente di sicurezza Deep micro-trend**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando uno dei seguenti controlli non viene soddisfatto:

* **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Oppure

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando uno dei seguenti controlli non viene soddisfatto:

* Verificare la versione di Symantec >= 12: percorso del registro di sistema: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion "-valore" ProductVersion "**

* Verificare lo stato di protezione in tempo reale: **HKLM: \ Software\Wow6432Node\Symantec\Symantec endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Verificare lo stato di aggiornamento della firma: **HKLM\Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 giorni**

* Controllare lo stato dell'analisi completa: **HKLM: \ Software\Symantec\Symantec endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 giorni**

* Trova firma numero di versione percorso della firma versione per Symantec 12: **percorsi registro di sistema + "CurrentVersion\SharedDefs"-valore "SRTSP"** 

* Percorso della versione della firma per Symantec 14: **percorsi del registro di sistema + "CurrentVersion\SharedDefs\SDSDefs"-valore "SRTSP"**

Percorsi del registro di sistema:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection per Windows

Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando uno dei seguenti controlli non viene soddisfatto:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** esiste

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando uno dei seguenti controlli non viene soddisfatto:

* Versione di McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Trovare la versione della firma: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "dwContentMajorVersion"**

* Trova data firma: **HKLM: \ Software\McAfee\AVSolution\DS\DS-valore "szContentCreationDate" >= 7 giorni**

* Trova analisi Data: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" >= 7 giorni**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security per Linux Threat Prevention 

Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando uno dei seguenti controlli non viene soddisfatto:

- Uscite file **/opt/ISEC/ENS/threatprevention/bin/isecav** 

- l'output **di "/opt/ISEC/ENS/threatprevention/bin/isecav-Version"** è: **nome McAfee = McAfee Endpoint Security per Linux Threat Prevention e McAfee Version >= 10**

Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando uno dei seguenti controlli non viene soddisfatto:

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** restituisce un'analisi **veloce, un'analisi completa** ed entrambe le analisi <= 7 giorni

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** restituisce la **data e l'ora di aggiornamento del motore** e di entrambi <= 7 giorni

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** restituisce lo stato **di analisi dell'accesso**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus per Linux 

Il Centro sicurezza consiglia **di "installare soluzioni Endpoint Protection nella macchina virtuale"** quando uno dei seguenti controlli non viene soddisfatto:

- Il file **/opt/Sophos-AV/bin/savdstatus** viene chiuso o cerca il percorso personalizzato **"readlink $ (savscan)"**

- **"/opt/Sophos-AV/bin/savdstatus--Version"** restituisce Sophos Name = **Sophos Anti-virus e Sophos Version >= 9**

Il Centro sicurezza consiglia **di "risolvere i problemi di integrità di Endpoint Protection nei computer"** quando uno dei seguenti controlli non viene soddisfatto:

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "analisi pianificata. \* operazione completata "| Tail-1 "**, restituisce un valore

- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep "analisi completata"** | Tail-1 ", restituisce un valore

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** restituisce LastUpdate, che deve essere <= 7 giorni 

- **"/opt/Sophos-AV/bin/savdstatus-v"** è uguale a **"l'analisi in corso è in esecuzione"** 

- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** restituisce Enabled

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I log dell'estensione Microsoft antimalware sono disponibili all'indirizzo: **%SystemDrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (o PaaSAntimalware) \1.5.5.x (versione #) \CommandExecution.log**

### <a name="support"></a>Supporto

Per ulteriori informazioni, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, archiviare una richiesta di supporto di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).