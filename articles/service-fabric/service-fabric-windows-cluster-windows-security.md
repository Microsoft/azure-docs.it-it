---
title: Proteggere un cluster in esecuzione in Windows usando la sicurezza di Windows
description: Informazioni su come configurare la sicurezza da nodo a nodo e da client a nodo in un cluster autonomo in esecuzione in Windows usando la protezione di Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76774453"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteggere un cluster autonomo in Windows usando la protezione di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo usando la protezione di Windows nel file *ClusterConfig.JSON*.  Il processo corrisponde al passaggio di configurazione della sicurezza in [Creare un cluster autonomo in esecuzione su Windows Server](service-fabric-cluster-creation-for-windows-server.md). Per altre informazioni sull'uso della protezione di Windows in Service Fabric, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

> [!NOTE]
> È opportuno considerare con attenzione la selezione della sicurezza da nodo a nodo, perché non esiste un aggiornamento del cluster da una scelta di sicurezza a un'altra. Per modificare la selezione della sicurezza, è necessario ricompilare il cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurare la funzionalità di sicurezza di Windows usando l'approccio account del servizio gestito del gruppo  
Il file di configurazione *ClusterConfig. gMSA. Windows. MultiMachine. JSON* di esempio scaricato con [Microsoft. Azure. ServiceFabric. WindowsServer\< . ](https://go.microsoft.com/fwlink/?LinkId=730690)il pacchetto del cluster autonomo versione>. zip contiene un modello per la configurazione della sicurezza di Windows tramite l' [account del servizio gestito del gruppo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Impostazione di configurazione** | **Descrizione** |
| --- | --- |
| ClusterCredentialType |Impostare su *Windows* per abilitare la sicurezza di Windows per la comunicazione tra nodi.  | 
| ServerCredentialType |Impostare su *Windows* per abilitare la sicurezza di Windows per la comunicazione da client a nodo. |
| WindowsIdentities |Contiene le identità del client e del cluster. |
| ClustergMSAIdentity |Configura la sicurezza da nodo a nodo. Account del servizio gestito del gruppo. |
| ClusterSPN |SPN registrato per l'account gMSA|
| ClientIdentities |Configura la sicurezza da client a nodo. Matrice di account utente del client. |
| Identità |Aggiungere l'utente del dominio, dominio\nome utente, per l'identità del client. |
| IsAdmin |Impostare su true per indicare che l'utente del dominio ha un accesso client come amministratore oppure su false per indicare un accesso client come utente. |

> [!NOTE]
> Il valore di ClustergMSAIdentity deve essere nelmysfgmsa@mydomainformato "".

La [protezione da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) viene configurata impostando **ClustergMSAIdentity** quando l'infrastruttura di servizi deve essere eseguita nell'account del servizio gestito del gruppo. Per creare relazioni di trust tra i nodi, è necessario che si riconoscano. Questa operazione può essere eseguita in due modi diversi: specificare l'account del servizio gestito del gruppo che include tutti i nodi del cluster oppure specificare il gruppo del computer di dominio che include tutti i nodi del cluster. È consigliabile usare l'approccio degli [account del servizio gestiti del gruppo](https://technet.microsoft.com/library/hh831782.aspx) , in particolare per i cluster più grandi (più di 10 nodi) o per i cluster soggetti a probabile crescita o riduzione.  
Non richiede inoltre la creazione di un gruppo di dominio per cui agli amministratori del cluster siano stati concessi i diritti di accesso per aggiungere e rimuovere membri. Questi account si rivelano utili anche nella gestione automatica delle password. Per altre informazioni, vedere [Introduzione gli account del servizio gestiti del gruppo](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security) è un'impostazione configurata tramite **ClientIdentities**. Per stabilire un trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità dei client può considerare attendibili. Questa operazione può essere eseguita in due modi diversi: specificare se la connessione è consentita agli utenti dei gruppo di dominio o agli utenti dei nodi di dominio. Infrastruttura di servizi supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Infrastruttura di servizi: amministratore e utente. La funzionalità di controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinati tipi di operazioni del cluster per diversi gruppi di utenti, rendendo il cluster più sicuro.  Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi. Per altre informazioni sul controllo degli accessi, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md).  
 
La sezione **security** dell'esempio seguente configura la sicurezza di Windows usando un account del servizio gestiti del gruppo e specifica che i computer nell'account del servizio gestiti del gruppo *ServiceFabric.clusterA.contoso.com* fanno parte del cluster e che a *CONTOSO\usera* è consentito l'accesso client come amministratore:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configurare la funzionalità di sicurezza di Windows usando un gruppo di computer  
Questo modello è in fase di deprecazione. È consigliabile usare gMSA come descritto sopra. Il file di configurazione *ClusterConfig. Windows. MultiMachine. JSON* di esempio scaricato con [Microsoft. Azure. ServiceFabric. WindowsServer\< . ](https://go.microsoft.com/fwlink/?LinkId=730690)il pacchetto del cluster autonomo versione>. zip contiene un modello per la configurazione della sicurezza di Windows.  La sicurezza di Windows viene configurata nella sezione **Proprietà** : 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Impostazione di configurazione** | **Descrizione** |
| --- | --- |
| ClusterCredentialType |Impostare su *Windows* per abilitare la sicurezza di Windows per la comunicazione tra nodi.  |
| ServerCredentialType |Impostare su *Windows* per abilitare la sicurezza di Windows per la comunicazione da client a nodo. |
| WindowsIdentities |Contiene le identità del client e del cluster. |
| ClusterIdentity |Usare il nome di un gruppo di computer, dominio\gruppo di computer, per configurare la sicurezza da nodo a nodo. |
| ClientIdentities |Configura la sicurezza da client a nodo. Matrice di account utente del client. |  
| Identità |Aggiungere l'utente del dominio, dominio\nome utente, per l'identità del client. |  
| IsAdmin |Impostare su true per indicare che l'utente del dominio ha un accesso client come amministratore oppure su false per indicare un accesso client come utente. |  

La [sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) viene configurata impostando l'uso di **ClusterIdentity** se si vuole usare un gruppo di computer all'interno di un dominio di Active Directory. Per altre informazioni, vedere l'articolo su come [Creare un gruppo di computer in Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

La [sicurezza da client a nodo](service-fabric-cluster-security.md#client-to-node-security) viene configurata tramite **ClientIdentities**. Per stabilire una relazione di trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca le identità dei client che il cluster può considerare attendibili. È possibile stabilire una relazione di trust in due modi diversi:

- Specificare gli utenti del gruppo di dominio che possono connettersi.
- Specificare gli utenti del nodo del dominio che possono connettersi.

Infrastruttura di servizi supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Infrastruttura di servizi: amministratore e utente. La funzionalità di controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinati tipi di operazioni del cluster per diversi gruppi di utenti, rendendo il cluster più sicuro.  Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.  

La sezione **security** dell'esempio seguente configura la protezione di Windows, specifica che i computer in *ServiceFabric/clusterA.contoso.com* fanno parte del cluster e specifica che a *CONTOSO\usera* è consentito l'accesso client come amministratore:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric non devono essere distribuito in un controller di dominio. Verificare che ClusterConfig.json non includa l'indirizzo IP del controller di dominio quando si usa un gruppo di computer o un account del servizio gestito del gruppo.
>
>

## <a name="next-steps"></a>Passaggi successivi
Dopo la configurazione della sicurezza di Windows nel file *ClusterConfig.JSON* , riprendere il processo di creazione del cluster in [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md)(Creare un cluster autonomo in esecuzione in Windows).

Per altre informazioni sulla sicurezza da nodo a nodo e da client a nodo e sul controllo degli accessi in base al ruolo, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

Per alcuni esempi di connessione tramite PowerShell o FabricClient, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md).
