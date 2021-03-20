---
title: Repliker och instanser i Azure Service Fabric
description: Lär dig mer om repliker och instanser i Service Fabric, inklusive en översikt över deras livscykler och funktioner.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84710753"
---
# <a name="replicas-and-instances"></a>Repliker och instanser 
Den här artikeln ger en översikt över livs cykeln för repliker av tillstånds känsliga tjänster och instanser av tillstånds lösa tjänster.

## <a name="instances-of-stateless-services"></a>Instanser av tillstånds lösa tjänster
En instans av en tillstånds lös tjänst är en kopia av tjänst logiken som körs på en av noderna i klustret. En instans inom en partition identifieras unikt med **InstanceID**. Livs cykeln för en instans modelleras i följande diagram:

![Instans livs cykel](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Inbyggning (IB)
När kluster resurs hanteraren bestämmer en placering för instansen, anges det här livs cykel läget. Instansen har startats på noden. Program värden startas, instansen skapas och öppnas sedan. När starten är klar övergår instansen till klar läge. 

Om program värden eller-noden för den här instansen kraschar övergår den till läget tappad.

### <a name="ready-rd"></a>Redo (RD)
I klart läge är instansen igång och körs på noden. Om den här instansen är en tillförlitlig tjänst har **RunAsync** anropats. 

Om program värden eller-noden för den här instansen kraschar övergår den till läget tappad.

### <a name="closing-cl"></a>Stängning (CL)
I stängnings tillstånd håller Azure Service Fabric processen att stänga av instansen på den här noden. Den här avstängningen kan bero på många orsaker, till exempel en program uppgradering, belastnings utjämning eller att tjänsten tas bort. När avstängningen är klar övergår den till läget tappad.

### <a name="dropped-dd"></a>Borttagen (DD)
Instansen körs inte längre på noden i tappat tillstånd. Vid det här tillfället underhåller Service Fabric metadata om den här instansen, som slutligen också tas bort.

> [!NOTE]
> Det går att övergå från vilket tillstånd som helst till läget släppt med hjälp av alternativet **ForceRemove** på `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Repliker av tillstånds känsliga tjänster
En replik av en tillstånds känslig tjänst är en kopia av tjänst logiken som körs på en av noderna i klustret. Dessutom har repliken en kopia av tjänstens status. Två relaterade begrepp beskriver livs cykeln och beteendet för tillstånds känsliga repliker:
- Replik livs cykel
- Replik roll

I följande diskussion beskrivs sparade tillstånds känsliga tjänster. För temporära (eller InMemory) tillstånds känsliga tjänster är nedsänkt och tappade tillstånd likvärdiga.

![Replik livs cykel](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Inbyggning (IB)
En inbyggd replik är en replik som skapas eller förbereds för anslutning till replik uppsättningen. I väntan på replik rollen har IB olika semantik. 

Om program värden eller noden för en inaktiv replik kraschar, övergår den till ned-tillstånd.

   - **Primära inbyggda repliker**: primär inbyggnad är de första replikerna för en partition. Den här repliken inträffar vanligt vis när partitionen skapas. Primära inbyggda repliker uppstår också när alla repliker av en partition startas om eller tas bort.

   - **IdleSecondary inbyggda repliker**: dessa är antingen nya repliker som skapas av kluster resurs hanteraren eller befintliga repliker som gick ned och som måste läggas till i uppsättningen igen. Dessa repliker har dirigerats eller skapats av den primära servern innan de kan ansluta till replik uppsättningen som ActiveSecondary och delta i kvorumet med att skapa åtgärder.

   - **ActiveSecondary Inbygge-repliker**: det här läget observeras i vissa frågor. Det är en optimering där replik uppsättningen inte ändras, men en replik måste skapas. Själva repliken följer normal tillstånds datorns över gångar (enligt beskrivningen i avsnittet om replik roller).

### <a name="ready-rd"></a>Redo (RD)
En färdig replik är en replik som deltar i replikering och kvorum bekräftelse av åtgärder. Det färdiga läget gäller för primära och aktiva sekundära repliker.

Om program värden eller noden för en färdig replik kraschar, övergår den över till ned-tillstånd.

### <a name="closing-cl"></a>Stängning (CL)
En replik går in i stängnings tillstånd i följande scenarier:

- **Stänger av koden för repliken**: Service Fabric kan behöva stänga av den kod som körs för en replik. Den här avstängningen kan vara av många skäl. Det kan till exempel inträffa på grund av ett program, en infrastruktur eller en infrastruktur uppgradering, eller på grund av ett fel som rapporteras av repliken. När replik stängningen är klar, övergår repliken till ned-tillstånd. Det sparade tillstånd som är associerat med den här repliken som lagras på disken rensas inte.

- **Tar bort repliken från klustret**: Service Fabric kanske måste ta bort det sparade läget och stänga av den kod som körs för en replik. Den här avstängningen kan vara av många skäl, till exempel belastnings utjämning.

### <a name="dropped-dd"></a>Borttagen (DD)
Instansen körs inte längre på noden i tappat tillstånd. Det finns inte heller något tillstånd kvar på noden. Vid det här tillfället underhåller Service Fabric metadata om den här instansen, som slutligen också tas bort.

### <a name="down-d"></a>Ned (D)
I läget down körs inte replik koden, men det sparade läget för repliken finns på noden. En replik kan stängas av på grund av många orsaker, till exempel att noden är avstängd, en krasch i replik koden, en program uppgradering eller ett replik fel.

En av replikerna öppnas genom att Service Fabric vid behov, till exempel när uppgraderingen är klar på noden.

Replik rollen är inte relevant i läget ned.

### <a name="opening-op"></a>Öppnar (OP)
En säkerhets kopia går in i öppnings tillstånd när Service Fabric måste återställa repliken igen. Det här läget kan till exempel vara efter att kod uppgraderingen för programmet har slutförts på en nod. 

Om program värden eller noden för en inledande replik kraschar, övergår den till ned-tillstånd.

Replik rollen är inte relevant i öppnings tillstånd.

### <a name="standby-sb"></a>Vänte läge (SB)
En StandBy-replik är en replik av en beständiga tjänst som gick ned och sedan öppnades. Den här repliken kan användas av Service Fabric om den behöver lägga till en annan replik i replik uppsättningen (eftersom repliken redan har en del av status och bygg processen är snabbare). När StandByReplicaKeepDuration har gått ut ignoreras standby-repliken.

Om program värden eller noden för en standby-replik kraschar, övergår den till ned-tillstånd.

Replik rollen är inte relevant i vänte läge.

> [!NOTE]
> Alla repliker som inte är nere eller borttagna anses vara *upp*.
>

> [!NOTE]
> Det går att övergå från vilket tillstånd som helst till läget tappad med alternativet **ForceRemove** på `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Replik roll 
Replik rollen bestämmer dess funktion i replik uppsättningen:

- **Primär (P)**: det finns en primär replik i den replik uppsättning som ansvarar för att utföra Läs-och skriv åtgärder. 
- **ActiveSecondary (S)**: det här är repliker som tar emot tillstånds uppdateringar från den primära, tillämpar dem och sedan skickar tillbaka bekräftelser. Det finns flera aktiva sekundär zoner i replik uppsättningen. Antalet fel som tjänsten kan hantera, avgör hur många av dessa aktiva sekundära sekundära
- **IdleSecondary (I)**: dessa repliker skapas av den primära. De får tillstånd från den primära servern innan de kan befordras till aktiv sekundär. 
- **Ingen (N)**: dessa repliker har inget ansvar i replik uppsättningen.
- **Okänd (U)**: det här är den första rollen i en replik innan den får ett **ChangeRole** -API-anrop från Service Fabric.

Följande diagram illustrerar replik Rolls över gångar och några exempel scenarier där de kan inträffa:

![Replik roll](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: skapa en ny primär replik.
- U-> I: skapa en ny inaktiv replik.
- U-> N: borttagning av en växlings replik.
- I-> S: befordran av sekundär sekundär till aktiv sekundär så att dess bekräftelser bidrar mot kvorum.
- I-> P: befordran av inaktiv sekundär till primär. Detta kan inträffa under särskilda omkonfigurationer när den sekundära inaktiva är rätt kandidat att vara primär.
- I-> N: borttagning av den inaktiva sekundära repliken.
- S-> P: befordran av aktiv sekundär till primär. Detta kan bero på redundansväxling av den primära eller en primär förflyttning som initieras av kluster resurs hanteraren. Det kan till exempel vara som svar på en program uppgradering eller belastnings utjämning.
- S-> N: borttagning av den aktiva sekundära repliken.
- P-> S: degradering av den primära repliken. Detta kan bero på en primär förflyttning som initieras av kluster resurs hanteraren. Det kan till exempel vara som svar på en program uppgradering eller belastnings utjämning.
- P-> N: borttagning av den primära repliken.

> [!NOTE]
> Programmerings modeller på högre nivå, till exempel [Reliable Actors](service-fabric-reliable-actors-introduction.md) och [Reliable Services](service-fabric-reliable-services-introduction.md), döljer begreppet replik roller från utvecklaren. I aktörer är begreppet en roll onödig. I tjänster är det i stort sett förenklat för de flesta scenarier.
>

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artikel:

[Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)

