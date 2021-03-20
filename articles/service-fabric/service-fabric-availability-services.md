---
title: Tillgänglighet för Service Fabric tjänster
description: Beskriver fel identifiering, redundans och återställning av en tjänst i ett Azure Service Fabric-program.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018910"
---
# <a name="availability-of-service-fabric-services"></a>Tillgänglighet för Service Fabric tjänster
Den här artikeln ger en översikt över hur Azure Service Fabric upprätthåller tillgängligheten för en tjänst.

## <a name="availability-of-service-fabric-stateless-services"></a>Tillgänglighet för Service Fabric tillstånds lösa tjänster
Service Fabric tjänster kan vara antingen tillstånds känsliga eller tillstånds lösa. En tillstånds lös tjänst är en program tjänst som inte har ett [lokalt tillstånd](service-fabric-concepts-state.md) som måste vara hög tillgängligt eller tillförlitligt.

Att skapa en tillstånds lös tjänst kräver att du definierar en `InstanceCount` . Instans antalet definierar antalet instanser av den tillstånds lösa tjänstens program logik som ska köras i klustret. Att öka antalet instanser är det rekommenderade sättet att skala ut en tillstånds lös tjänst.

När en instans av en tillstånds lös namngiven tjänst Miss lyckas skapas en ny instans på en berättigad nod i klustret. En tillstånds lös tjänst instans kan till exempel Miss Miss Missing på Nod1 och skapas på nytt på Nod5.

## <a name="availability-of-service-fabric-stateful-services"></a>Tillgänglighet för Service Fabric tillstånds känsliga tjänster
En tillstånds känslig tjänst har ett associerat tillstånd. I Service Fabric modelleras en tillstånds känslig tjänst som en uppsättning repliker. Varje replik är en aktiv instans av koden för tjänsten. Repliken har också en kopia av statusen för den tjänsten. Läs-och skriv åtgärder utförs på en replik, som kallas *primär*. Ändringar av tillstånd från Skriv åtgärder *replikeras* till de andra replikerna i replik uppsättningen, som kallas *aktiva sekundära* och tillämpas. 

Det kan bara finnas en primär replik, men det kan finnas flera aktiva sekundära repliker. Antalet aktiva sekundära repliker kan konfigureras och ett större antal repliker kan tolerera ett större antal samtidiga program-och maskin varu problem.

Om den primära repliken upphör att fungera, kan Service Fabric göra en av de aktiva sekundära replikerna den nya primära repliken. Den här aktiva sekundära repliken har redan den uppdaterade versionen av tillstånd, via *replikering* och kan fortsätta att bearbeta ytterligare Läs-/skriv åtgärder. Den här processen kallas *omkonfiguration* och beskrivs ytterligare i artikeln [omkonfiguration](service-fabric-concepts-reconfiguration.md) .

Konceptet för en replik som är antingen en primär eller aktiv sekundär, kallas för *replik rollen*. Dessa repliker beskrivs ytterligare i artikeln [repliker och instanser](service-fabric-concepts-replica-lifecycle.md) . 

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Skala Service Fabric tjänster](service-fabric-concepts-scalability.md)
- [Partitionera Service Fabric tjänster](service-fabric-concepts-partitioning.md)
- [Definiera och hantera tillstånd](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

