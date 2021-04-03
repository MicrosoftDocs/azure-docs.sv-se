---
title: Hantera Azure Service Fabric app-belastning med hjälp av mått
description: Lär dig hur du konfigurerar och använder mått i Service Fabric för att hantera användning av tjänst resurser.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173004"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Hantera resursförbrukning och belastning i Service Fabric med mått
*Mått* är de resurser som dina tjänster bryr sig om och som tillhandahålls av noderna i klustret. Ett mått är vad du vill hantera för att förbättra eller övervaka prestanda för dina tjänster. Du kan till exempel titta på minnes förbrukning för att veta om tjänsten är överbelastad. En annan användning är att ta reda på om tjänsten kan flyttas någon annan stans där minnet är mindre begränsat för att få bättre prestanda.

Sådant som minne, disk och processor användning är exempel på mått. Dessa mått är fysiska mått, resurser som motsvarar fysiska resurser på den nod som måste hanteras. Mått kan också vara (och ofta) logiska mått. Logiska mått är saker som "MyWorkQueueDepth" eller "MessagesToProcess" eller "TotalRecords". Logiska mått är programdefinierade och indirekt motsvarar en viss fysisk resurs användning. Logiska mått är vanliga eftersom det kan vara svårt att mäta och rapportera konsumtion av fysiska resurser per tjänst. Att mäta och rapportera dina egna fysiska mått är också anledningen till att Service Fabric tillhandahåller vissa standard mått.

## <a name="default-metrics"></a>Standard mått
Anta att du vill komma igång med att skriva och distribuera tjänsten. I det här läget vet du inte vilka fysiska eller logiska resurser som den förbrukar. Det är bra! Service Fabric Cluster Resource Manager använder vissa standard mått när inga andra mått har angetts. De är:

  - PrimaryCount-antal primära repliker på noden 
  - ReplicaCount-antal av den totala tillstånds känsliga replikerna på noden
  - Count-Count för alla tjänst objekt (tillstånds lösa och tillstånds känsliga) på noden

| Metric | Tillstånds lös instans inläsning | Tillstånds känslig sekundär belastning | Tillstånds känslig primär belastning | Vikt |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Högt |
| ReplicaCount |0 |1 |1 |Medel |
| Antal |1 |1 |1 |Låg |


För grundläggande arbets belastningar tillhandahåller standard måtten en vettigt-distribution av arbetet i klustret. I följande exempel ska vi se vad som händer när vi skapar två tjänster och förlitar sig på standard måtten för balansering. Den första tjänsten är en tillstånds känslig tjänst med tre partitioner och en mål replik uppsättnings storlek på tre. Den andra tjänsten är en tillstånds lös tjänst med en partition och ett instans antal på tre.

Det här får vi:

<center>

![Klustrad layout med standard mått][Image1]
</center>

Några saker att tänka på:
  - Primära repliker för tillstånds känsliga tjänster distribueras över flera noder
  - Repliker av samma partition finns på olika noder
  - Det totala antalet presidentval och sekundär zoner distribueras i klustret
  - Det totala antalet tjänst objekt är jämnt allokerat på varje nod

Bra!

Standard måtten fungerar bra som en start. Standard måtten kommer dock bara att innehålla dig hittills. Till exempel: Vad är sannolikheten för att partitionerings schemat som du har valt resulterar i perfekt jämn användning av alla partitioner? Vad är chansen att inläsningen för en specifik tjänst är konstant över tid, eller till och med bara samma för flera partitioner just nu?

Du kan bara köra med standard måtten. Det innebär dock vanligt vis att kluster användningen är lägre och mer ojämn än du vill. Detta beror på att standard måtten inte är anpassningsbara och förutsätter att allt är likvärdigt. Till exempel är en primär som är upptagen och en som inte båda bidrar till "1" till PrimaryCount-måttet. I värsta fall kan endast använda standard måtten leda till överschemade noder som resulterar i prestanda problem. Om du är intresse rad av att få ut mesta möjliga av klustret och undvika prestanda problem måste du använda anpassade mått och rapporter för dynamisk inläsning.

## <a name="custom-metrics"></a>Anpassade mått
Måtten konfigureras på en per-namngiven-service-instans-basis när du skapar tjänsten.

Alla mått har egenskaper som beskriver det: ett namn, en vikt och en standard belastning.

* Mått namn: namnet på måttet. Mått namnet är en unik identifierare för måttet i klustret från resurs hanterarens perspektiv.
* Vikt: mått vikten definierar hur viktigt detta mått är i förhållande till de andra måtten för den här tjänsten.
* Standard belastning: standard belastningen representeras på olika sätt beroende på om tjänsten är tillstånds lös eller tillstånds känslig.
  * För tillstånds lösa tjänster har varje mått en enda egenskap med namnet DefaultLoad
  * För tillstånds känsliga tjänster definierar du:
    * PrimaryDefaultLoad: standard beloppet för det här måttet som tjänsten förbrukar när det är en primär
    * SecondaryDefaultLoad: standard beloppet för det här måttet som tjänsten förbrukar när det är en sekundär

> [!NOTE]
> Om du definierar anpassade mått och du _även_ vill använda standard måtten, måste du _uttryckligen_ lägga till standard måtten igen och definiera vikter och värden. Detta beror på att du måste definiera relationen mellan standard måtten och dina anpassade mått. Till exempel kanske du bryr dig om ConnectionCount eller WorkQueueDepth över primär distribution. Som standard är vikten av PrimaryCount-måttet hög, så du vill minska den till medel när du lägger till dina andra mått för att se till att de prioriteras.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiera mått för tjänsten – ett exempel
Anta att du vill ha följande konfiguration:

  - Tjänsten rapporterar ett mått med namnet "ConnectionCount"
  - Du vill också använda standard måtten 
  - Du har gjort några mätningar och vet att normalt en primär replik av tjänsten tar upp 20 enheter av "ConnectionCount"
  - Sekundära servrar använder 5 enheter av "ConnectionCount"
  - Du vet att "ConnectionCount" är det viktigaste måttet när det gäller att hantera prestandan för den här specifika tjänsten
  - Du vill fortfarande ha primära repliker balanserade. Att balansera primära repliker är i allmänhet en bra idé oavsett vad. Detta bidrar till att förhindra förlust av vissa noder eller fel domäner från att påverka en majoritet av primära repliker tillsammans med den. 
  - Annars är standard måtten fina

Här är den kod som du skriver för att skapa en tjänst med den här mått konfigurationen:

Kod:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Ovanstående exempel och resten av det här dokumentet beskriver hur du hanterar mått per-till-tjänst-tjänst. Du kan också definiera mått för dina tjänster på tjänste _typ_ nivå. Detta åstadkommer du genom att ange dem i tjänst manifesten. Det rekommenderas inte att definiera typ nivå mått av flera orsaker. Den första orsaken är att mått namnen ofta är miljörelaterade. Om det inte finns något fast kontrakt på plats kan du inte vara säker på att måttet "kärnor" i en miljö inte är "MiliCores" eller "kärnor" i andra. Om dina mått definieras i ditt manifest måste du skapa nya manifest per miljö. Detta leder vanligt vis till en spridning av olika manifest med bara smärre skillnader, vilket kan leda till hanterings svårigheter.  
>
> Mått belastningar tilldelas ofta per namngiven tjänst instans. Anta till exempel att du skapar en instans av tjänsten för kunden som planerar att använda den enbart lätt. Vi antar också att du skapar ett annat för CustomerB som har en större arbets belastning. I det här fallet vill du förmodligen justera standard belastningarna för dessa tjänster. Om du har mått och belastningar definierade via manifest och du vill ha stöd för det här scenariot krävs olika program-och tjänst typer för varje kund. De värden som definieras när tjänsten skapas åsidosätter de som definierats i manifestet, så du kan använda det för att ange de angivna standardvärdena. Det innebär dock att värdena som deklarerats i manifesten inte matchar de som tjänsten faktiskt körs med. Detta kan leda till förvirring. 
>

Som en påminnelse: om du bara vill använda standard måtten behöver du inte trycka på mått samlingen alls eller göra något särskilt när du skapar tjänsten. Standard måtten används automatiskt när inga andra har definierats. 

Nu ska vi gå igenom var och en av de här inställningarna i detalj och prata om det beteende som påverkar det.

## <a name="load"></a>Inläsning
Hela punkten med att definiera mått är att representera en del belastning. *Belastningen* är hur mycket av ett visst mått som används av en viss tjänst instans eller replik på en viss nod. Belastningen kan konfigureras nästan vilken punkt som helst. Exempel:

  - Inläsning kan definieras när en tjänst skapas. Den här typen av belastnings konfiguration kallas _standard inläsning_.
  - Mått informationen, inklusive standard belastningar, för en tjänst kan uppdateras när tjänsten har skapats. Den här mått uppdateringen görs genom att _en tjänst uppdateras_.
  - Belastningarna för en specifik partition kan återställas till standardvärdena för den tjänsten. Den här mått uppdateringen kallas _återställning av partitionens belastning_.
  - Belastningen kan rapporteras per tjänst objekt, dynamiskt under körning. Den här mått uppdateringen kallas för _rapporterings belastning_.
  - Belastning för partitionens repliker eller instanser kan också uppdateras genom att rapportera inläsnings värden via ett Fabric API-anrop. Den här mått uppdateringen kallas _rapporterings belastning för en partition_.

Alla dessa strategier kan användas i samma tjänst under dess livs längd.

## <a name="default-load"></a>Standard belastning
*Standard belastning* är hur mycket av måttet varje tjänst objekt (tillstånds lös instans eller tillstånds känslig replik) för den här tjänsten förbrukar. Kluster resurs hanteraren använder det här numret för belastningen på serviceobjektet tills det får annan information, till exempel en dynamisk inläsnings rapport. För enklare tjänster är standard belastningen en statisk definition. Standard belastningen uppdateras aldrig och används för tjänstens livstid. Standard inläsningar passar bra för enkla kapacitets planerings scenarier där vissa resurser är avsedda för olika arbets belastningar och inte ändras.

> [!NOTE]
> Mer information om kapacitets hantering och hur du definierar kapacitet för noderna i klustret finns i [den här artikeln](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Kluster resurs hanteraren gör det möjligt för tillstånds känsliga tjänster att ange en annan standard belastning för deras presidentval och sekundära servrar. Tillstånds lösa tjänster kan bara ange ett värde som gäller för alla instanser. För tillstånds känsliga tjänster är standard belastningen för primära och sekundära repliker vanligt vis olika eftersom repliker gör olika typer av arbete i varje roll. Till exempel fungerar presidentval vanligt vis både läsningar och skrivningar och hanterar de flesta beräknings belastningar, medan sekundära inte används. Normalt är standard belastningen för en primär replik högre än standard belastningen för sekundära repliker. De reella talen bör vara beroende av dina egna mått.

## <a name="dynamic-load"></a>Dynamisk belastning
Anta att du har kört tjänsten en stund. Med viss övervakning har du märkt att:

1. Vissa partitioner eller instanser av en viss tjänst förbrukar fler resurser än andra
2. Vissa tjänster har belastning som varierar över tid.

Det finns många saker som kan orsaka dessa typer av belastnings variationer. Till exempel är olika tjänster eller partitioner kopplade till olika kunder med olika krav. Belastningen kan också ändras eftersom mängden arbete som tjänsten stöder varierar under dagen. Oavsett orsaken finns det vanligt vis inget enskilt tal som du kan använda som standard. Detta gäller särskilt om du vill få ut mesta möjliga användning av klustret. Ett värde som du väljer för standard inläsning är felaktigt för tillfället. Felaktigt standard inläsnings resultat i kluster resurs hanteraren, antingen över eller under tilldelning av resurser. Det innebär att du har noder som är över eller underutnyttjade även om kluster resurs hanteraren tror att klustret är balanserade. Standard belastningar är fortfarande lämpliga eftersom de ger viss information för inledande placering, men de är inte en komplett berättelse för verkliga arbets belastningar. För att kunna samla in ändringar av resurs kraven korrekt, tillåter kluster resurs hanteraren varje tjänst objekt att uppdatera sin egen belastning under körning. Detta kallas dynamisk inläsnings rapportering.

Med dynamiska inläsnings rapporter kan repliker eller instanser justera sin allokering/rapporterad belastning för mått under deras livstid. En tjänst replik eller en instans som är kall och inte utför något arbete skulle vanligt vis rapportera att den använde små mängder av ett specifikt mått. En upptagen replik eller instans skulle rapportera att de använder mer.

Med rapporterings belastning per replik eller instans kan kluster resurs hanteraren organisera om enskilda tjänst objekt i klustret. Genom att organisera om tjänsterna ser du till att de får de resurser som krävs. Upptagna tjänster kan faktiskt komma till "återta" resurser från andra repliker eller instanser som för närvarande är kalla eller utför mindre arbete.

I Reliable Services ser koden till rapport inläsningen dynamiskt ut så här:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

En tjänst kan rapportera om alla mått som definierats för den när den skapades. Om en tjänst rapporter läses in för ett mått som inte är konfigurerat att använda, Service Fabric ignorerar den rapporten. Om det finns andra mått som rapporter ATS vid samma tidpunkt som är giltiga, godkänns dessa rapporter. Service koden kan mäta och rapportera alla mått som den känner till och operatörer kan ange vilken mått konfiguration som ska användas utan att behöva ändra tjänst koden. 

## <a name="reporting-load-for-a-partition"></a>Rapport belastning för en partition
I föregående avsnitt beskrivs hur en rapport inläsning av tjänst repliker eller instanser. Det finns ytterligare ett alternativ för att dynamiskt rapportera inläsning med FabricClient. När du rapporterar belastning för en partition kan du rapportera om flera partitioner på en gång.

Dessa rapporter kommer att användas på exakt samma sätt som inläsnings rapporter som kommer från själva replikerna eller själva instanserna. Rapporterade värden är giltiga tills nya inläsnings värden rapporteras, antingen av repliken eller instansen eller genom att ett nytt inläsnings värde för en partition rapporteras.

Med det här API: et finns det flera sätt att uppdatera belastningen i klustret:

  - En tillstånds känslig tjänst partition kan uppdatera den primära replik belastningen.
  - Både tillstånds lösa och tillstånds känsliga tjänster kan uppdatera belastningen för alla dess sekundära repliker eller instanser.
  - Både tillstånds lösa och tillstånds känsliga tjänster kan uppdatera belastningen på en enskild replik eller instans på en nod.

Det är också möjligt att kombinera alla dessa uppdateringar per partition på samma tid.

Det går att uppdatera belastningar för flera partitioner med ett enda API-anrop, i så fall kommer utdata att innehålla ett svar per partition. Om uppdatering av partitionen inte har tillämpats av någon anledning, hoppas uppdateringar för den partitionen över, och motsvarande felkod för en riktad partition kommer att tillhandahållas:

  - PartitionNotFound-angivet partitions-ID finns inte.
  - ReconfigurationPending-partitionen håller på att omkonfigureras.
  - InvalidForStatelessServices-ett försök gjordes att ändra belastningen på en primär replik för en partition som tillhör en tillstånds lös tjänst.
  - ReplicaDoesNotExist-sekundär replik eller instans finns inte på en angiven nod.
  - InvalidOperation – kan inträffa i två fall: uppdatering av belastning för en partition som tillhör system programmet eller uppdateringen av förväntad belastning är inte aktive rad.

Om några av dessa fel returneras kan du uppdatera inmatarna för en viss partition och försöka uppdatera igen för en viss partition.

Kod:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

I det här exemplet ska du utföra en uppdatering av den senast rapporterade inläsningen för en partition _53df3d7f-5471-403b-B736-bde6ad584f42_. Primär replik belastning för en mått _CustomMetricName0_ kommer att uppdateras med värdet 100. Samtidigt kommer belastningen för samma mått för en speciell sekundär replik som finns på noden _NodeName0_ att uppdateras med värdet 200.

### <a name="updating-a-services-metric-configuration"></a>Uppdatera en tjänsts mått konfiguration
Listan över mått som är associerade med tjänsten och egenskaperna för dessa mått kan uppdateras dynamiskt när tjänsten är Live. Detta möjliggör experimentering och flexibilitet. Några exempel på när detta är användbart är:

  - inaktivera ett mått med en fel söknings rapport för en viss tjänst
  - Konfigurera om vikterna för mått baserat på önskat beteende
  - Aktivera endast ett nytt mått efter att koden redan har distribuerats och verifierats via andra mekanismer
  - ändra standard belastningen för en tjänst baserat på observerat beteende och förbrukning

Huvud-API: erna för ändring av mått konfigurationen finns `FabricClient.ServiceManagementClient.UpdateServiceAsync` i C# och `Update-ServiceFabricService` PowerShell. Den information som du anger med dessa API: er ersätter den befintliga mått informationen för tjänsten omedelbart. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Blanda standard inläsnings värden och dynamiska inläsnings rapporter
Standard belastning och dynamiska inläsningar kan användas för samma tjänst. När en tjänst använder både standard belastning och dynamiska inläsnings rapporter fungerar standard belastningen som en uppskattning tills dynamiska rapporter visas. Standard belastning är användbart eftersom det ger kluster resurs hanteraren något att arbeta med. Med standard belastningen kan kluster resurs hanteraren placera tjänst objekt på lämpliga platser när de skapas. Om ingen standard inläsnings information anges är placeringen av tjänsterna i praktiken slumpmässigt. När inläsnings rapporter kommer till senare är den första slumpmässiga placeringen ofta fel och kluster resurs hanteraren måste flytta tjänster.

Låt oss ta vårt tidigare exempel och se vad som händer när vi lägger till några anpassade mått och rapporter om dynamisk inläsning. I det här exemplet använder vi "MemoryInMb" som exempel mått.

> [!NOTE]
> Minnet är ett av de system mått som Service Fabric kan [resurs styrning](service-fabric-resource-governance.md), och det är vanligt vis svårt att rapportera det. Vi förväntar dig inte att rapportera om minnes förbrukning. Minne används här som ett stöd för att lära sig om funktionerna i kluster resurs hanteraren.
>

Vi utgår från att vi ursprungligen skapade den tillstånds känsliga tjänsten med följande kommando:

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Som en påminnelse är denna syntax ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Nu ska vi se hur en möjlig kluster layout kan se ut så här:

<center>

![Kluster balanserade med både standard mått och anpassade mått][Image2]
</center>

Några saker som är värda att notera:

* Sekundära repliker inom en partition kan ha sin egen belastning
* Allmänna måtten se balanserade. För minne är förhållandet mellan den högsta och lägsta belastningen 1,75 (noden med den mest inläsningen är N3, minst N2 och 28/16 = 1,75).

Det finns några saker som vi fortfarande behöver förklara:

* Vad avgörs om förhållandet mellan 1,75 var rimligt eller inte? Hur vet kluster resurs hanteraren om det är tillräckligt tillräckligt eller om det finns mer arbete att göra?
* När sker balansering?
* Vad betyder det att minnet viktades "hög"?

## <a name="metric-weights"></a>Mått vikter
Att spåra samma mått i olika tjänster är viktigt. I den globala vyn kan kluster resurs hanteraren spåra förbrukningen i klustret, balansera förbrukningen mellan noder och se till att noderna inte översätts över kapacitet. Men tjänster kan ha olika vyer för betydelsen av samma mått. I ett kluster med många mått och massor av tjänster kan det också finnas perfekt balanserade lösningar för alla mått. Hur ska kluster resurs hanteraren hantera dessa situationer?

Mått vikter gör det möjligt för kluster resurs hanteraren att bestämma hur klustret ska balansera när det inte finns något perfekt svar. Mät vikter gör det också möjligt för kluster resurs hanteraren att balansera vissa tjänster på olika sätt. Mått kan ha fyra olika vikt nivåer: noll, låg, medel och hög. Ett mått med en vikt på noll bidrar inget när du funderar på om saker är balanserade eller inte. Belastningen bidrar dock fortfarande till kapacitets hantering. Mått med noll vikt är fortfarande användbara och används ofta som en del av tjänstens beteende och prestanda övervakning. [Den här artikeln](service-fabric-diagnostics-event-generation-infra.md) innehåller mer information om hur du använder mått för övervakning och diagnostik av dina tjänster. 

Den verkliga effekten av olika mått vikter i klustret är att kluster resurs hanteraren genererar olika lösningar. Mått vikterna talar om för kluster resurs hanteraren att vissa mått är viktigare än andra. När det inte finns någon perfekt lösning kan kluster resurs hanteraren föredra lösningar som balanserar de högre viktade måtten bättre. Om en tjänst anser att ett visst mått är oviktigt kan det vara bra att använda detta mått. Detta gör att en annan tjänst kan få en jämn fördelning av ett mått som är viktigt för IT.

Nu ska vi titta på ett exempel på några inläsnings rapporter och hur olika mått vikter resulterar i olika allokeringar i klustret. I det här exemplet ser vi att växling av de relativa vikterna för måtten gör att kluster resurs hanteraren skapar olika arrangemang av tjänster.

<center>

![Exempel på mått viktning och dess inverkan på balans lösningarna][Image3]
</center>

I det här exemplet finns det fyra olika tjänster, som rapporterar olika värden för två olika mått, mått-och MetricB. I ett enda fall är alla tjänster som definierar måttet viktigt en (vikt = hög) och MetricB som oviktig (vikt = låg). Därför ser vi att kluster resurs hanteraren placerar tjänsterna så att måtten är bättre balanserade än MetricB. "Bättre balanserade" innebär att måtta har en lägre standard avvikelse än MetricB. I det andra fallet återförs mått vikterna. Därför byter kluster resurs hanteraren tjänster A och B till en tilldelning där MetricB är bättre balanserade än mått.

> [!NOTE]
> Mått vikterna avgör hur kluster resurs hanteraren ska balansera, men inte när balansering ska ske. Mer information om balansering finns i [den här artikeln](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globala mått vikter
Låt oss säga att serva definierar Metrics vikt högt och ServiceB anger vikten för måttet till låg eller noll. Vad är den faktiska vikten som används för att komma igång?

Det finns flera vikter som spåras för varje mått. Den första vikten är den som definierats för måttet när tjänsten skapas. Den andra vikten är en global vikt som beräknas automatiskt. Kluster resurs hanteraren använder både dessa vikter vid bedömnings lösningar. Att ta hänsyn till båda vikterna är viktigt. Detta gör det möjligt för kluster resurs hanteraren att balansera varje tjänst enligt sina egna prioriteringar och även se till att klustret som helhet allokeras korrekt.

Vad händer om kluster resurs hanteraren inte bryr sig om både globalt och lokalt saldo? Det är också enkelt att konstruera lösningar som är globalt balanserade, men som resulterar i dåliga resurs sal Don för enskilda tjänster. I följande exempel ska vi titta på en tjänst som kon figurer ATS med bara standard måtten och se vad som händer när endast global balans anses:

<center>

![Effekten av en global enda lösning][Image4]
</center>

I det översta exemplet baserat bara på global balans är klustret som helhet balanserade. Alla noder har samma antal presidentval och samma totala antal repliker. Men om du tittar på den faktiska effekten av den här allokeringen är det inte så viktigt: förlusten av noder påverkar en viss arbets belastning oproportionerligt, eftersom den tar ut hela sin presidentval. Om den första noden till exempel inte klarar de tre presidentval för de tre olika partitionerna i cirkel tjänsten kommer alla att gå förlorade. Dessutom har triangeln och Sexhörnings tjänsterna sina partitioner förlorar en replik. Detta orsakar inget avbrott, förutom att återställa ned-repliken.

I det nedre exemplet har kluster resurs hanteraren distribuerat replikerna baserat på både det globala saldot och per tjänst. När du beräknar resultatet av lösningen ger det störst vikt till den globala lösningen och en (konfigurerbar) del till enskilda tjänster. Ett globalt saldo för ett mått beräknas baserat på genomsnittet av mått vikterna från varje tjänst. Varje tjänst är bal anse rad enligt dess egna definierade mått vikter. Detta säkerställer att tjänsterna är balanserade i sig enligt sina egna behov. Det innebär att om samma första nod Miss lyckas, distribueras alla partitioner i alla tjänster. Påverkan på var och en är densamma.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster finns i [så här konfigurerar du tjänster](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-Cluster-Resource-Manager-Configure-Services.MD)
- Att definiera defragmentering-mått är ett sätt att konsolidera belastningen på noderna i stället för att sprida ut det. Information om hur du konfigurerar defragmentering finns i [den här artikeln](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Om du vill veta mer om hur kluster resurs hanteraren hanterar och balanserar belastningen i klustret kan du läsa artikeln om [balansering av belastning](service-fabric-cluster-resource-manager-balancing.md)
- Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Rörelse kostnaden är ett sätt att signalera till kluster resurs hanteraren att vissa tjänster är dyrare att flytta än andra. Läs mer om rörelse kostnader i [den här artikeln](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
