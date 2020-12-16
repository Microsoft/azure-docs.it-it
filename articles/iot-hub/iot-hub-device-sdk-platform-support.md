---
title: Supporto per la piattaforma di Azure IoT SDK per dispositivi | Microsoft Docs
description: Gli SDK per dispositivi Open Source sono disponibili su GitHub in C, .NET (C#), Java, Node.js e Python per connettere i dispositivi all'hub Azure e al servizio Device provisioning (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: b8f8403617a5fc8b442e8a3f74ab9226fe1479fe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588761"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Supporto della piattaforma SDK per dispositivi Azure

Microsoft si impegna a espandere continuamente l'universo dei dispositivi in grado di supportare l'hub Azure. Microsoft pubblica gli SDK per dispositivi Open Source su GitHub per consentire la connessione dei dispositivi all'hub di Azure e al servizio Device provisioning. Gli SDK per dispositivi sono disponibili per C, .NET (C#), Java, Node.js e Python. Microsoft testa ogni SDK per assicurarsi che sia in esecuzione sulle configurazioni supportate in modo dettagliato nella sezione relativa al [supporto di Microsoft SDK e della piattaforma per dispositivi](#microsoft-sdks-and-device-platform-support) .

Oltre agli SDK per dispositivi, Microsoft offre diverse altre strade per consentire ai clienti e agli sviluppatori di connettere i propri dispositivi ad Azure:

* Microsoft collabora con diverse società partner per contribuire alla pubblicazione di kit di sviluppo, in base all'SDK di Azure per le proprie piattaforme hardware.

* Microsoft collabora con i partner attendibili Microsoft per offrire un set di dispositivi in continua espansione che sono stati testati e certificati per Azure. Per un elenco aggiornato di questi dispositivi, vedere il [Catalogo dei dispositivi Azure Certified per](https://catalog.azureiotsolutions.com/)l'it.

* Microsoft offre un livello di astrazione della piattaforma (PAL) nell'SDK del dispositivo hub Azure Internet che consente agli sviluppatori di trasferire facilmente l'SDK alla propria piattaforma. Per ulteriori informazioni, vedere la [Guida al porting di C SDK](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Questo argomento fornisce informazioni sugli SDK Microsoft e sulle configurazioni della piattaforma supportate, oltre a tutte le altre opzioni elencate in precedenza.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK e supporto della piattaforma per dispositivi

Microsoft pubblica gli SDK open source su GitHub per le lingue seguenti: C, .NET (C#), Node.js, Java e Python. In questa sezione sono elencati gli SDK e le relative dipendenze. Gli SDK sono supportati in qualsiasi piattaforma per dispositivi che soddisfi tali dipendenze.

Per ognuno degli SDK elencati, Microsoft:

* Crea ed esegue continuamente test end-to-end rispetto al ramo principale dell'SDK pertinente in GitHub su diverse piattaforme più diffuse.  Per fornire il code coverage dei test in diverse versioni del compilatore, in genere si testa la versione LTS più recente e la versione più diffusa.

* Fornisce le linee guida per l'installazione o i pacchetti di installazione, se applicabile.

* Supporta completamente gli SDK su GitHub con codice open source, un percorso per i contributi dei clienti e il coinvolgimento del team del prodotto con i problemi di GitHub.

### <a name="c-sdk"></a>SDK per C

L' [SDK per dispositivi C dell'hub Azure](https://github.com/Azure/azure-iot-sdk-c) è stato testato con e supporta le configurazioni seguenti.

| OS                  | Libreria TLS                  | Requisiti aggiuntivi                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL o BearSSL | Socket Berkeley</br></br>POSIX (Portable Operating System Interface)                       |
| iOS 12,2            | OpenSSL                      | XCode emulato in OSX 10.13.4                                                               |
| Famiglia Windows 10   | SChannel                     |                                                                                             |
| Sistema operativo mbed 5,4         | Mbed TLS 2                   | [Kit di sviluppo MXChip](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Sistema operativo Azure Sphere     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 o ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

L' [SDK per dispositivi Python per hub Azure](https://github.com/Azure/azure-iot-sdk-python) è stato testato con e supporta le configurazioni seguenti.

| OS                  | Compilatore                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 o versione successiva |
| macOS High Sierra   | Python 2,7. *, 3,5 o versione successiva |
| Famiglia Windows 10   | Python 2,7. *, 3,5 o versione successiva |

Solo Python Version 3.5.3 o versioni successive supportano le API asincrone, è consigliabile usare la versione 3,7 o successiva.

### <a name="net-sdk"></a>.NET SDK

L' [SDK per dispositivi .NET (C#) dell'hub Azure](https://github.com/Azure/azure-iot-sdk-csharp) è testato con e supporta le configurazioni seguenti.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKU per desktop e server di Windows 10   | .NET Core 2,1, .NET Framework 4.5.1 o .NET Framework 4,7 |

.NET SDK può essere usato anche con Windows Internet core con l' [agente di dispositivo di Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) o con [un ntservice personalizzato che può usare RPC per comunicare con le applicazioni UWP](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

L' [Hub Azure e l'SDK Node.js Device SDK](https://github.com/Azure/azure-iot-sdk-node) sono testati con e supportano le configurazioni seguenti.

| OS                  | Versione del nodo    |
|---------------------|-----------------|
| Linux               | LTS e Current |
| Famiglia Windows 10   | LTS e Current |

### <a name="java-sdk"></a>SDK per Java

L' [SDK per dispositivi Java dell'hub Azure](https://github.com/Azure/azure-iot-sdk-java) è stato testato con e supporta le configurazioni seguenti.

| OS                     | Versione Java |
|------------------------|--------------|
| API Android 28         | Java 8       |
| Linux x64             | Java 8       |
| Famiglia Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kit di sviluppo supportati dai partner

Microsoft collabora con diversi partner per fornire kit di sviluppo per diverse architetture del microprocessore. Questi partner hanno trasferito l'SDK di Azure per la propria piattaforma. I partner creano e gestiscono la piattaforma Abstraction Layer (PAL) dell'SDK. Microsoft collabora con questi partner per offrire supporto esteso.

| Partner             | Dispositivi                            | Collegamento                     | Supporto |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem Iot Qualcomm MDM9206 LTE     | [Qualcomm LTE per IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Serie STM32L4 <br/> Serie STM32F4 <br/>  Serie STM32F7 <br/>  STM32L4 Kit Discovery per nodo IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Supporto tecnico](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | LaunchPad CC3220SF </br> LaunchPad CC3220S </br> LaunchPad CC3235SF </br> LaunchPad CC3235S </br> LaunchPad MSP432E4 | [Plug-in Azure IoT per SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum di TI E2E](https://e2e.ti.com) <br/> [Forum di E2E TI per CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum di E2E TI per MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Porting di Microsoft Azure SDK di C

Se la piattaforma del dispositivo non è coperta da una delle sezioni precedenti, è possibile prendere in considerazione il porting dell'SDK di Azure. Il porting di C SDK comporta principalmente l'implementazione di Platform Abstraction Layer (PAL) dell'SDK. L'elenco di accesso alla pubblicazione definisce primitive che forniscono la colla tra il dispositivo e le funzioni di livello superiore nell'SDK. Per ulteriori informazioni, vedere la [Guida al porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partner Microsoft e dispositivi Azure Azure per la certificazione

Microsoft collabora con diversi partner per espandere continuamente l'universo di Azure Internet con i dispositivi testati e certificati di Azure.

* Per esplorare i dispositivi certificati Azure, vedere [Microsoft Azure Certified per IOT catalogo dei dispositivi](https://catalog.azureiotsolutions.com/).

* Per altre informazioni sull'ecosistema di Azure Certified per Internet, vedere l'articolo relativo [all'aggiunta dell'ecosistema Certified for](https://catalog.azureiotsolutions.com/register)Internet.

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Connessione all'hub Internet senza SDK

Se non si è in grado di usare uno degli SDK per dispositivi dell'hub Internet delle cose, è possibile connettersi direttamente all'hub Internet usando un protocollo di comunicazione supportato. Per altre informazioni, vedere [sviluppare senza un SDK di Azure](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Supporto e altre risorse

Se si verificano problemi durante l'uso degli SDK per dispositivi Azure, sono disponibili diversi modi per cercare il supporto riepilogato di seguito. Per informazioni complete su tutte le opzioni di supporto, vedere le [Opzioni di supporto e](../iot-fundamentals/iot-support-help.md)di assistenza per Azure. 

**Segnalazione di bug** : è possibile segnalare bug negli SDK per dispositivi nella pagina problemi del progetto GitHub pertinente. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

* [Problemi dell'SDK C dell'hub Azure](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemi dell'SDK .NET (C#) dell'hub Azure.](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemi di Java SDK per hub Azure.](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemi dell'SDK Node.js dell'hub Azure](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemi di Python SDK per hub Azure](https://github.com/Azure/azure-iot-sdk-python/issues)

**Domande tecniche** : è possibile porre domande tecniche su [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) e [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) usando il tag *Azure-* Internet e l'SDK.

**Team di supporto clienti Microsoft** : gli utenti che dispongono di un [piano di supporto](https://azure.microsoft.com/support/plans/) possono coinvolgere il team di supporto clienti Microsoft creando una nuova richiesta di supporto direttamente dalla [portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Richieste di funzionalità** : le richieste delle funzionalità di Azure Internet vengono rilevate tramite la [pagina vocale dell'utente](https://feedback.azure.com/forums/321918-azure-iot)del prodotto.

## <a name="next-steps"></a>Passaggi successivi

* [SDK di servizi e dispositivi](iot-hub-devguide-sdks.md)
* [Linee guida per il porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)