---
title: Azure Cloud Services (utökat stöd) NetworkConfiguration Schema | Microsoft Docs
description: Information som rör schemat för nätverkskonfiguration för Cloud Services (utökat stöd)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719033"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services konfigurationsschema (utökat stöd)

Elementet `NetworkConfiguration` i tjänstkonfigurationsfilen anger Virtual Network DNS-värden. De här inställningarna är valfria för Cloud Services.

Du kan använda följande resurs för att lära dig mer om virtuella nätverk och tillhörande scheman:

- [Konfigurationsschema för Molntjänst (utökat stöd).](schema-cscfg-file.md)
- [Cloud Service (utökat stöd) DefinitionSschema](schema-csdef-file.md).
- [Skapa en Virtual Network](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>NetworkConfiguration-element
I följande exempel visas `NetworkConfiguration` elementet och dess underordnade element.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

I följande tabell beskrivs de underordnade elementen i `NetworkConfiguration` elementet .

| Element       | Beskrivning |
| ------------- | ----------- |
| AccessControl | Valfritt. Anger regler för åtkomst till slutpunkter i en molntjänst. Namnet på åtkomstkontrollen definieras av en sträng för `name` attributet. Elementet `AccessControl` innehåller ett eller flera `Rule` element. Mer än ett `AccessControl` element kan definieras.|
| Regel | Valfritt. Anger vilken åtgärd som ska vidtas för ett angivet undernätsintervall med IP-adresser. Regelns ordning definieras av ett strängvärde för `order` attributet . Ju lägre regelnummer, desto högre prioritet. Regler kan till exempel anges med ordernumren 100, 200 och 300. Regeln med ordernumret 100 har företräde framför regeln med ordningen 200.<br /><br /> Åtgärden för regeln definieras av en sträng för `action` attributet . Möjliga värden:<br /><br /> -   `permit` – Anger att endast paket från det angivna undernätsintervallet kan kommunicera med slutpunkten.<br />-   `deny` – Anger att åtkomst nekas till slutpunkterna i det angivna undernätsintervallet.<br /><br /> Undernätsintervallet för IP-adresser som påverkas av regeln definieras av en sträng för `remoteSubnet` attributet . Beskrivningen för regeln definieras av en sträng för `description` attributet .|
| EndpointAcl | Valfritt. Anger tilldelningen av regler för åtkomstkontroll till en slutpunkt. Namnet på rollen som innehåller slutpunkten definieras av en sträng för `role` attributet. Namnet på slutpunkten definieras av en sträng för `endpoint` attributet . Namnet på den uppsättning regler `AccessControl` som ska tillämpas på slutpunkten definieras i en sträng för `accessControl` attributet. Fler än ett `EndpointAcl` element kan definieras.|
| DnsServer | Valfritt. Anger inställningarna för en DNS-server. Du kan ange inställningar för DNS-servrar utan Virtual Network. Namnet på DNS-servern definieras av en sträng för `name` attributet . IP-adressen för DNS-servern definieras av en sträng för `IPAddress` attributet . IP-adressen måste vara en giltig IPv4-adress.|
| VirtualNetworkSite | Valfritt. Anger namnet på den Virtual Network där du vill distribuera din molntjänst. Den här inställningen skapar inte en Virtual Network plats. Den refererar till en plats som tidigare har definierats i nätverksfilen för Virtual Network. En molntjänst kan bara vara medlem i en Virtual Network. Om du inte anger den här inställningen distribueras inte molntjänsten till en Virtual Network. Namnet på den Virtual Network definieras av en sträng för `name` attributet .|
| InstanceAddress | Valfritt. Anger associationen mellan en roll och ett undernät eller en uppsättning undernät i Virtual Network. När du associerar ett rollnamn med en instansadress kan du ange de undernät som du vill att den här rollen ska associeras till. `InstanceAddress`innehåller elementet Subnets. Namnet på den roll som är associerad med undernätet eller undernäten definieras av en sträng för `roleName` attributet .|
| Undernät | Valfritt. Anger det undernät som motsvarar undernätets namn i nätverkskonfigurationsfilen. Namnet på undernätet definieras av en sträng för `name` attributet .|
| ReservedIP | Valfritt. Anger den reserverade IP-adress som ska associeras med distributionen. Allokeringsmetoden för en reserverad IP-adress måste anges som `Static` för mall- och powershell-distributioner. Varje distribution i en molntjänst kan bara associeras med en reserverad IP-adress. Namnet på den reserverade IP-adressen definieras av en sträng för `name` attributet .|

## <a name="see-also"></a>Se även
[Konfigurationsschema för Molntjänst (utökat stöd).](schema-cscfg-file.md)
