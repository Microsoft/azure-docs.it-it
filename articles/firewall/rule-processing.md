---
title: Logica di elaborazione delle regole del Firewall di Azure
description: Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/01/2021
ms.author: victorh
ms.openlocfilehash: bbf838cfa2a6addc665df4b62e2322d056778b49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741362"
---
# <a name="configure-azure-firewall-rules"></a>Configurare le regole del firewall di Azure
È possibile configurare le regole NAT, le regole di rete e le regole delle applicazioni nel firewall di Azure. Le raccolte di regole vengono elaborate in base al tipo di regola in ordine di priorità, con numeri più alti da 100 a 65.000. Il nome di una raccolta di regole può includere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. Deve iniziare con una lettera o un numero e terminare con una lettera, un numero o un carattere di sottolineatura. La lunghezza massima del nome è di 80 caratteri.

È preferibile spaziare inizialmente i numeri di priorità della raccolta regole in incrementi di 100 (100, 200, 300 e così via), in modo da avere spazio per aggiungere altre raccolte di regole, se necessario.

> [!NOTE]
> Se si Abilita il filtro basato su Intelligence per le minacce, tali regole hanno la priorità più alta e vengono sempre elaborate per prime. Il filtro di intelligence per le minacce può negare il traffico prima dell'elaborazione di qualsiasi regola configurata. Per altre informazioni, vedere [filtro basato su Intelligence](threat-intel.md)per le minacce del firewall di Azure.

## <a name="outbound-connectivity"></a>Connettività in uscita

### <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione

Se si configurano le regole di rete e le regole dell'applicazione, le regole di rete vengono applicate in ordine di priorità prima delle regole dell'applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Quindi, se viene trovata una corrispondenza in una regola di rete, non vengono elaborate altre regole.  Se non è presente alcuna regola di rete corrispondente e se il protocollo è HTTP, HTTPS o MSSQL, il pacchetto viene quindi valutato dalle regole dell'applicazione in ordine di priorità. Se non viene trovata alcuna corrispondenza, il pacchetto viene valutato rispetto alla [raccolta di regole dell'infrastruttura](infrastructure-fqdns.md). Se non è ancora presente alcuna corrispondenza, il pacchetto viene negato per impostazione predefinita.

#### <a name="network-rule-protocol"></a>Protocollo della regola di rete

Le regole di rete possono essere configurate per **TCP**, **UDP**, **ICMP** o **qualsiasi** protocollo IP. Qualsiasi protocollo IP include tutti i protocolli IP definiti nel documento relativo ai [numeri di protocollo IANA (Internet Assigned Numbers Authority)](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) . Se una porta di destinazione è configurata in modo esplicito, la regola viene convertita in una regola TCP + UDP.

Prima del 9 novembre 2020 **qualsiasi** **protocollo TCP**, **UDP** o **ICMP**. Quindi, è possibile che sia stata configurata una regola prima di tale data con Protocol = any e porte di destinazione =' *'. Se non si prevede effettivamente di consentire il protocollo IP come attualmente definito, modificare la regola per configurare in modo esplicito i protocolli desiderati (TCP, UDP o ICMP).

## <a name="inbound-connectivity"></a>Connettività in ingresso

### <a name="nat-rules"></a>Regole NAT

La connettività Internet in ingresso può essere abilitata configurando DNAT (Network Address Translation) di destinazione come descritto in [esercitazione: filtrare il traffico in ingresso con il firewall di Azure DNAT usando il portale di Azure](tutorial-firewall-dnat.md). Le regole NAT vengono applicate in precedenza prima delle regole di rete. Se viene trovata una corrispondenza, viene aggiunta in modo implicito una regola di rete corrispondente per consentire il traffico convertito. Per motivi di sicurezza, l'approccio consigliato consiste nell'aggiungere un'origine Internet specifica per consentire l'accesso DNAT alla rete ed evitare l'uso di caratteri jolly.

Le regole dell'applicazione non sono applicate per le connessioni in ingresso. Se quindi si desidera filtrare il traffico HTTP/S in ingresso, è necessario utilizzare Web Application Firewall (WAF). Per altre informazioni, vedere [che cos'è il firewall applicazione Web di Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Esempio

Negli esempi seguenti vengono illustrati i risultati di alcune di queste combinazioni di regole.

### <a name="example-1"></a>Esempio 1

La connessione a google.com è consentita a causa di una regola di rete corrispondente.

**Regola di rete**

- Azione: Consenti


|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Consenti-Web     |TCP|Indirizzo IP|*|Indirizzo IP|*|80,443

**Regola applicazione**

- Azione: Nega

|name  |Tipo di origine  |Source (Sorgente)  |Protocollo:Porta|FQDN di destinazione|
|---------|---------|---------|---------|----------|----------|
|Nega-Google     |Indirizzo IP|*|http: 80, https: 443|google.com

**Risultato**

La connessione a google.com è consentita perché il pacchetto corrisponde alla regola di rete *Allow-Web* . A questo punto si interrompe l'elaborazione della regola.

### <a name="example-2"></a>Esempio 2

Il traffico SSH viene negato perché una raccolta di regole di rete *Deny* con priorità più elevata lo blocca.

**Raccolta regole di rete 1**

- Nome: Allow-Collection
- Priorità: 200
- Azione: Consenti

|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Consenti-SSH     |TCP|Indirizzo IP|*|Indirizzo IP|*|22

**Raccolta regole di rete 2**

- Nome: Deny-Collection
- Priorità: 100
- Azione: Nega

|name  |Protocollo  |Tipo di origine  |Source (Sorgente)  |Tipo destinazione  |Indirizzo di destinazione  |Porte di destinazione|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Indirizzo IP|*|Indirizzo IP|*|22

**Risultato**

Le connessioni SSH vengono negate perché una raccolta di regole di rete con priorità più elevata lo blocca. A questo punto si interrompe l'elaborazione della regola.

## <a name="rule-changes"></a>Modifiche alle regole

Se si modifica una regola per negare il traffico precedentemente consentito, verranno eliminate tutte le sessioni esistenti pertinenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare un firewall di Azure](tutorial-firewall-deploy-portal.md).
