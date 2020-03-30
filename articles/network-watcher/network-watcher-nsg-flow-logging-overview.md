---
title: Introduzione alla registrazione del flusso per i gruppi di sicurezza di sicurezza di sicurezza di sistemaIntroduction to flow logging for NSGs
titleSuffix: Azure Network Watcher
description: Questo articolo illustra come usare la funzionalità dei log dei flussi dei gruppi di sicurezza di rete di Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228252"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete

I log dei flussi del gruppo di sicurezza di rete (NSG) sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita per ogni regola, l'interfaccia di rete (NIC) a cui si applica il flusso, informazioni a 5 tuple relative al flusso (indirizzo IP di origine/destinazione, porta di origine/destinazione e protocollo), se il traffico è consentito o meno e informazioni sulla velocità effettiva (byte e pacchetti) nella versione 2.


![Panoramica dei log di flusso](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo in un account di archiviazione e hanno un percorso di registrazione come quello dell'esempio seguente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
È possibile analizzare i log dei flussi e ottenere informazioni approfondite del traffico di rete usando l'[analisi del traffico](traffic-analytics.md).

Ai log dei flussi si applicano gli stessi criteri di conservazione degli altri log. È possibile impostare i criteri di conservazione dei log da 1 giorno a 365 giorni. Se non viene impostato alcun criterio di conservazione, i log vengono conservati per sempre.

## <a name="log-file"></a>File di registro

I log dei flussi includono le proprietà seguenti:

* **time**: ora in cui l'evento è stato registrato.
* **systemId**: ID risorsa del gruppo di sicurezza di rete.
* **category**: categoria dell'evento. La categoria è sempre **NetworkSecurityGroupFlowEvent**
* **resourceid**: ID risorsa del gruppo di sicurezza di rete.
* **operationName**: sempre NetworkSecurityGroupFlowEvents.
* **properties**: raccolta di proprietà del flusso.
    * **Version**: numero di versione dello schema di eventi del log dei flussi.
    * **flows** - Una raccolta di flussi. Questa proprietà ha più voci per regole diverse.
        * **rule**: regola per cui vengono elencati i flussi.
            * **flows**: raccolta di flussi.
                * **mac**: indirizzo MAC della scheda di interfaccia di rete per la VM in cui è stato raccolto il flusso.
                * **flowTuples**: stringa che contiene più proprietà per la tupla del flusso nel formato con valori separati da virgole.
                    * **Indicatore di data** e ora - Questo valore è il timestamp di quando il flusso si è verificato nel formato epoca UNIX
                    * **Source IP**: IP di origine.
                    * **Destination IP**: IP di destinazione.
                    * **Source Port**: porta di origine.
                    * **Destination Port**: porta di destinazione.
                    * **Protocol**: protocollo del flusso. I valori validi sono **T** per TCP e **U** per UDP.
                    * **Traffic Flow**: direzione del flusso del traffico. I valori validi sono **I** per traffico in ingresso e **O** per il traffico in uscita.
                    * **Traffic Decision**: indica se il traffico è stato consentito o negato. I valori validi sono **A** per il traffico consentito e **D** per il traffico negato.
                    * **Flow State - Version 2 Only**: acquisisce lo stato del flusso. Gli stati possibili sono **B**: indica la creazione di un flusso. Non vengono fornite statistiche. **C**: indica un flusso in corso. Vengono fornite statistiche a intervalli di 5 minuti. **E**: indica un flusso terminato. Vengono fornite statistiche.
                    * **Packets - Source to destination - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento.
                    * **Bytes sent - Source to destination - Version 2 Only** Numero totale di byte dei pacchetti TCP o UDP inviati dall'origine alla destinazione dall'ultimo aggiornamento. I byte dei pacchetti includono l'intestazione del pacchetto e il payload.
                    * **Packets - Destination to source - Version 2 Only** Numero totale di pacchetti TCP o UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento.
                    * **Bytes sent - Destination to source - Version 2 Only** Numero totale di byte dei pacchetti TCP e UDP inviati dalla destinazione all'origine dall'ultimo aggiornamento. I byte dei pacchetti includono payload e intestazione del pacchetto.

## <a name="nsg-flow-logs-version-2"></a>Log di flusso del gruppo di sicurezza di rete versione 2

La versione 2 dei log introduce lo stato del flusso. È possibile configurare la versione del log di flusso ricevuta. Per informazioni su come abilitare i log dei flussi, vedere l'argomento relativo all'[abilitazione della registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-portal.md).

Lo stato del flusso *B* viene registrato quando viene avviato un flusso. Gli stati del flusso *C* ed *E* contrassegnano rispettivamente la continuazione e la fine di un flusso. Gli stati *C* ed *E* contengono informazioni sulla larghezza di banda del traffico.

**Esempio**: tuple dei flussi da una conversazione TCP tra 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Per gli stati *C* ed *E* del flusso, i conteggi di byte e pacchetti sono conteggi aggregati dal momento della registrazione della tupla del flusso precedente. Facendo riferimento alla conversazione di esempio precedente, il numero totale di pacchetti trasferiti è 1021+52+8005+47=9125. Il numero totale di byte trasferiti è 588096+29952+4610880+27072=5256000.

Di seguito è riportato un testo di esempio di log dei flussi. Come si può osservare, più record seguono l'elenco di proprietà descritto nella sezione precedente.

## <a name="nsg-flow-logging-considerations"></a>Considerazioni sulla registrazione del flusso del gruppo di sicurezza di gruppo

**Considerazioni sull'account di archiviazione**: 

- Percorso: l'account di archiviazione usato deve trovarsi nella stessa area del gruppo di sicurezza di rete.
- Rotazione automatica delle chiavi: se si modificano/ruotano le chiavi di accesso all'account di archiviazione, i registri di flusso del gruppo di sicurezza di rete smetteranno di funzionare. Per risolvere questo problema, è necessario disattivare e quindi riattivare i registri di flusso del gruppo di sicurezza di rete.

Abilitare la registrazione del flusso del gruppo di sicurezza di rete in tutti i gruppi di sicurezza di rete **collegati a una risorsa:** la registrazione del flusso in Azure è configurata nella risorsa del gruppo di sicurezza di rete. Un flusso sarà associato a una sola regola di gruppo di sicurezza di rete. Negli scenari in cui vengono usati più gruppi di sicurezza di rete, è consigliabile abilitare la registrazione dei flussi in tutti i gruppi di sicurezza di rete applicati all'interfaccia di rete o subnet di una risorsa per assicurarsi che tutto il traffico venga registrato. Per altre informazioni, vedere [come viene valutato il traffico](../virtual-network/security-overview.md#how-traffic-is-evaluated) nei gruppi di sicurezza di rete.

**Costi di registrazione del flusso:** la registrazione del flusso del gruppo di sicurezza di rete viene fatturata nel volume di log prodotti. Un volume di traffico elevato può avere come effetto un volume elevato dei log dei flussi, con i conseguenti costi associati. I prezzi dei log dei flussi dei gruppi di sicurezza di rete non includono i costi di archiviazione sottostanti. L'utilizzo della funzionalità dei criteri di conservazione con la registrazione del flusso del gruppo di sicurezza di rete comporta costi di archiviazione separati per lunghi periodi di tempo. Se non è necessario usare la funzionalità dei criteri di conservazione, è consigliabile impostare questo valore su 0. Per altre informazioni, vedere [Network Watcher Pricing](https://azure.microsoft.com/pricing/details/network-watcher/) e Azure Storage [Pricing](https://azure.microsoft.com/pricing/details/storage/) per altri dettagli.

**Flussi in ingresso registrati da indirizzi IP Internet a macchine virtuali senza indirizzi**IP pubblici: macchine virtuali che non dispongono di un indirizzo IP pubblico assegnato tramite un indirizzo IP pubblico associato alla scheda di interfaccia di rete come ip pubblico a livello di istanza o che fanno parte di un pool back-end di bilanciamento del carico di base, usano [SNAT predefinito](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e dispongono di un indirizzo IP assegnato da Azure per facilitare la connettività in uscita. Di conseguenza, è possibile visualizzare le voci del log di flusso per i flussi dagli indirizzi IP Internet, se il flusso è destinato a una porta nell'intervallo di porte assegnate per SNAT. Anche se Azure non consente questi flussi nella macchina virtuale, il tentativo viene registrato e viene visualizzato nel log di flusso del gruppo di sicurezza di rete di Network Watcher in base alla progettazione. È consigliabile bloccare esplicitamente il traffico Internet in ingresso indesiderato con il gruppo di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sicurezza di sistema.

**Conteggi di byte e pacchetti non corretti per i flussi senza stato:** i gruppi di sicurezza di [rete (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) vengono implementati come [firewall con stato](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Tuttavia molte regole predefinite/interne che controllano il flusso di traffico vengono implementate in modo senza stato. A causa delle limitazioni della piattaforma, i conteggi di byte e pacchetti non vengono registrati per i flussi senza stato (ovvero i flussi di traffico che passano attraverso le regole senza stato), vengono registrati solo per i flussi con stato. Di conseguenza, il numero di byte e pacchetti riportati nei registri di flusso del gruppo di sicurezza di rete (e analisi del traffico) potrebbe essere diverso dai flussi effettivi. Questa limitazione è prevista per essere corretta entro giugno 2020.

## <a name="sample-log-records"></a>Record di log di esempio

Di seguito è riportato un testo di esempio di log dei flussi. Come si può osservare, più record seguono l'elenco di proprietà descritto nella sezione precedente.


> [!NOTE]
> I valori nella proprietà*flowTuples* sono un elenco separato da virgole.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Esempio di formato di log di flusso del gruppo di sicurezza di rete versione 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come abilitare i log dei flussi, vedere l'argomento relativo all'[abilitazione della registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-portal.md).
- Per informazioni su come leggere i log di flusso, vedere [Leggere i log dei flussi del gruppo di sicurezza di rete](network-watcher-read-nsg-flow-logs.md).
- Per altre informazioni sulla registrazione dei gruppi di sicurezza di rete, vedere Log di Monitoraggio di Azure per i gruppi di sicurezza di [rete.](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- Per determinare se il traffico è consentito o negato da o verso una macchina virtuale, vedere l'argomento relativo a [come diagnosticare un problema di filtro del traffico di rete di una macchina virtuale](diagnose-vm-network-traffic-filtering-problem.md)
