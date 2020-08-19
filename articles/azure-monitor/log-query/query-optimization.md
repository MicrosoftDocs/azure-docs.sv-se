---
title: Optimera logg frågor i Azure Monitor
description: Metod tips för att optimera logg frågor i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: ec5717135ec7bbf2236b5f5672dbf0b5d1413b44
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565731"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimera logg frågor i Azure Monitor
Azure Monitor loggar använder [Azure datautforskaren (ADX)](/azure/data-explorer/) för att lagra loggdata och köra frågor för att analysera data. Den skapar, hanterar och underhåller ADX-kluster åt dig, och optimerar dem för din logg analys arbets belastning. När du kör en fråga optimeras den och dirigeras till lämpligt ADX-kluster som lagrar arbets ytans data. Både Azure Monitor loggar och Azure Datautforskaren använder många automatiska metoder för optimering av frågor. Även om automatiska optimeringar ger betydande ökning, finns det i vissa fall där du kan förbättra dina frågeresultat dramatiskt. Den här artikeln beskriver prestanda överväganden och flera tekniker för att åtgärda dem.

De flesta metoder är vanliga för frågor som körs direkt på Azure Datautforskaren och Azure Monitor loggar, men det finns flera unika Azure Monitor loggar som beskrivs här. Mer information om optimerings tips för Azure Datautforskaren finns i [metod tips för frågor](/azure/kusto/query/best-practices).

Optimerade frågor kommer att:

- Kör snabbare, minska den totala tiden för frågekörningen.
- Få mindre chans att bli begränsad eller avvisad.

Du bör ha särskild uppmärksamhet på frågor som används för återkommande och burst-användning, till exempel instrument paneler, aviseringar, Logic Apps och Power BI. Effekten av en ineffektiv fråga i dessa fall är väsentlig.

## <a name="query-performance-pane"></a>Fönstret fråga prestanda
När du har kört en fråga i Log Analytics klickar du på nedpilen ovanför frågeresultaten för att visa fönstret frågeresultat som visar resultatet av flera prestanda indikatorer för frågan. Dessa prestanda indikatorer beskrivs i följande avsnitt.

![Fönstret fråga prestanda](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Fråga prestanda indikatorer

Följande prestanda indikatorer för frågor är tillgängliga för alla frågor som körs:

- [Total processor](#total-cpu): övergripande beräkning som används för att bearbeta frågan över alla Compute-noder. Den representerar tiden som används för data behandling, parsning och data hämtning. 

- [Data som används för bearbetad fråga](#data-used-for-processed-query): övergripande data som har använts för att bearbeta frågan. Påverkas av storleken på mål tabellen, tids rymden som används, filter tillämpas och antalet kolumner som refereras.

- [Tidsintervallet för den bearbetade frågan](#time-span-of-the-processed-query): avståndet mellan de senaste och äldsta data som användes för att bearbeta frågan. Påverkas av det explicita tidsintervallet som anges för frågan.

- [Ålder på bearbetade data](#age-of-processed-data): mellanrummet mellan nu och de äldsta data som har öppnats för att bearbeta frågan. Det påverkar starkt effektiviteten för data hämtning.

- [Antal arbets ytor](#number-of-workspaces): hur många arbets ytor som användes under bearbetningen av frågan på grund av implicit eller explicit val.

- [Antal regioner](#number-of-regions): hur många regioner som har öppnats under bearbetningen av frågan baserat på implicit eller explicit val av arbets ytor. Frågor med flera regioner är mycket mindre effektiva och prestanda indikatorer utgör delvis täckning.

- [Parallellitet](#parallelism): anger hur mycket systemet kunde köra frågan på flera noder. Endast relevant för frågor som har hög CPU-förbrukning. Påverkas av användning av vissa funktioner och operatorer.


## <a name="total-cpu"></a>Total CPU
Den faktiska beräknings processor som investerats för att bearbeta den här frågan över alla noder för bearbetning av frågor. Eftersom de flesta frågor körs på ett stort antal noder blir detta vanligt vis mycket större än den tid som frågan faktiskt tog att köra. 

Tid för bearbetning av fråga har lagts på:
- Data hämtning – det tar längre tid att hämta gamla data än att hämta senaste data.
- Data bearbetning – logik och utvärdering av data. 

Förutom tids åtgång i noden för bearbetning av frågor finns det ytterligare tid som Azure Monitor loggar för att: autentisera användaren och kontrol lera att de har behörighet att komma åt dessa data, leta upp data lagret, parsa frågan och allokera noderna för bearbetning av frågor. Den här tiden ingår inte i frågans totala CPU-tid.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Tidig filtrering av poster innan du använder höga processor funktioner

Några av frågans kommandon och funktioner är tungt i CPU-förbrukningen. Detta gäller särskilt för kommandon som tolkar JSON och XML eller extraherar komplexa reguljära uttryck. Sådan parsning kan ske direkt via [parse_json ()](/azure/kusto/query/parsejsonfunction) eller [parse_xml ()](/azure/kusto/query/parse-xmlfunction) -funktioner eller implicit när du refererar till dynamiska kolumner.

Dessa funktioner förbrukar CPU i förhållande till antalet rader som de bearbetar. Den effektivaste optimeringen är att lägga till vilka villkor som är tidiga i frågan och som kan filtrera ut så många poster som möjligt innan processor intensiv funktionen körs.

Följande frågor ger till exempel exakt samma resultat, men den andra är den mest effektiva som [WHERE](/azure/kusto/query/whereoperator) -villkoret innan tolkningen utesluter många poster:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Undvik att använda utvärderade WHERE-satser

Frågor som innehåller [WHERE](/azure/kusto/query/whereoperator) -satser i en utvärderad kolumn i stället för kolumner som är fysiskt närvarande i data uppsättningen förlorar effektiviteten. Filtrering av utvärderade kolumner förhindrar vissa system optimeringar när stora mängder data hanteras.
Följande frågor ger till exempel exakt samma resultat, men den andra är mer effektiv som [WHERE](/azure/kusto/query/whereoperator) -villkoret avser inbyggd kolumn

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count(), make_set(IPRegion) by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count(), make_set(IPRegion) by Computer
```

### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Använd effektiva agg regerings kommandon och dimensioner i sammanfatta och delta

Vissa agg regerings kommandon som [Max ()](/azure/kusto/query/max-aggfunction), [Sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)och [AVG ()](/azure/kusto/query/avg-aggfunction) har låg processor påverkan på grund av deras logik, andra är mer komplexa och innehåller heuristik och uppskattningar som gör att de kan köras effektivt. Till exempel använder [DCount ()](/azure/kusto/query/dcount-aggfunction) HyperLogLog-algoritmen för att ge en nära bedömning av distinkta mängder av stora data uppsättningar utan att faktiskt räkna varje värde. percentils funktionerna gör liknande uppskattningar med hjälp av den närmaste rang-algoritmen. Flera av kommandona är valfria parametrar för att minska deras påverkan. Funktionen [makeset ()](/azure/kusto/query/makeset-aggfunction) har till exempel en valfri parameter för att definiera maximal uppsättnings storlek, vilket avsevärt påverkar processor och minne.

[Sammanfognings](/azure/kusto/query/joinoperator?pivots=azuremonitor) -och [sammanfattnings](/azure/kusto/query/summarizeoperator) kommandon kan orsaka hög processor användning när de bearbetar en stor uppsättning data. Deras komplexitet är direkt relaterad till antalet möjliga värden, som kallas *kardinalitet*, för de kolumner som används som `by` i sammanfatta eller som kopplings attribut. Förklaring och optimering av sammanfogning och sammanfattning finns i dokumentations artiklar och optimerings tips.

Följande frågor ger till exempel exakt samma resultat eftersom **CounterPath** alltid är en-till-en-mappad till **CounterName** och **ObjectName**. Den andra är mer effektiv eftersom agg regerings dimensionen är mindre:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU-förbrukning kan också påverkas av WHERE-villkor eller utökade kolumner som kräver intensiv data bearbetning. Alla enkla sträng jämförelser, t. ex. [Equal = =](/azure/kusto/query/datatypes-string-operators) och [StartsWith](/azure/kusto/query/datatypes-string-operators) , har ungefär samma CPU-effekt medan avancerade text träffar har större påverkan. Mer specifikt är [operatorn är mer](/azure/kusto/query/datatypes-string-operators) effektiv än operatorn [contains](/azure/kusto/query/datatypes-string-operators) . På grund av sträng hanterings tekniker är det mer effektivt att söka efter strängar som är längre än fyra tecken än korta strängar.

Följande frågor ger till exempel liknande resultat, beroende på dator namngivnings princip, men den andra är mer effektiv:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Den här indikatorn visar bara CPU från det omedelbara klustret. I frågor med flera regioner representerar den bara en av regionerna. I frågor med flera arbets ytor kan det hända att den inte innehåller alla arbets ytor.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Undvik fullständig XML-och JSON-parsning när sträng parsning fungerar
Fullständig parsning av ett XML-eller JSON-objekt kan förbruka höga processor-och minnes resurser. I många fall är det lättare att parsa dem som strängar med hjälp av [operatorn parser](/azure/kusto/query/parseoperator) eller andra [text tolknings tekniker](./parse-text.md)när bara en eller två parametrar behövs och XML-eller JSON-objekten är enkla. Prestanda ökningen blir mer betydelsefull eftersom antalet poster i XML-eller JSON-objektet ökar. Det är viktigt när antalet poster når flera miljoner.

Följande fråga returnerar till exempel exakt samma resultat som frågorna ovan utan att utföra fullständig XML-parsning. Observera att det gör vissa antaganden på XML-filstrukturen, till exempel att fil Sök vägens element kommer efter FileHash och att ingen av dem har attribut. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Data som används för bearbetad fråga

En kritisk faktor vid bearbetningen av frågan är den data volym som genomsöks och används för bearbetningen av frågan. Azure Datautforskaren använder aggressiva optimeringar som dramatiskt minskar data volymen jämfört med andra data plattformar. Det finns fortfarande kritiska faktorer i frågan som kan påverka den data volym som används.

I Azure Monitor loggar används kolumnen **TimeGenerated** som ett sätt att indexera data. Genom att begränsa **TimeGenerated** -värdena till så smala ett intervall som möjligt kommer att göra en betydande förbättring av frågans prestanda genom att avsevärt begränsa mängden data som ska bearbetas.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Undvik onödig användning av Sök-och Union-operatörer

En annan faktor som ökar data bearbetningen är användningen av stora antal tabeller. Detta inträffar vanligt vis när `search *` och `union *` kommandon används. Dessa kommandon tvingar systemet att utvärdera och genomsöka data från alla tabeller i arbets ytan. I vissa fall kan det finnas hundratals tabeller i arbets ytan. Försök att undvika så mycket som möjligt med "search *" eller en sökning utan att omfånget till en särskild tabell.

Följande frågor ger till exempel exakt samma resultat, men den sista är den mest effektiva:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Lägg till tidiga filter i frågan

En annan metod för att minska data volymen är att [ha de](/azure/kusto/query/whereoperator) villkor som infaller tidigt i frågan. Azure Datautforskaren-plattformen innehåller en cache som gör det möjligt för dem att veta vilka partitioner som innehåller data som är relevanta för ett bestämt Where-villkor. Om en fråga till exempel innehåller så `where EventID == 4624` distribuerar den frågan endast till noder som hanterar partitioner med matchande händelser.

Följande exempel frågor ger exakt samma resultat, men den andra är mer effektiv:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>Undvik flera avsökningar av samma källdata med villkorliga agg regerings funktioner och materialisera funktion
När en fråga har flera under frågor som sammanfogas med hjälp av JOIN-eller union-operatorer, genomsöker varje under fråga hela källan separat och sammanfogar sedan resultaten. Detta är flera gånger det antal gånger som data genomsöks – kritisk faktor i mycket stora data mängder.

En teknik för att undvika detta är genom att använda funktionerna för villkorlig agg regering. De flesta av [agg regerings funktionerna](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) som används i sammanfattnings operatorn har en tillstånds version som gör att du kan använda en enda sammanfattnings operator med flera villkor. 

Följande frågor visar till exempel antalet inloggnings händelser och antalet process körnings händelser för varje konto. De returnerar samma resultat, men den första genomsöker data två gånger, den andra genomsöker den bara en gång:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Ett annat fall där under frågor är onödigt för filtrering för parse- [operatorn](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) för att se till att det bearbetar endast poster som matchar ett speciellt mönster. Detta är onödigt eftersom operatorn parser och andra liknande operatorer returnerar tomma resultat när mönstret inte matchar. Här följer två frågor som returnerar exakt samma resultat medan data för den andra frågan bara genomsöks en gång. I den andra frågan är varje parse-kommando bara relevant för dess händelser. Operatorn utöka efteråt visar hur du refererar till en tom data situation.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

När ovanstående inte tillåter att du använder under frågor, är en annan metod att tipsa till frågesyntaxen om att det finns en enda data källa som används i var och en av dem med hjälp av [funktionen materialisera ()](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor). Detta är användbart när käll informationen kommer från en funktion som används flera gånger i frågan.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Minska antalet kolumner som hämtas

Eftersom Azure Datautforskaren är ett data lager i kolumner, är hämtning av alla kolumner oberoende av de andra. Antalet kolumner som hämtas direkt påverkar den totala data volymen. Du bör bara inkludera de kolumner i utdata som behövs för att [sammanfatta](/azure/kusto/query/summarizeoperator) resultaten eller [projicera](/azure/kusto/query/projectoperator) de angivna kolumnerna. Azure Datautforskaren har flera optimeringar för att minska antalet hämtade kolumner. Om den avgör att en kolumn inte behövs, till exempel om den inte refereras till i kommandot [sammanfatta](/azure/kusto/query/summarizeoperator) , kommer den inte att hämta den.

Den andra frågan kan till exempel bearbeta tre gånger mer data eftersom den måste hämta en kolumn utan tre:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Tidsintervallet för den bearbetade frågan

Alla loggar i Azure Monitor loggar partitioneras enligt kolumnen **TimeGenerated** . Antalet partitioner som nås är direkt relaterat till tidsintervallet. Att minska tidsintervallet är det mest effektiva sättet att säkerställa en fråga om att köra frågor.

Tidsintervallet kan anges med hjälp av tidsintervalls väljaren på Log Analytics skärmen enligt beskrivningen i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](scope.md#time-range). Detta är den rekommenderade metoden eftersom det valda tidsintervallet skickas till Server delen med hjälp av metadata för frågan. 

En alternativ metod är att uttryckligen inkludera ett [WHERE](/azure/kusto/query/whereoperator) -villkor för **TimeGenerated** i frågan. Du bör använda den här metoden som försäkrar att tidsintervallet är fast, även om frågan används från ett annat gränssnitt.
Se till att alla delar av frågan har **TimeGenerated** -filter. När en fråga har under frågor som hämtar data från olika tabeller eller samma tabell måste båda innehålla sina egna [WHERE](/azure/kusto/query/whereoperator) -villkor.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Se till att alla under frågor har TimeGenerated filter

I följande fråga, till exempel, medan **prestanda** tabellen bara genomsöks under den senaste dagen genomsöks tabellen **pulsslag** för all historik, vilket kan vara upp till två år:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Ett vanligt fall där ett sådant fel inträffar är när [arg_max ()](/azure/kusto/query/arg-max-aggfunction) används för att hitta den senaste förekomsten. Exempel:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Detta kan enkelt korrigeras genom att lägga till ett tids filter i den inre frågan:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Ett annat exempel på det här felet är när du utför omfångs filtreringen strax efter en [union](/azure/kusto/query/unionoperator?pivots=azuremonitor) över flera tabeller. När du utför en union ska varje under fråga begränsas. Du kan använda [let](/azure/kusto/query/letstatement) -instruktionen för att garantera en konsekvent omfattning.

Följande fråga genomsöker t. ex. alla data i *heartbeat* -och *perf* -tabellerna, inte bara de senaste 1 dagarna:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Den här frågan bör korrigeras enligt följande:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Begränsningar för tids periodens mått

Måttet är alltid större än den faktiska tiden som angetts. Om filtret i frågan till exempel är 7 dagar kan systemet Skanna 7,5 eller 8,1 dagar. Detta beror på att systemet partitionerar data i segment i varierande storlek. För att säkerställa att alla relevanta poster genomsöks, genomsöks hela partitionen som kan se flera timmar och ännu mer än en dag.

Det finns flera fall där systemet inte kan tillhandahålla en korrekt mätning av tidsintervallet. Detta händer i de flesta fall där frågans intervall är mindre än en dag eller i frågor med flera arbets ytor.


> [!IMPORTANT]
> Den här indikatorn visar endast data som bearbetas i det omedelbara klustret. I frågor med flera regioner representerar den bara en av regionerna. I frågor med flera arbets ytor kan det hända att den inte innehåller alla arbets ytor.

## <a name="age-of-processed-data"></a>Ålder på bearbetade data
Azure Datautforskaren använder flera lagrings nivåer: minnes intern, lokal SSD-diskar och mycket långsammare Azure-blobbar. Ju högre data, desto högre är chansen att den lagras på en mer presterande nivå med mindre latens, vilket minskar frågans varaktighet och CPU. Förutom själva data, innehåller systemet också en cache för metadata. De äldre data, som är mindre chansen att cachelagra metadata.

Även om vissa frågor kräver användning av gamla data finns det fall där gamla data används av misstag. Detta händer när frågor körs utan att tillhandahålla tidsintervall i sina meta-data och inte alla tabell referenser inkluderar filter i kolumnen **TimeGenerated** . I dessa fall kommer systemet att söka igenom alla data som lagras i tabellen. När data kvarhållning är lång kan det avse långa tidsintervall och därmed är data som är så gamla som data lagrings perioden.

Sådana fall kan till exempel vara:

- Det går inte att ange tidsintervallet i Log Analytics med en under fråga som inte är begränsad. Se exemplet ovan.
- Med hjälp av API: et utan tidsintervallet valfria parametrar.
- Använda en klient som inte tvingar fram ett tidsintervall som Power BI-anslutningen.

Se exempel och anteckningar i avsnittet tidigare, eftersom de också är relevanta i det här fallet.

## <a name="number-of-regions"></a>Antal regioner
Det finns flera situationer där en enskild fråga kan köras i olika regioner:

- När flera arbets ytor anges explicit och de finns i olika regioner.
- När en resurs omfattnings fråga hämtar data och data lagras i flera arbets ytor som finns i olika regioner.

Fråga om flera regioner kräver att systemet serialiseras och överförs i Server delens stora delar av mellanliggande data som vanligt vis är mycket större än resultatet av den slutliga frågan. Det begränsar också systemets möjlighet att utföra optimeringar, heuristik och använda cacheminnen.
Om det inte finns någon verklig anledning att söka igenom alla dessa regioner bör du justera omfattningen så att den täcker färre regioner. Om resurs omfånget är minimerat men fortfarande många regioner används kan det bero på en felaktig konfiguration. Till exempel skickas gransknings loggar och diagnostikinställningar till olika arbets ytor i olika regioner eller så finns det flera konfigurationer för diagnostiska inställningar. 

> [!IMPORTANT]
> När en fråga körs i flera regioner är processor-och data mätningarna inte korrekta och kommer endast att representera måttet på en av regionerna.

## <a name="number-of-workspaces"></a>Antal arbets ytor
Arbets ytor är logiska behållare som används för att särskilja och administrera loggar data. Server delen optimerar arbets ytans placeringar på fysiska kluster i den valda regionen.

Användning av flera arbets ytor kan resultera i följande: 

- Där flera arbets ytor anges explicit.
- När en fråga för resurs omfång hämtar data och data lagras i flera arbets ytor.
 
Körning av frågor mellan regioner och flera kluster kräver att systemet kan serialiseras och överföras i Server delens stora delar av mellanliggande data som vanligt vis är mycket större än resultatet av den slutliga frågan. Det begränsar också systemets möjlighet att utföra optimeringar, heuristik och användning av cacheminnen.

> [!IMPORTANT]
> I vissa scenarier med flera arbets ytor är processor-och data mätningarna inte korrekta och kommer endast att representera mätningarna till några av arbets ytorna.

## <a name="parallelism"></a>Parallellitet
Azure Monitor loggar använder stora kluster av Azure Datautforskaren för att köra frågor och dessa kluster varierar i skala, vilket kan få upp till dussin tals datornoder. Systemet skalar automatiskt klustret enligt logik och kapacitet för arbets ytan.

För att effektivt köra en fråga är den partitionerad och distribuerad till Compute-noder baserat på de data som krävs för bearbetningen. Det finns vissa situationer där systemet inte kan göra detta effektivt. Detta kan leda till att frågan tar lång tid. 

Fråge beteenden som kan minska parallellitet är:

- Användning av serialisering och fönster funktioner, till exempel [serializer-operatorn](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [föreg ()](/azure/kusto/query/prevfunction)och [rad](/azure/kusto/query/rowcumsumfunction) funktionerna. Tids serier och användar analys funktioner kan användas i vissa av dessa fall. Ineffektiv serialisering kan också inträffa om följande operatorer inte används i slutet av frågan: [Range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-Hitters](/azure/kusto/query/tophittersoperator), [Get schema](/azure/kusto/query/getschemaoperator).
-    Användning av funktionen [DAntal ()](/azure/kusto/query/dcount-aggfunction) agg regering tvingar systemet att ha en central kopia av de distinkta värdena. När data skalan är hög kan du överväga att använda DAntal-funktionen valfria parametrar för att minska noggrannheten.
-    I många fall sänker [kopplings](/azure/kusto/query/joinoperator?pivots=azuremonitor) operatorn den övergripande parallellitet. Undersök blanda koppling som ett alternativ när prestanda är problematisk.
-    I resurs omfattnings frågor kan de förköra RBAC-kontrollerna Linger i situationer där det är mycket stort antal roll tilldelningar i Azure. Detta kan leda till längre kontroller som leder till lägre parallellitet. En fråga körs till exempel på en prenumeration där det finns tusentals resurser och varje resurs har många roll tilldelningar på resurs nivå, inte på prenumerationen eller resurs gruppen.
-    Om en fråga bearbetar små mängder data, kommer dess parallellitet att vara låg eftersom systemet inte sprider det över flera datornoder.



## <a name="next-steps"></a>Nästa steg

- [Referens dokumentation för Kusto-frågespråket](/azure/kusto/query/).
