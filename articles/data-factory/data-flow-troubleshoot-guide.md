---
title: Felsöka data flöden för mappning
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 3c73d1652d4fdaa34fa65e7186298bafc0a905a9
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453265"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Felsöka mappning av data flöden i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för att mappa data flöden i Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Vanliga felkoder och meddelanden 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-utförar-SourceInvalidPayload
- **Meddelande**: det gick inte att köra data flöde för förhands granskning, fel sökning och pipeline-data flöde eftersom behållaren inte finns
- **Orsak**: en data uppsättning innehåller en behållare som inte finns i lagrings utrymmet.
- **Rekommendation**: kontrol lera att behållaren som refereras till i data uppsättningen finns och att den kan nås.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian

- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsak**: implicita kartesiska-produkter för inre kopplingar mellan logiska planer stöds inte. Om du använder kolumner i kopplingen skapar du en unik nyckel med minst en kolumn från båda sidor i relationen.
- **Rekommendation**: Använd anpassad kors koppling för icke-jämlikhetbaserade kopplingar.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson

- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsak**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som ett enda dokument på flera kapslade rader.
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. I käll omvandlingen som använder en JSON-datauppsättning expanderar du **JSON-inställningar** och aktiverar **enstaka dokument**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-utförar-BroadcastTimeout

- **Meddelande**: timeout-fel vid sändnings anslutning, kontrol lera att sändnings strömmen genererar data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningar
- **Orsak**: sändningen har en standard tids gräns på 60 sekunder för fel söknings körningar och 300 sekunder för jobb körningar. Den data ström som valts för sändning är för stor för att producera data inom den här gränsen.
- **Rekommendation**: kontrol lera att fliken **optimera** finns i dina data flödes omvandlingar för Join, exists och lookup. Standard alternativet för sändning är **Auto**. Om **Auto** har angetts, eller om du manuellt ställer in den vänstra eller högra sidan för sändning under **fast**, kan du antingen ange en större IR-konfiguration (Azure integration Runtime) eller inaktivera sändning. För bästa prestanda i data flöden rekommenderar vi att du tillåter Spark att sända genom att använda **Auto** och använda ett minnesoptimerade Azure IR. 
 
  Om du kör data flödet i ett fel söknings test för att köra en fel söknings-pipeline kan du köra det här villkoret oftare. Det beror på att Azure Data Factory begränsar sändningens tids gräns till 60 sekunder för att få en snabbare fel sökning. Du kan förlänga tids gränsen till 300-sekunders tids gränsen för en utlöst körning. Om du vill göra det kan du använda det  >  Azure IR som definierats i pipeline-aktiviteten kör data flöde för **att använda** alternativet för att felsöka.

- **Meddelande**: timeout-fel i sändnings anslutning. du kan välja "off" av sändnings alternativet i sammanfogning/exists/lookup-transformeringen för att undvika det här problemet. Om du vill skicka kopplings alternativ för att förbättra prestandan ska du kontrol lera att sändnings strömmen kan producera data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna.
- **Orsak**: sändningen har en standard-timeout på 60 sekunder i fel söknings körningar och 300 sekunder i jobbet körs. På sändnings anslutningen är den data ström som valts för sändning är för stor för att producera data inom den här gränsen. Om en sändnings anslutning inte används, kan standard sändningen via data ström uppnå samma gräns.
- **Rekommendation**: inaktivera sändnings alternativet eller Undvik att sända stora data strömmar för vilka bearbetningen kan ta mer än 60 sekunder. Välj en mindre ström att sända. Stora Azure SQL Data Warehouse tabeller och källfiler är vanligt vis lämpliga alternativ. Om det inte finns någon sändnings anslutning använder du ett större kluster om felet uppstår.

### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion

- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsak**: data har inte det förväntade formatet.
- **Rekommendation**: Använd rätt datatyp.

### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-utförar-InvalidColumn
- **Meddelande**: kolumn namnet måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsak**: inget kolumn namn har angetts.
- **Rekommendation**: Ange ett alias om du använder en SQL-funktion som min () eller Max ().

### <a name="error-code-df-executor-drivererror"></a>Felkod: DF-utförar-DriverError
- **Meddelande**: INT96 är en äldre timestamp-typ som inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.
- **Orsak**: driv rutins fel.
- **Rekommendation**: INT96 är en äldre timestamp-typ som inte stöds av Azure Data Factory data flöde. Överväg att uppgradera kolumn typen till den senaste typen.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Felkod: DF-utförar-BlockCountExceedsLimitError
- **Meddelande**: antalet icke-allokerade block får inte överskrida Max gränsen på 100 000 block. Kontrol lera BLOB-konfigurationen.
- **Orsak**: det maximala antalet icke allokerade block i en blob är 100 000.
- **Rekommendation**: kontakta Microsofts produkt team om du vill ha mer information om det här problemet.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Felkod: DF-utförar-PartitionDirectoryError
- **Meddelande**: den angivna käll Sök vägen har antingen flera partitionerade kataloger (t. ex. <Source Path> /<partitionens rot Katalog 1>/a = 10/b = 20, <Source Path> /<partitionens rot Katalog 2>/c = 10/d = 30) eller partitionerad katalog med andra fil-eller icke-partitionerade kataloger (till exempel <Source Path> /<partitions rot Katalog 1>/a = 10/b = 20, <Source Path> /katalog 2/fil1), ta bort partitionens rot Katalog från käll Sök vägen och Läs den via separat käll omvandling.
- **Orsak**: käll Sök vägen har antingen flera partitionerade kataloger eller en partitionerad katalog som har en annan fil eller en icke-partitionerad katalog.
- **Rekommendation**: ta bort den partitionerade rot katalogen från käll Sök vägen och Läs den via separat käll omvandling.

### <a name="error-code-df-executor-invalidtype"></a>Felkod: DF-utförar-InvalidType
- **Meddelande**: kontrol lera att den typ av parameter som matchar typen av värde har överförts. Det finns för närvarande inte stöd för att skicka flytt ALS parametrar från pipeliner.
- **Orsak**: data typen för den deklarerade typen är inte kompatibel med det faktiska parametervärdet.
- **Rekommendation**: kontrol lera att de parameter värden som skickas till data flödet matchar den deklarerade typen.

### <a name="error-code-df-executor-columnunavailable"></a>Felkod: DF-utförar-ColumnUnavailable
- **Meddelande**: kolumn namnet som används i uttrycket är inte tillgängligt eller ogiltigt
- **Orsak**: ett ogiltigt eller inte tillgängligt kolumn namn som används i ett uttryck.
- **Rekommendation**: kontrol lera kolumn namn i uttryck.

### <a name="error-code-df-executor-parseerror"></a>Felkod: DF-utförar-ParseError
- **Meddelande**: det går inte att parsa uttrycket
- **Orsak**: ett uttryck genererade tolknings fel på grund av felaktig formatering.
- **Rekommendation**: kontrol lera formateringen i uttrycket.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian
- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsak**: implicita kartesiska-produkter för inre kopplingar mellan logiska planer stöds inte. Om du använder kolumner i kopplingen skapar du en unik nyckel.
- **Rekommendation**: Använd kors koppling för icke-jämlikhetbaserade kopplingar.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson
- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsak**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som ett enda dokument på flera kapslade rader.
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. I käll omvandlingen som använder en JSON-datauppsättning expanderar du **JSON-inställningar** och aktiverar **enstaka dokument**.



### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion
- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsak**: data har inte det förväntade formatet.
- **Rekommendation**: Använd rätt datatyp.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Felkod: DF-utförar-BlockCountExceedsLimitError
- **Meddelande**: antalet icke-allokerade block får inte överskrida Max gränsen på 100 000 block. Kontrol lera BLOB-konfigurationen.
- **Orsak**: det maximala antalet icke allokerade block i en blob är 100 000.
- **Rekommendation**: kontakta Microsofts produkt team om du vill ha mer information om det här problemet.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Felkod: DF-utförar-PartitionDirectoryError
- **Meddelande**: den angivna käll Sök vägen har antingen flera partitionerade kataloger (t. ex. *<Source Path> /<partitionens rot Katalog 1>/a = 10/b = 20, <Source Path> /<partitionens rot Katalog 2>/c = 10/d = 30*) eller partitionerad katalog med andra fil-eller icke-partitionerade kataloger (t. ex. *<Source Path> /<partition rot Katalog 1>/A = 10/b = 20, <Source Path> /katalog 2/fil1*), ta bort partitionens rot Katalog från käll Sök vägen och Läs den via separata käll omvandling.
- **Orsak**: käll Sök vägen har antingen flera partitionerade kataloger eller en partitionerad katalog som har en annan fil eller en icke-partitionerad katalog. 
- **Rekommendation**: ta bort den partitionerade rot katalogen från käll Sök vägen och Läs den via separat käll omvandling.

### <a name="error-code-getcommand-outputasync-failed"></a>Felkod: GetCommand OutputAsync misslyckades
- **Meddelande**: vid fel sökning av data flöde och data förhands granskning: GetCommand OutputAsync misslyckades med...
- **Orsak**: det här felet är ett Server dels fel. 
- **Rekommendation**: försök igen och starta om felsökningssessionen. Kontakta kund support om du inte kan lösa problemet genom att försöka igen och starta om. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Felkod: DF-utförar-OutOfMemoryError
 
- **Meddelande**: klustret fick ett slut på minnes problem under körningen, försök igen med en integrerings körning med större antal kärnor och/eller minnesoptimerade beräknings typ
- **Orsak**: klustret har slut på minne.
- **Rekommendation**: fel söknings kluster är avsedda för utveckling. Använd data sampling och en lämplig beräknings typ och storlek för att köra nytto lasten. Prestanda tips finns i [prestanda guiden för att mappa data flöde](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Felkod: DF-utförar-illegalArgument

- **Meddelande**: kontrol lera att åtkomst nyckeln i den länkade tjänsten är korrekt
- **Orsak**: konto namnet eller åtkomst nyckeln är felaktig.
- **Rekommendation**: kontrol lera att konto namnet eller åtkomst nyckeln som anges i den länkade tjänsten är korrekt. 

### <a name="error-code-df-executor-invalidtype"></a>Felkod: DF-utförar-InvalidType
- **Meddelande**: kontrol lera att den typ av parameter som matchar typen av värde har överförts. Det finns för närvarande inte stöd för att skicka flytt ALS parametrar från pipeliner.
- **Orsak**: data typen för den deklarerade typen är inte kompatibel med det faktiska parametervärdet. 
- **Rekommendation**: ange rätt data typer.

### <a name="error-code-df-executor-columnunavailable"></a>Felkod: DF-utförar-ColumnUnavailable
- **Meddelande**: kolumn namnet som används i uttrycket är inte tillgängligt eller ogiltigt.
- **Orsak**: ett ogiltigt eller otillgängligt kolumn namn används i ett uttryck.
- **Rekommendation**: kontrol lera kolumn namnen som används i uttryck.


### <a name="error-code-df-executor-parseerror"></a>Felkod: DF-utförar-ParseError
- **Meddelande**: det går inte att parsa uttrycket.
- **Orsak**: ett uttryck genererade tolknings fel på grund av felaktig formatering. 
- **Rekommendation**: kontrol lera formateringen i uttrycket.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Felkod: DF-utförar-OutOfDiskSpaceError
- **Meddelande**: internt Server fel
- **Orsak**: klustrets disk utrymme håller på att ta slut.
- **Rekommendation**: försök att köra pipelinen igen. Om detta inte löser problemet kontaktar du kund support.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Felkod: DF-utförar-StoreIsNotDefined
- **Meddelande**: Store-konfigurationen har inte definierats. Det här felet kan bero på ogiltig parameter tilldelning i pipelinen.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera tilldelning av parameter värde i pipelinen. Ett parameter uttryck kan innehålla ogiltiga tecken.

### <a name="error-code-df-excel-invalidconfiguration"></a>Felkod: DF-Excel-InvalidConfiguration
- **Meddelande**: namn eller index för Excel-blad krävs.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera värdet för parametern. Ange kalkyl bladets namn eller index för att läsa Excel-data.

- **Meddelande**: Excel-bladets namn och index kan inte finnas samtidigt.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera värdet för parametern. Ange kalkyl bladets namn eller index för att läsa Excel-data.

- **Meddelande**: ogiltigt intervall har angetts.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera värdet för parametern. Ange ett giltigt intervall med referens. Mer information finns i [Egenskaper för Excel](./format-excel.md#dataset-properties).

- **Meddelande**: en ogiltig Excel-fil anges när endast. xlsx och. xls stöds
- **Orsak**: obestämd.
- **Rekommendation**: se till att fil namns tillägget för Excel är antingen. xlsx eller. xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Felkod: DF-Excel-InvalidData
- **Meddelande**: Excel-kalkylbladet finns inte.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera värdet för parametern. Ange ett giltigt kalkyl blads namn eller index för att läsa Excel-data.

- **Meddelande**: det finns inte stöd för att läsa Excel-filer med ett annat schema nu.
- **Orsak**: obestämd.
- **Rekommendation**: Använd en Excel-fil som stöds.

- **Meddelande**: data typen stöds inte.
- **Orsak**: obestämd.
- **Rekommendation**: Använd fil data typer som stöds av Excel.

### <a name="error-code-4502"></a>Felkod: 4502
- **Meddelande**: det finns betydande samtidiga MappingDataflow-körningar som orsakar fel på grund av begränsning av integration Runtime.
- **Orsak**: ett stort antal körningar av data flödes aktiviteter sker samtidigt i integration Runtime. Mer information finns i [Azure Data Factory gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Rekommendation**: om du vill köra fler data flödes aktiviteter parallellt distribuerar du dem över flera integrerings körningar.


### <a name="error-code-invalidtemplate"></a>Felkod: InvalidTemplate
- **Meddelande**: det går inte att utvärdera pipeline-uttrycket.
- **Orsak**: det pipeline-uttryck som skickades i data flödes aktiviteten bearbetas inte korrekt på grund av ett syntaxfel.
- **Rekommendation**: kontrol lera aktiviteten i aktivitets övervakning för att verifiera uttrycket.

### <a name="error-code-2011"></a>Felkod: 2011
- **Meddelande**: aktiviteten kördes på Azure integration Runtime och det gick inte att dekryptera autentiseringsuppgifterna för data lagret eller data bearbetningen som är ansluten via en egen värd integration Runtime. Kontrol lera konfigurationen av länkade tjänster som är associerade med den här aktiviteten och se till att du använder rätt typ av integration Runtime.
- **Orsak**: data flödet stöder inte länkade tjänster på integration runtime med egen värd.
- **Rekommendation**: Konfigurera data flöde som ska köras på en hanterad Virtual Network integration Runtime.

## <a name="miscellaneous-troubleshooting-tips"></a>Övriga fel söknings tips
- **Problem**: ett oväntat undantag inträffade och körningen misslyckades.
    - **Meddelande**: vid körning av data flödes aktivitet: det gick inte att köra ett oväntat undantag och körnings fel.
    - **Orsak**: det här felet är ett Server dels fel. Försök igen och starta om felsökningssessionen.
    - **Rekommendation**: om du försöker igen och omstarten inte löser problemet kontaktar du kund support.

-  **Problem**: inga utdata-data vid koppling under för hands versionen av fel söknings data.
    - **Meddelande**: det finns ett stort antal null-värden eller saknade värden som kan bero på att det inte finns några rader som samplats. Försök att uppdatera gränsen för fel söknings rad och uppdatera data.
    - **Orsak**: kopplings villkoret matchade inte några rader eller resulterade i ett stort antal null-värden under förhands granskningen av data.
    - **Rekommendation**: öka antalet rader i käll rads gränsen i **fel söknings inställningar**. Se till att välja en Azure IR som har ett data flödes kluster som är tillräckligt stort för att hantera mer data.
    
- **Problem**: validerings fel vid källa med CSV-filer med flera rader. 
    - **Meddelande**: något av följande fel meddelanden visas:
        - Den sista kolumnen är null eller saknas.
        - Schema validering vid källan misslyckades.
        - Schema import kan inte visas korrekt i UX och den sista kolumnen har ett nytt rad avstånd i namnet.
    - **Orsak**: i mappnings data flödet fungerar inte csv-källfilerna för flera rader när \r\n används som rad avgränsare. Ibland kan extra rader vid vagn returer orsaka fel. 
    - **Rekommendation**: generera filen vid källan genom att använda \n som rad avgränsare i stället för \r\n. Eller Använd kopierings aktiviteten för att konvertera CSV-filen för att använda \n som en rad avgränsare.

## <a name="general-troubleshooting-guidance"></a>Allmän fel söknings vägledning
1. Kontrol lera statusen för dina data uppsättnings anslutningar. I varje omvandling av källa och mottagare går du till den länkade tjänsten för varje data uppsättning som du använder och testar anslutningarna.
2. Kontrol lera status för dina fil-och tabell anslutningar i data flödes designern. I fel söknings läge väljer du **Förhandsgranska data** på käll omvandlingarna för att se till att du har åtkomst till dina data.
3. Om allting ser korrekt ut i data förhands granskningen, går du till pipeline-designern och sätter ditt data flöde i en pipeline-aktivitet. Felsök pipelinen för ett slut punkt till slut punkts test.

## <a name="next-steps"></a>Nästa steg

Mer hjälp med fel sökning finns i följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
