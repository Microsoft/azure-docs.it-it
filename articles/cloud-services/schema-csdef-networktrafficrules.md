---
title: Schema dei servizi cloud di Azure (versione classica) def. NetworkTrafficRules | Microsoft Docs
description: Informazioni su NetworkTrafficRules, che limita i ruoli che possono accedere agli endpoint interni di un ruolo. Combina i ruoli in un file di definizione del servizio.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2c8ab53068b71652d03d03bf79a224fe5e34dff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739769"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Schema NetworkTrafficRules di definizione di servizi cloud di Azure (versione classica)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Il nodo `NetworkTrafficRules` è un elemento facoltativo nel file di definizione del servizio che specifica come i ruoli comunicano tra loro. Impone un limite ai ruoli che possono accedere agli endpoint interni del ruolo specifico. `NetworkTrafficRules` non è un elemento autonomo. Viene combinato con due o più ruoli Web nel file di definizione del servizio.

L'estensione predefinita per il file di definizione del servizio è csdef.

> [!NOTE]
>  Il nodo `NetworkTrafficRules` è disponibile solo se si usa Azure SDK versione 1.3 o successiva.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Schema di definizione del servizio di base per le regole di traffico di rete
Il formato di base di un file di definizione del servizio contenente le definizioni del traffico di rete è il seguente.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementi dello schema
Il nodo `NetworkTrafficRules` del file di definizione del servizio include gli elementi descritti in dettaglio nelle sezioni successive di questo argomento:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento Destinations](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

Elemento AllowAllTraffic

[Elemento WhenSource](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> Elemento NetworkTrafficRules
L'elemento `NetworkTrafficRules` specifica quali ruoli possono comunicare con quali endpoint in un altro ruolo. Un servizio può contenere una definizione `NetworkTrafficRules`.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> Elemento OnlyAllowTrafficTo
L'elemento `OnlyAllowTrafficTo` descrive una raccolta di endpoint di destinazione e i ruoli che vi possono comunicare. È possibile specificare più nodi `OnlyAllowTrafficTo`.

##  <a name="destinations-element"></a><a name="Destinations"></a> Elemento Destinations
L'elemento `Destinations` descrive una raccolta di RoleEndpoints con i quali è possibile comunicare.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> Elemento RoleEndpoint
L'elemento `RoleEndpoint` descrive un endpoint in un ruolo per consentire le comunicazioni tra i due. È possibile specificare più elementi `RoleEndpoint` se sono presenti più di un endpoint nel ruolo.

| Attributo      | Type     | Descrizione |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Obbligatorio. Il nome dell'endpoint verso il quale consentire il traffico.|
| `roleName`     | `string` | Obbligatorio. Il nome del ruolo Web con il quale consentire le comunicazioni.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
L'elemento `AllowAllTraffic` è una regola che consente a tutti i ruoli di comunicare con gli endpoint definiti nel nodo `Destinations`.

##  <a name="whensource-element"></a><a name="WhenSource"></a> Elemento WhenSource
L'elemento `WhenSource` descrive una raccolta di ruoli che possono comunicare con gli endpoint definiti nel nodo `Destinations`.

| Attributo | Type     | Descrizione |
| --------- | -------- | ----------- |
| `matches` | `string` | Obbligatorio. Specifica la regola da applicare quando vengono consentite le comunicazioni. Al momento l'unico valore valido è `AnyRule`.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> Elemento FromRole
L'elemento `FromRole` specifica i ruoli che possono comunicare con gli endpoint definiti nel nodo `Destinations`. È possibile specificare più elementi `FromRole` se sono presenti più ruoli in grado di comunicare con gli endpoint.

| Attributo  | Type     | Descrizione |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Obbligatorio. Il nome del ruolo da cui consentire la comunicazione.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica)




