---
title: Creare avvisi personalizzati
description: Comprendere, creare e assegnare avvisi personalizzati per il servizio Azure Defender per il servizio di sicurezza Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: e59bcca9561c33610170f4bc3674eef307cf0de9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809046"
---
# <a name="create-custom-alerts"></a>Creare avvisi personalizzati

L'uso di gruppi di sicurezza e avvisi personalizzati sfrutta al meglio le informazioni di sicurezza end-to-end e la conoscenza categoriale dei dispositivi per garantire una maggiore sicurezza nell'intera soluzione IoT.

## <a name="why-use-custom-alerts"></a>Perché usare gli avvisi personalizzati?

Solo il proprietario conosce bene i propri dispositivi.

Per i clienti che comprendono appieno il comportamento del dispositivo previsto, Defender per IoT consente di tradurre questa conoscenza in un criterio di comportamento di un dispositivo, avvisando in caso di eventuali deviazioni dal normale comportamento previsto.

## <a name="security-groups"></a>Gruppi di sicurezza

I gruppi di sicurezza consentono di definire gruppi logici di dispositivi e di gestirne lo stato della sicurezza in modo centralizzato.

Questi gruppi possono rappresentare i dispositivi con hardware specifico, i dispositivi distribuiti in un determinato percorso o qualsiasi altro gruppo adatto a esigenze specifiche.

I gruppi di sicurezza sono definiti da una proprietà tag dei dispositivi gemelli denominata **SecurityGroup**. Per impostazione predefinita, ogni soluzione IoT sull'hub IoT ha un gruppo di sicurezza denominato **default**. Per cambiare il gruppo di sicurezza di un dispositivo, cambiare il valore della proprietà **SecurityGroup**.

Ad esempio:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Usare i gruppi di sicurezza per raggruppare i dispositivi in categorie logiche. Dopo aver creato i gruppi, assegnarli agli avvisi personalizzati a scelta, per ottenere la soluzione di sicurezza per IoT end-to-end più efficace.

## <a name="customize-an-alert"></a>Personalizzare un avviso

1. Aprire l'hub IoT e quindi scegliere **Impostazioni** dal menu **Sicurezza**.

1. Selezionare gli **avvisi personalizzati**.

1. Scegliere un gruppo di sicurezza a cui si vuole applicare la personalizzazione.

1. Selezionare **Aggiungi un avviso personalizzato**.

1. Selezionare un avviso personalizzato nell'elenco a discesa.

1. Modificare le proprietà obbligatorie e selezionare **OK**.

1. Assicurarsi di selezionare **Salva**. Se non si salva il nuovo avviso, l'avviso verrà eliminato la volta successiva che si chiude l'hub IoT.

## <a name="alerts-available-for-customization"></a>Avvisi disponibili per la personalizzazione

Defender for Internet è un numero elevato di avvisi, che può essere personalizzato in base alle esigenze specifiche. Esaminare la [tabella degli avvisi personalizzabile](concept-customizable-security-alerts.md) per la gravità degli avvisi, l'origine dati, la descrizione e i passaggi consigliati per la correzione se e quando ogni avviso viene ricevuto.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un agente di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
