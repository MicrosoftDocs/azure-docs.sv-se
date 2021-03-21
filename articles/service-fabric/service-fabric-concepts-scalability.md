---
title: Skalbarhet för Service Fabric tjänster
description: Lär dig mer om skalning i Azure Service Fabric och de olika teknikerna som används för att skala program.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 87ac89edc1c9996afc03e7c2bd6743202fdfcb52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786199"
---
# <a name="scaling-in-service-fabric"></a>Skalning i Service Fabric
Azure Service Fabric gör det enkelt att bygga skalbara program genom att hantera tjänsterna, partitionerna och replikerna på noderna i ett kluster. Att köra många arbets belastningar på samma maskin vara möjliggör maximal resursutnyttjande, men ger också flexibilitet när det gäller hur du väljer att skala dina arbets belastningar. Den här kanalen 9-videon beskriver hur du kan bygga skalbara mikrotjänster-program:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalning i Service Fabric utförs på flera olika sätt:

1. Skalning genom att skapa eller ta bort tillstånds lösa tjänst instanser
2. Skalning genom att skapa eller ta bort nya namngivna tjänster
3. Skalning genom att skapa eller ta bort nya namngivna program instanser
4. Skalning med hjälp av partitionerade tjänster
5. Skalning genom att lägga till och ta bort noder från klustret 
6. Skalning med hjälp av kluster resurs hanterarens mått

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalning genom att skapa eller ta bort tillstånds lösa tjänst instanser
Ett av de enklaste sätten att skala inom Service Fabric fungerar med tillstånds lösa tjänster. När du skapar en tillstånds lös tjänst får du möjlighet att definiera en `InstanceCount` . `InstanceCount` definierar hur många körnings kopior av den här tjänst koden som skapas när tjänsten startas. Anta till exempel att det finns 100 noder i klustret. Vi antar också att en tjänst har skapats med `InstanceCount` 10. Under körningen kan de 10 löpande kopiorna av koden bli för upptagna (eller inte vara tillräckligt upptagna). Ett sätt att skala arbets belastningen är att ändra antalet instanser. Till exempel kan vissa övervaknings-eller hanterings koder ändra det befintliga antalet instanser till 50 eller 5, beroende på om arbets belastningen måste skalas in eller ut baserat på belastningen. 

C#

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Använda dynamiskt antal instanser
Service Fabric är specifikt för tillstånds lösa tjänster och erbjuder ett automatiskt sätt att ändra antalet instanser. Detta gör att tjänsten kan skalas dynamiskt med antalet tillgängliga noder. Sättet att välja det här beteendet är att ange antalet instanser =-1. InstanceCount =-1 är en instruktion som Service Fabric med texten "kör den här tillstånds lösa tjänsten på varje nod". Om antalet noder ändras ändrar Service Fabric automatiskt antalet instanser som ska matchas, vilket säkerställer att tjänsten körs på alla giltiga noder. 

C#

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalning genom att skapa eller ta bort nya namngivna tjänster
En namngiven tjänst instans är en specifik instans av en tjänst typ (se [Service Fabric livs cykel för program](service-fabric-application-lifecycle.md)) inom en namngiven program instans i klustret. 

Nya namngivna tjänst instanser kan skapas (eller tas bort) när tjänster blir mer eller mindre upptagna. Detta gör att förfrågningar kan spridas över fler tjänst instanser, vilket vanligt vis tillåter att befintliga tjänster laddas ned. När du skapar tjänster placerar Service Fabric Cluster Resource Manager tjänsterna i klustret på ett distribuerat sätt. De exakta besluten styrs av [måtten](service-fabric-cluster-resource-manager-metrics.md) i klustret och andra placerings regler. Tjänster kan skapas på flera olika sätt, men de vanligaste är antingen genom administrativa åtgärder som någon som ringer [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice) , eller genom kod anrop [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) . `CreateServiceAsync` kan till och med anropas från andra tjänster som körs i klustret.

Att skapa tjänster dynamiskt kan användas i alla typer av scenarier och är ett vanligt mönster. Anta till exempel en tillstånds känslig tjänst som representerar ett visst arbets flöde. Anrop som representerar arbete kommer att visas i den här tjänsten och den här tjänsten kommer att utföra stegen för arbets flödet och registrera förloppet. 

Hur gör du för att göra den här specifika tjänst skala? Tjänsten kan vara flera innehavare i en viss form och acceptera samtal och vidta åtgärder för många olika instanser av samma arbets flöde samtidigt. Men det kan göra koden mer komplex, eftersom den måste bekymra sig över flera olika instanser av samma arbets flöde, allt i olika faser och från olika kunder. Hantering av flera arbets flöden på samma tid löser också inte skalnings problemet. Detta beror på att tjänsten använder för många resurser för att få plats på en viss dator. Många tjänster som inte har skapats för det här mönstret på den första platsen får också svårt på grund av viss Flask hals eller lägre kod. De här typerna av problem medför att tjänsten inte fungerar, även om antalet samtidiga arbets flöden som spåras blir större.  

En lösning är att skapa en instans av den här tjänsten för varje annan instans av arbets flödet som du vill spåra. Detta är ett bra mönster och fungerar oavsett om tjänsten är tillstånds lös eller tillstånds känslig. För att det här mönstret ska fungera finns det vanligt vis en annan tjänst som fungerar som en "arbets belastnings hanterings tjänst". Jobbet för den här tjänsten är att ta emot begär Anden och dirigera dessa förfrågningar till andra tjänster. Chefen kan skapa en instans av arbets belastnings tjänsten dynamiskt när den får meddelandet, och sedan skicka vidare förfrågningar till tjänsterna. Hanterings tjänsten kan också ta emot återanrop när en specifik arbets flödes tjänst slutfört sitt jobb. När chefen tar emot dessa återanrop kan den ta bort den instansen av arbets flödes tjänsten eller lämna den om fler anrop förväntas. 

Avancerade versioner av den här typen av chef kan till och med skapa pooler för de tjänster som hanteras av den. Poolen hjälper till att se till att när en ny begäran kommer till måste den inte vänta på att tjänsten ska starta. I stället kan chefen bara välja en arbets flödes tjänst som för närvarande inte är upptagen från poolen eller dirigeras slumpmässigt. Att behålla en pool av tjänster som är tillgängliga gör hanteringen av nya begär Anden snabbare, eftersom det är mindre troligt att begäran måste vänta på att en ny tjänst ska kunna anpassas. Att skapa nya tjänster är snabbt, men inte gratis eller momentant. Poolen bidrar till att minimera den tid som begäran måste vänta innan den kan servas. Du kan ofta se det här mönstret och pool mönstret när svars tiderna är de flesta. Köa begäran och skapa tjänsten i bakgrunden och _sedan_ skickar den också ett populärt chefs mönster, som skapar och tar bort tjänster baserat på en spårning av mängden arbete som tjänsten för närvarande har väntande. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalning genom att skapa eller ta bort nya namngivna program instanser
Att skapa och ta bort hela program instanser liknar mönstret för att skapa och ta bort tjänster. För det här mönstret finns det vissa hanterings tjänster som fattar beslutet baserat på de begär Anden som den ser och den information som tas emot från de andra tjänsterna i klustret. 

När ska du skapa en ny namngiven program instans som ska användas i stället för att skapa en ny namngiven tjänst instans i ett befintligt program? Det finns några fall:

  * Den nya program instansen är för en kund vars kod måste köras under vissa specifika identiteter eller säkerhets inställningar.
    * Med Service Fabric kan du definiera olika kod paket som ska köras under särskilda identiteter. För att kunna starta samma kod paket under olika identiteter måste aktiveringarna ske i olika program instanser. Tänk dig ett fall där du har en befintlig kunds arbets belastningar distribuerade. Dessa kan köras under en viss identitet så att du kan övervaka och kontrol lera åtkomsten till andra resurser, t. ex. fjärrdatabaser eller andra system. I det här fallet, när en ny kund registrerar sig, vill du förmodligen inte aktivera sin kod i samma kontext (process utrymme). Även om du skulle kunna göra det blir det svårare för din tjänst kod att agera inom kontexten för en viss identitet. Du måste vanligt vis ha mer säkerhet, isolering och identitets hanterings kod. I stället för att använda olika namngivna tjänst instanser inom samma program instans och därmed samma process utrymme kan du använda olika namngivna Service Fabric program instanser. Detta gör det enklare att definiera olika identitets kontexter.
  * Den nya program instansen fungerar också som ett sätt att konfigurera
    * Som standard körs alla namngivna tjänst instanser av en viss tjänst typ inom en program instans i samma process på en viss nod. Det innebär att när du kan konfigurera varje tjänst instans på olika sätt, så är det komplicerat. Tjänsterna måste ha en token som de använder för att söka efter sin konfiguration i ett konfigurations paket. Detta är vanligt vis bara tjänstens namn. Detta fungerar bra, men det couplesr konfigurationen till namnen på de enskilda namngivna tjänst instanserna i program instansen. Detta kan vara förvirrande och svårt att hantera eftersom konfigurationen vanligt vis är ett design tids artefakt med specifika programinstans värden. Att skapa fler tjänster innebär alltid att fler program uppgraderingar kan ändra informationen i konfigurations paketen eller att distribuera nya så att de nya tjänsterna kan slå upp sin specifika information. Det är ofta enklare att skapa en helt ny namngiven program instans. Sedan kan du använda program parametrarna för att ange vilken konfiguration som behövs för tjänsterna. På så sätt kan alla tjänster som skapas inom den namngivna program instansen ärva särskilda konfigurations inställningar. I stället för att ha en enda konfigurations fil med inställningarna och anpassningarna för varje kund, till exempel hemligheter eller per kund resurs gränser, så har du i stället en annan program instans för varje kund med de här inställningarna åsidosatta. 
  * Det nya programmet fungerar som en uppgraderings gränser
    * Inom Service Fabric fungerar olika namngivna program instanser som gränser för uppgradering. En uppgradering av en namngiven program instans påverkar inte koden som en annan namngiven program instans kör. De olika programmen kommer att köra olika versioner av samma kod på samma noder. Detta kan vara en faktor när du behöver göra ett skalnings beslut eftersom du kan välja om den nya koden ska följa samma uppgraderingar som en annan tjänst eller inte. Anta till exempel att ett samtal anländer till hanterings tjänsten som ansvarar för skalning av en viss kunds arbets belastningar genom att skapa och ta bort tjänster dynamiskt. I det här fallet är anropet för en arbets belastning som är kopplad till en _ny_ kund. De flesta kunder är isolerade från varandra, inte bara för de säkerhets-och konfigurations orsaker som anges ovan, men eftersom det ger mer flexibilitet när det gäller att köra vissa versioner av program varan och välja när de ska uppgraderas. Du kan också skapa en ny program instans och skapa tjänsten där du enkelt kan partitionera mängden av dina tjänster som en uppgradering kommer att röra. Separata program instanser ger större precision när du gör program uppgraderingar och aktiverar även en/B-testning och blå/grön-distribution. 
  * Den befintliga program instansen är full
    * I Service Fabric är [program kapaciteten](service-fabric-cluster-resource-manager-application-groups.md) ett koncept som du kan använda för att kontrol lera hur mycket resurser som är tillgängliga för specifika program instanser. Du kan till exempel bestämma att en specifik tjänst måste ha en annan instans för att kunna skalas. Den här program instansen har dock inte tillräckligt med kapacitet för ett visst mått. Om den här specifika kunden eller arbets belastningen fortfarande ska beviljas fler resurser kan du antingen öka den befintliga kapaciteten för programmet eller skapa ett nytt program. 

## <a name="scaling-at-the-partition-level"></a>Skalning på partitions nivå
Service Fabric stöder partitionering. Partitionering delar upp en tjänst i flera logiska och fysiska avsnitt, som var och en fungerar oberoende av varandra. Detta är användbart med tillstånds känsliga tjänster eftersom ingen uppsättning repliker måste hantera alla anrop och samtidigt ändra hela statusen på en gång. [Översikt över partitionering](service-fabric-concepts-partitioning.md) innehåller information om vilka typer av partitionerings scheman som stöds. Replikerna för varje partition sprids över noderna i ett kluster och distribuerar tjänstens belastning och säkerställer att varken tjänsten som helhet eller någon partition har en enskild felpunkt. 

Överväg en tjänst som använder ett intervall för partitionerings scheman med en låg nyckel på 0, en hög nyckel på 99 och ett antal partitioner på 4. I ett kluster med tre noder kan tjänsten anges med fyra repliker som delar resurserna på varje nod, som du ser här:

<center>

![Partitionens layout med tre noder](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Om du ökar antalet noder kommer Service Fabric att flytta några av de befintliga replikerna dit. Anta till exempel att antalet noder ökar till fyra och att replikerna blir omdistribuerade. Nu har tjänsten tre repliker som körs på varje nod och som tillhör olika partitioner. Detta ger bättre resursutnyttjande eftersom den nya noden inte är kall. Vanligt vis förbättrar det också prestanda eftersom varje tjänst har fler tillgängliga resurser.

<center>

![Partitionera layout med fyra noder](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalning med hjälp av Service Fabric Cluster Resource Manager och mått
[Mått](service-fabric-cluster-resource-manager-metrics.md) är hur tjänster uttrycker resurs förbrukningen att Service Fabric. Med hjälp av mått ger kluster resurs hanteraren möjlighet att ordna om och optimera klustrets layout. Det kan till exempel finnas massor av resurser i klustret, men de är kanske inte allokerade till de tjänster som för närvarande fungerar. Med hjälp av mått kan kluster resurs hanteraren organisera om klustret för att säkerställa att tjänsterna har åtkomst till de tillgängliga resurserna. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalning genom att lägga till och ta bort noder från klustret 
Ett annat alternativ för skalning med Service Fabric är att ändra klustrets storlek. Att ändra kluster storleken innebär att lägga till eller ta bort noder för en eller flera av nodtypen i klustret. Anta till exempel ett fall där alla noder i klustret är aktiva. Det innebär att klustrets resurser nästan allt används. I det här fallet är det bästa sättet att skala för att lägga till fler noder i klustret. När de nya noderna ansluter till klustret, kommer Service Fabric Cluster Resource Manager att flytta tjänster till dem, vilket resulterar i mindre total belastning på de befintliga noderna. För tillstånds lösa tjänster med instans antal =-1 skapas fler tjänst instanser automatiskt. Detta gör att vissa anrop kan flyttas från befintliga noder till de nya noderna. 

Mer information finns i [kluster skalning](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Välja en plattform

På grund av implementerings skillnader mellan operativ system kan du välja att använda Service Fabric med Windows eller Linux, och det kan vara en viktig del av att skala ditt program. En möjlig barriär är hur mellanlagrad loggning utförs. Service Fabric i Windows använder en kernel-drivrutin för en per dator-logg som delas mellan tillstånds känsliga tjänste repliker. Den här loggen väger om 8 GB. Linux, å andra sidan, använder en lagrings logg på 256 MB för varje replik, vilket gör det mindre idealiskt för program som vill maximera antalet Lightweight-tjänstereplikeringar som körs på en specifik nod. Dessa skillnader i tillfälliga lagrings krav kan eventuellt meddela önskad plattform för Service Fabric kluster distribution.

## <a name="putting-it-all-together"></a>Färdigställa allt
Vi tar med alla idéer som vi har diskuterat här och pratar med ett exempel. Tänk på följande tjänst: du försöker att bygga en tjänst som fungerar som en adress bok som håller på till namn och kontakt information. 

Du har ett stort antal frågor som rör skalning: hur många användare kommer att ha? Hur många kontakter kommer varje användar lagring? Det är svårt att försöka ta en titt på den här tjänsten när du är färdig med din tjänst för första gången. Anta att du ska gå med en enda statisk tjänst med ett visst antal partitioner. Konsekvenserna av att välja fel antal partitioner kan orsaka skalnings problem senare. På samma sätt, även om du väljer rätt antal, kanske du inte har all information du behöver. Till exempel måste du också bestämma storleken på klustret fram, både vad gäller antalet noder och deras storlek. Det är vanligt vis svårt att förutse hur många resurser en tjänst ska konsumeras under sin livs längd. Det kan också vara svårt att veta i förväg det trafik mönster som tjänsten faktiskt ser. Till exempel kanske personer bara lägger till och tar bort sina kontakter först i morgon, eller så kanske de fördelas jämnt under dagen. Utifrån detta kan du behöva skala ut och dynamiskt. Du kanske kan lära dig att förutsäga när du behöver skala ut och in, men på något sätt kommer du förmodligen att behöva reagera på att ändra resurs förbrukningen för din tjänst. Detta kan innebära att du ändrar klustrets storlek för att tillhandahålla fler resurser när du omorganiserar användningen av befintliga resurser. 

Men varför ska du även försöka välja ett enda partitionsschema för alla användare? Varför begränsa dig själv till en tjänst och ett statiskt kluster? Den faktiska situationen är vanligt vis mer dynamisk. 

När du skapar för skalning bör du tänka på följande dynamiska mönster. Du kan behöva anpassa den efter din situation:

1. I stället för att försöka välja ett partitionerings schema för alla klient versioner skapar du en "Manager-tjänst".
2. Jobbet för hanterings tjänsten är att titta på kund information när de registrerar sig för din tjänst. Beroende på den informationen skapar Manager-tjänsten en instans av din _faktiska_ kontakt lagrings tjänst _precis för kunden_. Om de kräver viss konfiguration, isolering eller uppgraderingar kan du också välja att skapa en program instans för kunden. 

Detta dynamiska mönster för skapande av flera fördelar:

  - Du försöker inte gissa antalet korrekta partitioner för alla användare uppåt eller skapa en enda tjänst som är oändligt skalbar allt på egen hand. 
  - Olika användare behöver inte ha samma antal partitioner, antal repliker, placerings begränsningar, mått, standard belastningar, tjänst namn, DNS-inställningar eller någon av de andra egenskaperna som anges på tjänst-eller program nivå. 
  - Du får ytterligare data segmentering. Varje kund har sin egen kopia av tjänsten
    - Varje kund tjänst kan konfigureras på olika sätt och beviljas mer eller färre resurser, med fler eller färre partitioner eller repliker efter behov baserat på den förväntade skalningen.
      - Anta till exempel att kunden betalar för "guld"-nivån – de kan få fler repliker eller större antal partitioner, och potentiellt resurser som är dedikerade till deras tjänster via mått och program kapacitet.
      - Eller säg att de tillhandahöll information som indikerar antalet kontakter som de behövde var "små" – de får bara några partitioner eller kan till och med ingå i en pool med delade tjänster med andra kunder.
  - Du kör inte en mängd tjänst instanser eller repliker medan du väntar på att kunder ska visas
  - Om en kund någonsin lämnar, är det lika enkelt att ta bort sin information från tjänsten som att projektledaren tar bort tjänsten eller programmet som den skapade.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgänglighet för Service Fabric tjänster](service-fabric-availability-services.md)
* [Partitionera Service Fabric tjänster](service-fabric-concepts-partitioning.md)
