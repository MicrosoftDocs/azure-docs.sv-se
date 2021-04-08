---
title: Använd referens data för sökningar i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder referens data för att söka efter eller korrelera data i ett Azure Stream Analytics jobbs frågans design.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99560210"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Använda referens data för sökningar i Stream Analytics

Referens data (kallas även en uppslags tabell) är en begränsad data uppsättning som är statisk eller långsamt föränderlig i natur, som används för att utföra en sökning eller för att utöka dina data strömmar. I ett IoT-scenario kan du till exempel lagra metadata om sensorer (som inte ändras ofta) i referens data och ansluta dem med IoT-dataströmmar i real tid. Azure Stream Analytics läser in referens data i minnet för att uppnå låg latens för strömnings bearbetning. Om du vill använda referens data i ditt Azure Stream Analytics jobb använder du vanligt vis en [referens data koppling](/stream-analytics-query/reference-data-join-azure-stream-analytics) i din fråga. 

## <a name="example"></a>Exempel  
Du kan ha en real tids ström med händelser som genereras när bilar överför en väg-monter. Avgiftsbelagt monter kan fånga upp licens skylten i real tid och gå med i en statisk data uppsättning som har registrerings information för att identifiera licens plattor som har upphört att gälla.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics stöder Azure Blob Storage och Azure SQL Database som lagrings lager för referens data. Du kan också transformera och/eller kopiera referens data till Blob Storage från Azure Data Factory om du vill använda [valfritt antal molnbaserade och lokala data lager](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referens data modelleras som en sekvens av blobbar (definieras i inmatnings konfigurationen) i stigande ordning för datum/tid som anges i BLOB-namnet. Den har **bara** stöd för att lägga till i slutet av sekvensen genom att använda en datum/tid som är **större** än den som anges av den sista blobben i sekvensen. Mer information finns i [använda referens data från en Blob Storage för ett Azure Stream Analytics jobb](data-protection.md).

### <a name="configure-blob-reference-data"></a>Konfigurera referens data för BLOB

Om du vill konfigurera dina referens data måste du först skapa en indata som är av typen **referens data**. I tabellen nedan förklaras varje egenskap som du måste ange när du skapar referens data inmatningen med beskrivningen:

|**Egenskapens namn**  |**Beskrivning**  |
|---------|---------|
|Indataalias   | Ett eget namn som ska användas i jobb frågan för att referera till den här indatamängden.   |
|Lagringskonto   | Namnet på det lagrings konto där blobarna finns. Om det är i samma prenumeration som ditt Stream Analytics jobb kan du välja det från List rutan.   |
|Lagrings konto nyckel   | Den hemliga nyckeln som är kopplad till lagrings kontot. Detta fylls i automatiskt om lagrings kontot finns i samma prenumeration som ditt Stream Analytics-jobb.   |
|Lagrings behållare   | Behållare tillhandahåller en logisk gruppering för blobbar som lagras i Microsoft Azure Blob Service. När du laddar upp en blob till Blob Service måste du ange en behållare för denna blob.   |
|Sökvägsmönster   | Detta är en obligatorisk egenskap som används för att hitta dina blobbar i den angivna behållaren. I sökvägen kan du välja att ange en eller flera instanser av följande två variabler:<BR>{Date}, {Time}<BR>Exempel 1: Products/{date}/{time}/product-list.csv<BR>Exempel 2: Products/{date}/product-list.csv<BR>Exempel 3: product-list.csv<BR><br> Om blobben inte finns på den angivna sökvägen kommer Stream Analytics jobbet att vänta oändligt för att blobben ska bli tillgängligt.   |
|Datum format [valfritt]   | Om du har använt {date} inom Sök vägs mönstret som du har angett kan du välja det datum format som dina blobbar är ordnade i list rutan med format som stöds.<BR>Exempel: ÅÅÅÅ/MM/DD, MM/DD/ÅÅÅÅ, osv.   |
|Tids format [valfritt]   | Om du har använt {Time} inom Sök vägs mönstret som du har angett kan du välja det tids format som dina blobbar organiseras från i list rutan med format som stöds.<BR>Exempel: HH, HH/mm eller HH-mm.  |
|Format för händelse serialisering   | För att se till att dina frågor fungerar som du förväntar dig måste Stream Analytics veta vilket serialiserat format du använder för inkommande data strömmar. För referens data är de format som stöds CSV och JSON.  |
|Kodning   | UTF-8 är det enda kodnings format som stöds just nu.  |

### <a name="static-reference-data"></a>Statiska referens data

Om dina referens data inte förväntas ändras, så aktive ras stöd för statiska referens data genom att ange en statisk sökväg i indata-konfigurationen. Azure Stream Analytics hämtar bloben från den angivna sökvägen. {date} och {Time} ersättnings-token krävs inte. Eftersom referens data är oföränderliga i Stream Analytics, rekommenderas inte att skriva över en statisk referens data-BLOB.

### <a name="generate-reference-data-on-a-schedule"></a>Generera referens data enligt ett schema

Om dina referens data är en långsam ändring av data uppsättningen aktive ras stöd för uppdatering av referens data genom att ange ett Sök vägs mönster i indata-konfigurationen med hjälp av {date}-och {Time}-token för ersättning. Stream Analytics hämtar de uppdaterade referens data definitionerna baserat på den här Sök vägs mönstret. Ett mönster i `sample/{date}/{time}/products.csv` med datum formatet **"åååå-mm-dd"** och ett tids format på **"HH-mm"** instruerar till exempel Stream Analytics att hämta den uppdaterade blobben `sample/2015-04-16/17-30/products.csv` vid 5:30 PM den 16 april 2015 UTC Time Zone.

Azure Stream Analytics söker automatiskt efter uppdaterade referens data blobbar med ett minut intervall. Om en blob med tidsstämpel 10:30:00 överförs med en liten fördröjning (till exempel 10:30:30) ser du en liten fördröjning i Stream Analytics jobb som refererar till denna blob. För att undvika sådana scenarier rekommenderar vi att du överför blobben som är tidigare än målets effektiva tid (10:30:00 i det här exemplet) för att tillåta Stream Analytics jobbet tillräckligt med tid för att identifiera och läsa in det i minnet och utföra åtgärder. 

> [!NOTE]
> För närvarande Stream Analytics jobb bara att leta efter BLOB-uppdateringen när dator tiden går ut till den tid som kodas i BLOB-namnet. Jobbet kommer till exempel att söka efter `sample/2015-04-16/17-30/products.csv` så snart som möjligt, men inte tidigare än 5:30 PM den 16 april, 2015 UTC-tidszonen. Det kommer *aldrig* att leta efter en blob med en kodad tid som är tidigare än den sista som identifierades.
> 
> När jobbet till exempel hittar blobben `sample/2015-04-16/17-30/products.csv` ignoreras alla filer med ett kodat datum som är tidigare än 5:30 april, 2015, så om en sen inkommande `sample/2015-04-16/17-25/products.csv` BLOB skapas i samma behållare används den inte av jobbet.
> 
> Även om `sample/2015-04-16/17-30/products.csv` bara 10:03 produceras den 16 april, 2015 men ingen BLOB med ett tidigare datum finns i behållaren, kommer jobbet att använda den här filen från och med 10:03 den 16 april, 2015 och använda tidigare referens data fram till dess.
> 
> Ett undantag till detta är när jobbet måste bearbeta data igen i tid eller när jobbet startas första gången. Vid start tiden söker jobbet efter den senaste blob som producerats innan jobbets start tid har angetts. Detta görs för att se till att det finns en **icke-tom** referens data uppsättning när jobbet startas. Om det inte går att hitta någon, visar jobbet följande diagnostik: `Initializing input without a valid reference data blob for UTC time <start time>` .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan användas för att dirigera uppgiften att skapa de uppdaterade blobbar som krävs av Stream Analytics för att uppdatera referens data definitioner. Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Data Factory har stöd för [att ansluta till ett stort antal molnbaserade och lokala data lager](../data-factory/copy-activity-overview.md) och flytta data enkelt enligt ett regelbundet schema som du anger. Mer information och stegvisa anvisningar om hur du konfigurerar en Data Factory pipeline för att generera referens data för Stream Analytics som uppdateras i ett fördefinierat schema, finns i det här [GitHub exemplet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tips om att uppdatera BLOB-referenser

1. Skriv inte över referens data blobbar eftersom de är oföränderliga.
2. Det rekommenderade sättet att uppdatera referens data är att:
    * Använd {date}-/{time} i Sök vägs mönstret
    * Lägg till en ny BLOB med samma behållare och Sök vägs mönster som definierats i jobb indatatypen
    * Använd en datum/tid som är **större** än det som anges av den sista blobben i sekvensen.
3. Referens data blobbar beställs **inte** av blobens "senast ändrad"-tid, men bara vid den tid och det datum som anges i BLOB-namnet med hjälp av {date} och {Time} ersättningar.
3. Överväg att ta bort mycket gamla blobbar som bearbetningen inte längre ska utföras för att undvika att lista ett stort antal blobbar. Observera att ASA kanske måste bearbeta en liten del i vissa scenarier som en omstart.

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database referens data hämtas av ditt Stream Analytics-jobb och lagras som en ögonblicks bild i minnet för bearbetning. Ögonblicks bilden av dina referens data lagras också i en behållare i ett lagrings konto som du anger i konfigurations inställningarna. Behållaren skapas automatiskt när jobbet startas. Om jobbet stoppas eller anger ett felaktigt tillstånd raderas de automatiskt skapade behållarna när jobbet startas om.  

Om dina referens data är en långsam ändring av data uppsättningen, måste du regelbundet uppdatera den ögonblicks bild som används i jobbet. Med Stream Analytics kan du ange ett uppdaterings intervall när du konfigurerar din Azure SQL Database-ingångs anslutning. Stream Analytics runtime kommer att fråga din Azure SQL Database enligt intervallet som anges i uppdaterings intervallet. Det snabbaste uppdaterings intervallet som stöds är en gång per minut. För varje uppdatering lagrar Stream Analytics en ny ögonblicks bild i det angivna lagrings kontot.

Stream Analytics innehåller två alternativ för att skicka frågor till Azure SQL Database. En ögonblicks bild fråga är obligatorisk och måste inkluderas i varje jobb. Stream Analytics kör ögonblicks bild frågan med jämna mellanrum baserat på ditt uppdaterings intervall och använder resultatet från frågan (ögonblicks bilden) som referens data uppsättning. Ögonblicks bilds frågan bör passa de flesta scenarier, men om du stöter på prestanda problem med stora data uppsättningar och snabba uppdaterings hastigheter kan du använda delta-frågealternativet. Frågor som tar mer än 60 sekunder att returnera referens data uppsättningen leder till en tids gräns.

Med alternativet delta fråga kör Stream Analytics ögonblicks bild frågan från början för att hämta en referens data uppsättning för bas linjen. När Stream Analytics kör delta frågan med jämna mellanrum baserat på ditt uppdaterings intervall för att hämta stegvisa ändringar. De här stegvisa ändringarna tillämpas kontinuerligt på referens data uppsättningen för att hålla dem uppdaterade. Genom att använda delta frågor kan du minska lagrings kostnaderna och I/O-åtgärder I nätverket.

### <a name="configure-sql-database-reference"></a>Konfigurera SQL Database referens

Om du vill konfigurera dina SQL Database referens data måste du först skapa **referenser för data** inmatning. I tabellen nedan förklaras varje egenskap som du måste ange när du skapar referens data indata med en beskrivning. Mer information finns i [använda referens data från en SQL Database för ett Azure Stream Analytics jobb](sql-reference-data.md).

Du kan använda en [hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) som referens data inmatning. Du måste [Konfigurera den offentliga slut punkten i SQL-hanterad instans](../azure-sql/managed-instance/public-endpoint-configure.md) och manuellt konfigurera följande inställningar i Azure Stream Analytics. Den virtuella Azure-datorn som kör SQL Server med en databas ansluten stöds också genom att konfigurera inställningarna manuellt nedan.

|**Egenskapens namn**|**Beskrivning**  |
|---------|---------|
|Inmatat alias|Ett eget namn som ska användas i jobb frågan för att referera till den här indatamängden.|
|Prenumeration|Välj din prenumeration|
|Databas|Azure SQL Database som innehåller dina referens data. För SQL-hanterad instans måste du ange port 3342. Till exempel *sampleserver. public. Database. Windows. net, 3342*|
|Användarnamn|Det användar namn som är associerat med din Azure SQL Database.|
|Lösenord|Lösen ordet som är kopplat till Azure SQL Database.|
|Uppdatera regelbundet|Med det här alternativet kan du välja ett uppdaterings intervall. Om du väljer "på" kan du ange uppdaterings frekvensen i DD: HH: MM.|
|Ögonblicks bild fråga|Detta är standard alternativet fråga som hämtar referens data från SQL Database.|
|Delta fråga|För avancerade scenarier med stora data uppsättningar och en kort uppdaterings takt väljer du att lägga till en delta fråga.|

## <a name="size-limitation"></a>Storleks begränsning

Vi rekommenderar att du använder referens data uppsättningar som är mindre än 300 MB för bästa prestanda. Referens data uppsättningar 5 GB eller lägre stöds i jobb med 6 SUs eller mer. Om du använder en mycket stor referens data kan det påverka svars tiden från slut punkt till slut punkt för jobbet. När frågans komplexitet ökar för att inkludera tillstånds känslig bearbetning, till exempel fönster mängd, temporala kopplingar och temporala analys funktioner, förväntas det att den maximala storlek som stöds för referens data minskar. Om Azure Stream Analytics inte kan läsa in referens data och utföra komplexa åtgärder, kommer jobbet att ta slut på minne och Miss kan köras. I sådana fall når måttet SU% nyttjande 100%.    

|**Antal enheter för strömning**  |**Rekommenderad storlek**  |
|---------|---------|
|1   |50 MB eller lägre   |
|3   |150 MB eller lägre   |
|6 och senare   |5 GB eller lägre.    |

Det finns inte stöd för komprimering för referens data.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Koppla flera referens data uppsättningar i ett jobb
Du kan bara ansluta till en Stream-inmatning med en referens data inmatning i ett enda steg i frågan. Du kan dock koppla flera referens data uppsättningar genom att bryta ned frågan till flera steg. Ett exempel på detta visas nedan.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>IoT Edge jobb

Endast lokala referens data stöds för Stream Analytics Edge-jobb. När ett jobb distribueras till IoT Edge enhet laddar det referens data från den användardefinierade fil Sök vägen. Har en referens data fil som är klar på enheten. För en Windows-behållare, placera referens data filen på den lokala enheten och dela den lokala enheten med Docker-behållaren. För en Linux-behållare skapar du en Docker-volym och fyller i data filen på volymen.

Referens data på IoT Edge uppdatering utlöses av en distribution. När den Utlös ATS väljer modulen Stream Analytics den uppdaterade informationen utan att stoppa jobbet som körs.

Det finns två sätt att uppdatera referens data:

* Uppdatera referens data Sök vägen i ditt Stream Analytics jobb från Azure Portal.

* Uppdatera IoT Edge-distributionen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
