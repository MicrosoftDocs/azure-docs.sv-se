---
title: Felsöka data flöden för mappning
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 8370940b1d7131142810d53e93c35f65e14b7575
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367936"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Felsöka mappning av data flöden i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för att mappa data flöden i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-utförar-SourceInvalidPayload
- **Meddelande**: det gick inte att köra data flöde för förhands granskning, fel sökning och pipeline-data flöde eftersom behållaren inte finns
- **Orsaker**: när data uppsättningen innehåller en behållare som inte finns i lagringen
- **Rekommendation**: kontrol lera att den behållare som refereras till i data uppsättningen finns eller är tillgänglig.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian

- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsaker**: implicit kartesiska-produkt för inre koppling mellan logiska planer stöds inte. Om de kolumner som används i kopplingen skapar den unika nyckeln, krävs minst en kolumn från båda sidor i relationen.
- **Rekommendation**: för icke-jämlikhetbaserade kopplingar måste du välja anpassad kors koppling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson

- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsaker**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som enskilt dokument på flera kapslade rader
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. På den käll omvandling som använder en JSON-datauppsättning expanderar du JSON-inställningar och aktiverar ett enskilt dokument.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-utförar-BroadcastTimeout

- **Meddelande**: timeout-fel vid sändnings anslutning, kontrol lera att sändnings strömmen genererar data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningar
- **Orsaker**: sändningen har en standard tids gräns på 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna. Den data ström som valts för sändning verkar vara för stor för att producera data inom den här gränsen.
- **Rekommendation**: kontrol lera att fliken optimera finns i dina data flödes omvandlingar för Join, exists och lookup. Standard alternativet för sändning är "Auto". Om "Auto" är inställt, eller om du anger att vänster eller höger sida ska sändas manuellt under "fast", kan du antingen ange en större Azure Integration Runtime-konfiguration eller stänga av sändningen. Den rekommenderade metoden för bästa prestanda i data flöden är att tillåta Spark att sända med "Auto" och använda ett Minnesoptimerade Azure IR.

Om du kör data flödet i ett fel söknings test av en fel söknings-pipeline kan du köra det här villkoret oftare. Detta beror på att ADF begränsar sändningens tids gräns till 60 sekunder för att få en snabbare fel sökning. Om du vill förlänga värdet för tids gränsen på 300 sekunder från en utlöst körning kan du använda den > använda alternativet för aktivitets körning för att använda Azure IR som definierats i pipeline-aktiviteten kör data flöde.

### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion

- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsaker**: data har inte förväntat format
- **Rekommendation**: Använd rätt datatyp

### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-utförar-InvalidColumn

- **Meddelande**: kolumn namnet måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsaker**: inget kolumn namn har angetts
- **Rekommendation**: Ange ett alias om du använder en SQL-funktion som min ()/max () osv.

 ### <a name="error-code-df-executor-drivererror"></a>Felkod: DF-utförar-DriverError
- **Meddelande**: INT96 är en äldre timestamp-typ som inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.
- **Orsaker**: driv rutins fel
- **Rekommendation**: INT96 är en äldre timestamp-typ, vilket inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Felkod: DF-utförar-BlockCountExceedsLimitError
- **Meddelande**: antalet icke-allokerade block får inte överskrida Max gränsen på 100 000 block. Kontrol lera BLOB-konfigurationen.
- **Orsaker**: det kan finnas högst 100 000 icke allokerade block i en blob.
- **Rekommendation**: kontakta Microsofts produkt team om det här problemet för mer information

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Felkod: DF-utförar-PartitionDirectoryError
- **Meddelande**: den angivna käll Sök vägen har antingen flera partitionerade kataloger (t. ex. <Source Path> /<partitionens rot Katalog 1>/a = 10/b = 20, <Source Path> /<partitionens rot Katalog 2>/c = 10/d = 30) eller partitionerad katalog med andra fil-eller icke-partitionerade kataloger (till exempel <Source Path> /<partitions rot Katalog 1>/a = 10/b = 20, <Source Path> /katalog 2/fil1), ta bort partitionens rot Katalog från käll Sök vägen och Läs den via separat käll omvandling.
- **Orsaker**: käll Sök vägen har antingen flera partitionerade kataloger eller partitionerad katalog med annan fil eller icke-partitionerad katalog.
- **Rekommendation**: ta bort partitionerade rot kataloger från käll Sök vägen och Läs den via separat käll omvandling.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Felkod: DF-utförar-OutOfMemoryError
- **Meddelande**: klustret fick ett slut på minnes problem under körningen, försök igen med en integrerings körning med större antal kärnor och/eller minnesoptimerade beräknings typ
- **Orsaker**: klustret håller på att ta slut på minne
- **Rekommendation**: fel söknings kluster är avsedda för utvecklings syfte. Utnyttja data sampling, lämplig beräknings typ och storlek för att köra nytto lasten. Se [prestanda guiden för data flöde](concepts-data-flow-performance.md) för att justera för att uppnå bästa prestanda.

 ### <a name="error-code-df-executor-illegalargument"></a>Felkod: DF-utförar-illegalArgument
- **Meddelande**: kontrol lera att åtkomst nyckeln i den länkade tjänsten är korrekt
- **Orsaker**: konto namnet eller åtkomst nyckeln är felaktigt
- **Rekommendation**: kontrol lera att konto namnet eller åtkomst nyckeln som anges i den länkade tjänsten är korrekt. 

 ### <a name="error-code-df-executor-invalidtype"></a>Felkod: DF-utförar-InvalidType
- **Meddelande**: kontrol lera att den typ av parameter som matchar typen av värde har överförts. Det finns för närvarande inte stöd för att skicka flytt ALS parametrar från pipeliner.
- **Orsaker**: inkompatibla data typer mellan deklarerad typ och faktiskt parameter värde
- **Rekommendation**: kontrol lera att de parameter värden som skickas till ett data flöde matchar den deklarerade typen.

 ### <a name="error-code-df-executor-columnunavailable"></a>Felkod: DF-utförar-ColumnUnavailable
- **Meddelande**: kolumn namnet som används i uttrycket är inte tillgängligt eller ogiltigt
- **Orsaker**: ogiltigt eller inte tillgängligt kolumn namn som används i uttryck
- **Rekommendation**: kontrol lera kolumn namn som används i uttryck

 ### <a name="error-code-df-executor-parseerror"></a>Felkod: DF-utförar-ParseError
- **Meddelande**: det går inte att parsa uttrycket
- **Orsaker**: uttrycket innehåller tolknings fel på grund av formatering
- **Rekommendation**: kontrol lera formateringen i uttrycket

### <a name="error-code-getcommand-outputasync-failed"></a>Felkod: GetCommand OutputAsync misslyckades

- **Meddelande**: vid fel sökning av data flöde och data förhands granskning: GetCommand OutputAsync misslyckades med...
- **Orsaker**: det här är ett fel i backend-tjänsten. Du kan försöka igen och starta om din felsökningssession.
- **Rekommendation**: om försök igen och omstart inte löser problemet kontaktar du kund support.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Felkod: ett oväntat undantag och körning misslyckades

- **Meddelande**: vid körning av data flödes aktivitet: det gick inte att köra ett oväntat undantag och körnings fel.
- **Orsaker**: det här är ett fel i backend-tjänsten. Du kan försöka igen och starta om din felsökningssession.
- **Rekommendation**: om försök igen och omstart inte löser problemet kontaktar du kund support.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Felkod: fel meddelande vid förhands granskning av data inga utdata vid koppling

- **Meddelande**: det finns ett stort antal null-värden eller saknade värden som kan bero på att det inte finns några rader som samplats. Försök att uppdatera gränsen för fel söknings rad och uppdatera data.
- **Orsaker**: JOIN-villkoret matchade inte några rader eller resulterade i ett stort antal null-värden under förhands granskningen.
- **Rekommendation**: gå till fel söknings inställningar och öka antalet rader i käll rad gränsen. Kontrol lera att du har valt en Azure IR med ett tillräckligt stort data flödes kluster för att hantera mer data.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Felkod: validerings fel vid källa med CSV-filer med flera rader 

- **Meddelande**: ett av följande fel meddelanden kan visas:
   - Den sista kolumnen är null eller saknas.
   - Schema validering vid källan misslyckades.
   - Schema import kan inte visas korrekt i UX och den sista kolumnen har ett nytt rad avstånd i namnet.
- **Orsaker**: för närvarande fungerar inte CSV-källan för flera rader med den rad avgränsare som finns i data flödet för mappning. Ibland returnerar extra rader vid vagnen brytnings värden. 
- **Rekommendation**: skapa filen på källan med \n som en rad avgränsare i stället för \r\n. Du kan också använda kopierings aktiviteten för att konvertera CSV-filen med \r\n till \n som en rad avgränsare.

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-utförar-SourceInvalidPayload
- **Meddelande**: det gick inte att köra data flöde för förhands granskning, fel sökning och pipeline-data flöde eftersom behållaren inte finns
- **Orsaker**: när data uppsättningen innehåller en behållare som inte finns i lagringen
- **Rekommendation**: kontrol lera att den behållare som refereras till i data uppsättningen finns eller är tillgänglig.


 ### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian
- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsaker**: implicit kartesiska-produkt för inre koppling mellan logiska planer stöds inte. Om de kolumner som används i kopplingen skapar den unika nyckeln
- **Rekommendation**: för icke-jämlikhetbaserade kopplingar måste du välja mellan koppling.


 ### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson
- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsaker**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som enskilt dokument på flera kapslade rader
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. På den käll omvandling som använder en JSON-datauppsättning expanderar du JSON-inställningar och aktiverar ett enskilt dokument.


 ### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-utförar-BroadcastTimeout
- **Meddelande**: timeout-fel i sändnings anslutning. du kan välja "off" av sändnings alternativet i sammanfogning/exists/lookup-transformeringen för att undvika det här problemet. Om du vill skicka kopplings alternativ för att förbättra prestandan ska du kontrol lera att sändnings strömmen kan producera data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna.
- **Orsaker**: sändningen har en standard tids gräns på 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna. Vid broadcast-anslutning verkar den data ström som valts för sändning vara för stor för att producera data inom den här gränsen. Om en sändnings anslutning inte används, kan standard sändningen som utförs av data ström uppnå samma gräns
- **Rekommendation**: inaktivera sändnings alternativet eller Undvik att sända stora data strömmar där bearbetningen kan ta mer än 60 sekunder. Välj en mindre ström att sända i stället. Stora SQL/DW-tabeller och källfiler är vanligt vis Felaktiga kandidater. Om det inte finns någon sändnings anslutning använder du ett större kluster om felet uppstår.


 ### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion
- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsaker**: data har inte förväntat format
- **Rekommendation**: Använd rätt datatyp


 ### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-utförar-InvalidColumn
- **Meddelande**: kolumn namnet måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsaker**: inget kolumn namn har angetts.


 ### <a name="error-code-df-executor-drivererror"></a>Felkod: DF-utförar-DriverError
- **Meddelande**: INT96 är en äldre timestamp-typ som inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.
- **Orsaker**: det är ett driv rutins fel.
- **Rekommendation**: INT96 är en äldre timestamp-typ som inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.


 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Felkod: DF-utförar-BlockCountExceedsLimitError
- **Meddelande**: antalet icke-allokerade block får inte överskrida Max gränsen på 100 000 block. Kontrol lera BLOB-konfigurationen.
- **Orsaker**: det kan finnas högst 100 000 icke allokerade block i en blob.
- **Rekommendation**: kontakta Microsofts produkt team om du har frågor om det här problemet för mer information

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Felkod: DF-utförar-PartitionDirectoryError
- **Meddelande**: den angivna käll Sök vägen har antingen flera partitionerade kataloger (t. ex. <Source Path> /<partitionens rot Katalog 1>/a = 10/b = 20, <Source Path> /<partitionens rot Katalog 2>/c = 10/d = 30) eller partitionerad katalog med andra fil-eller icke-partitionerade kataloger (t. ex. <Source Path> /<partition rot Katalog 1>/a = 10/b = 20, <Source Path> /katalog 2/fil1), ta bort partitionens rot Katalog från käll Sök vägen och Läs den via separata käll omvandling.
- **Orsaker**: käll Sök vägen har antingen flera partitionerade kataloger eller partitionerad katalog med annan fil eller icke-partitionerad katalog.
- **Rekommendation**: ta bort partitionerade rot kataloger från käll Sök vägen och Läs den via separat käll omvandling.


 ### <a name="error-code-df-executor-outofmemoryerror"></a>Felkod: DF-utförar-OutOfMemoryError
- **Meddelande**: klustret fick ett slut på minnes problem under körningen, försök igen med en integrerings körning med större antal kärnor och/eller minnesoptimerade beräknings typ
- **Orsaker**: klustret håller på att ta slut på minne.
- **Rekommendation**: fel söknings kluster är avsedda för utvecklings syfte. Använd data samplings lämplig beräknings typ och storlek för att köra nytto lasten. Se [prestanda guiden](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) för data flöde för att justera data flöden för bästa prestanda.


 ### <a name="error-code-df-executor-illegalargument"></a>Felkod: DF-utförar-illegalArgument
- **Meddelande**: kontrol lera att åtkomst nyckeln i den länkade tjänsten är korrekt.
- **Orsaker**: konto namnet eller åtkomst nyckeln är felaktig.
- **Rekommendation**: ange rätt konto namn eller åtkomst nyckel.


 ### <a name="error-code-df-executor-invalidtype"></a>Felkod: DF-utförar-InvalidType
- **Meddelande**: kontrol lera att den typ av parameter som matchar typen av värde har överförts. Det finns för närvarande inte stöd för att skicka flytt ALS parametrar från pipeliner.
- **Orsaker**: inkompatibla data typer mellan deklarerad typ och faktiskt parameter värde
- **Rekommendation**: ange rätt data typer.


 ### <a name="error-code-df-executor-columnunavailable"></a>Felkod: DF-utförar-ColumnUnavailable
- **Meddelande**: kolumn namnet som används i uttrycket är inte tillgängligt eller ogiltigt.
- **Orsaker**: ogiltigt eller inte tillgängligt kolumn namn används i uttryck.
- **Rekommendation**: kontrol lera kolumn namn som används i uttryck.


 ### <a name="error-code-df-executor-parseerror"></a>Felkod: DF-utförar-ParseError
- **Meddelande**: det går inte att parsa uttrycket.
- **Orsaker**: uttrycket innehåller tolknings fel på grund av formatering.
- **Rekommendation**: kontrol lera formatering i uttryck.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Felkod: DF-utförar-OutOfDiskSpaceError
- **Meddelande**: internt Server fel
- **Orsaker**: klustret håller på att ta slut på disk utrymme.
- **Rekommendation**: försök att köra pipelinen igen. Kontakta kund support om problemet kvarstår.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Felkod: DF-utförar-StoreIsNotDefined
- **Meddelande**: Store-konfigurationen har inte definierats. Det här felet kan bero på ogiltig parameter tilldelning i pipelinen.
- **Orsaker**: inte fastställt
- **Rekommendation**: kontrol lera tilldelningen av parameter värde i pipelinen. Parameter uttryck kan innehålla ogiltiga tecken.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Felkod: DF-Excel-InvalidConfiguration
- **Meddelande**: namn eller index för Excel-blad krävs.
- **Orsaker**: inte fastställt
- **Rekommendation**: kontrol lera parametervärdet och ange bladets namn eller index för att läsa Excel-data.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Felkod: DF-Excel-InvalidConfiguration
- **Meddelande**: Excel-bladets namn och index kan inte finnas samtidigt.
- **Orsaker**: inte fastställt
- **Rekommendation**: kontrol lera parametervärdet och ange bladets namn eller index för att läsa Excel-data.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Felkod: DF-Excel-InvalidConfiguration
- **Meddelande**: ogiltigt intervall har angetts.
- **Orsaker**: inte fastställt
- **Rekommendation**: kontrol lera parametervärdet och ange ett giltigt intervall enligt referens: [Egenskaper för Excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).


 ### <a name="error-code-df-excel-invaliddata"></a>Felkod: DF-Excel-InvalidData
- **Meddelande**: Excel-kalkylbladet finns inte.
- **Orsaker**: inte fastställt
- **Rekommendation**: kontrol lera parameter värde och ange ett giltigt bladnamn eller index för att läsa Excel-data.

 ### <a name="error-code-df-excel-invaliddata"></a>Felkod: DF-Excel-InvalidData
- **Meddelande**: det finns inte stöd för att läsa Excel-filer med ett annat schema nu.
- **Orsaker**: inte fastställt
- **Rekommendation**: Använd rätt Excel-fil.


 ### <a name="error-code-df-excel-invaliddata"></a>Felkod: DF-Excel-InvalidData
- **Meddelande**: data typen stöds inte.
- **Orsaker**: inte fastställt
- **Rekommendation**: Använd Excel-filens högra data typer.

 ### <a name="error-code-df-excel-invalidconfiguration"></a>Felkod: DF-Excel-InvalidConfiguration
- **Meddelande**: en ogiltig Excel-fil anges när endast. xlsx och. xls stöds
- **Orsaker**: inte fastställt
- **Rekommendation**: se till att fil namns tillägget för Excel är antingen. xlsx eller. xls.

## <a name="general-troubleshooting-guidance"></a>Allmän fel söknings vägledning
1. Kontrol lera statusen för dina data uppsättnings anslutningar. I varje käll-och mottagar omvandling kan du gå till den länkade tjänsten för varje data uppsättning som du använder och testa anslutningarna.
2. Kontrol lera status för dina fil-och tabell anslutningar från data flödes designern. Växla vid fel sökning och klicka på Förhandsgranska data på käll omvandlingarna för att säkerställa att du kan komma åt dina data.
3. Om allt ser bra ut från data förhands granskningen, går du till pipeline-designern och sätter ditt data flöde i en pipeline-aktivitet. Felsök pipelinen för ett slut punkt till slut punkts test.


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:
*  [Data Factory blogg](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för ADF-mappning av data flöden](concepts-data-flow-performance.md)
