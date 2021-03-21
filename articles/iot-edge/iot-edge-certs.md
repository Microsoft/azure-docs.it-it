---
title: Certificati per la sicurezza dei dispositivi - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge usa i certificati per convalidare i dispositivi, i moduli e i dispositivi foglia, nonché per stabilire connessioni protette tra di essi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: ffe2f2b7f94d546cdfe393170da2fd2ca6ac0149
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490994"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Informazioni sul modo in cui Azure IoT Edge usa i certificati

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Per verificare l'identità e la legittimità del modulo di runtime dell' [hub IOT Edge](iot-edge-runtime.md#iot-edge-hub) , vengono usati i certificati IOT Edge. Queste verifiche consentono di attivare una connessione protetta TLS tra il runtime, i moduli e i dispositivi IoT. Esattamente come hub IoT, IoT Edge richiede la presenza di una connessione protetta e crittografata con i dispositivi IoT a valle e i moduli di IoT Edge. Per stabilire una connessione TLS sicura, il modulo dell'hub di IoT Edge presenta una catena di certificati del server ai client che si devono connettere affinché questi possano verificarne l'identità.

>[!NOTE]
>Questo articolo illustra i certificati usati per proteggere le connessioni tra i diversi componenti in un dispositivo IoT Edge o tra un dispositivo IoT Edge e tutti i dispositivi foglia. È anche possibile usare i certificati per autenticare il dispositivo IoT Edge nell'hub Internet. Questi certificati di autenticazione sono diversi e non vengono discussi in questo articolo. Per altre informazioni sull'autenticazione del dispositivo con certificati, vedere [creare ed effettuare il provisioning di un dispositivo IOT Edge usando certificati X. 509](how-to-auto-provision-x509-certs.md).

Questo articolo illustra in che modo i certificati di IoT Edge funzionano in scenari di produzione, di sviluppo e di test. Sebbene gli script siano diversi (PowerShell e bash), i concetti sono gli stessi tra Linux e Windows.

## <a name="iot-edge-certificates"></a>Certificati di IoT Edge

Esistono due scenari comuni per la configurazione dei certificati in un dispositivo IoT Edge. A volte l'utente finale, o l'operatore, di un dispositivo acquista un dispositivo generico creato da un produttore, quindi gestisce i certificati stessi. In altri casi, il produttore lavora con contratto per creare un dispositivo personalizzato per l'operatore ed esegue una firma iniziale del certificato prima di distribuire il dispositivo. La struttura del certificato di IoT Edge tenta di prendere in considerazione entrambi gli scenari.

Nella figura seguente viene illustrato l'uso dei certificati di IoT Edge. Tra il certificato CA radice e il certificato CA del dispositivo potrebbero essere presenti nessuno, uno o molti certificati di firma intermedi in base al numero di entità coinvolte. Di seguito viene illustrato un caso.

![Diagramma tipico delle relazioni tra certificati](./media/iot-edge-certs/edgeCerts-general.png)

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo. Questa limitazione si applica al certificato della CA del dispositivo, a tutti i certificati nel bundle di trust e ai certificati ID del dispositivo usati per i metodi di provisioning X. 509.

:::moniker-end

### <a name="certificate-authority"></a>Autorità di certificazione

L'autorità di certificazione, o CA, è un'entità che emette i certificati digitali. Un'autorità di certificazione funge da terze parti attendibili tra il proprietario e il destinatario del certificato. Un certificato digitale fornisce un'attestazione della proprietà di una chiave pubblica al destinatario del certificato. La catena di certificati attendibili funziona generando inizialmente un certificato radice, che rappresenta la base per la relazione di trust di tutti i certificati rilasciati dall'autorità. Successivamente, il proprietario può usare il certificato radice per emettere altri certificati intermedi (certificati "foglia").

### <a name="root-ca-certificate"></a>Certificato CA radice

Un certificato CA radice è la base per l'attendibilità dell'intero processo. Negli scenari di produzione, in genere è presente un certificato CA acquistato da un'autorità di certificazione commerciale attendibile, ad esempio Baltimore, Verisign o DigiCert. Chi ha il controllo completo dei dispositivi che si connettono ai dispositivi IoT Edge può usare un'autorità di certificazione di livello aziendale. In entrambi i casi, viene eseguito il rollback dell'intera catena di certificati dall'hub IoT Edge, quindi i dispositivi foglia devono considerare attendibile il certificato radice. È possibile archiviare il certificato CA radice nell'archivio dell'autorità per i certificati radice attendibili o specificare i dettagli del certificato nel codice dell'applicazione.

### <a name="intermediate-certificates"></a>Certificati intermedi

I certificati CA radice sono un modo tipico per rendere sicuri i dispositivi e raramente vengono usati direttamente, principalmente a causa del rischio di perdita o di esposizione. Il certificato CA radice crea e firma digitalmente uno o più certificati CA intermedi. Può essere presente una sola o una catena di questi certificati intermedi. Gli scenari che richiedono una catena di certificati intermedi includono:

* Una gerarchia di reparti all'interno del sistema di un produttore.

* Più aziende coinvolte in serie nella produzione di un dispositivo.

* Un cliente acquista un certificato CA radice e ne deriva un certificato di firma con cui il produttore firma i dispositivi prodotti per conto del cliente.

In ogni caso, il produttore usa un certificato CA intermedio alla fine della catena per firmare il certificato CA del dispositivo finale. In genere, questi certificati intermedi sono custoditi con cura presso l'impianto di produzione. Il loro utilizzo è subordinato a rigorosi processi sia fisici che elettronici.

### <a name="device-ca-certificate"></a>Certificato CA del dispositivo

Il certificato CA del dispositivo è generato e firmato dal certificato intermedio finale del processo. Questo certificato viene installato nel dispositivo IoT Edge stesso, preferibilmente in un archivio protetto, ad esempio un modulo di protezione hardware. Inoltre, un certificato CA del dispositivo identifica in modo univoco un dispositivo IoT Edge. Il certificato della CA del dispositivo può firmare altri certificati.

### <a name="iot-edge-workload-ca"></a>Certificato CA del carico di lavoro di IoT Edge

Al primo avvio di IoT Edge, il [Gestore sicurezza di IoT Edge](iot-edge-security-manager.md) genera il certificato CA del carico di lavoro, il primo sul lato operatore del processo. Questo certificato viene generato da e firmato dal certificato della CA del dispositivo. Questo certificato, ovvero un altro certificato di firma intermedio, viene usato per generare e firmare altri certificati usati dal runtime di IoT Edge. Al momento, si tratta principalmente del certificato del server dell'hub di IoT Edge descritto nella sezione seguente, ma in futuro potrebbe trattarsi di altri certificati per autenticare i componenti di IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certificato del server dell'hub di IoT Edge

Il certificato del server dell'hub di IoT Edge è il certificato vero e proprio presentato ai dispositivi foglia e ai moduli che si occupano della verifica delle identità durante la creazione delle connessioni TLS richieste da IoT Edge. Questo certificato presenta l'intera catena di certificati di firma usati per generarlo fino al certificato CA radice, che il dispositivo foglia IoT deve considerare attendibile. Quando viene generato da IoT Edge, il nome comune (CN) di questo certificato dell'hub IoT Edge viene impostato sulla proprietà' hostname ' nel file config dopo la conversione in lettere minuscole. Questa configurazione è una fonte comune di confusione con IoT Edge.

## <a name="production-implications"></a>Implicazioni di produzione

Potrebbe essere ragionevole chiedersi "Perché IoT Edge ha bisogno del certificato CA del carico di lavoro aggiuntivo? Non potrebbe usare il certificato CA del dispositivo per generare direttamente il certificato del server dell'hub di IoT Edge?". Tecnicamente, potrebbe. Tuttavia, lo scopo di questo certificato di "carico di lavoro" intermedio è quello di separare i compiti tra il produttore del dispositivo e l'operatore del dispositivo. Si immagini uno scenario in cui un dispositivo IoT Edge è venduto o trasferito da un cliente a un altro. È opportuno che il certificato CA del dispositivo fornito dal produttore non venga modificato. Tuttavia, i certificati del "carico di lavoro" specifici del dispositivo devono essere cancellati e ricreati per il nuovo impiego.

Poiché i processi di produzione e operativi sono separati, tenere in considerazione le implicazioni seguenti quando si preparano i dispositivi di produzione:

* Per qualsiasi processo basato su certificato, il certificato CA radice e tutti i certificati CA intermedi devono essere protetti e monitorati durante l'intero processo di implementazione di un dispositivo IoT Edge. Il produttore del dispositivo IoT Edge deve predisporre processi rigorosi per l'archiviazione e l'utilizzo dei certificati intermedi. Inoltre, il certificato CA del dispositivo deve essere conservato in un archivio sicuro nel dispositivo stesso, preferibilmente in un modulo di protezione hardware.

* Il certificato del server dell'hub di IoT Edge viene presentato dall'hub di IoT Edge ai dispositivi client e ai moduli con cui deve connettersi. Il nome comune (CN) del certificato della CA del dispositivo **non deve corrispondere** al nome host che verrà usato nel file di configurazione nel dispositivo IOT Edge. Il nome usato dai client per connettersi a IoT Edge (ad esempio, tramite il parametro GatewayHostName della stringa di connessione o il comando CONNECT in MQTT) **non può corrispondere** al nome comune usato nel certificato della CA del dispositivo. Questo perché l'hub di IoT Edge presenta l'intera catena di certificati per la verifica da parte dei client. Se il certificato del server dell'hub di IoT Edge e il certificato CA del dispositivo condividono lo stesso nome comune, si entra in un ciclo continuo di verifica che invalida il certificato.

* Poiché viene usato dal daemon di sicurezza di IoT Edge per generare i certificati IoT Edge finali, il certificato CA del dispositivo deve essere un certificato di firma, cioè deve avere funzionalità di firma del certificato. È sufficiente aggiungere la stringa "V3 Basic constraints CA:True" al certificato del dispositivo CA per impostare automaticamente le proprietà di utilizzo della chiave necessarie.

>[!Tip]
> Se è già stata eseguita la configurazione di IoT Edge come gateway trasparente in uno scenario di sviluppo/test usando i "script pratici" (vedere la sezione successiva) ed è stato usato lo stesso nome host quando si crea il certificato della CA del dispositivo come per il nome host nel file di configurazione, è possibile chiedersi perché ha funzionato. Allo scopo di semplificare l'esperienza di sviluppo, negli script rapidi viene aggiunta la stringa ".ca"" alla fine del nome passato allo script. Se, ad esempio, è stato usato "gateway" per il nome del dispositivo negli script e nel nome host nel file di configurazione, il primo verrà trasformato in mygateway.ca prima di essere usato come CN per il certificato CA del dispositivo.

## <a name="devtest-implications"></a>Implicazioni di sviluppo/test

Per facilitare la creazione di scenari di sviluppo e test, Microsoft mette a disposizione un set di [script rapidi](https://github.com/Azure/iotedge/tree/master/tools/CACertificates) per la generazione di certificati non di produzione adatti per IoT Edge nello scenario gateway trasparente. Per esempi di funzionamento degli script, vedere [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md).

>[!Tip]
> Per connettere le applicazioni e i dispositivi "foglia" IoT che usano l'SDK per dispositivi IoT tramite IoT Edge, è necessario aggiungere il parametro facoltativo GatewayHostName alla fine della stringa di connessione del dispositivo. Quando viene generato il certificato del server dell'hub perimetrale, si basa su una versione in minuscolo del nome host dal file di configurazione, pertanto, affinché i nomi corrispondano e la verifica del certificato TLS abbia esito positivo, è necessario immettere il parametro GatewayHostName in lettere minuscole.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Esempio di gerarchia dei certificati di IoT Edge

Per illustrare questo percorso dei certificati, viene proposto lo screenshot seguente preso da un dispositivo IoT Edge in esecuzione configurato come gateway trasparente. OpenSSL viene utilizzato per connettersi all'hub di IoT Edge, convalidare ed eseguire il dump dei certificati.

![Screenshot della gerarchia di certificati a ogni livello](./media/iot-edge-certs/iotedge-cert-chain.png)

La gerarchia di profondità dei certificati rappresentata nello screenshot è la seguente:

| Certificato CA radice         | Solo test certificato CA di hub IoT di Azure                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificato CA intermedio | Solo test certificato intermedio di hub IoT di Azure                                                                 |
| Certificato CA del dispositivo       | iotgateway.ca ("iotgateway" è stato passato come < nome host gateway > agli script rapidi)   |
| Certificato CA carico di lavoro     | iotedge workload ca                                                                                       |
| Certificato del server dell'hub di IoT Edge | iotedgegw. local (corrisponde a' hostname ' dal file di configurazione)                                            |

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)

[Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
