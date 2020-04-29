---
title: Guida introduttiva a StriIm
description: Informazioni su come iniziare rapidamente a usare Striim e Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349935"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Guida all'installazione dell'offerta Striim nel marketplace di Azure SQL DW

Questa guida introduttiva presuppone che l'utente abbia già un'istanza pre-esistente di SQL Data Warehouse.

Eseguire la ricerca di Striim in Azure Marketplace e selezionare l'opzione Striim for Data Integration to SQL Data Warehouse (Staged) (Striim per l'integrazione di dati in SQL Data Warehouse - Gestione temporanea) 

![Installare Striim][install]

Configurare la macchina virtuale di Striim con le proprietà specificate, prendendo nota del nome del cluster Striim, della password e della password amministratore

![Configurare Striim][configure]

Al termine della distribuzione, \<fare clic sul nome della macchina virtuale>-masternode nel portale di Azure, fare clic su Connetti e copiare l'account di accesso con l'account VM locale 

![Collegare Striim a SQL Data Warehouse][connect]

Scaricare il file sqljdbc42.jar da <https://www.microsoft.com/en-us/download/details.aspx?id=54671> nel computer locale. 

Aprire una finestra della riga di comando e passare alla directory in cui è stato scaricato il file JDBC con estensione jar. Copiare il file JAR nella macchina virtuale Striim ottenendo l'indirizzo e la password dal portale di Azure

![Copia del file JAR nella macchina virtuale][copy-jar]

Aprire un'altra finestra della riga di comando o usare un'utilità SSH per stabilire una connessione SSH al cluster Striim

![Connessione SSH al cluster][ssh]

Eseguire i comandi seguenti per spostare il file JDBC con estensione JAR nella directory lib di Striim e avviare e arrestare il server.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Avviare il cluster Striim][start-striim]

A questo punto, aprire il browser preferito e \<passare al nome DNS>:9080

![Passare alla schermata di accesso][navigate]

Accedere con il nome utente e la password impostati nel portale di Azure e selezionare la procedura guidata preferita per iniziare subito oppure accedere alla pagina delle app per iniziare a usare l'interfaccia utente con funzione di trascinamento e rilascio

![Accedere con le credenziali del server][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
