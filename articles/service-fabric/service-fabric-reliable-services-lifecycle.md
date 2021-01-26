---
title: Översikt över Reliable Servicess livs cykel
description: Lär dig mer om livs cykel händelser i ett Azure Service Fabric Reliable Services-program för tillstånds känsliga och tillstånds lösa tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784604"
---
# <a name="reliable-services-lifecycle-overview"></a>Översikt över Reliable Services livs cykel
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

När du tänker på livs cykeln för Azure Service Fabric Reliable Services är grunderna i livs cykeln de viktigaste. I allmänhet innehåller livs cykeln följande:

- Under start:
  - Tjänsterna är konstruerade.
  - Tjänsterna har möjlighet att konstruera och returnera noll eller fler lyssnare.
  - Alla returnerade lyssnare öppnas, vilket möjliggör kommunikation med tjänsten.
  - Tjänstens **RunAsync** -metod anropas, så att tjänsten kan utföra tids krävande uppgifter eller bakgrunds arbete.
- Under avstängning:
  - Token för annullering som skickades till **RunAsync** har avbrutits och lyssnarna är stängda.
  - När lyssnarna stängs, är själva tjänst objektet destructed.

Det finns information kring den exakta ordningen för dessa händelser. Händelse ordningen kan ändras något beroende på om den tillförlitliga tjänsten är tillstånds lös eller tillstånds känslig. För tillstånds känsliga tjänster måste du dessutom hantera det primära växlings scenariot. Under den här sekvensen överförs primär rollen till en annan replik (eller kommer tillbaka) utan att tjänsten stängs av. Slutligen måste vi tänka på fel-eller fel villkor.

## <a name="stateless-service-startup"></a>Tillstånds lös tjänst start
Livs cykeln för en tillstånds lös tjänst är enkel. Här är händelse ordningen:

1. Tjänsten är konstruerad.
2. Sedan sker parallellt av två saker:
    - `StatelessService.CreateServiceInstanceListeners()` anropas och returnerade lyssnare öppnas. `ICommunicationListener.OpenAsync()` kallas för varje lyssnare.
    - Tjänstens `StatelessService.RunAsync()` metod anropas.
3. Om det finns `StatelessService.OnOpenAsync()` anropas tjänstens metod. Det här anropet är en ovanlig åsidosättning, men det är tillgängligt. Initierings aktiviteter för utökade tjänster kan startas för tillfället.

Tänk på att det inte finns någon ordning mellan anropen för att skapa och öppna lyssnare och **RunAsync**. Lyssnarna kan öppnas innan **RunAsync** startas. På samma sätt kan du anropa **RunAsync** innan kommunikations lyssnarna är öppna eller till och med konstrueras. Om en synkronisering krävs lämnas den som en övning till Implementeraren. Här följer några vanliga lösningar:

  - Ibland fungerar inte lyssnare förrän en annan information skapas eller fungerar. För tillstånds lösa tjänster kan det arbetet vanligt vis göras på andra platser, till exempel följande: 
    - I tjänstens konstruktor.
    - Under `CreateServiceInstanceListeners()` anropet.
    - Som en del av själva lyssnaren.
  - Ibland startar inte koden i **RunAsync** förrän lyssnarna är öppna. I detta fall krävs ytterligare samordning. En vanlig lösning är att det finns en flagga i de lyssnare som visar när de är klara. Den här flaggan kontrol leras sedan i **RunAsync** innan du fortsätter med det faktiska arbetet.

## <a name="stateless-service-shutdown"></a>Tillstånds lös tjänst avstängning
För att stänga av en tillstånds lös tjänst följs samma mönster, precis i omvänd:

1. Parallellt:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()` kallas för varje lyssnare.
    - Annullerings-token som skickades till `RunAsync()` har avbrutits. En kontroll av egenskapen för den inställda token `IsCancellationRequested` returnerar true och om den anropas genererar token- `ThrowIfCancellationRequested` metoden en `OperationCanceledException` .
2. När `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också har slutförts, `StatelessService.OnCloseAsync()` anropas tjänstens metod, om sådan finns.  OnCloseAsync anropas när den tillstånds lösa tjänst instansen ska stängas av på ett smidigt sätt. Detta kan inträffa när tjänstens kod uppgraderas, om tjänst instansen flyttas på grund av belastnings utjämning eller om ett tillfälligt fel upptäcks. Det är ovanligt att åsidosätta `StatelessService.OnCloseAsync()` , men det kan användas för att på ett säkert sätt stänga resurser, stoppa bakgrunds bearbetning, slutföra sparande av externt tillstånd eller stänga befintliga anslutningar.
3. När `StatelessService.OnCloseAsync()` har slutförts är serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Start av tillstånds känslig tjänst
Tillstånds känsliga tjänster har liknande mönster för tillstånds lösa tjänster, med några ändringar. För att starta en tillstånds känslig tjänst är händelse ordningen följande:

1. Tjänsten är konstruerad.
2. `StatefulServiceBase.OnOpenAsync()` kallas. Det här anropet åsidosätts vanligt vis inte i tjänsten.
3. Följande saker sker parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` anropas. 
      - Om tjänsten är en primär tjänst öppnas alla returnerade lyssnare. `ICommunicationListener.OpenAsync()` kallas för varje lyssnare.
      - Om tjänsten är en sekundär tjänst öppnas bara de lyssnare som marker ATS som `ListenOnSecondary = true` Öppna. Att ha lyssnare som är öppna på sekundär servrar är mindre vanliga.
    - Om tjänsten för närvarande är en primär, `StatefulServiceBase.RunAsync()` anropas tjänstens metod.
4. När alla replik lyssnare har `OpenAsync()` slutförts och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet åsidosätts vanligt vis inte i tjänsten.

Precis som för tillstånds lösa tjänster finns det ingen samordning mellan i vilken ordning lyssnarna skapas och öppnas och när **RunAsync** anropas. Om du behöver samordning är lösningarna i stort sett desamma. Det finns ett ytterligare Skift läge för tillstånds känslig tjänst. Anta att de anrop som kommer till kommunikations lyssnarna kräver information som finns i några [pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Eftersom kommunikations lyssnarna kan öppnas innan de pålitliga samlingarna är läsbara eller skrivbara, och innan **RunAsync** kunde starta, krävs ytterligare samordning. Den enklaste och vanligaste lösningen är att kommunikations lyssnarna returnerar en felkod som klienten använder för att försöka utföra begäran igen.

## <a name="stateful-service-shutdown"></a>Avstängning av tillstånds känslig tjänst
Som tillstånds lösa tjänster är livs cykel händelser vid avstängning samma som vid start, men omvänt. När en tillstånds känslig tjänst stängs av inträffar följande händelser:

1. Parallellt:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()` kallas för varje lyssnare.
    - Annullerings-token som skickades till `RunAsync()` har avbrutits. En kontroll av egenskapen för den inställda token `IsCancellationRequested` returnerar true och om den anropas genererar token- `ThrowIfCancellationRequested` metoden en `OperationCanceledException` .
2. När `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` även slutförts, `StatefulServiceBase.OnChangeRoleAsync()` anropas tjänsten. Det här anropet åsidosätts vanligt vis inte i tjänsten.

   > [!NOTE]  
   > Behovet av att vänta tills **RunAsync** är slut krävs bara om repliken är en primär replik.

3. `StatefulServiceBase.OnChangeRoleAsync()`Metoden anropas när metoden har slutförts `StatefulServiceBase.OnCloseAsync()` . Det här anropet är en ovanlig åsidosättning, men det är tillgängligt.
3. När `StatefulServiceBase.OnCloseAsync()` har slutförts är serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Primär växlingar för tillstånds känslig tjänst
Medan en tillstånds känslig tjänst körs, har bara de primära replikerna av den tillstånds känsliga tjänsten sina kommunikations lyssnare öppna och deras **RunAsync** -metod anropas. Sekundära repliker skapas, men inga ytterligare anrop visas. Även om en tillstånds känslig tjänst körs, kan repliken som för närvarande är den primära ändras på grund av fel eller optimering av kluster utjämning. Vad betyder detta i förhållande till de livs cykel händelser som en replik kan se? Det beteende som den tillstånds känsliga repliken ser beror på om det är den replik som degraderas eller höjs under växlingen.

### <a name="for-the-primary-thats-demoted"></a>För den primära som är nedgraderad
För den primära replik som har degrader ATS behöver Service Fabric den här repliken för att sluta bearbeta meddelanden och avsluta alla bakgrunds arbeten som den gör. Därför ser det här steget ut när tjänsten stängs av. En skillnad är att tjänsten inte är destructed eller stängd eftersom den förblir sekundär. Följande API: er kallas:

1. Parallellt:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()` kallas för varje lyssnare.
    - Annullerings-token som skickades till `RunAsync()` har avbrutits. En kontroll av egenskapen för den inställda token `IsCancellationRequested` returnerar true och om den anropas genererar token- `ThrowIfCancellationRequested` metoden en `OperationCanceledException` .
2. När `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` även slutförts, `StatefulServiceBase.OnChangeRoleAsync()` anropas tjänsten. Det här anropet åsidosätts vanligt vis inte i tjänsten.

### <a name="for-the-secondary-thats-promoted"></a>För den sekundära som uppgraderas
På samma sätt behöver Service Fabric den sekundära repliken som befordras för att börja lyssna efter meddelanden i kabeln och starta eventuella bakgrunds aktiviteter som den måste utföra. Det innebär att den här processen ser ut som när tjänsten skapades, förutom att själva repliken redan finns. Följande API: er kallas:

1. Parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` anropas och returnerade lyssnare öppnas. `ICommunicationListener.OpenAsync()` kallas för varje lyssnare.
    - Tjänstens `StatefulServiceBase.RunAsync()` metod anropas.
2. När alla replik lyssnare har `OpenAsync()` slutförts och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet åsidosätts vanligt vis inte i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid tillstånds känslig tjänst avstängning och primär degradering
Service Fabric ändrar primärt för en tillstånds känslig tjänst av olika orsaker. Det vanligaste är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [program uppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder (och vid normal avstängning av tjänsten, t. ex. När du skulle ta bort tjänsten), är det viktigt att tjänsten respekterar `CancellationToken` . 

Tjänster som inte kan hantera avbrott kan drabba flera problem. De här åtgärderna är långsamma eftersom Service Fabric väntar på att tjänsterna ska sluta fungera korrekt. Detta kan i slut ändan leda till misslyckade uppgraderingar och återställa. Om du inte följer den inställda token kan du också orsaka obalanserade kluster. Kluster blir obalanserade eftersom noderna får frekventa data, men tjänsterna kan inte ombalanseras eftersom det tar för lång tid att flytta dem till en annan plats. 

Eftersom tjänsterna är tillstånds känsliga är det också troligt att de använder de [pålitliga samlingarna](service-fabric-reliable-services-reliable-collections.md). I Service Fabric, när en primär degraderas, är en av de första saker som händer att skriv åtkomst till det underliggande läget återkallas. Detta leder till en andra uppsättning problem som kan påverka livs cykeln för tjänsten. Samlingarna returnerar undantag baserat på tids inställningen och om repliken flyttas eller stängs av. Dessa undantag bör hanteras korrekt. Undantag som har utlösts av Service Fabric hamnar i permanenta [( `FabricException` )](/dotnet/api/system.fabric.fabricexception) och tillfälliga [( `FabricTransientException` )](/dotnet/api/system.fabric.fabrictransientexception) kategorier. Permanenta undantag ska loggas och genereras medan tillfälliga undantag kan göras om baserat på en viss omprövnings logik.

Hantering av undantag som kommer från att användas `ReliableCollections` tillsammans med tjänstens livs cykel händelser är en viktig del av testning och validering av en tillförlitlig tjänst. Vi rekommenderar att du alltid kör din tjänst under belastningen samtidigt som du utför uppgraderingar och [kaos-testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. Dessa grundläggande steg hjälper till att säkerställa att tjänsten är korrekt implementerad och hanterar livs cykel händelser på rätt sätt.


## <a name="notes-on-the-service-lifecycle"></a>Anteckningar om tjänste livs cykeln
  - Både `RunAsync()` metoden och `CreateServiceReplicaListeners/CreateServiceInstanceListeners` anropen är valfria. En tjänst kan ha en av dem, båda eller inga. Om tjänsten till exempel har allt arbete som svar på användar anrop behöver den inte implementeras `RunAsync()` . Endast kommunikations lyssnarna och deras associerade kod krävs. På samma sätt är det valfritt att skapa och returnera kommunikations lyssnare, eftersom tjänsten bara kan hantera bakgrunds arbete och behöver därför bara implementera `RunAsync()` .
  - Den är giltig för att en tjänst ska kunna slutföras `RunAsync()` och returnera från den. Slutför är inte ett fel tillstånd. Slutför `RunAsync()` anger att tjänstens bakgrunds arbete har slutförts. För tillstånds känsliga Reliable-tjänster `RunAsync()` anropas du igen om repliken sänks från primär till sekundär och sedan höjs tillbaka till primär.
  - Om en tjänst avslutas från genom att orsaka ett `RunAsync()` oväntat undantag utgör detta ett fel. Serviceobjektet stängs av och ett hälso fel rapporteras.
  - Även om det inte finns någon tids gräns för att returnera från dessa metoder förlorar du omedelbart möjligheten att skriva till pålitliga samlingar och kan därför inte slutföra något verkligt arbete. Vi rekommenderar att du returnerar så snabbt som möjligt när du tar emot en begäran om uppsägning. Om tjänsten inte svarar på dessa API-anrop inom rimlig tid, kan Service Fabric tvinga fram att avsluta tjänsten. Detta sker vanligt vis under program uppgraderingar eller när en tjänst tas bort. Denna timeout är som standard 15 minuter.
  - Fel i `OnCloseAsync()` Sök vägs resultatet `OnAbort()` anropas, vilket är en senaste chans för bästa möjliga affärs möjlighet för tjänsten att rensa och frigöra de resurser som de har begärt. Detta kallas vanligt vis när ett permanent fel upptäcks på noden, eller när Service Fabric inte kan hantera tjänst instansens livs cykel på ett tillförlitligt sätt på grund av interna fel.
  - `OnChangeRoleAsync()` anropas när den tillstånds känsliga tjänst repliken ändrar roll, till exempel till primär eller sekundär. Primära repliker får skrivnings status (tillåts skapa och skriva till Reliable Collections). Sekundära repliker får Läs status (kan bara läsa från befintliga pålitliga samlingar). Det mesta arbetet i en tillstånds känslig tjänst utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddad verifiering, rapportgenerering, Data utvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
- [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
