---
title: Gestire i processi di backup con l'API REST
description: Questo articolo illustra come tenere traccia e gestire i processi di backup e ripristino di backup di Azure usando l'API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: ced0e0020fe955734bf6cc767480fbadd6eaffc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88890281"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Tenere traccia dei processi di backup e ripristino con l'API REST

Il servizio backup di Azure attiva i processi eseguiti in background in diversi scenari, ad esempio l'attivazione del backup, le operazioni di ripristino e la disabilitazione del backup. Questi processi possono essere registrati tramite i relativi ID.

## <a name="fetch-job-information-from-operations"></a>Recuperare informazioni sui processi dalle operazioni

Un'operazione come l'attivazione del backup restituirà sempre un jobID. Ad esempio, la risposta finale di un' [operazione dell'API REST di backup del trigger](backup-azure-arm-userestapi-backupazurevms.md#example-responses-for-on-demand-backup) è la seguente:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Il processo di backup di macchine virtuali di Azure è identificato dal campo "jobId" e può essere registrato come indicato [qui](/rest/api/backup/jobdetails/) usando una semplice richiesta *GET*.

## <a name="tracking-the-job"></a>Tenere traccia del processo

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

`{jobName}` è il "jobId" indicato in precedenza. La risposta è sempre 200 OK con il campo "status" che indica lo stato corrente del processo. Una volta "completed" o "CompletedWithWarnings", la sezione ' extendedInfo ' Mostra ulteriori dettagli sul processo.

### <a name="response"></a>Risposta

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|200 - OK     | [JobResource](/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Risposta di esempio

Dopo che l'URI *GET* è stato inviato, viene restituita una risposta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
