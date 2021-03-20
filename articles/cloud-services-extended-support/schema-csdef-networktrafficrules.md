---
title: Azure Cloud Services (Extended support) Def. NetworkTrafficRules-schema | Microsoft Docs
description: Information om nätverks trafik regler som är associerade med Cloud Services (utökad support)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744796"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Azure Cloud Services (Extended support) definition NetworkTrafficRules-schema

`NetworkTrafficRules`Noden är ett valfritt element i tjänst definitions filen som anger hur roller kommunicerar med varandra. Den begränsar vilka roller som har åtkomst till de interna slut punkterna för den aktuella rollen. `NetworkTrafficRules`Är inte ett fristående element. det kombineras med två eller flera roller i en tjänst definitions fil.

Standard tillägget för tjänst definitions filen är csdef.

> [!NOTE]
>  `NetworkTrafficRules`Noden är bara tillgänglig med Azure SDK version 1,3 eller senare.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Grundläggande tjänst definitions schema för nätverks trafik regler
Det grundläggande formatet för en tjänst definitions fil som innehåller definitioner av nätverks trafik är följande.

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

## <a name="schema-elements"></a>Schema element
`NetworkTrafficRules`Noden för tjänst definitions filen innehåller dessa element, som beskrivs i detalj i följande avsnitt i det här avsnittet:

[NetworkTrafficRules-element](#NetworkTrafficRules)

[OnlyAllowTrafficTo-element](#OnlyAllowTrafficTo)

[Mål element](#Destinations)

[RoleEndpoint-element](#RoleEndpoint)

[AllowAllTraffic-element](#AllowAllTraffic)

[WhenSource-element](#WhenSource)

[FromRole-element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules-element
`NetworkTrafficRules`Elementet anger vilka roller som kan kommunicera med vilken slut punkt på en annan roll. En tjänst kan innehålla en `NetworkTrafficRules` definition.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo-element
`OnlyAllowTrafficTo`Elementet beskriver en samling mål slut punkter och de roller som kan kommunicera med dem. Du kan ange flera `OnlyAllowTrafficTo` noder.

##  <a name="destinations-element"></a><a name="Destinations"></a> Mål element
`Destinations`Elementet beskriver en samling RoleEndpoints än vad som kan kommunicera med.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint-element
`RoleEndpoint`Elementet beskriver en slut punkt för en roll för att tillåta kommunikation med. Du kan ange flera `RoleEndpoint` element om det finns fler än en slut punkt på rollen.

| Attribut      | Typ     | Beskrivning |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Krävs. Namnet på slut punkten som tillåter trafik till.|
| `roleName`     | `string` | Krävs. Namnet på webb rollen som tillåter kommunikation till.|

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> AllowAllTraffic-element
`AllowAllTraffic`Elementet är en regel som tillåter att alla roller kommunicerar med de slut punkter som definierats i `Destinations` noden.

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource-element
`WhenSource`Elementet beskriver en samling roller än vad som kan kommunicera med de slut punkter som definierats i `Destinations` noden.

| Attribut | Typ     | Beskrivning |
| --------- | -------- | ----------- |
| `matches` | `string` | Krävs. Anger den regel som ska användas när kommunikation tillåts. Det enda giltiga värdet är för närvarande `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole-element
`FromRole`Elementet anger de roller som kan kommunicera med de slut punkter som definierats i `Destinations` noden. Du kan ange flera `FromRole` element om det finns mer än en roll som kan kommunicera med slut punkterna.

| Attribut  | Typ     | Beskrivning |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Krävs. Namnet på den roll som kommunikation ska tillåtas från.|

## <a name="see-also"></a>Se även
[Definitions schema för moln tjänst (utökad support)](schema-csdef-file.md).




