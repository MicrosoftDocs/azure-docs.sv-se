---
title: 'Testning: tjänst kommunikation'
description: Tjänst-till-tjänst-kommunikation är en viktig integrations plats för ett Service Fabric program. Den här artikeln beskriver design överväganden och testnings tekniker.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575932"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric testnings scenarier: tjänst kommunikation
Mikrotjänster och tjänsteorienterad arkitektur format yta naturligt i Azure Service Fabric. I den här typen av distribuerade arkitekturer består komponentbaserade mikrotjänstprogram vanligt vis av flera tjänster som måste kommunicera med varandra. I de enklaste fallen har du vanligt vis minst en tillstånds lös webb tjänst och en tillstånds känslig data lagrings tjänst som behöver kommunicera.

Tjänst-till-tjänst-kommunikation är en kritisk integrations punkt för ett program eftersom varje tjänst exponerar ett fjärr-API till andra tjänster. Att arbeta med en uppsättning API-gränser som involverar I/O kräver vanligt vis lite vård, med en stor mängd tester och validering.

Det finns flera saker att tänka på när dessa tjänst gränser är kabelanslutna i ett distribuerat system:

* *Transport protokoll*. Kommer du att använda HTTP för ökad interoperabilitet, eller ett anpassat binärt protokoll för maximalt data flöde?
* *Fel hantering*. Hur hanteras permanenta och tillfälliga fel? Vad händer när en tjänst flyttas till en annan nod?
* *Tids gränser och latens*. Hur hanteras varje tjänst skikts svars tid via stacken och till användaren i program med flera nivåer?

Oavsett om du använder någon av de inbyggda tjänst kommunikations komponenterna som tillhandahålls av Service Fabric eller skapar en egen, är det viktigt att testa interaktionerna mellan dina tjänster för att säkerställa återhämtning i ditt program.

## <a name="prepare-for-services-to-move"></a>Förbered för tjänster som ska flyttas
Tjänst instanser kan flyttas över tid. Detta gäller särskilt när de konfigureras med inläsnings mått för anpassad, anpassad, optimal resurs utjämning. Service Fabric flyttar tjänst instanserna för att maximera tillgängligheten även under uppgraderingar, redundans, skalbarhet och andra situationer som inträffar under ett distribuerat Systems livs längd.

När tjänster flyttas runt i klustret, bör dina klienter och andra tjänster vara för beredda för att hantera två scenarier när de pratar med en tjänst:

* Tjänst instansen eller diskpartitionen har flyttats sedan den senaste gången du talat. Detta är en normal del av livs cykeln för tjänsten och den bör förväntas ske under programmets livs längd.
* Tjänst instansen eller partitionen håller på att flyttas. Även om redundansväxlingen av en tjänst från en nod till en annan inträffar mycket snabbt i Service Fabric, kan det finnas en fördröjning i tillgänglighet om kommunikations komponenten för tjänsten är långsam att starta.

Att hantera de här scenarierna är vanligt för ett system som körs smidigt. Tänk på följande:

* Varje tjänst som kan anslutas till har en *adress* som den lyssnar på (till exempel http eller WebSockets). När en tjänst instans eller partition flyttas ändras dess adress slut punkt. (Den flyttas till en annan nod med en annan IP-adress.) Om du använder de inbyggda kommunikations komponenterna, kommer de att hantera tjänst adresser som du kan använda för att lösa dem.
* Det kan finnas en temporär ökning av tjänst fördröjningen eftersom tjänst instansen startar sin lyssnare igen. Detta beror på hur snabbt tjänsten öppnar lyssnaren när tjänst instansen har flyttats.
* Alla befintliga anslutningar måste stängas och öppnas igen när tjänsten har öppnats på en ny nod. En korrekt avstängning eller omstart av en nod gör att befintliga anslutningar kan stängas av på ett smidigt sätt.

### <a name="test-it-move-service-instances"></a>Testa det: flytta tjänst instanser
Genom att använda Service Fabric verktyg för test verktyg kan du skapa ett test scenario för att testa dessa situationer på olika sätt:

1. Flytta en tillstånds känslig tjänsts primära replik.
   
    Den primära repliken av en tillstånds känslig tjänst partition kan flyttas av valfritt antal orsaker. Använd detta för att rikta in den primära repliken av en speciell partition för att se hur dina tjänster reagerar på flytten på ett mycket kontrollerat sätt.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Stoppa en nod.
   
    När en nod stoppas flyttar Service Fabric alla tjänst instanser eller partitioner som fanns på noden till en av de andra tillgängliga noderna i klustret. Använd detta för att testa en situation där en nod förloras från klustret och alla tjänst instanser och repliker på noden måste flyttas.
   
    Du kan stoppa en nod med PowerShell-cmdleten **Stop-ServiceFabricNode** :
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Underhåll tjänstens tillgänglighet
Som plattform är Service Fabric utformad för att tillhandahålla hög tillgänglighet för dina tjänster. Men i extrema fall kan underliggande infrastruktur problem fortfarande orsaka otillgänglighet. Det är viktigt att testa även för dessa scenarier.

Tillstånds känsliga tjänster använder ett kvorum-baserat system för att replikera tillstånd för hög tillgänglighet. Det innebär att ett kvorum med repliker måste vara tillgängligt för att utföra Skriv åtgärder. I sällsynta fall, till exempel ett omfattande maskin varu haveri, är det inte säkert att ett kvorum med repliker är tillgängligt. I dessa fall kommer du inte att kunna utföra Skriv åtgärder, men du kommer fortfarande att kunna utföra Läs åtgärder.

### <a name="test-it-write-operation-unavailability"></a>Testa det: Skriv åtgärden är inte tillgänglig
Genom att använda test verktyg i Service Fabric kan du mata in ett fel som inducerar kvorum som ett test. Även om ett sådant scenario är sällsynt är det viktigt att klienter och tjänster som är beroende av en tillstånds känslig tjänst förbereds för att hantera situationer där de inte kan göra Skriv förfrågningar till den. Det är också viktigt att den tillstånds känsliga tjänsten själva är medveten om den här möjligheten och kan kommunicera på ett smidigt sätt till anropare.

Du kan inducera kvorum genom att använda PowerShell **Invoke-ServiceFabricPartitionQuorumLoss-** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

I det här exemplet anger vi `QuorumLossMode` för `QuorumReplicas` att indikera att vi vill inducera kvorum utan att ta bort alla repliker. På så sätt är Läs åtgärder fortfarande möjliga. Om du vill testa ett scenario där en hel partition inte är tillgänglig kan du ange den här växeln till `AllReplicas` .

## <a name="next-steps"></a>Nästa steg
[Läs mer om testnings åtgärder](service-fabric-testability-actions.md)

[Lär dig mer om testnings scenarier](service-fabric-testability-scenarios.md)

