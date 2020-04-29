---
title: includere il file
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049000"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

In questo articolo viene creato un servizio back-end che pianifica un processo per richiamare un metodo diretto in un dispositivo, pianifica un processo per l'aggiornamento del dispositivo gemello e monitora lo stato di ogni processo. Per eseguire queste operazioni, il servizio richiede le autorizzazioni di **lettura** del registro di sistema e di **scrittura del registro** di sistema. Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **registryReadWrite** che concede queste autorizzazioni.
