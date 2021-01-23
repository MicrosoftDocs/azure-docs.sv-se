---
title: Definitions schema för Azure Cloud Services (klassisk) (. csdef-fil) | Microsoft Docs
description: En tjänst definitions fil (. csdef) definierar en tjänst modell för ett program som innehåller tillgängliga roller, slut punkter och konfigurations värden för tjänsten.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: de81b6ffb5b4dc944f3d538a116383d06145661b
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739809"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Definitions schema för Azure Cloud Services (klassisk) (. csdef-fil)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Tjänst definitions filen definierar tjänst modellen för ett program. Filen innehåller definitionerna för de roller som är tillgängliga för en moln tjänst, anger tjänstens slut punkter och upprättar konfigurations inställningar för tjänsten. Konfigurations inställnings värden anges i tjänst konfigurations filen, enligt beskrivningen i [konfigurations schema för moln tjänst (klassisk)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Som standard installeras den Azure-diagnostik konfigurations schema filen i `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogen. Ersätt `<version>` med den installerade versionen av [Azure SDK](https://www.windowsazure.com/develop/downloads/).

Standard tillägget för tjänst definitions filen är. csdef.

## <a name="basic-service-definition-schema"></a>Grundläggande tjänst definitions schema
Tjänst definitions filen måste innehålla ett- `ServiceDefinition` element. Tjänst definitionen måste innehålla minst ett roll `WebRole` element (eller `WorkerRole` ). Den kan innehålla upp till 25 roller som definierats i en enda definition och du kan blanda roll typer. Tjänst definitionen innehåller också det valfria `NetworkTrafficRules` element som begränsar vilka roller som kan kommunicera med angivna interna slut punkter. Tjänst definitionen innehåller också det valfria `LoadBalancerProbes` element som innehåller kunddefinierade hälso avsökningar av slut punkter.

Det grundläggande formatet för tjänst definitions filen är följande.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Schema definitioner
I följande avsnitt beskrivs schemat:

- [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-schema](schema-csdef-webrole.md)
- [WorkerRole-schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> Service definition-element
`ServiceDefinition`Elementet är det översta elementet i tjänst definitions filen.

I följande tabell beskrivs attributen för- `ServiceDefinition` elementet.

| Attribut               | Beskrivning |
| ----------------------- | ----------- |
| name                    |Krävs. Namnet på tjänsten. Namnet måste vara unikt inom tjänst kontot.|
| topologyChangeDiscovery | Valfritt. Anger typ av meddelande om ändring av topologin. Möjliga värden:<br /><br /> -   `Blast` – Skickar uppdateringen så snart som möjligt till alla roll instanser. Om du väljer alternativet ska rollen kunna hantera topologins uppdatering utan att starta om.<br />-   `UpgradeDomainWalk` – Skickar uppdateringen till varje roll instans på ett sekventiellt sätt efter att uppdateringen har godkänts av föregående instans.|
| Schema           | Valfritt. Anger versionen för tjänst definitions schema. Med schema versionen kan Visual Studio välja rätt SDK-verktyg som ska användas för schema validering om fler än en version av SDK: n installeras sida vid sida.|
| upgradeDomainCount      | Valfritt. Anger det antal uppgraderings domäner över vilka roller i den här tjänsten allokeras. Roll instanser tilldelas en uppgraderings domän när tjänsten distribueras. Mer information finns i [Uppdatera en moln tjänst roll eller distribution](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Hantera tillgängligheten för virtuella datorer](../virtual-machines/manage-availability.md) och [vad som är en moln tjänst modell](./cloud-services-model-and-package.md).<br /><br /> Du kan ange upp till 20 uppgraderings domäner. Om inget värde anges är standard antalet uppgraderings domäner 5.|