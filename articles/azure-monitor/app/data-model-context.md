---
title: Modello di dati di Azure Application Insights Telemetry - Contesto dei dati di telemetria | Microsoft Docs
description: Modello di contesto dei dati di telemetria di Application Insights
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: cbd717243ed35fc90772cb2b727f7670bb77ee70
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757300"
---
# <a name="telemetry-context-application-insights-data-model"></a>Contesto dei dati di telemetria: modello di dati di Application Insights

Ogni elemento di telemetria può contenere campi di contesto fortemente tipizzati. Ogni campo consente uno specifico scenario di monitoraggio. Usare la raccolta di proprietà personalizzate per archiviare informazioni contestuali personalizzate o specifiche dell'applicazione.


## <a name="application-version"></a>Versione dell'applicazione

Le informazioni contenute nei campi di contesto dell'applicazione si riferiscono sempre all'applicazione che invia i dati di telemetria. La versione dell'applicazione viene usata per analizzare i cambiamenti nei trend di comportamento dell'applicazione e la correlazione con le distribuzioni.

Lunghezza massima: 1024


## <a name="client-ip-address"></a>Indirizzo IP client

L'indirizzo IP del dispositivo client. Sono supportati sia IPv4 che IPv6. Quando i dati di telemetria vengono inviati da un servizio, il contesto della posizione si riferisce all'utente che ha avviato l'operazione del servizio. Application Insights estrae i dati di geolocalizzazione dall'IP client, per poi troncarli. L'IP client da solo non può quindi essere usato come informazione personale dell'utente finale. 

Lunghezza massima: 46


## <a name="device-type"></a>Tipo di dispositivo

In origine questo campo serviva a indicare il tipo di dispositivo usato dall'utente finale dell'applicazione. Oggi viene usato principalmente per distinguere i dati di telemetria JavaScript con tipo di dispositivo "Browser" dai dati di telemetria lato server con tipo di dispositivo "PC".

Lunghezza massima: 64


## <a name="operation-id"></a>ID operazione

Un identificatore univoco dell'operazione di root. Questo identificatore univoco consente di raggruppare i dati di telemetria tra componenti multipli. Per informazioni dettagliate, vedere [Correlazione di dati di telemetria](./correlation.md). L'ID operazione viene creato da una richiesta o da una visualizzazione pagina. Tutti gli altri dati di telemetria impostano questo campo sul valore per la richiesta o la visualizzazione pagina che lo contiene. 

Lunghezza massima: 128


## <a name="parent-operation-id"></a>ID operazione padre

L'identificatore univoco dell'elemento padre diretto dell'elemento di telemetria. Per informazioni dettagliate, vedere [Correlazione di dati di telemetria](./correlation.md).

Lunghezza massima: 128


## <a name="operation-name"></a>Nome operazione

Il nome (gruppo) dell'operazione. Il nome dell'operazione viene creato da una richiesta o da una visualizzazione pagina. Tutti gli altri elementi di telemetria impostano questo campo sul valore della richiesta o dalla visualizzazione pagina che lo contiene. Il nome dell'operazione è usato per individuare tutti gli elementi di telemetria per un gruppo di operazioni (ad esempio "GET Home/Index"). Questa proprietà di contesto viene usata per rispondere a domande come "Quali sono le eccezioni tipiche generate in questa pagina?".

Lunghezza massima: 1024


## <a name="synthetic-source-of-the-operation"></a>Origine sintetica dell'operazione

Il nome dell'origine sintetica. Alcuni dati di telemetria generati dall'applicazione possono rappresentare traffico sintetico. Potrebbe trattarsi di agenti di ricerca Web che indicizzano il sito Web, test di disponibilità del sito o tracce di librerie di diagnostica come Application Insights SDK stesso.

Lunghezza massima: 1024


## <a name="session-id"></a>ID sessione

L'ID sessione, cioè l'istanza di interazione dell'utente con l'app. Le informazioni contenute nei campi di contesto della sessione si riferiscono sempre all'utente finale. Quando i dati di telemetria vengono inviati da un servizio, il contesto di sessione si riferisce all'utente che ha avviato l'operazione nel servizio.

Lunghezza massima: 64


## <a name="anonymous-user-id"></a>ID utente anonimo

ID utente anonimo. Rappresenta l'utente finale dell'applicazione. Quando i dati di telemetria vengono inviati da un servizio, il contesto utente si riferisce all'utente che ha avviato l'operazione nel servizio.

Il [campionamento](./sampling.md) è una delle tecniche usate per ridurre al minimo la quantità di dati di telemetria raccolti. L'algoritmo di campionamento tenta di sondare tutti i dati di telemetria correlati in ingresso o in uscita. L'ID utente anonimo viene usato per generare un punteggio di campionamento. L'ID utente anonimo deve essere quindi un valore sufficientemente casuale. 

L'uso di un ID utente anonimo per archiviare il nome utente rappresenta un uso improprio del campo. Usare un ID utente autenticato.

Lunghezza massima: 128


## <a name="authenticated-user-id"></a>ID utente autenticato

ID utente autenticato. Il valore opposto all'ID utente anonimo, questo campo rappresenta l'utente con un nome descrittivo. Le sue informazioni personali non vengono infatti raccolte per impostazione predefinita dalla maggior parte degli SDK.

Lunghezza massima: 1024


## <a name="account-id"></a>ID account

In applicazioni multi-tenant, rappresenta l'ID o il nome dell'account con cui l'utente opera. Esempi possono essere ID sottoscrizione per portale di Azure o il nome del Blog per una piattaforma di Blog.

Lunghezza massima: 1024


## <a name="cloud-role"></a>Ruolo del cloud

Il nome del ruolo di cui l'applicazione fa parte. Esegue direttamente il mapping al nome del ruolo in Azure. Consente anche di distinguere i micro servizi che fanno parte di una singola applicazione.

Lunghezza massima: 256


## <a name="cloud-role-instance"></a>Istanza del ruolo del cloud

Il nome dell'istanza in cui l'applicazione è in esecuzione. Il nome computer in locale, il nome dell'istanza in Azure.

Lunghezza massima: 256


## <a name="internal-sdk-version"></a>Informazione interna: versione dell'SDK

La versione dell'SDK. Per informazioni, vedere [questo articolo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) .

Lunghezza massima: 64


## <a name="internal-node-name"></a>Informazione interna: nome del nodo

Questo campo rappresenta il nome del nodo usato a scopi di fatturazione. È possibile usarlo per eseguire l'override del rilevamento standard dei nodi.

Lunghezza massima: 256


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [estendere e filtrare i dati di telemetria](./api-filtering-sampling.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- Estrarre la [configurazione](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) di una raccolta di proprietà di contesto standard.

