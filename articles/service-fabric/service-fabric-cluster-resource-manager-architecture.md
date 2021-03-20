---
title: Resource Manager-arkitektur
description: En översikt över och arkitektur information om Azure Service Fabric Cluster Resource Manager-tjänsten.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94685842"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Översikt över kluster Resource Manager-arkitektur
Service Fabric Cluster Resource Manager är en central tjänst som körs i klustret. Den hanterar det önskade läget för tjänsterna i klustret, särskilt vad gäller resursförbrukning och eventuella placerings regler. 

För att kunna hantera resurserna i klustret måste Service Fabric Cluster Resource Manager ha flera delar av information:

- Vilka tjänster som finns för närvarande
- Varje tjänsts aktuella (eller standard) resurs förbrukning 
- Återstående kluster kapacitet 
- Kapaciteten för noderna i klustret 
- Mängden resurser som förbrukas på varje nod

Resursförbrukning för en specifik tjänst kan ändras över tid och tjänster är vanligt vis mer än en typ av resurs. Det kan finnas både verkliga fysiska och fysiska resurser som mäts mellan olika tjänster. Tjänster kan spåra fysiska mått som minnes-och disk förbrukning. Oftare kan tjänster vara försiktiga om logiska mått, till exempel "WorkQueueDepth" eller "TotalRequests". Både logiska och fysiska mått kan användas i samma kluster. Mått kan delas mellan många tjänster eller vara specifika för en viss tjänst.

## <a name="other-considerations"></a>Ytterligare överväganden
Ägare och operatörer i klustret kan skilja sig från tjänst-och program författare, eller så är det minst samma personer som använder olika hattar. När du utvecklar ditt program vet du några saker om vad som krävs. Du har en uppskattning av de resurser som den kommer att använda och hur olika tjänster ska distribueras. Till exempel måste webb nivån köras på noder som exponeras för Internet, medan databas tjänsterna inte bör vara det. Som ett annat exempel är webb tjänsterna förmodligen begränsade av CPU och nätverk, medan data skikt tjänsterna är mer om minnes-och disk användning. Den person som hanterar en incident för Live-platsen för tjänsten i produktion, eller som hanterar en uppgradering till tjänsten, har dock ett annat jobb att göra och kräver olika verktyg. 

Både klustret och tjänsterna är dynamiska:

- Antalet noder i klustret kan växa och krympa
- Noder av olika storlekar och typer kan komma och gå
- Tjänster kan skapas, tas bort och ändras och de önskade resurs tilldelningarna och placerings reglerna ändras
- Uppgraderingar och andra hanterings åtgärder kan gå igenom klustret i programmet på infrastruktur nivåer
- Det kan hända att felen inträffar när som helst.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Kluster resurs hanterarens komponenter och data flöde
Kluster resurs hanteraren måste spåra kraven för varje tjänst och förbrukningen av resurser efter varje tjänst objekt i dessa tjänster. Kluster resurs hanteraren har två koncept komponenter: agenter som körs på varje nod och en feltolerant tjänst. Agenterna på varje nod spårar belastnings rapporter från tjänster, aggregerar dem och rapporterar dem med jämna mellanrum. Tjänsten Cluster Resource Manager sammanställer all information från de lokala agenterna och reagerar utifrån dess aktuella konfiguration.

Nu ska vi titta på följande diagram:

<center>

![Diagram som visar klustret Resource Manager-tjänsten aggregerar all information från de lokala agenterna och reagerar utifrån dess aktuella konfiguration.][Image1]
</center>

Under körningen finns det många ändringar som kan uppstå. Anta till exempel mängden resurser som vissa tjänster förbrukar förändringar, vissa tjänster inte fungerar och vissa noder ansluter till och lämnar klustret. Alla ändringar på en nod sammanställs och skickas regelbundet till kluster resurs hanterings tjänsten (1, 2) där de aggregeras igen, analyseras och lagras. Några sekunder som tjänsten tittar på ändringarna och avgör om några åtgärder krävs (3). Det kan till exempel hända att vissa tomma noder har lagts till i klustret. Det innebär att du kan flytta några tjänster till dessa noder. Kluster resurs hanteraren kan också märka att en viss nod är överbelastad eller att vissa tjänster har misslyckats eller tagits bort, vilket frigör resurser någon annan stans.

Nu ska vi titta på följande diagram och se vad som händer härnäst. Anta att kluster resurs hanteraren avgör att ändringar är nödvändiga. Den koordinerar med andra system tjänster (särskilt Redundanshanteraren) för att göra nödvändiga ändringar. Sedan skickas de nödvändiga kommandona till lämpliga noder (4). Anta till exempel att Resource Manager har märkt att Nod5 överbelastats och att du har valt att flytta tjänst B från Nod5 till nod4. I slutet av omkonfigurationen (5) ser klustret ut så här:

<center>

![Arkitektur för resurs utjämning][Image2]
</center>

## <a name="next-steps"></a>Nästa steg
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Läs mer om dem i den här artikeln om hur du [beskriver ett Service Fabric-kluster](./service-fabric-cluster-resource-manager-cluster-description.md)
- Kluster resurs hanterarens primära uppgifter balanserar om klustret och tillämpar placerings regler. Mer information om hur du konfigurerar dessa beteenden finns i [balansera ditt Service Fabric-kluster](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
