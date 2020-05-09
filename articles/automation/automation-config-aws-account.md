---
title: Autenticare i runbook di Automazione di Azure con Amazon Web Services
description: Questo articolo descrive come creare e convalidare le credenziali di AWS per i runbook in Automazione di Azure che gestiscono le risorse di AWS.
keywords: autenticazione AWS, configurare AWS
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 92919d2e0cc7ca685d2b60a8e7a8cf20433bbefc
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994703"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Autenticare i runbook di Automazione di Azure con Amazon Web Services

I runbook di automazione di Azure consentono di automatizzare le attività comuni relative alle risorse in Amazon Web Services (AWS). Esattamente come avviene per le risorse in Azure, con i runbook di Automazione è possibile automatizzare numerose attività in AWS. A questo scopo, sono necessari due elementi:

* Una sottoscrizione di AWS e un set di credenziali. Nello specifico, si tratta della chiave di accesso e della chiave privata di AWS. Per altre informazioni, vedere l'articolo [Using AWS Credentials](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) (Uso di credenziali AWS).
* Una sottoscrizione di Azure e un account di Automazione.

Per eseguire l'autenticazione in AWS è necessario specificare un set di credenziali di AWS per autenticare i runbook in esecuzione in Automazione di Azure. Se è già stato creato un account di Automazione e si vuole usarlo per l'autenticazione in AWS, seguire la procedura illustrata nella sezione successiva. Se si vuole dedicare un account per manuali operativi per le risorse AWS, è prima necessario creare un nuovo [account di automazione](automation-create-standalone-account.md) e ignorare il passaggio per creare un account RunAs. Dopo aver creato l'account, attenersi alla procedura seguente per completare la configurazione.

## <a name="configure-automation-account"></a>Configurare l'account di Automazione

Per consentire ad Automazione di Azure di comunicare con AWS, è prima di tutto necessario recuperare le credenziali di AWS e archiviarle come asset in Automazione di Azure. Seguire questa procedura illustrata nel documento AWS relativo alla [gestione delle chiavi di accesso per l'account AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) per creare una chiave di accesso e quindi copiare i valori di **Access Key ID** e **Secret Access Key** oppure scaricare il file di chiave e archiviarlo in una posizione sicura.

Dopo aver creato e copiato le chiavi di sicurezza di AWS, è necessario creare un asset credenziali con un account di Automazione di Azure per archiviarle in modo sicuro e farvi riferimento con i runbook. Seguire la procedura illustrata nella sezione **Creazione di un nuovo asset credenziali** dell'articolo [Asset credenziali in Automazione di Azure](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) e specificare le informazioni seguenti:

1. Nella casella **Nome** immettere **AWScred** o un valore appropriato in base agli standard di denominazione in uso.
2. Nella casella **Nome utente** digitare il valore in **ID accesso** e immettere il valore di **Secret Access Key** nelle caselle **Password** e **Conferma password**.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare manuali operativi per automatizzare le attività in AWS, vedere [automazione della distribuzione di una macchina virtuale in Amazon Web Services](automation-scenario-aws-deployment.md) .
