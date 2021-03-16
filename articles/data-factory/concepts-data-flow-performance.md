---
title: Mappa prestanda-och justerings guide för data flöde
description: Lär dig mer om viktiga faktorer som påverkar prestanda för att mappa data flöden i Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/15/2021
ms.openlocfilehash: dd5b857c274e757f70920f244786df61c2770085
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561693"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Prestanda-och justerings guiden för att mappa data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Att mappa data flöden i Azure Data Factory tillhandahålla ett kod fritt gränssnitt för att utforma och köra data transformationer i stor skala. Om du inte är bekant med att mappa data flöden kan du läsa [Översikt över kart data flödet](concepts-data-flow-overview.md). I den här artikeln beskrivs olika sätt att finjustera och optimera dina data flöden så att de uppfyller dina prestanda mått.

Titta på videon nedan och se några exempel på tids inställningar som omvandlar data med data flöden.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Testa data flödes logik

När du designar och testar data flöden från ADF-UX kan du i fel söknings läge interaktivt testa mot ett aktivt Spark-kluster. På så sätt kan du förhandsgranska data och köra dina data flöden utan att vänta på att ett kluster ska värmas upp. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Prestanda för övervakning av data flöde

När du har verifierat din omvandlings logik med fel söknings läge kör du ditt data flöde från slut punkt till slut punkt som en aktivitet i en pipeline. Data flöden fungerar i en pipeline med hjälp av [aktiviteten kör data flöde](control-flow-execute-data-flow-activity.md). Data flödes aktiviteten har en unik övervaknings upplevelse jämfört med andra Azure Data Factory aktiviteter som visar en detaljerad körnings plan och prestanda profil för omvandlings logiken. Om du vill visa detaljerad övervaknings information för ett data flöde klickar du på glasögon-ikonen i aktiviteten kör utdata för en pipeline. Mer information finns i [övervaka mappning av data flöden](concepts-data-flow-monitoring.md).

![Data flödes övervakare](media/data-flow/monitoring-details.png "Data flödes övervakare 2")

När du övervakar data flödes prestanda finns det fyra möjliga Flask halsar att se ut för:

* Kluster start tid
* Läser från en källa
* Omvandlings tid
* Skriver till en mottagare 

![Dataflödesövervakning](media/data-flow/monitoring-performance.png "Data flödes övervakare 3")

Kluster start tid är den tid det tar att skapa ett Apache Spark-kluster. Det här värdet finns i det övre högra hörnet på övervaknings skärmen. Data flöden körs på en just-in-Time-modell där varje jobb använder ett isolerat kluster. Den här start tiden tar vanligt vis 3-5 minuter. För sekventiella jobb kan detta minskas genom att ett TTL-värde aktive ras. Mer information finns i [optimera Azure integration runtime](#ir).

Data flöden använder en spark-optimering som ordnar om och kör affärs logiken i "faser" för att utföra så snabbt som möjligt. För varje mottagare som data flödet skriver till visar övervaknings resultatet varaktigheten för varje omvandlings steg, tillsammans med den tid det tar att skriva data till mottagaren. Tiden som är störst är sannolikt Flask halsen för ditt data flöde. Om det omvandlings steg som tar störst innehåller en källa kanske du vill titta närmare på att optimera din läsnings tid. Om en omvandling tar lång tid kan du behöva partitionera om eller öka storleken på integrerings körningen. Om bearbetnings tiden för mottagaren är stor kan du behöva skala upp databasen eller kontrol lera att du inte lägger till en enskild fil.

När du har identifierat Flask halsen för ditt data flöde kan du använda följande optimerings strategier för att förbättra prestanda.

## <a name="optimize-tab"></a>Fliken Optimera

Fliken **Optimize** innehåller inställningar för att konfigurera partitionerings schema för Spark-klustret. Den här fliken finns i varje omvandling av data flöde och anger om du vill partitionera om data **efter** att omvandlingen har slutförts. Genom att justera partitionering får du kontroll över distributionen av dina data över Compute-noder och optimeringar av data lokaler som kan ha både positiva och negativa effekter på dina övergripande data flödes prestanda.

![Skärm bild som visar fliken optimering, som innehåller partitionsalternativ, partitionstyper och antalet partitioner.](media/data-flow/optimize.png)

Som standard är *Använd aktuell partitionering* markerad, vilket instruerar Azure Data Factory att behålla den aktuella utdata-partitionering av omvandlingen. Eftersom ompartitionering av data tar tid rekommenderas *Använd nuvarande partitionering* i de flesta fall. Scenarier där du kanske vill partitionera om dina data är efter AGG regeringar och kopplingar som påtagligt snedställer data eller när du använder käll partitionering på en SQL-databas.

Om du vill ändra partitionering för en omvandling väljer du fliken **optimera** och väljer alternativ knappen **Ange partitionering** . Du får en serie alternativ för partitionering. Den bästa metoden för partitionering skiljer sig beroende på dina data volymer, kandidat nycklar, null-värden och kardinalitet. 

> [!IMPORTANT]
> En enskild partition kombinerar alla distribuerade data till en enda partition. Det här är en mycket långsam åtgärd som också påverkar all underordnad omvandling och skrivning. Azure Data Factory starkt rekommenderar att du använder det här alternativet om det inte finns ett explicit affärs skäl.

Följande partitionerings alternativ är tillgängliga i varje omvandling:

### <a name="round-robin"></a>Resursallokering 

Round Robin distribuerar data jämnt mellan partitioner. Använd Round-Robin när du inte har bra viktiga kandidater för att implementera en solid, smart partitionerings strategi. Du kan ange antalet fysiska partitioner.

### <a name="hash"></a>Hash

Azure Data Factory skapar en hash av kolumner för att skapa enhetliga partitioner, så att rader med liknande värden hamnar i samma partition. När du använder hash-alternativet kan du testa om det finns en sned partition. Du kan ange antalet fysiska partitioner.

### <a name="dynamic-range"></a>Dynamiskt intervall

Det dynamiska intervallet använder Spark-dynamiska intervall baserat på de kolumner eller uttryck som du anger. Du kan ange antalet fysiska partitioner. 

### <a name="fixed-range"></a>Fast intervall

Bygg ett uttryck som ger ett fast intervall för värden i dina partitionerade data kolumner. För att undvika separering av partitionen bör du ha en god förståelse för dina data innan du använder det här alternativet. De värden som du anger för uttrycket används som en del av en partitions funktion. Du kan ange antalet fysiska partitioner.

### <a name="key"></a>Nyckel

Om du har en god förståelse för data kardinalitet kan nyckel partitionering vara en god strategi. Med nyckel partitionering skapas partitioner för varje unikt värde i kolumnen. Du kan inte ange antalet partitioner eftersom antalet baseras på unika värden i data.

> [!TIP]
> Genom att ange partitionerings schema manuellt kan du blanda data och förskjuta fördelarna med Spark optimering. Vi rekommenderar att du inte anger partitionering manuellt om du inte behöver det.

## <a name="logging-level"></a>Loggnings nivå

Om du inte behöver varje pipeline-körning av dina data flödes aktiviteter för att fullständigt logga alla utförliga telemetri loggar kan du ange loggnings nivån till "Basic" eller "none". När du kör dina data flöden i "VERBOSE"-läge (standard) begär du att ADF ska logga in fullständigt på varje enskild partitions nivå under din data omvandling. Detta kan vara en dyr åtgärd, så att bara aktivera utförligt läge när fel sökning kan förbättra ditt totala data flöde och prestanda för pipelinen. "Grundläggande"-läget loggar bara omvandlings varaktigheter medan "ingen" bara innehåller en sammanfattning av varaktigheter.

![Loggnings nivå](media/data-flow/logging.png "Ange loggnings nivå")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Optimera Azure Integration Runtime

Data flöden körs i Spark-kluster som är i körnings läge. Konfigurationen för det använda klustret definieras i aktivitetens integration Runtime (IR). Det finns tre prestanda saker att göra när du definierar integration Runtime: kluster typ, kluster storlek och tid till Live.

Mer information om hur du skapar en Integration Runtime finns [i integration runtime i Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Kluster typ

Det finns tre tillgängliga alternativ för typen av Spark-kluster: generell användning, minnesoptimerade och Compute-optimerad.

**Generella användnings** kluster är standard valet och är perfekt för de flesta arbets belastningar för data flöde. Dessa tenderar att bli bästa möjliga balans mellan prestanda och kostnad.

Om ditt data flöde har många kopplingar och uppslag, kanske du vill **använda ett minnesoptimerade** kluster. Minnesoptimerade kluster kan lagra mer data i minnet och minimera eventuella slut på minnes fel som du kan få. Minnesoptimerade har det högsta pris-/Core-priset, men det kan också leda till en mer framgångs rik pipeline. Om du får slut på minnes fel när du kör data flöden växlar du till en minnes optimerad Azure IR konfiguration. 

**Compute-optimerad** är inte idealisk för ETL-arbetsflöden och rekommenderas inte av Azure Data Factory-teamet för de flesta produktions arbets belastningar. För enklare icke-minnes intensiva data transformationer som att filtrera data eller lägga till härledda kolumner, kan Compute-optimerade kluster användas till ett billigare pris per kärna.

### <a name="cluster-size"></a>Kluster storlek

Data flöden distribuerar data bearbetningen över olika noder i ett Spark-kluster för att utföra åtgärder parallellt. Ett Spark-kluster med fler kärnor ökar antalet noder i beräknings miljön. Fler noder ökar bearbetnings kraften hos data flödet. Att öka kluster storleken är ofta ett enkelt sätt att minska bearbetnings tiden.

Standard kluster storleken är fyra driv rutins noder och fyra arbetsnoder.  När du bearbetar mer data rekommenderas större kluster. Nedan visas möjliga alternativ för storleks ändring:

| Arbets kärnor | Driv rutins kärnor | Totalt antal kärnor | Anteckningar |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Inte tillgängligt för beräknings optimering |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Data flöden priss ätts i vCore, vilket innebär att både kluster storlek och körnings tids faktor i detta. När du skalar upp ökar din kluster kostnad per minut, men den totala tiden minskar.

> [!TIP]
> Det finns ett tak för hur mycket kluster storleken påverkar ett data flödes prestanda. Beroende på storleken på dina data kommer det att gå att öka prestandan genom att öka storleken på ett kluster. Om du till exempel har fler noder än datapartitioner kan du inte lägga till fler noder. Ett bra tips är att starta små och skala upp för att uppfylla dina prestanda behov. 

### <a name="time-to-live"></a>Time to live

Varje data flödes aktivitet snurrar som standard ett nytt kluster baserat på IR-konfigurationen. Kluster start tiden tar några minuter och data bearbetningen kan inte starta förrän den är klar. Om pipelinen innehåller flera **sekventiella** data flöden kan du aktivera ett TTL-värde (Time to Live). Om du anger ett TTL-värde för en viss tids period, är ett kluster aktivt under en viss tids period när körningen har slutförts. Om ett nytt jobb börjar använda IR under TTL-tiden kommer det att återanvända det befintliga klustret och start tiden minskas avsevärt. När det andra jobbet har slutförts kommer klustret återigen att vara i drift för TTL-tiden.

Endast ett jobb kan köras i ett enskilt kluster i taget. Om det finns ett tillgängligt kluster, men två data flöden startar, använder bara ett aktivt kluster. Det andra jobbet kommer att skapa ett eget isolerat kluster.

Om de flesta data flöden körs parallellt rekommenderar vi inte att du aktiverar TTL. 

> [!NOTE]
> Time to Live är inte tillgängligt när integrerings körningen för automatisk lösning används

## <a name="optimizing-sources"></a>Optimera källor

För varje källa förutom Azure SQL Database rekommenderar vi att du behåller den **aktuella partitionering** som det valda värdet. Vid läsning från alla andra käll system, flödar data automatiskt partitionerar data jämnt baserat på data storleken. En ny partition skapas för ungefär var 128 MB data. När data storleken ökar ökar antalet partitioner.

Eventuell Anpassad partitionering sker *efter* att Spark har lästs in i data och påverkar prestandan för data flödet negativt. Detta rekommenderas inte eftersom data är jämnt partitionerade vid läsning. 

> [!NOTE]
> Läs hastigheten kan begränsas av data flödet i ditt käll system.

### <a name="azure-sql-database-sources"></a>Azure SQL Database källor

Azure SQL Database har ett unikt partitionerings alternativ som kallas källa partitionering. Att aktivera käll partitionering kan förbättra dina Läs tider från Azure SQL DB genom att aktivera parallella anslutningar i käll systemet. Ange antalet partitioner och hur dina data ska partitioneras. Använd en partition kolumn med hög kardinalitet. Du kan också ange en fråga som matchar partitionerings schemat i käll tabellen.

> [!TIP]
> För käll partitionering är I/O i SQL Server Flask halsen. Om du lägger till för många partitioner kan käll databasen fylla. Normalt är fyra eller fem partitioner idealiska när du använder det här alternativet.

![Käll partitionering](media/data-flow/sourcepart3.png "Käll partitionering")

#### <a name="isolation-level"></a>Isolerings nivå

Isolerings nivån för läsningen på ett Azure SQL-källdokument påverkar prestandan. Om du väljer Läs ej allokerat får du snabbast prestanda och förhindrar alla databas lås. Om du vill veta mer om isolerings nivåer för SQL kan du se [förstå isolerings nivåer](/sql/connect/jdbc/understanding-isolation-levels).

#### <a name="read-using-query"></a>Läsa med fråga

Du kan läsa från Azure SQL Database med hjälp av en tabell eller en SQL-fråga. Om du kör en SQL-fråga måste frågan slutföras innan omvandlingen kan starta. SQL-frågor kan vara användbara för push-åtgärder som kan köras snabbare och minska mängden data som läses från en SQL Server, till exempel SELECT, WHERE och JOIN-instruktioner. När du använder åtgärder förlorar du möjligheten att spåra härkomst och prestanda för omvandlingarna innan data kommer in i data flödet.

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics-källor

När du använder Azure Synapse Analytics finns det en inställning som kallas **Aktivera mellanlagring** i käll alternativen. Detta gör att ADF kan läsa från Synapse med ```Staging``` , vilket avsevärt förbättrar Läs prestanda. ```Staging```Om du aktiverar måste du ange en Azure-Blob Storage eller Azure Data Lake Storage Gen2 mellanlagrings plats i data flödets aktivitets inställningar.

![Aktivera mellanlagring](media/data-flow/enable-staging.png "Aktivera mellanlagring")

### <a name="file-based-sources"></a>Filbaserade källor

Medan data flöden stöder en mängd olika filtyper rekommenderar Azure Data Factory att använda Spark-Native Parquet-formatet för optimala Läs-och skriv tider.

Om du kör samma data flöde på en uppsättning filer rekommenderar vi att du läser från en mapp med sökvägar till jokertecken eller läser från en lista med filer. En enda körning av data flödes aktivitet kan bearbeta alla filer i batch. Mer information om hur du anger dessa inställningar finns i anslutnings dokumentationen, till exempel [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Undvik om möjligt att använda For-Each-aktivitet för att köra data flöden över en uppsättning filer. Detta medför varje iteration av for-each för att skapa ett eget Spark-kluster, vilket ofta inte är nödvändigt och kan vara dyrt. 

## <a name="optimizing-sinks"></a>Optimera mottagare

När data flödar till handfat sker eventuell Anpassad partitionering omedelbart före skrivningen. Precis som källan rekommenderar vi i de flesta fall att du behåller den **aktuella** partitionering som alternativet för den valda partitionen. Partitionerade data skrivs betydligt snabbare än opartitionerade data, även om målet inte är partitionerat. Nedan visas de enskilda övervägandena för olika typer av mottagare. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database mottagare

Med Azure SQL Database bör standard partitionering fungera i de flesta fall. Det finns en risk att din mottagare kan ha för många partitioner för att din SQL-databas ska kunna hantera. Om du kör i det här fallet minskar du antalet partitioner som anges av din SQL Database-mottagare.

#### <a name="impact-of-error-row-handling-to-performance"></a>Effekt av fel rad hantering till prestanda

När du aktiverar fel rad hantering ("Fortsätt vid fel") i Sink-omvandlingen, vidtar ADF ett ytterligare steg innan du skriver de kompatibla raderna till mål tabellen. Det här ytterligare steget kommer att ha en liten prestanda försämring som kan vara i intervallet på 5% som lagts till för det här steget med en ytterligare liten prestanda träff läggs också till om du anger alternativet till att även använda de inkompatibla raderna i en loggfil.

#### <a name="disabling-indexes-using-a-sql-script"></a>Inaktivera index med ett SQL-skript

Inaktive ring av index innan en belastning i en SQL-databas kan förbättra prestanda vid skrivning till tabellen. Kör kommandot nedan innan du skriver till din SQL-mottagare.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

När skrivningen har slutförts bygger du om indexen med följande kommando:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

De kan båda göras inbyggda med pre-och post-SQL-skript i en Azure SQL DB-eller Synapse-mottagare i mappnings data flöden.

![Inaktivera index](media/data-flow/disable-indexes-sql.png "Inaktivera index")

> [!WARNING]
> Vid inaktive ring av index tar data flödet effektiv kontroll över en databas och frågor är sannolikt inte att lyckas för tillfället. Därför utlöses många ETL-jobb mitt i natten för att undvika den här konflikten. Mer information finns i [begränsningarna vid inaktive ring av index](/sql/relational-databases/indexes/disable-indexes-and-constraints)

#### <a name="scaling-up-your-database"></a>Skala upp databasen

Schemalägg en storleks ändring för din källa och mottagar Azure SQL DB och DW innan din pipeline kör för att öka data flödet och minimera Azure-begränsningen när du når DTU-gränser. När din pipeline-körning har slutförts ändrar du storlek på databaserna till normal körnings frekvens.

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics-mottagare

När du skriver till Azure Synapse Analytics ska du kontrol lera att **Aktivera mellanlagring** är inställt på sant. Detta gör att ADF kan skriva med [kommandot SQL Copy](/sql/t-sql/statements/copy-into-transact-sql) som effektivt läser in data i bulk. Du måste referera till ett Azure Data Lake Storage Gen2-eller Azure Blob Storage-konto för att kunna mellanlagra data när du använder mellanlagring.

Förutom mellanlagring gäller samma rekommenderade metoder för Azure Synapse Analytics som Azure SQL Database.

### <a name="file-based-sinks"></a>Filbaserade handfat 

Medan data flöden stöder en mängd olika filtyper rekommenderar Azure Data Factory att använda Spark-Native Parquet-formatet för optimala Läs-och skriv tider.

Om data är jämnt distribuerade, är **Använd aktuell partitionering** det snabbaste partitionering alternativet för att skriva filer.

#### <a name="file-name-options"></a>Alternativ för fil namn

När du skriver filer kan du välja mellan olika namngivnings alternativ som påverkar prestandan.

![Mottagar alternativ](media/data-flow/file-sink-settings.png "mottagar alternativ")

Om du väljer **standard** alternativet skrivs det snabbast. Varje partition kommer att likställas med en fil med Spark-standardnamnet. Detta är användbart om du bara läser från mappen med data.

Om du anger ett namn **mönster** byts varje partition till ett mer användarvänligt namn. Den här åtgärden sker efter skrivning och är något långsammare än om du väljer standardvärdet. Per partition kan du namnge varje enskild partition manuellt.

Om en kolumn motsvarar hur du vill att data ska matas ut, kan du välja **som data i kolumnen**. Detta blandar data och kan påverka prestanda om kolumnerna inte är jämnt fördelade.

**Utdata till en enskild fil** kombinerar alla data till en enda partition. Detta leder till långa Skriv tider, särskilt för stora data uppsättningar. Azure Data Factorys teamet rekommenderar starkt att du **inte** väljer det här alternativet om det inte finns ett explicit affärs skäl.

### <a name="cosmosdb-sinks"></a>CosmosDB-mottagare

När du skriver till CosmosDB kan du förbättra prestanda genom att ändra genomflödet och batchstorleken under körningen av data flödet. Ändringarna börjar gälla under körningen av data flödes aktiviteten och återgår till de ursprungliga samlings inställningarna efter slut satsen. 

**Batchstorlek:** Normalt räcker det att börja med standard storleken på batch. Om du vill justera det här värdet ytterligare beräknar du den stora objekt storleken för dina data och kontrollerar att objekt storleken * batchstorleken är mindre än 2 MB. Om så är fallet kan du öka batchstorleken för att få bättre data flöde.

**Data flöde:** Ange en högre data flödes inställning här så att dokument kan skrivas snabbare till CosmosDB. Kom ihåg de högre RU-kostnaderna baserat på en hög data flödes inställning.

**Skriv data flödes budget:** Använd ett värde som är mindre än det totala antalet ru: er per minut. Om du har ett data flöde med ett stort antal Spark-partitioner, kan du ställa in en budget genom strömning för att öka balansen mellan dessa partitioner.

## <a name="optimizing-transformations"></a>Optimera transformeringar

### <a name="optimizing-joins-exists-and-lookups"></a>Optimera kopplingar, finns och uppslag

#### <a name="broadcasting"></a>Broadcasting

I kopplingar, sökningar och exists-transformeringar, om en eller båda data strömmarna är tillräckligt små för att passa in i arbetsnodens minne, kan du optimera prestanda genom att aktivera **sändning**. Sändning är när du skickar små data ramar till alla noder i klustret. Detta gör att Spark-motorn kan utföra en koppling utan att reshuffling data i den stora data strömmen. Som standard bestämmer Spark-motorn om en sida av en anslutning ska sändas automatiskt eller inte. Om du är bekant med inkommande data och vet att en data ström kommer att vara betydligt mindre än den andra kan du välja **fast** sändning. Fast sändning tvingar Spark att sända den valda strömmen. 

Om storleken på de distribuerade data är för stor för Spark-noden kan du få ett slut på minnes fel. Använd **minnesoptimerade** kluster för att undvika minnes fel. Om du upplever sändnings tids gränser vid körning av data flöden kan du inaktivera sändnings optimering. Detta kommer dock att leda till långsammare data flöden.

När du arbetar med data källor som kan ta längre tid, t. ex. stora databas frågor, rekommenderar vi att du stänger av sändningen för kopplingar. Källan med långa fråge tider kan orsaka Spark-tidsgräns när klustret försöker sända till Compute-noder. Ett annat bra val för att inaktivera sändning är om du har en data ström i ditt data flöde som aggregerar värden för användning i en söknings omvandling senare. Det här mönstret kan förväxla Spark-optimeringen och orsaka timeout.

![Optimering av kopplings omvandling](media/data-flow/joinoptimize.png "Delta i optimering")

#### <a name="cross-joins"></a>Kors kopplingar

Om du använder litterala värden i dina kopplings villkor eller har flera matchningar på båda sidor av en koppling, kommer Spark att köra kopplingen som en kors koppling. En kors koppling är en fullständig kartesiska-produkt som sedan filtrerar ut de kopplade värdena. Detta är betydligt långsammare än andra kopplings typer. Se till att du har kolumn referenser på båda sidor om dina kopplings villkor för att undvika prestanda påverkan.

#### <a name="sorting-before-joins"></a>Sortera före kopplingar

Till skillnad från sammanfognings koppling i verktyg som SSIS är Join-omvandlingen inte en obligatorisk sammanfognings kopplings åtgärd. Kopplings nycklarna kräver inte sortering före transformeringen. Azure Data Factory-teamet rekommenderar inte att du använder sorterings omvandlingar i mappnings data flöden.

### <a name="window-transformation-performance"></a>Fönster omvandlings prestanda

[Fönstrets transformering](data-flow-window.md) partitionerar dina data efter värde i kolumner som du väljer som en del av- ```over()``` satsen i omvandlings inställningarna. Det finns ett antal mycket populära mängd-och analys funktioner som exponeras i Windows-omvandlingen. Men om ditt användnings fall är att generera ett fönster över hela data uppsättningen för rangordning ```rank()``` eller rad nummer ```rowNumber()``` , rekommenderar vi att du i stället använder [rang omvandlingen](data-flow-rank.md) och [surrogat Key Transformation](data-flow-surrogate-key.md). Dessa omvandlingar utför bättre igen fullständig data uppsättnings åtgärder med hjälp av funktionerna.

### <a name="repartitioning-skewed-data"></a>Ompartitionering av skevade data

Vissa transformeringar, till exempel sammanfogningar och aggregat, kommer att blanda dina datapartitioner och kan ibland leda till skevade data. Skevade data innebär att data inte är jämnt fördelade över partitionerna. Mycket sneda data kan leda till långsammare omvandlingar och Sink-skrivningar. Du kan kontrol lera snedheten för dina data när som helst i en data flödes körning genom att klicka på omvandlingen i övervaknings visningen.

![Snedhet och-avvikelse](media/data-flow/skewness-kurtosis.png "Snedhet och-avvikelse")

Övervaknings visningen visar hur data fördelas på varje partition tillsammans med två mått, snedhet och värde. **Snedheten** är ett mått på hur asymmetrisk data är och kan ha ett positivt, noll, negativt eller odefinierat värde. Negativ skevning innebär att den vänstra änden är längre än höger. **Grund är** måttet på huruvida data är tungt eller ljusa. Höga värden är inte önskvärda. De perfekta intervallen för snedheten är mellan-3 och 3 och intervall med för-och-värden är mindre än 10. Ett enkelt sätt att tolka dessa tal är att titta på partitionerings diagrammet och se om 1 bar är betydligt större än resten.

Om dina data inte är jämnt partitionerade efter en omvandling kan du använda [fliken optimera](#optimize-tab) för att partitionera om. Reshuffling-data tar tid och förbättrar inte prestandan för data flödet.

> [!TIP]
> Om du partitionerar om data, men har underordnade omvandlingar som blandar dina data, använder du hash-partitionering i en kolumn som används som kopplings nyckel.

## <a name="using-data-flows-in-pipelines"></a>Använda data flöden i pipelines 

När du skapar komplexa pipelines med flera data flöden kan ditt logiska flöde ha stor inverkan på tids inställningen och kostnaden. I det här avsnittet beskrivs effekterna av olika arkitektur strategier.

### <a name="executing-data-flows-in-parallel"></a>Köra data flöden parallellt

Om du kör flera data flöden parallellt, snurrar ADF upp separata Spark-kluster för varje aktivitet. Detta gör att varje jobb kan isoleras och köras parallellt, men kommer att leda till att flera kluster körs samtidigt.

Om dina data flödar parallellt, rekommenderar vi att du inte aktiverar Azure IR Time to Live-egenskapen eftersom det leder till flera oanvända, varmt pooler.

> [!TIP]
> I stället för att köra samma data flöde flera gånger i en för varje aktivitet, ska du mellanlagra dina data i en data Lake och använda sökvägar för jokertecken för att bearbeta data i ett enda data flöde.

### <a name="execute-data-flows-sequentially"></a>Köra data flöden sekventiellt

Om du kör dina data flödes aktiviteter i följd rekommenderar vi att du anger ett TTL-värde i Azure IR-konfigurationen. ADF återanvänder beräknings resurserna som resulterar i en snabbare kluster start tid. Varje aktivitet kommer fortfarande att isoleras och ta emot en ny Spark-kontext för varje körning.

Körning av jobb sekventiellt tar sannolikt den längsta tiden att köra slut punkt till slut punkt, men ger en ren separation av logiska åtgärder.

### <a name="overloading-a-single-data-flow"></a>Överlagring av ett enskilt data flöde

Om du ger all din logik i ett enda data flöde kör ADF hela jobbet på en enda Spark-instans. Även om det kan verka som ett sätt att minska kostnaderna, blandar man ihop olika logiska flöden och kan vara svåra att övervaka och felsöka. Om en komponent Miss lyckas kommer alla andra delar av jobbet också att Miss lyckas. Azure Data Factorys teamet rekommenderar att data flöden organiseras av oberoende affärs logik. Om ditt data flöde blir för stort och du kan dela upp det i separata komponenter gör det enklare att övervaka och felsöka. Även om det inte finns någon hård gräns för antalet omvandlingar i ett data flöde, gör jobbet komplext för många.

### <a name="execute-sinks-in-parallel"></a>Kör handfat parallellt

Standard beteendet för data flödes mottagare är att köra varje mottagare sekventiellt, på ett seriellt sätt och för att inte köra data flödet när ett fel påträffas i mottagaren. Dessutom är alla Sinks som standard i samma grupp, om du inte går in i data flödes egenskaperna och anger olika prioriteter för mottagare.

Med data flöden kan du gruppera Sinks tillsammans i grupper från fliken Data flödes egenskaper i UI designer. Du kan både ställa in ordningen på körningarna av dina handfat och gruppera Sinks med samma grupp nummer. För att kunna hantera grupper kan du be ADF att köra handfat i samma grupp, för att köra parallellt.

I pipeline-aktiviteten kör data flöde under avsnittet "mottagar egenskaper" är ett alternativ för att aktivera inläsning av parallella mottagare. När du aktiverar "kör parallellt" instruerar du data flöden att skriva till anslutna mottagare samtidigt i stället för på ett sekventiellt sätt. För att kunna använda det parallella alternativet måste sinkarna grupperas samman och anslutas till samma ström via en ny gren eller villkorlig delning.

## <a name="next-steps"></a>Nästa steg

Se andra data flödes artiklar relaterade till prestanda:

- [Data flödes aktivitet](control-flow-execute-data-flow-activity.md)
- [Övervaka data flödes prestanda](concepts-data-flow-monitoring.md)
