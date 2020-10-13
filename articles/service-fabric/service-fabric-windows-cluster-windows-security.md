---
title: Proteggere un cluster in esecuzione in Windows usando la sicurezza di Windows
description: Informazioni su come configurare la sicurezza da nodo a nodo e da client a nodo in un cluster autonomo in esecuzione in Windows usando la protezione di Windows.
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: e97a951f6dc0a97b1cfa8f960ed762084c82d2ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839481"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteggere un cluster autonomo in Windows usando la protezione di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo usando la protezione di Windows nel file *ClusterConfig.JSON*.  Il processo corrisponde al passaggio di configurazione della sicurezza in [Creare un cluster autonomo in esecuzione su Windows Server](service-fabric-cluster-creation-for-windows-server.md). Per altre informazioni sull'uso della protezione di Windows in Service Fabric, vedere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

> [!NOTE]
> È opportuno considerare con attenzione la selezione della sicurezza da nodo a nodo, perché non esiste un aggiornamento del cluster da una scelta di sicurezza a un'altra. Per modificare la selezione della sicurezza, è necessario ricompilare il cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurare la funzionalità di sicurezza di Windows usando l'approccio account del servizio gestito del gruppo  
Il file di configurazione di esempio *ClusterConfig.gMSA.Windows.MultiMachine.JSON* scaricato con il pacchetto del cluster autonomo [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) contiene un modello per la configurazione della sicurezza di Windows usando l'approccio [account del servizio gestito del gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).  

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
| ClustergMSAIdentity |Configura la sicurezza da nodo a nodo. Un account del servizio gestito del gruppo. |
| ClusterSPN |SPN registrato per l'account gMSA|
| ClientIdentities |Configura la sicurezza da client a nodo. Matrice di account utente del client. |
| Identità |Aggiungere l'utente del dominio, dominio\nome utente, per l'identità del client. |
| IsAdmin |Impostare su true per indicare che l'utente del dominio ha un accesso client come amministratore oppure su false per indicare un accesso client come utente. |

> [!NOTE]
> Il valore di ClustergMSAIdentity deve essere nel formato " mysfgmsa@mydomain ".

La [protezione da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) viene configurata impostando **ClustergMSAIdentity** quando l'infrastruttura di servizi deve essere eseguita nell'account del servizio gestito del gruppo. Per creare relazioni di trust tra i nodi, è necessario che si riconoscano. Questa operazione può essere eseguita in due modi diversi: specificare l'account del servizio gestito del gruppo che include tutti i nodi del cluster oppure specificare il gruppo del computer di dominio che include tutti i nodi del cluster. È consigliabile usare l'approccio degli [account del servizio gestiti del gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) , in particolare per i cluster più grandi (più di 10 nodi) o per i cluster soggetti a probabile crescita o riduzione.  
Non richiede inoltre la creazione di un gruppo di dominio per cui agli amministratori del cluster siano stati concessi i diritti di accesso per aggiungere e rimuovere membri. Questi account si rivelano utili anche nella gestione automatica delle password. Per altre informazioni, vedere [Introduzione gli account del servizio gestiti del gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).  
 
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
Questo modello è in fase di deprecazione. È consigliabile usare gMSA come descritto sopra. Il file di configurazione di esempio *ClusterConfig.Windows.MultiMachine.JSON* scaricato con il pacchetto del cluster autonomo [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) contiene un modello per la configurazione della sicurezza di Windows.  La sicurezza di Windows viene configurata nella sezione **Proprietà** : 

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

La [sicurezza da nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) viene configurata impostando l'uso di **ClusterIdentity** se si vuole usare un gruppo di computer all'interno di un dominio di Active Directory. Per altre informazioni, vedere l'articolo su come [Creare un gruppo di computer in Active Directory](/previous-versions/commerce-server/aa545347(v=cs.70)).

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
