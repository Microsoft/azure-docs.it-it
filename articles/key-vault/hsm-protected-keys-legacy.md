---
title: Come generare e trasferire chiavi protette dal modulo di protezione hardware per Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Questo argomento permette di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure. Anche noto come BYOK o Bring Your Own Key.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 0d51e1aaf1d9b0472245b9a7f29148517a0a7b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77429306"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Importare chiavi protette da HSM per l'insieme di credenziali delle chiavi (legacy)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key*o BYOK. L'insieme di credenziali delle chiavi di Azure usa nCipher nShield della famiglia di HSM (convalidato FIPS 140-2 Livello 2) per proteggere le chiavi.

Questo argomento include informazioni utili per pianificare, generare e quindi trasferire le proprie chiavi protette da HSM da usare con l'insieme di credenziali delle chiavi di Azure.

Questa funzionalità non è disponibile per la versione di Azure per la Cina.

> [!NOTE]
> Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](key-vault-overview.md) credenziali delle chiavi di Azure.For more information about Azure Key Vault, see What is Azure Key Vault?  
> Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi con protezione HSM, vedere [Che cos'è Azure Key Vault?](key-vault-overview.md).

Altre informazioni su generazione e trasferimento di una chiave HSM protetta tramite Internet:

* La chiave viene generata in una workstation offline per ridurre la superficie di attacco.
* La crittografia della chiave viene eseguita tramite una chiave per lo scambio delle chiavi che rimane crittografata fino al momento del trasferimento ai moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure. Solo la versione crittografata della chiave viene inviata dalla workstation originale.
* Il set di strumenti imposta proprietà sulla chiave che consentono di associarla all'ambiente di sicurezza dell'insieme di credenziali delle chiavi di Azure. Di conseguenza, dopo che i moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure avranno ricevuto e decrittografato la chiave, saranno gli unici componenti a poterla usare. La chiave non può essere esportata. Questa associazione viene applicata dagli HSM nCipher.
* La chiave per lo scambio delle chiavi usata per crittografare la chiave viene generata nei moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure e non è esportabile. I moduli di protezione hardware applicano la regola in base alla quale non può esistere una versione non crittografata della chiave per lo scambio delle chiavi all'esterno dei moduli stessi. Inoltre, il set di strumenti include attestazione da nCipher che la KEK non è esportabile ed è stato generato all'interno di un vero ESM che è stato prodotto da nCipher.
* Il set di strumenti include l'attestazione da nCipher che il mondo della sicurezza di Azure Key Vault è stato generato anche su un HSM originale prodotto da nCipher. Questo attestato conferma che Microsoft usa hardware originale.
* Microsoft usa certificati KEK separati e ambienti di sicurezza separati in ogni area geografica. Questa separazione assicura che la chiave possa essere usata solo nei data center appartenenti all'area in cui è stata crittografata. Una chiave di un cliente Europao, ad esempio, non può essere usata in data center che si trovano in America del Nord o in Asia.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Ulteriori informazioni su nCipher HSMs e servizi Microsoft

nCipher Security, una società Entrust Datacard, è leader nel mercato HSM per scopi generali, potenziando le organizzazioni leader a livello mondiale fornendo fiducia, integrità e controllo alle loro informazioni e applicazioni aziendali critiche. n Le soluzioni crittografiche di Cipher proteggono le tecnologie emergenti – cloud, IoT, blockchain, pagamenti digitali – e aiutano a soddisfare i nuovi mandati di conformità, utilizzando la stessa tecnologia collaudata da cui le organizzazioni globali dipendono oggi per proteggersi dalle minacce sensibili, le comunicazioni di rete e l'infrastruttura aziendale. nCipher fornisce fiducia per le applicazioni business critical, garantendo l'integrità dei dati e mettendo i clienti in completo controllo - oggi, domani, in ogni momento.

Microsoft ha collaborato con nCipher Security per migliorare lo stato dell'arte per gli HSM. per consentire all'utente di sfruttare i vantaggi tipici dei servizi ospitati senza perdere il controllo sulle proprie chiavi. In particolare, tali miglioramenti consentono a Microsoft di gestire i moduli di protezione hardware in modo che questa operazione non debba essere eseguita dall'utente. In quanto servizio cloud, l'insieme di credenziali delle chiavi di Azure è in grado di supportare la scalabilità verticale con breve preavviso per soddisfare i picchi d'uso dell'organizzazione. Contemporaneamente, la chiave è protetta all'interno dei moduli di protezione hardware di Microsoft e l'utente mantiene il controllo sul ciclo di vita della chiave, perché genera la chiave e la trasferisce ai moduli di protezione hardware di Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementazione di BYOK (Bring Your Own Key) per l'insieme di credenziali delle chiavi di Azure

Usare le informazioni e le procedure seguenti se si genera una chiave HSM protetta e quindi la si trasferisce all'insieme di credenziali delle chiavi di Azure, ovvero lo scenario BYOK (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Prerequisiti per la modalità BYOK

Nella tabella seguente sono elencati i prerequisiti relativi alla modalità BYOK per l'insieme di credenziali delle chiavi di Azure.

| Requisito | Ulteriori informazioni |
| --- | --- |
| Sottoscrizione di Azure |Per creare un insieme di credenziali delle chiavi di Azure, è necessaria una sottoscrizione di Azure: [Iscriversi per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| È inoltre necessario il livello di servizio Premium dell'insieme di credenziali delle chiavi di Azure per supportare chiavi HSM protette. |Per altre informazioni su livelli di servizio e funzionalità per l'insieme di credenziali delle chiavi di Azure, vedere il sito Web relativo ai [prezzi dell'insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| nCipher nShield HSM, smart card e software di supporto |È necessario avere accesso a un nCipher Hardware Security Module e alle conoscenze operative di base degli HSM nCipher nShield. Vedere [nCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) per l'elenco dei modelli compatibili o per acquistare un HSM se non si dispone di uno. |
| Componenti hardware e software seguenti:<ol><li>Una workstation x64 offline con un sistema operativo Windows minimo del software Windows 7 e nCipher nShield che è almeno la versione 11.50.<br/><br/>Se questa workstation esegue Windows 7, è necessario [installare Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Una workstation connessa a Internet con sistema operativo Windows 7 o versione successiva e con [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **1.1.0** o versione successiva installato.</li><li>Unità USB o un altro dispositivo di archiviazione portatile con almeno 16 MB di spazio disponibile.</li></ol> |Per motivi di sicurezza, si consiglia che la prima workstation non sia connessa a una rete. Questa indicazione tuttavia non viene applicata a livello di codice.<br/><br/>Nelle istruzioni seguenti questa workstation viene indicata come workstation disconnessa.</p></blockquote><br/>Inoltre, se la chiave del tenant è destinata a una rete di produzione, è consigliabile usare una seconda workstation separata per scaricare il set di strumenti e caricare la chiave del tenant. A scopo di test è comunque possibile usare la prima workstation.<br/><br/>Nelle istruzioni seguenti la seconda workstation viene indicata come workstation connessa a Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generare e trasferire la chiave al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure

Per generare e trasferire la chiave al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure, eseguire i cinque passaggi seguenti:

* [Passaggio 1: Preparare la workstation connessa a Internet](#step-1-prepare-your-internet-connected-workstation)
* [Passaggio 2: Preparare la workstation disconnessa](#step-2-prepare-your-disconnected-workstation)
* [Passaggio 3: Generare la chiave](#step-3-generate-your-key)
* [Passaggio 4: Preparare la chiave per il trasferimento](#step-4-prepare-your-key-for-transfer)
* [Passaggio 5: Trasferire la chiave all'insieme di credenziali delle chiavi di Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passaggio 1: Preparare la workstation connessa a Internet

Per questo primo passaggio è necessario eseguire le procedure seguenti nella workstation connessa a Internet.

### <a name="step-11-install-azure-powershell"></a>Passaggio 1.1: Installare Azure PowerShell

Dalla workstation connessa a Internet scaricare e installare il modulo di Azure PowerShell che include i cmdlet per la gestione dell'insieme di credenziali delle chiavi di Azure. Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Passaggio 1.2: Ottenere l'ID sottoscrizione di Azure

Avviare una sessione di Azure PowerShell e accedere al proprio account Azure con il comando seguente:

```Powershell
   Connect-AzAccount
```
Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Usare quindi il comando [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
   Get-AzSubscription
```
Nell'output individuare l'ID della sottoscrizione che si userà per l'insieme di credenziali delle chiavi di Azure. Questo ID sottoscrizione verrà usato in seguito.

Non chiudere la finestra di Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passaggio 1.3: Scaricare il set di strumenti BYOK per l'insieme di credenziali delle chiavi di Azure

Accedere all'Area download Microsoft e [scaricare il set di strumenti BYOK per l'insieme di credenziali delle chiavi di Azure](https://www.microsoft.com/download/details.aspx?id=45345) per la propria area geografica o istanza di Azure. Usare le informazioni seguenti per identificare il nome del pacchetto da scaricare e il relativo hash del pacchetto SHA-256:

---
**Stati Uniti:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europa.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asia:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**America Latina:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Giappone:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Corea del Sud:**

KeyVault-BYOK-strumenti-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Sud Africa:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Uae:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962A0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australia:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure per enti pubblici:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Dipartimento della difesa del US Gov:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Canada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Germania:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Germania Pubblico:**

KeyVault-BYOK-Tools-Germania-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Francia:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Regno Unito:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Svizzera:**

KeyVault-BYOK-Tools-Svizzera.zip

88CF8D39899E26D456D4E0BC57E57E5C913ABF1D73A89013FCE3BBD9599AD2FE9

---


Per convalidare l'integrità del set di strumenti BYOK scaricato, nella sessione di Azure PowerShell usare il cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Il set di strumenti include:

* Un pacchetto di chiavi per lo scambio di chiavi (KEK) con un nome che inizia con **BYOK-KEK-pkg-.**
* Un pacchetto relativo all'ambiente di sicurezza con un nome che inizia con **BYOK-SecurityWorld-pkg-.**
* Uno script python denominato **verifykeypackage.py.**
* File eseguibile dalla riga di comando denominato **KeyTransferRemote.exe** e DLL associate.
* Componente Visual C++ Redistributable Package denominato **vcredist_x64.exe.**

Copiare il pacchetto in un'unità USB o in un altro dispositivo di archiviazione portatile.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Passaggio 2: Preparare la workstation disconnessa

Per questo secondo passaggio eseguire le procedure seguenti nella workstation non connessa alla rete (Internet o la rete interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Passaggio 2.1: Preparare la workstation disconnessa con nCipher nShield HSM

Installare il software di supporto nCipher in un computer Windows e quindi collegare un nCipher nShield HSM a quel computer.

Assicurarsi che gli strumenti nCipher siano nel percorso (**%nfast_home% .** Digitare ad esempio:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Per ulteriori informazioni, vedere la guida utente inclusa in nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passaggio 2.2: Installare il set di strumenti BYOK nella workstation disconnessa

Copiare il pacchetto del set di strumenti BYOK dall'unità USB o da un altro dispositivo di archiviazione portatile, quindi eseguire le operazioni seguenti:

1. Estrarre i file dal pacchetto scaricato in una cartella qualsiasi.
2. In tale cartella eseguire vcredist_x64.exe.
3. Seguire le istruzioni per installare i componenti di runtime di Visual C++ per Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Passaggio 3: Generare la chiave

Per questo terzo passaggio eseguire le procedure seguenti nella workstation disconnessa. Per poter completare questo passaggio, il modulo di protezione hardware deve essere in modalità di inizializzazione. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Passaggio 3.1: Modificare la modalità del modulo di protezione hardware in 'I'

Se si utilizza nCipher nShield Edge, per cambiare la modalità: 1. Usare il pulsante Modalità per evidenziare la modalità richiesta. 2. Entro pochi secondi, premere e tenere premuto per pochi secondi il pulsante Cancella. Se la modalità viene modificata, il LED della nuova modalità smette di lampeggiare e rimane acceso. È possibile che il LED di stato lampeggi in modo irregolare per pochi secondi e quindi lampeggi in modo regolare quando il dispositivo è pronto. In caso contrario, il dispositivo rimane nella modalità corrente, con il LED della modalità appropriata acceso.

### <a name="step-32-create-a-security-world"></a>Passaggio 3.2: Creare un ambiente di sicurezza

Avviare un prompt dei comandi ed eseguire il programma new-world nCipher.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Tale programma crea un file di **ambiente di sicurezza** nel percorso %NFAST_KMDATA%\local\world, che corrisponde alla cartella C:\ProgramData\nCipher\Key Management Data\local. È possibile creare valori diversi per il quorum, ma nell'esempio viene chiesto di immettere tre schede vuote e un codice PIN per ogni scheda. Qualsiasi coppia di schede consente di accedere in modo completo all'ambiente di sicurezza. Tali schede diventano il **set di schede amministrative** per il nuovo ambiente di sicurezza.

> [!NOTE]
> Se il modulo di protezione hardware non supporta il gruppo di crittografia DLf3072s256mRijndael più recente, è possibile sostituire --cipher-suite= DLf3072s256mRijndael con --cipher-suite=DLf1024s160mRijndael
> 
> Il mondo della sicurezza creato con new-world.exe fornito con nCipher software versione 12.50 non è compatibile con questa procedura BYOK. Sono disponibili due opzioni:
> 1) Declassare la versione del software nCipher a 12.40.2 per creare un nuovo mondo di sicurezza.
> 2) Contattare il supporto nCipher e richiedere loro di fornire un hotfix per la versione del software 12.50, che consente di utilizzare la versione 12.40.2 di new-world.exe che è compatibile con questa procedura BYOK.

Eseguire quindi le operazioni seguenti:

* Eseguire il backup del file relativo all'ambiente. Proteggere il file relativo all'ambiente, le schede amministrative e i codici PIN relativi e verificare che nessuna singola persona possa accedere a più di una scheda.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Passaggio 3.3: Modificare la modalità del modulo di protezione hardware in 'O'

Se si utilizza nCipher nShield Edge, per cambiare la modalità: 1. Usare il pulsante Modalità per evidenziare la modalità richiesta. 2. Entro pochi secondi, premere e tenere premuto per pochi secondi il pulsante Cancella. Se la modalità viene modificata, il LED della nuova modalità smette di lampeggiare e rimane acceso. È possibile che il LED di stato lampeggi in modo irregolare per pochi secondi e quindi lampeggi in modo regolare quando il dispositivo è pronto. In caso contrario, il dispositivo rimane nella modalità corrente, con il LED della modalità appropriata acceso.

### <a name="step-34-validate-the-downloaded-package"></a>Passaggio 3.4: Convalidare il pacchetto scaricato

Questo passaggio è facoltativo, ma è consigliato in modo che sia possibile convalidare gli elementi seguenti:

* La chiave di scambio delle chiavi inclusa nel set di strumenti è stata generata da un hSM nCipher originale.
* L'hash del mondo di sicurezza incluso nel set di strumenti è stato generato in un vero e proprio nCipher nShield HSM.
* Impossibilità di esportare la chiave per lo scambio delle chiavi.

> [!NOTE]
> Per convalidare il pacchetto scaricato, il modulo di protezione hardware deve essere connesso e acceso e deve essere associato a un ambiente di sicurezza (ad esempio quello appena creato).

Per convalidare il pacchetto scaricato:

1. Eseguire lo script verifykeypackage.py associando uno dei comandi seguenti, a seconda dell'area geografica o istanza di Azure:

   * America del Nord

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Europa

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Asia

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * America Latina

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Giappone

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Per la Corea del Sud:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Per il Sudafrica:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Per gli Emirati Arabi Uniti:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Per l'Australia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Per [Azure per enti pubblici](https://azure.microsoft.com/features/gov/), che usa l'istanza di Azure per il Governo degli Stati Uniti:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Per il Dipartimento della difesa del US Gov:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Per il Canada:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Per la Germania:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Per la Germania Pubblico:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Per l'India:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Per la Francia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Per il Regno Unito:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Per la Svizzera:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Il software nCipher nShield include python in %NFAST_HOME%
     >
     >
2. Assicurarsi di visualizzare il risultato positivo seguente, che indica il completamento della convalida: **Result: SUCCESS**

Questo script convalida la catena del firmatario fino alla chiave radice nShield. La funzione hash di questa chiave radice è incorporata nello script e il relativo valore deve essere **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. È inoltre possibile confermare questo valore separatamente visitando il [sito web nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

È ora possibile creare una nuova chiave.

### <a name="step-35-create-a-new-key"></a>Passaggio 3.5: Creare una nuova chiave

Generare una chiave utilizzando il programma nCipher nShield **generatekey.**

Eseguire il comando seguente per generare la chiave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando si esegue il comando, usare le istruzioni seguenti:

* Il parametro *protect* deve essere impostato sul valore **module**, come illustrato. Verrà creata una chiave protetta tramite modulo. Il set di strumenti BYOK non supporta le chiavi protette con OCS.
* Sostituire il valore *contosokey* per **ident** e **plainname** con qualsiasi valore di stringa. Per ridurre il sovraccarico amministrativo e il rischio di errori, è consigliabile usare lo stesso valore per entrambi gli elementi. Il valore **ident** deve contenere solo numeri, trattini e lettere minuscole.
* L'elemento pubexp viene lasciato vuoto in questo esempio (impostazione predefinita), ma è possibile indicare valori specifici. Per ulteriori informazioni, vedere la documentazione di [nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Questo comando crea un file di chiave in formato token nella cartella %NFAST_KMDATA%\local con un nome che inizia con **key_simple_** seguito dall'elemento **ident** specificato nel comando. Ad esempio: **key_simple_contosokey**. Questo file contiene una chiave crittografata.

Eseguire il backup del file di chiave in formato token in un percorso sicuro.

> [!IMPORTANT]
> Quando in seguito si trasferisce la chiave all'insieme di credenziali delle chiavi di Azure, Microsoft non può esportarla nuovamente nei dispositivi dell'utente, quindi è estremamente importante eseguire il backup della chiave e dell'ambiente di sicurezza in modo sicuro. Contatta [nCipher](https://www.ncipher.com/about-us/contact-us) per indicazioni e best practice per il backup della chiave.
>


È ora possibile trasferire la chiave all'insieme di credenziali delle chiavi di Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Passaggio 4: Preparare la chiave per il trasferimento

Per questo quarto passaggio eseguire le procedure seguenti nella workstation disconnessa.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passaggio 4.1: Creare una copia della chiave con autorizzazioni ridotte

Aprire un nuovo prompt dei comandi e passare alla directory in cui è stato decompresso il file ZIP BYOK. Per ridurre le autorizzazioni sulla chiave, in un prompt dei comandi eseguire uno dei comandi seguenti in base all'area geografica o all'istanza di Azure:

* America del Nord

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Europa

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Asia

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* America Latina

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Giappone

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Per la Corea del Sud:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Per il Sudafrica:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Per gli Emirati Arabi Uniti:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Per l'Australia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Per [Azure per enti pubblici](https://azure.microsoft.com/features/gov/), che usa l'istanza di Azure per il Governo degli Stati Uniti:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Per il Dipartimento della difesa del US Gov:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Per il Canada:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Per la Germania:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Per la Germania Pubblico:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Per l'India:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Per la Francia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Per il Regno Unito:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Per la Svizzera:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Quando si esegue il comando, sostituire *contosokey* con lo stesso valore specificato in **Passaggio 3.5: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).

Viene chiesto di inserire le schede amministrative relative all'ambiente di sicurezza.

Al termine del comando, nel file denominato key_xferacId_\<> contosokey è visualizzato **Risultato: SUCCESSO** e la copia della chiave con autorizzazioni ridotte.

È possibile esaminare l'ACLS utilizzando i seguenti comandi utilizzando le utilità nCipher nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Quando si eseguono questi comandi, sostituire contosokey con lo stesso valore specificato in **Passaggio 3.5: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passaggio 4.2: Crittografare la chiave tramite la chiave per lo scambio di chiavi di Microsoft

Eseguire uno di questi comandi, in base all'area geografica o all'istanza di Azure:

* America del Nord

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Europa

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Asia

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* America Latina

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Giappone

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per la Corea del Sud:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per il Sudafrica:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per gli Emirati Arabi Uniti:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per l'Australia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per [Azure per enti pubblici](https://azure.microsoft.com/features/gov/), che usa l'istanza di Azure per il Governo degli Stati Uniti:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per il Dipartimento della difesa del US Gov:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per il Canada:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per la Germania:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per la Germania Pubblico:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per l'India:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per la Francia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per il Regno Unito:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Per la Svizzera:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Quando si esegue il comando, usare le istruzioni seguenti:

* Sostituire *contosokey* con l'identificatore usato per generare la chiave in **Passaggio 3.5: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).
* Sostituire *SubscriptionID* con l'ID della sottoscrizione di Azure che contiene l'insieme di credenziali delle chiavi. Questo valore è stato recuperato in precedenza, in **Passaggio 1.2: Ottenere l'ID sottoscrizione di Azure** nel passaggio [Preparare la workstation connessa a Internet](#step-1-prepare-your-internet-connected-workstation).
* Sostituire *ContosoFirstHSMKey* con un'etichetta che viene usata per il nome del file di output.

Se l'operazione ha esito positivo, viene visualizzato il messaggio **Result: SUCCESS** e nella cartella corrente è presente un nuovo file con il nome: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passaggio 4.3: Copiare il pacchetto di trasferimento della chiave nella workstation connessa a Internet

Usare un'unità USB o un altro dispositivo di archiviazione portatile per copiare il file di output creato nel passaggio precedente (KeyTransferPackage-ContosoFirstHSMkey.byok) nella workstation connessa a Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passaggio 5: Trasferire la chiave all'insieme di credenziali delle chiavi di Azure

Per questo passaggio finale, nella workstation connessa a Internet usare il cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), per caricare il pacchetto di trasferimento della chiave copiato dalla workstation disconnessa al modulo di protezione hardware di Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Se il pacchetto viene caricato correttamente, verranno visualizzate le proprietà della chiave aggiunta.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere questo [confronto](https://azure.microsoft.com/pricing/details/key-vault/)tra prezzo e funzionalità .
