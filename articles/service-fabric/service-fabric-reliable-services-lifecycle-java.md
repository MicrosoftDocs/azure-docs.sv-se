---
title: Azure Service Fabric Reliable Services-livscykel
description: Lär dig mer om livs cykel händelser i ett Azure Service Fabric Reliable Services-program med Java för tillstånds känsliga och tillstånds lösa tjänster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324615"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services-livscykel
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services är en av de programmerings modeller som är tillgängliga i Azure Service Fabric. När du lär dig mer om livs cykeln för Reliable Services är det viktigast att förstå de grundläggande livs cykel händelserna. Den exakta ordningen av händelser är beroende av konfigurations information. 

I allmänhet innehåller Reliable Services livs cykeln följande händelser:

* Under start:
  * Tjänsterna är konstruerade.
  * -Tjänster har möjlighet att konstruera och returnera noll eller fler lyssnare.
  * Alla returnerade lyssnare öppnas för kommunikation med tjänsten.
  * Tjänstens `runAsync` metod anropas, så att tjänsten kan utföra tids krävande eller bakgrunds arbete.
* Under avstängning:
  * Den token som skickades till `runAsync` avbryts och lyssnarna stängs.
  * Själva tjänst objektet är destructed.

Ordningen på händelser i Reliable Services kan ändras något beroende på om den tillförlitliga tjänsten är tillstånds lös eller tillstånds känslig. 

För tillstånds känsliga tjänster måste du också åtgärda det primära växlings scenariot. Under den här sekvensen överförs primär rollen till en annan replik (eller kommer tillbaka) utan att tjänsten stängs av. 

Slutligen måste du tänka på fel-eller fel villkor.

## <a name="stateless-service-startup"></a>Tillstånds lös tjänst start
Livs cykeln för en tillstånds lös tjänst är ganska enkel. Här är händelse ordningen:

1. Tjänsten är konstruerad.
2. Dessa händelser inträffar parallellt:
    - `StatelessService.createServiceInstanceListeners()` anropas och returnerade lyssnare öppnas. `CommunicationListener.openAsync()` kallas för varje lyssnare.
    - Tjänstens `runAsync` metod ( `StatelessService.runAsync()` ) anropas.
3. Om den är tillgänglig anropas tjänstens egen `onOpenAsync` metod. Specifikt `StatelessService.onOpenAsync()` kallas. Detta är en ovanlig åsidosättning, men den är tillgänglig.

Det är viktigt att Observera att det inte finns någon ordning mellan anropet att skapa och öppna lyssnarna och anropet till `runAsync` . Lyssnarna kan öppna innan `runAsync` startas. På samma sätt `runAsync` kan anropas innan kommunikations lyssnarna är öppna, eller innan de har skapats ännu. Om en synkronisering krävs måste den utföras av Implementeraren. Här följer några vanliga lösningar:

* Ibland fungerar inte lyssnare förrän annan information har skapats eller annat arbete utförs. För tillstånds lösa tjänster kan det här arbetet vanligt vis göras i tjänstens konstruktor. Det kan göras under `createServiceInstanceListeners()` anropet eller som en del av själva lyssnaren.
* Ibland `runAsync` startar inte koden förrän lyssnarna är öppna. I detta fall krävs ytterligare samordning. En vanlig lösning är att lägga till en flagga i lyssnarna. Flaggan anger när lyssnarna har avslut ATS. `runAsync`Metoden kontrollerar detta innan det faktiska arbetet fortsätter.

## <a name="stateless-service-shutdown"></a>Tillstånds lös tjänst avstängning
När du stänger av en tillstånds lös tjänst följs samma mönster, men omvänt:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()` kallas för varje lyssnare.
    - Den avbrutna token som skickades till `runAsync()` har avbrutits. Kontrollerar om egenskapen för en token-token `isCancelled` returneras `true` och om den anropar token- `throwIfCancellationRequested` metoden `CancellationException` .
2. När `closeAsync()` du är klar med varje lyssnare och `runAsync()` även slutförs, `StatelessService.onCloseAsync()` anropas tjänstens metod, om den finns. Detta är inte en vanlig åsidosättning, men den kan användas för att på ett säkert sätt stänga resurser, stoppa bakgrunds bearbetning, slutföra sparande av externt tillstånd eller stänga befintliga anslutningar.
3. När `StatelessService.onCloseAsync()` har slutförts är serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Start av tillstånds känslig tjänst
Tillstånds känsliga tjänster har ett mönster som liknar tillstånds lösa tjänster, med några ändringar.  Här är händelse ordningen för att starta en tillstånds känslig tjänst:

1. Tjänsten är konstruerad.
2. `StatefulServiceBase.onOpenAsync()` kallas. Det här anropet åsidosätts vanligt vis inte i tjänsten.
3. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` anropas. 
      - Om tjänsten är en primär tjänst öppnas alla returnerade lyssnare. `CommunicationListener.openAsync()` kallas för varje lyssnare.
      - Om tjänsten är en sekundär tjänst öppnas endast lyssnare som marker ATS som `listenOnSecondary = true` Öppna. Att ha lyssnare som är öppna på sekundär servrar är mindre vanliga.
    - Om tjänsten för närvarande är en primär, `StatefulServiceBase.runAsync()` anropas tjänstens metod.
4. När alla replik lyssnare har `openAsync()` slutförts och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet åsidosätts vanligt vis inte i tjänsten.

På liknande sätt som tillstånds lösa tjänster, finns det ingen samordning mellan i vilken ordning lyssnarna skapas och öppnas och när `runAsync` anropas. Om du behöver samordning är lösningarna i stort sett desamma. Men det finns ett ytterligare Skift läge för tillstånds känslig tjänst. Anta att de anrop som kommer till kommunikations lyssnarna kräver information som finns i några [pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikations lyssnarna kan öppnas innan de pålitliga samlingarna är läsbara eller skrivbara, och innan `runAsync` Start, krävs ytterligare samordning. Den enklaste och vanligaste lösningen är att kommunikations lyssnarna returnerar en felkod. Klienten använder felkoden för att lära sig att försöka utföra begäran igen.

## <a name="stateful-service-shutdown"></a>Avstängning av tillstånds känslig tjänst
Som tillstånds lösa tjänster är livs cykel händelser vid avstängning samma som vid start, men omvänt. När en tillstånds känslig tjänst stängs av inträffar följande händelser:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()` kallas för varje lyssnare.
    - Den avbrutna token som skickades till `runAsync()` har avbrutits. Ett anrop till metoden returnera token för `isCancelled()` metoden returnerar `true` , och om den anropas genererar token- `throwIfCancellationRequested()` metoden en `OperationCanceledException` .
2. När `closeAsync()` har slutförts på varje lyssnare och `runAsync()` även slutförts, `StatefulServiceBase.onChangeRoleAsync()` anropas tjänsten. Det här anropet åsidosätts vanligt vis inte i tjänsten.

   > [!NOTE]  
   > Väntar på `runAsync` att slutföras är bara nödvändigt om repliken är en primär replik.

3. `StatefulServiceBase.onChangeRoleAsync()`Metoden anropas när metoden har slutförts `StatefulServiceBase.onCloseAsync()` . Det här anropet är en ovanlig åsidosättning, men det är tillgängligt.
3. När `StatefulServiceBase.onCloseAsync()` har slutförts är serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Primär växlingar för tillstånds känslig tjänst
När en tillstånds känslig tjänst körs, öppnas kommunikations lyssnare och `runAsync` metoden anropas bara för de primära replikerna av dessa tillstånds känsliga tjänster. Sekundära repliker skapas, men inga ytterligare anrop visas. När en tillstånds känslig tjänst körs, kan den replik som för närvarande är primär ändras. De livs cykel händelser som en tillstånds känslig replik kan se beror på om det är repliken som degraderas eller höjs under växlingen.

### <a name="for-the-demoted-primary"></a>För nedsänkt primär
Service Fabric behöver den primära repliken som har nedgraderats för att stoppa bearbetningen av meddelanden och förhindra bakgrunds arbete. Det här steget liknar när tjänsten stängs av. En skillnad är att tjänsten inte är destructed eller stängd, eftersom den förblir sekundär. Följande sker:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()` kallas för varje lyssnare.
    - Den avbrutna token som skickades till `runAsync()` har avbrutits. En kontroll av metoden returnera token för annullering `isCancelled()` `true` . Om den anropas genererar token- `throwIfCancellationRequested()` metoden en `OperationCanceledException` .
2. När `closeAsync()` har slutförts på varje lyssnare och `runAsync()` även slutförts, `StatefulServiceBase.onChangeRoleAsync()` anropas tjänsten. Det här anropet åsidosätts vanligt vis inte i tjänsten.

### <a name="for-the-promoted-secondary"></a>För den framhävda sekundära
På samma sätt behöver Service Fabric den sekundära repliken som befordras för att börja lyssna efter meddelanden i kabeln och för att starta bakgrunds aktiviteter som krävs för att slutföra. Den här processen liknar när tjänsten skapas. Skillnaden är att själva repliken redan finns. Följande sker:

1. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` anropas och returnerade lyssnare öppnas. `CommunicationListener.openAsync()` kallas för varje lyssnare.
    - Tjänstens `StatefulServiceBase.runAsync()` metod anropas.
2. När alla replik lyssnare har `openAsync()` slutförts och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet åsidosätts vanligt vis inte i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid tillstånds känslig tjänst avstängning och primär degradering
Service Fabric ändrar primärt för en tillstånds känslig tjänst av flera orsaker. De vanligaste orsakerna är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [program uppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder är det viktigt att tjänsten respekterar `cancellationToken` . Detta gäller även vid normal avstängning av tjänsten, till exempel om tjänsten har tagits bort.

Tjänster som inte kan hantera avbrott kan drabba flera problem. De här åtgärderna är långsamma eftersom Service Fabric väntar på att tjänsterna ska sluta fungera korrekt. Detta kan i slut ändan leda till misslyckade uppgraderingar som är timeout och återställning. Om du inte följer den token för annullering kan du även orsaka obalanserade kluster. Kluster blir obalanserade eftersom noder får frekvent åtkomst. Tjänsterna kan dock inte ombalanseras eftersom det tar för lång tid att flytta dem till en annan plats. 

Eftersom tjänsterna är tillstånds känsliga är det också troligt att tjänsterna använder [pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md). I Service Fabric, när en primär degraderas, är en av de första saker som händer att skriv åtkomst till det underliggande läget återkallas. Detta leder till en andra uppsättning problem som kan påverka livs cykeln för tjänsten. Samlingarna returnerar undantag baserat på tids inställningen och om repliken flyttas eller stängs av. Det är viktigt att hantera dessa undantag korrekt. 

Undantag som har utlösts av Service Fabric är antingen permanenta [( `FabricException` )](/java/api/system.fabric.exception) eller tillfälliga [( `FabricTransientException` )](/java/api/system.fabric.exception.fabrictransientexception). Permanenta undantag ska loggas och utlöstes. Tillfälliga undantag kan provas igen baserat på logik för omprövning.

En viktig del av att testa och verifiera Reliable Services hanterar undantagen som kommer från att använda `ReliableCollections` tillsammans med tjänstens livs cykel händelser. Vi rekommenderar att du alltid kör tjänsten under belastning. Du bör också utföra uppgraderingar och [kaos-testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. Dessa grundläggande steg hjälper till att säkerställa att din tjänst implementeras korrekt och att den hanterar livs cykel händelser på rätt sätt.

## <a name="notes-on-service-lifecycle"></a>Anteckningar om livs cykeln för tjänsten
* Både `runAsync()` metoden och `createServiceInstanceListeners/createServiceReplicaListeners` anropen är valfria. En tjänst kan ha en, båda eller ingen. Om tjänsten till exempel har allt arbete som svar på användar anrop behöver den inte implementeras `runAsync()` . Endast kommunikations lyssnarna och deras associerade kod krävs.  På samma sätt är det valfritt att skapa och returnera kommunikations lyssnare. Tjänsten kan ha endast ett bakgrunds arbete att göra, så den behöver bara implementera `runAsync()` .
* Den är giltig för att en tjänst ska kunna slutföras `runAsync()` och returnera från den. Detta betraktas inte som ett fel tillstånd. Den representerar bakgrunds arbetet i tjänst sluten. För tillstånds känsliga Reliable Services `runAsync()` anropas igen om tjänsten degraderas från primärt, och sedan uppgraderas tillbaka till primär.
* Om en tjänst avslutas från genom att orsaka ett `runAsync()` oväntat undantag är detta ett fel. Serviceobjektet stängs av och ett hälso fel rapporteras.
* Även om det inte finns någon tids gräns för att returnera från dessa metoder förlorar du direkt möjligheten att skriva. Därför kan du inte slutföra något verkligt arbete. Vi rekommenderar att du returnerar så snabbt som möjligt när du tar emot en begäran om uppsägning. Om tjänsten inte svarar på dessa API-anrop inom rimlig tid, kan Service Fabric tvinga tjänsten att avslutas. Detta sker vanligt vis bara vid program uppgraderingar eller när en tjänst tas bort. Denna timeout är som standard 15 minuter.
* Det går inte `onCloseAsync()` att anropa Sök vägs resultatet `onAbort()` . Det här anropet är en senaste chans för bästa möjliga möjlighet för tjänsten att rensa och lansera de resurser som de har begärt. Detta kallas vanligt vis när ett permanent fel upptäcks på noden, eller när Service Fabric inte kan hantera tjänst instansens livs cykel på ett tillförlitligt sätt på grund av interna fel.
* `OnChangeRoleAsync()` anropas när den tillstånds känsliga tjänst repliken ändrar roll, till exempel till primär eller sekundär. Primära repliker får skrivnings status (tillåts skapa och skriva till Reliable Collections). Sekundära repliker får Läs status (kan bara läsa från befintliga pålitliga samlingar). Det mesta arbetet i en tillstånds känslig tjänst utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddad verifiering, rapportgenerering, Data utvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
* [Reliable Services snabb start](service-fabric-reliable-services-quick-start-java.md)
