---
title: Ange mått och placerings inställningar
description: Lär dig hur du beskriver en Service Fabric tjänst genom att ange mått, placerings begränsningar och andra placerings principer.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75610105"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurera kluster resurs hanterarens inställningar för Service Fabric Services
Service Fabric Cluster Resource Manager ger detaljerad kontroll över reglerna som styr varje enskild namngiven tjänst. Varje namngiven tjänst kan ange regler för hur den ska allokeras i klustret. Varje namngiven tjänst kan också definiera de mått uppsättningar som den vill rapportera, inklusive hur viktiga de är för den tjänsten. Konfigurering av tjänster delas upp i tre olika uppgifter:

1. Konfigurera placerings begränsningar
2. Konfigurera mått
3. Konfigurera avancerade placerings principer och andra regler (mindre vanliga)

## <a name="placement-constraints"></a>Placerings begränsningar
Placerings begränsningar används för att styra vilka noder i klustret som en tjänst faktiskt kan köras på. Vanligt vis är en viss namngiven tjänst instans eller alla tjänster av en viss typ begränsad till körning på en viss typ av nod. Placerings begränsningar är utöknings bara. Du kan definiera en uppsättning egenskaper per nodtyp och sedan välja att använda begränsningar när du skapar tjänster. Du kan också ändra en tjänsts placerings begränsningar när den körs. På så sätt kan du svara på ändringar i klustret eller kraven för tjänsten. Egenskaperna för en specifik nod kan också uppdateras dynamiskt i klustret. Mer information om placerings begränsningar och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mått
Mått är den uppsättning resurser som en angiven tjänst behöver. En tjänsts mått konfiguration omfattar hur mycket av resursen varje tillstånds känslig replik eller tillstånds lös instans av tjänsten förbrukar sig som standard. Måtten innehåller också en vikt som anger hur viktiga saldon är för tjänsten, om kompromisser behövs.

## <a name="advanced-placement-rules"></a>Avancerade placerings regler
Det finns andra typer av placerings regler som är användbara i mindre vanliga scenarier. Några exempel är:
- Begränsningar som bidrar till geografiskt distribuerade kluster
- Vissa program arkitekturer

Andra placerings regler konfigureras via antingen korrelationer eller principer.

## <a name="next-steps"></a>Nästa steg
- Mått är hur Service Fabric Cluster Resource Manager hanterar förbrukning och kapacitet i klustret. Mer information om mått och hur du konfigurerar dem finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md)
- Tillhörighet är ett läge som du kan konfigurera för dina tjänster. Det är inte vanligt, men om du behöver det kan du lära dig mer om det [här](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Det finns många olika placerings regler som kan konfigureras på din tjänst för att hantera fler scenarier. Du kan ta reda på om de olika placerings principerna [här](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Läs mer om dem i den här artikeln om hur du [beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)
