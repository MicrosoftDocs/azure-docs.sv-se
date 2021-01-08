---
title: Azure Stream Analytics-lösningsmönster
description: Lär dig mer om vanliga lösnings mönster för Azure Stream Analytics, till exempel instrument paneler, händelse meddelanden, data lager, referens data och övervakning.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020323"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics-lösningsmönster

Precis som många andra tjänster i Azure används Stream Analytics bäst med andra tjänster för att skapa en större lösning från slut punkt till slut punkt. I den här artikeln beskrivs enkla Azure Stream Analytics lösningar och olika arkitektur mönster. Du kan bygga vidare på dessa mönster för att utveckla mer komplexa lösningar. Mönstren som beskrivs i den här artikeln kan användas i många olika scenarier. Exempel på scenario-/regionsspecifika mönster finns tillgängliga i [Azures lösnings arkitekturer](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Skapa ett Stream Analytics jobb för att få en real tids instrument panel

Med Azure Stream Analytics kan du snabbt hitta instrument paneler och aviseringar i real tid. En enkel lösning matar in händelser från Event Hubs eller IoT Hub och [feeds Power BI instrument panelen med en strömmande data uppsättning](/power-bi/service-real-time-streaming). Mer information finns i den detaljerade självstudien [analysera falska anrops data med Stream Analytics och visualisera resultat i Power BI instrument panel](stream-analytics-real-time-fraud-detection.md).

![ASA Power BI instrument panel](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Den här lösningen kan skapas på bara några minuter från Azure Portal. Det finns ingen omfattande kod och SQL-språket används för att uttrycka affärs logiken.

Det här lösnings mönstret ger den lägsta svars tiden från händelse källan till Power BI instrument panelen i en webbläsare. Azure Stream Analytics är den enda Azure-tjänsten med den här inbyggda funktionen.

## <a name="use-sql-for-dashboard"></a>Använd SQL för instrument panelen

Power BI instrument panelen har låg latens, men kan inte användas för att skapa fullständiga fullfjädrade Power BI-rapporter. Ett gemensamt rapporterings mönster är att mata ut dina data till SQL Database först. Använd sedan Power BI SQL-anslutning för att fråga SQL efter den senaste informationen.

![SQL-instrumentpanel för ASA](media/stream-analytics-solution-patterns/sql-dashboard.png)

Om du använder SQL Database får du mer flexibilitet, men på kostnaden för en något högre latens. Den här lösningen är optimal för jobb med latens krav som är större än en sekund. Med den här metoden kan du maximera Power BI-funktioner för att ytterligare segmentera och tärninga data för rapporter och mycket fler visualiserings alternativ. Du får också flexibiliteten att använda andra instrument panels lösningar, till exempel Tableau.

SQL är inte ett data lager med hög data flöde. Det maximala data flödet som SQL Database från Azure Stream Analytics är för närvarande cirka 24 MB/s. Om händelse källorna i lösningen ger data till en högre taxa måste du använda bearbetnings logik i Stream Analytics för att minska utmatnings hastigheten till SQL. Metoder som filtrering, fönster mängd, mönster matchning med temporala kopplingar och analys funktioner kan användas. Utmatnings hastigheten till SQL kan optimeras ytterligare med hjälp av tekniker som beskrivs i [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Införliva insikter i dina program i real tid med event Messaging

Den andra populäraste användningen av Stream Analytics är att generera aviseringar i real tid. I det här lösnings mönstret kan affärs logik i Stream Analytics användas för att identifiera [temporala och spatiala mönster](stream-analytics-geospatial-functions.md) eller [avvikelser](stream-analytics-machine-learning-anomaly-detection.md), och sedan skapa aviserings signaler. Men till skillnad från den instrument panels lösning där Stream Analytics använder Power BI som önskad slut punkt, kan ett antal mellanliggande data mottagare användas. Dessa mottagare omfattar Event Hubs, Service Bus och Azure Functions. Som Application Builder måste du bestämma vilken data mottagare som fungerar bäst för ditt scenario.

Konsument logiken för underordnade händelser måste implementeras för att generera aviseringar i ditt befintliga affärs arbets flöde. Eftersom du kan implementera anpassad logik i Azure Functions är Azure Functions det snabbaste sättet som du kan utföra den här integrationen. En själv studie kurs om hur du använder Azure Function som utdata för ett Stream Analytics jobb finns i [kör Azure Functions från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md). Azure Functions stöder också olika typer av meddelanden, inklusive text och e-post. Logic app kan också användas för sådan integrering, med Event Hubs mellan Stream Analytics-och Logic-appen.

![ASA Event Messaging-appen](media/stream-analytics-solution-patterns/event-messaging-app.png)

Event Hubs erbjuder å andra sidan den mest flexibla integrations punkten. Många andra tjänster, t. ex. Azure Datautforskaren och Time Series Insights kan använda händelser från Event Hubs. Tjänster kan vara direkt anslutna till Event Hubs mottagare från Azure Stream Analytics för att slutföra lösningen. Event Hubs är också den högsta tillgängliga meddelande utströmnings tjänsten på Azure för sådana integrerings scenarier.

## <a name="dynamic-applications-and-websites"></a>Dynamiska program och webbplatser

Du kan skapa anpassade visualiseringar i real tid, till exempel instrument panel eller kart visualisering, med hjälp av Azure Stream Analytics-och Azure SignalR-tjänsten. Med hjälp av SignalR kan webb klienter uppdateras och Visa dynamiskt innehåll i real tid.

![ASA dynamisk app](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Införliva insikter i ditt program i real tid via data lager

De flesta webb tjänster och webb program använder idag ett fråge-/svars mönster för att betjäna presentations lagret. Mönstret för begäran/svar är enkelt att bygga och kan enkelt skalas med låg svars tid med hjälp av en tillstånds lös klient dels-och skalbara lager, t. ex. Cosmos DB.

Hög data volym skapar ofta prestanda Flask halsar i ett CRUD-baserat system. [Lösnings mönstret för händelse källor](/azure/architecture/patterns/event-sourcing) används för att åtgärda prestanda Flask halsar. Temporala mönster och insikter är också svåra och ineffektiva att extrahera från ett traditionellt data lager. Moderna data drivna program med hög volym använder ofta en data flödes-baserad arkitektur. Azure Stream Analytics som beräknings motor för data i rörelse är en grundbult i den arkitekturen.

![ASA-appen för händelse källa](media/stream-analytics-solution-patterns/event-sourcing-app.png)

I det här lösnings mönstret bearbetas händelser och sammanställs i data lager med Azure Stream Analytics. Program lagret interagerar med data lager med hjälp av det traditionella mönstret för begäran/svar. På grund av Stream Analyticss möjlighet att bearbeta ett stort antal händelser i real tid, är programmet mycket skalbart utan att du behöver skapa data lager skiktet. Data lager lagret är i grunden en materialiserad vy i systemet. [Azure Stream Analytics utdata till Azure Cosmos DB](stream-analytics-documentdb-output.md) beskriver hur Cosmos DB används som en Stream Analytics utdata.

I verkliga program där bearbetnings logiken är komplex och det finns behov av att uppgradera vissa delar av logiken oberoende av varandra kan flera Stream Analytics jobb sammanställas tillsammans med Event Hubs som händelse koordinator för den förmedlande tjänsten.

![ASA komplex händelse källa, app](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Det här mönstret förbättrar systemets återhämtning och hanterbarhet. Men även om Stream Analytics garanterar exakt en gång efter bearbetningen, finns det en liten chans att dubbletter av händelser kan hamna i den mellanliggande Event Hubs. Det är viktigt att det underordnade Stream Analytics jobbet avduplicerar händelser med hjälp av logik nycklar i ett lookback-fönster. Mer information om händelse leverans finns i referens för [händelse leverans garantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Använda referens data för program anpassning

Funktionen Azure Stream Analytics referens data har utformats specifikt för anpassning av slutanvändare, t. ex. aviserings tröskel, bearbetnings regler och [avgränsningar](geospatial-scenarios.md). Program lagret kan acceptera parameter ändringar och lagra dem i SQL Database. Stream Analyticss jobbet begär regelbundet ändringar från databasen och gör anpassnings parametrarna tillgängliga via en referens data koppling. Mer information om hur du använder referens data för program anpassning finns i referens data för [SQL](sql-reference-data.md) och [referens data anslutning](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Det här mönstret kan också användas för att implementera en regel motor där regelns tröskelvärden definieras från referens data. Mer information om regler finns [i processen konfigurations bara tröskel-baserade regler i Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA-referens data program](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Lägg till Machine Learning i real tids insikter

Azure Stream Analytics inbyggd [avvikelse identifierings modell](stream-analytics-machine-learning-anomaly-detection.md) är ett bekvämt sätt att introducera Machine Learning för ditt program i real tid. En större mängd Machine Learning behov finns i [Azure Stream Analytics integreras med Azure Machine Learning bedömnings tjänst](stream-analytics-machine-learning-integration-tutorial.md).

För avancerade användare som vill införliva onlineutbildning och värdera till samma Stream Analytics pipeline, se det här exemplet på hur gör det med [linjär regression](stream-analytics-high-frequency-trading.md).

![ASA Machine Learning app](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Data lager hantering i real tid

Ett annat vanligt mönster är data lager i real tid, även kallat strömnings informations lager. Förutom händelser som anländer till Event Hubs och IoT Hub från ditt program kan [Azure Stream Analytics som körs på IoT Edge](stream-analytics-edge.md) användas för att uppfylla data rengöring, data reducering och data lagring och vidarebefordran. Stream Analytics som körs på IoT Edge kan hantera bandbredds begränsning och anslutnings problem på ett smidigt sätt i systemet. Stream Analytics kan stödja data flödes hastigheter på upp till 200 MB per sekund vid skrivning till Azure Synapse Analytics.

![Data lager hantering för ASA](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Arkivera real tids data för analys

De flesta data vetenskaps-och analys aktiviteter sker fortfarande offline. Data kan arkiveras genom att Azure Stream Analytics via Azure Data Lake Store Gen2 utdata och Parquet. Den här funktionen tar bort friktionen för att mata in data direkt i Azure Data Lake Analytics, Azure Databricks och Azure HDInsight. Azure Stream Analytics används som en nära real tids ETL-motor i den här lösningen. Du kan utforska arkiverade data i Data Lake med olika beräknings motorer.

> [!div class="mx-imgBorder"]
> ![ASA offline Analytics](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Använd referens data för berikning

Data berikning är ofta ett krav för ETL-motorer. Azure Stream Analytics stöder data anrikning med [referens data](stream-analytics-use-reference-data.md) från både SQL Database och Azure Blob Storage. Data berikning kan göras för data landning i både Azure Data Lake och Azure Synapse Analytics.


![ASA offline Analytics med data anrikning](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Operationalisera insikter från arkiverade data

Om du kombinerar offline Analytics-mönstret med nära real tids program mönster, kan du skapa en feedback-slinga. Med feedback-slingan kan programmet justeras automatiskt för att ändra mönster i data. Den här feedback-slingan kan vara så enkel som att ändra tröskelvärdet för avisering, eller så komplex som att träna Machine Learning modeller. Samma lösnings arkitektur kan tillämpas på båda ASA-jobben som körs i molnet och på IoT Edge.

![ASA Insights driftsättning](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Så här övervakar du ASA-jobb

Ett Azure Stream Analytics jobb kan köras 24/7 för att bearbeta inkommande händelser kontinuerligt i real tid. Drift tiden är avgörande för hälsan för det övergripande programmet. Även om Stream Analytics är den enda streaming Analytics-tjänsten i branschen och erbjuder en  [garanti på 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), kan du fortfarande stöta på en viss nivå av drift tid. Under åren har Stream Analytics infört statistik, loggar och jobb tillstånd för att avspegla jobbens hälso tillstånd. Alla är anslutna via Azure Monitor-tjänsten och kan exporteras ytterligare till OMS. Mer information finns i [förstå Stream Analytics jobb övervakning och övervaka frågor](stream-analytics-monitoring.md).

![ASA-övervakning](media/stream-analytics-solution-patterns/monitoring.png)

Det finns två viktiga saker att övervaka:

- [Status för misslyckat jobb](job-states.md)

    Först och främst måste du kontrol lera att jobbet körs. Utan jobbet i körnings tillstånd genereras inga nya mått eller loggar. Jobb kan ändras till ett fel tillstånd av olika anledningar, inklusive att ha en hög SU-utgångs nivå (t. ex. slut på resurser).

- [Fördröjnings mått för vattenstämpel](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Måttet visar hur långt bakom bearbetnings pipelinen är i väggens Klock tid (sekunder). En del av fördröjningen hänförs till den olika bearbetnings logiken. Därför är det mycket viktigare att övervaka den ökande trenden än att övervaka det absoluta värdet. Den stabila tillstånds fördröjningen bör åtgärdas av din program design, inte av övervakning eller aviseringar.

Vid fel är aktivitets [loggar och diagnostikloggar](stream-analytics-job-diagnostic-logs.md) de bästa platserna för att börja söka efter fel.

## <a name="build-resilient-and-mission-critical-applications"></a>Bygg elastiska och verksamhets kritiska program

Oavsett Azure Stream Analytics "SLA-garantin och hur noggrant du kör ditt program från slut punkt till slut punkt inträffar drifts störningar. Om ditt program är verksamhets kritiskt måste du förbereda för drift störningar för att kunna återställas på ett smidigt sätt.

För aviserings program är det viktigaste att identifiera nästa avisering. Du kan välja att starta om jobbet från den aktuella tiden vid återställning, ignorera tidigare aviseringar. Jobbets start tid är av den första gången, inte den första ingångs tiden. Indatan spolas tillbaka till en lämplig tids period för att garantera att den första utmatningen vid den angivna tiden är fullständig och korrekt. Du får inte partiella mängder och utlöser aviseringar oväntade som ett resultat.

Du kan också välja att starta utdata från en viss tid tidigare. Både Event Hubs och IoT Hubs bevarande principer innehåller en rimlig mängd data som tillåter bearbetning tidigare. Kompromissen är hur snabbt du kan fånga upp till den aktuella tiden och börja generera nya aviseringar i rätt tid. Data förlorar sitt värde snabbt med tiden, så det är viktigt att snabbt komma igång med den aktuella tiden. Det finns två sätt att komma igång snabbt:

- Etablera fler resurser (SU) när de fångas upp.
- Starta om från aktuell tid.

Det är enkelt att starta om från den aktuella tiden, med kompromissen att lämna en lucka under bearbetningen. Att starta om på det här sättet kan vara OK för aviserings scenarier, men det kan vara problematiskt för instrument panels scenarier och är ett icke-startläge för arkivering och data lager scenarier.

Etableringen av fler resurser kan påskynda processen, men effekterna av överföringshastigheten är komplexa.

- Testa att jobbet är skalbart till ett större antal SUs. Alla frågor är inte skalbara. Du måste kontrol lera att frågan är [parallellt](stream-analytics-parallelization.md).

- Se till att det finns tillräckligt med partitioner i den överordnade Event Hubs eller IoT Hub att du kan lägga till fler data flödes enheter (antal) för att skala indata-genomflödet. Kom ihåg att varje Event Hubs data flödes enheter maxas ut med en utgångs takt på 2 MB/s.

- Kontrol lera att det finns tillräckligt med resurser i utgående mottagare (t. ex. SQL Database Cosmos DB), så att de inte begränsar överbelastningen i utdata, vilket ibland kan leda till att systemet låser sig.

Det viktigaste är att förutse bearbetnings takts ändringen, testa de här scenarierna innan du går i produktion och är redo att skala bearbetningen korrekt under haveri återställnings tiden.

I det extrema scenario som inkommande händelser är fördröjt, [är det möjligt att alla fördröjda händelser släpps](stream-analytics-time-handling.md) om du har använt ett sent tillkommande fönster för jobbet. Det kan verka som om händelsen släpps ut för att vara ett mystiska beteende i början. att överväger Stream Analytics är en real tids bearbetnings motor, men det förväntar sig att inkommande händelser ska ligga nära väggens klock klock klocka. Det måste ta bort händelser som strider mot dessa begränsningar.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda-arkitekturer eller bakfyllnings process

Lyckligt vis kan tidigare data Arkiv mönster användas för att bearbeta dessa sena händelser på ett smidigt sätt. Idén är att arkiverings jobbet bearbetar inkommande händelser vid tiden och arkiverar händelser i rätt tidsgrupp i Azure Blob eller Azure Data Lake Store med händelse tiden. Det spelar ingen roll hur sent en händelse kommer till. den tas aldrig bort. Den hamnar alltid inom rätt tidsgrupp. Under återställningen är det möjligt att bearbeta arkiverade händelser och fylla resultaten till valfri butik. Detta liknar hur lambda-mönster implementeras.

![ASA-fyllning](media/stream-analytics-solution-patterns/back-fill.png)

Den här processen måste göras med ett system för offline-batchbearbetning, som förmodligen har en annan programmerings modell än Azure Stream Analytics. Det innebär att du måste implementera hela bearbetnings logiken igen.

För att kunna fylla på, är det fortfarande viktigt att minst tillfälligt etablera mer resurs till utgående mottagare för att hantera högre data flöde än de stabila tillstånds bearbetnings behoven.

|Scenarier  |Starta om från nu  |Starta om från senast stoppad tid |Starta om från nu + Fyll med arkiverade händelser|
|---------|---------|---------|---------|
|**Instrument panel**   |Skapar mellanrum    |OK för kort avbrott    |Använd för långvarigt avbrott |
|**Aviseringar**   |Användas |OK för kort avbrott    |Inte nödvändigt |
|**App för händelse källa** |Användas |OK för kort avbrott    |Använd för långvarigt avbrott |
|**Data lager hantering**   |Dataförluster  |Användas |Inte nödvändigt |
|**Offline-analys**  |Dataförluster  |Användas |Inte nödvändigt|

## <a name="putting-it-all-together"></a>Färdigställa allt

Det är inte svårt att tro att alla lösnings mönster som nämns ovan kan kombineras tillsammans i ett komplext slut punkt till slut punkt-system. Det kombinerade systemet kan omfatta instrument paneler, aviseringar, händelse käll program, data lager och funktioner för offline-analys.

Nyckeln är att utforma systemet i sammanställnings bara mönster, så att varje under system kan skapas, testas, uppgraderas och återställas separat.

## <a name="next-steps"></a>Nästa steg

Nu har du sett en rad olika lösnings mönster med Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
