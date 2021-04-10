---
title: Skalbarhet för Azure Service Fabric nätappar
description: En av fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster, antingen manuellt eller med principer för automatisk skalning.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3f0e115e596925878bf9fdd43b7074cefdba47b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626866"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skala Service Fabric nätprogram

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

En av de största fördelarna med att distribuera program till Service Fabric nät är möjligheten att enkelt skala dina tjänster i eller ut. Detta bör användas för att hantera olika belastnings mängder på dina tjänster eller förbättra tillgängligheten. Du kan skala dina tjänster manuellt i eller ut eller konfigurera principer för automatisk skalning.

## <a name="manual-scaling-instances"></a>Manuella skalnings instanser

I distributionsmallen för programresursen har varje tjänst en *replicaCount*-egenskap som kan användas för att ange hur många gånger du vill att tjänsten distribueras. Ett program kan bestå av flera tjänster, och varje tjänst kan ha ett unikt *replicaCount*-värde, som distribueras och hanteras tillsammans. Om du vill skala antalet tjänstrepliker ändra du *replicaCount*-värdet för varje tjänst som du vill skala i distributionsmallen eller parameterfilen. Sedan uppgraderar du programmet.

Exempel på manuell skalning av tjänst instanser finns i [manuellt skala dina tjänster i eller ut](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Tjänst instanser för automatisk skalning
Automatisk skalning är en ytterligare funktion i Service Fabric för att dynamiskt skala antalet tjänst instanser (vågrät skalning). Automatisk skalning ger bra elastiskhet och möjliggör etablering eller borttagning av tjänst instanser baserat på processor-eller minnes användning.  Med automatisk skalning kan du köra rätt antal tjänst instanser för din arbets belastning och optimera för kostnad.

En princip för automatisk skalning definieras per tjänst i tjänst resurs filen. Varje skalnings princip består av två delar:

- En skalnings utlösare som beskriver när skalningen av tjänsten ska utföras. Det finns tre faktorer som avgör när tjänsten ska skalas. *Lägre tröskelvärde för inläsning* är ett värde som avgör när tjänsten ska skalas i. Om den genomsnittliga belastningen för alla instanser av partitionerna är lägre än det här värdet kommer tjänsten att skalas i. Det *övre tröskelvärdet för inläsning* är ett värde som anger när tjänsten ska skalas ut. Om den genomsnittliga belastningen för alla instanser av partitionen är högre än det här värdet kommer tjänsten att skalas ut. *Skalnings intervallet* avgör hur ofta (i sekunder) utlösaren ska kontrol leras. När utlösaren är markerad kommer mekanismen att tillämpas om skalning krävs. Om skalning inte behövs vidtas ingen åtgärd. I båda fallen kontrol leras inte utlösaren igen innan skalnings intervallet upphör att gälla.

- En skalnings funktion som beskriver hur skalningen ska utföras när den utlöses. Med *skalnings ökningen* bestäms hur många instanser som ska läggas till eller tas bort när mekanismen utlöses. *Maximalt antal instanser* definierar den övre gränsen för skalning. Om antalet instanser når den här gränsen kommer tjänsten inte att skalas ut oavsett belastningen. *Lägsta instans antal* definierar den nedre gränsen för skalning. Om antalet instanser av partitionen når den här gränsen kommer tjänsten inte att skalas i oberoende av belastningen.

Läs [tjänsterna för autoskalning](service-fabric-mesh-howto-auto-scale-services.md)för att lära dig hur du ställer in en princip för autoskalning för tjänsten.

## <a name="next-steps"></a>Nästa steg

Information om program modellen finns [Service Fabric resurser](service-fabric-mesh-service-fabric-resources.md)
