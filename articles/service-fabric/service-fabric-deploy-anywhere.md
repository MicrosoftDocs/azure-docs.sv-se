---
title: Översikt över Azure och fristående Service Fabric kluster
description: Du kan skapa Service Fabric kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric program i vilken miljö som helst där du har en uppsättning Windows Server-eller Linux-datorer som är sammankopplade lokalt, Microsoft Azure eller med någon annan moln leverantör.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576136"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Jämföra Azure och fristående Service Fabric kluster på Windows Server och Linux

Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, Service Fabric balanseringen av tjänste partitionens repliker och instanser över det ökade antalet noder. Övergripande program prestanda förbättras och konkurrens för åtkomst till minnes minskningar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric åter balanserar partitionens repliker och instanser över det minskade antalet noder för att bättre kunna använda maskin varan på varje nod.

Service Fabric gör det möjligt att skapa Service Fabric-kluster på alla virtuella datorer eller datorer som kör Windows Server eller Linux. Det innebär att du kan distribuera och köra Service Fabric program i valfri miljö där du har en uppsättning Windows Server-eller Linux-datorer som är anslutna till varandra, som är lokala, Microsoft Azure eller med valfri moln leverantör.

## <a name="benefits-of-clusters-on-azure"></a>Fördelar med kluster i Azure

I Azure erbjuder vi integration med andra Azure-funktioner och-tjänster, som gör det enklare och mer tillförlitligt att hantera klustret.

* **Azure Portal:** Azure Portal gör det enkelt att skapa och hantera kluster.
* **Azure Resource Manager:** Användning av Azure Resource Manager möjliggör enkel hantering av alla resurser som används av klustret som en enhet och fören klar kostnads spårning och fakturering.
* **Service Fabric kluster som en Azure-resurs** Ett Service Fabric kluster är en Azure-resurs, så du kan modellera det precis som du gör med andra resurser i Azure.
* **Integrering med Azure-infrastruktur** Service Fabric koordineras med den underliggande Azure-infrastrukturen för operativ system, nätverk och andra uppgraderingar för att förbättra tillgängligheten och tillförlitligheten för dina program.  
* **Diagnostik:** I Azure ger vi integrering med Azure Diagnostics och Azure Monitor loggar.
* Automatisk **skalning:** För kluster i Azure tillhandahåller vi inbyggda funktioner för automatisk skalning via virtuella dator skalnings uppsättningar. I lokala miljöer och andra moln miljöer måste du bygga en egen automatisk skalnings funktion eller skala manuellt med hjälp av de API: er som Service Fabric visar för skalnings kluster.

## <a name="benefits-of-standalone-clusters"></a>Fördelar med fristående kluster

* Du kan välja vilken moln leverantör som helst som värd för klustret.
* Service Fabric program, som har skrivits, kan köras i flera värd miljöer med minimala ändringar.
* Kunskap om att skapa Service Fabric program går över från en värd miljö till en annan.
* Drifts upplevelse för att köra och hantera Service Fabric-kluster överför från en miljö till en annan.
* Bred kunds räckvidd begränsas inte av värd miljö begränsningar.
* Det finns ett extra lager av tillförlitlighet och skydd mot omfattande drift störningar eftersom du kan flytta tjänsterna till en annan distributions miljö om ett Data Center eller en moln leverantör har en inaktive rad.

## <a name="next-steps"></a>Nästa steg

* Läs översikten över [Service Fabric kluster i Azure](service-fabric-azure-clusters-overview.md)
* Läs översikten över [Service Fabric fristående kluster](service-fabric-standalone-clusters-overview.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)