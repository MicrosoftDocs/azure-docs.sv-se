---
title: Service Fabric Cluster Resource Manager – program grupper
description: Översikt över program grupps funktionen i Service Fabric Cluster Resource Manager
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452157"
---
# <a name="introduction-to-application-groups"></a>Introduktion till program grupper
Service Fabricens kluster resurs hanterare hanterar vanligt vis kluster resurser genom att sprida belastningen (representeras via [mått](service-fabric-cluster-resource-manager-metrics.md)) jämnt i klustret. Service Fabric hanterar kapaciteten för noderna i klustret och klustret som helhet via [kapacitet](service-fabric-cluster-resource-manager-cluster-description.md). Mått och kapacitet fungerar utmärkt för många arbets belastningar, men mönster som gör stor användning av olika Service Fabric program instanser kan ibland medföra ytterligare krav. Till exempel kanske du vill:

- Reservera viss kapacitet på noderna i klustret för tjänsterna i en namngiven program instans
- Begränsa det totala antalet noder som tjänsterna i en namngiven program instans kör på (i stället för att sprida ut dem över hela klustret)
- Definiera kapaciteter på den namngivna program instansen för att begränsa antalet tjänster eller total resursförbrukning för de tjänster som ingår i den

För att uppfylla dessa krav stöder Service Fabric Cluster Resource Manager en funktion som kallas program grupper.

## <a name="limiting-the-maximum-number-of-nodes"></a>Begränsa maximalt antal noder
Det enklaste användnings fallet för program kapaciteten är när en program instans måste begränsas till ett visst maximalt antal noder. Detta konsoliderar alla tjänster inom den program instansen till ett angivet antal datorer. Konsolidering är användbart när du försöker förutsäga eller Cap fysisk resurs användning av tjänsterna inom den namngivna program instansen. 

Följande bild visar en program instans med och utan maximalt antal definierade noder:

<center>

![Program instans som definierar maximalt antal noder][Image1]
</center>

I det vänstra exemplet har programmet inget maximalt antal definierade noder och det har tre tjänster. Kluster resurs hanteraren har distribuerat alla repliker över sex tillgängliga noder för att uppnå bästa möjliga balans i klustret (standard beteendet). I det högra exemplet ser vi samma program som är begränsad till tre noder.

Den parameter som styr det här beteendet kallas MaximumNodes. Den här parametern kan anges när programmet skapas eller uppdateras för en program instans som redan har körts.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

I en uppsättning noder garanterar inte kluster resurs hanteraren vilka tjänst objekt som placeras tillsammans eller vilka noder som används.

## <a name="application-metrics-load-and-capacity"></a>Program mått, belastning och kapacitet
Med program grupper kan du också definiera mått som är kopplade till en specifik namngiven program instans och den program instansens kapacitet för dessa mått. Med program mått kan du spåra, reservera och begränsa resurs förbrukningen för tjänsterna i program instansen.

Det finns två värden som kan ställas in för varje applikations mått:

- **Total program kapacitet** – den här inställningen representerar programmets totala kapacitet för ett visst mått. Kluster resurs hanteraren tillåter inte att nya tjänster skapas i den här program instansen, vilket skulle leda till att den totala belastningen överskrider detta värde. Anta till exempel att program instansen hade en kapacitet på 10 och redan hade belastningen på fem. Det är inte tillåtet att skapa en tjänst med en total standard belastning på 10.
- **Maximal Node-kapacitet** – den här inställningen anger den maximala totala belastningen för programmet på en enda nod. Om belastningen går över den här kapaciteten flyttar kluster resurs hanteraren repliker till andra noder så att belastningen minskar.


PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reservera kapacitet
Ett annat vanligt användnings sätt för program grupper är att se till att resurserna i klustret är reserverade för en specifik program instans. Utrymmet reserveras alltid när program instansen skapas.

Reservering av utrymme i klustret för programmet sker omedelbart även när:
- program instansen skapas men har inga tjänster inom den
- antalet tjänster inom program instansen ändras varje gång 
- tjänsterna finns men förbrukar inte resurserna 

Reservering av resurser för en program instans kräver att du anger två ytterligare parametrar: *MinimumNodes* och *NodeReservationCapacity*

- **MinimumNodes** – definierar det minsta antalet noder som program instansen ska köras på.  
- **NodeReservationCapacity** – den här inställningen är per mått för programmet. Värdet är den mängd som det mått som har reserver ATS för programmet på alla noder där tjänsterna i programmet körs.

Att kombinera **MinimumNodes** och **NodeReservationCapacity** garanterar en minimal inläsnings reservation för programmet i klustret. Om det finns mindre återstående kapacitet i klustret än den totala reservationen som krävs, Miss lyckas det att skapa programmet. 

Nu ska vi titta på ett exempel på kapacitets reservation:

<center>

![Program instanser som definierar reserverad kapacitet][Image2]
</center>

I det vänstra exemplet har program kapaciteten ingen definierad program kapacitet. Kluster resurs hanteraren balanserar allt enligt de normala reglerna.

I exemplet till höger säger vi att application1 skapades med följande inställningar:

- MinimumNodes har angetts till två
- Ett program mått som definierats med
  - NodeReservationCapacity av 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserverar kapacitet på två noder för application1 och tillåter inte att tjänster från Application2 konsumerar den kapaciteten även om det inte finns någon belastning som används av tjänsterna i application1 vid tidpunkten. Den här reserverade program kapaciteten anses vara förbrukad och räknas mot den återstående kapaciteten på noden och i klustret.  Reservationen dras från den återstående kluster kapaciteten omedelbart, men den reserverade förbrukningen dras bara av från kapaciteten för en enskild nod när minst ett tjänst objekt placeras på den. Den senare reservationen ger flexibilitet och bättre resursutnyttjande eftersom resurser bara reserveras på noder vid behov.

## <a name="obtaining-the-application-load-information"></a>Hämta programmets inläsnings information
För varje program som har en angiven program kapacitet för ett eller flera mått kan du hämta information om den sammanställda belastningen som rapporteras av repliker av sina tjänster.

PowerShell

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

Frågan ApplicationLoad returnerar grundläggande information om program kapaciteten som har angetts för programmet. Den här informationen inkluderar lägsta antal noder och information om högsta antal noder och numret som programmet håller på att för närvarande. Den innehåller också information om varje belastnings mått för programmet, inklusive:

* Metric Name: namnet på måttet.
* Reservations kapacitet: kluster kapacitet som är reserverad i klustret för det här programmet.
* Program belastning: den totala belastningen på det här programmets underordnade repliker.
* Program kapacitet: högsta tillåtna värde för program inläsning.

## <a name="removing-application-capacity"></a>Tar bort program kapacitet
När program kapacitets parametrarna har angetts för ett program kan de tas bort med hjälp av API: er för uppdaterings program eller PowerShell-cmdletar. Exempel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Det här kommandot tar bort alla parametrar för program kapacitets hantering från program instansen. Detta inkluderar MinimumNodes, MaximumNodes och programmets mått, om det finns några. Kommandots effekter är omedelbart. När det här kommandot har slutförts använder kluster resurs hanteraren standard beteendet för att hantera program. Du kan ange program kapacitets parametrar igen via `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Begränsningar för program kapacitet
Det finns flera begränsningar för parametrarna för program kapacitet som måste respekteras. Om det uppstår verifierings fel sker inga ändringar.

- Alla heltals parametrar måste vara icke-negativa tal.
- MinimumNodes får aldrig vara större än MaximumNodes.
- Om kapaciteten för ett belastnings mått har definierats måste de följa dessa regler:
  - Nodens reservations kapacitet får inte vara större än den maximala nodens kapacitet. Du kan till exempel inte begränsa kapaciteten för måttet "CPU" på noden till två enheter och försöka reservera tre enheter på varje nod.
  - Om MaximumNodes anges får inte produkten av MaximumNodes och maximal nod kapacitet vara större än den totala program kapaciteten. Anta till exempel att den maximala nodens kapacitet för belastnings måttet "CPU" är inställt på åtta. Anta också att du anger maximalt 10 noder. I det här fallet måste den totala program kapaciteten vara större än 80 för det här belastnings måttet.

Begränsningarna tillämpas både när programmet skapas och uppdateras.

## <a name="how-not-to-use-application-capacity"></a>Så här använder du inte program kapacitet
- Försök inte att använda program grupps funktionerna för att begränsa programmet till en _viss_ delmängd noder. Med andra ord kan du ange att programmet ska köras på högst fem noder, men inte vilka specifika fem noder i klustret. Att begränsa ett program till specifika noder kan uppnås med hjälp av placerings begränsningar för tjänster.
- Försök inte att använda program kapaciteten för att säkerställa att två tjänster från samma program placeras på samma noder. Använd i stället [tillhörighets](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) -eller [placerings begränsningar](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster finns i [så här konfigurerar du tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Mer information om hur måtten fungerar i allmänhet finns i [Service Fabric belastnings mått](service-fabric-cluster-resource-manager-metrics.md)
- Kluster resurs hanteraren har många alternativ för att beskriva klustret. Läs mer om dem i den här artikeln om hur du [beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
