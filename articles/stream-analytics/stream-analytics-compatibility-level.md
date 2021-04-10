---
title: Azure Stream Analytics kompatibilitetsnivå
description: Lär dig hur du ställer in en kompatibilitetsnivå för ett Azure Stream Analytics jobb och större ändringar på den senaste kompatibilitetsnivån
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: a040aecbdee40832bd21256e26a140a986b65e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606250"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivå för Azure Stream Analytics jobb

I den här artikeln beskrivs alternativet kompatibilitetsnivå i Azure Stream Analytics.

Stream Analytics är en hanterad tjänst med [regelbundna funktions uppdateringar och prestanda förbättringar](https://azure.microsoft.com/updates/?product=stream-analytics). De flesta av tjänstens körnings uppdateringar görs automatiskt tillgängliga för slutanvändare, oberoende av kompatibilitetsnivån. Men när en ny funktion medför en ändring av beteendet hos befintliga jobb, eller om en ändring av hur data förbrukas i jobb som körs, så introducerar vi den här ändringen under en ny kompatibilitetsnivå. Du kan behålla dina befintliga Stream Analytics-jobb som körs utan större ändringar genom att lämna inställningen för kompatibilitetsnivå lägre. När du är redo för de senaste körnings funktionerna kan du välja att öka kompatibilitetsnivån.


## <a name="choose-a-compatibility-level"></a>Välj en kompatibilitetsnivå

Kompatibilitetsnivå styr körnings beteendet för ett Stream Analytics-jobb.

Azure Stream Analytics stöder för närvarande tre kompatibilitetsnivå:

* 1,2-senaste beteende med de senaste förbättringarna
* 1,1-tidigare beteende
* 1,0 – ursprunglig kompatibilitetsnivå, som introducerades under allmän tillgänglighet för Azure Stream Analytics flera år sedan. 

När du skapar ett nytt Stream Analytics jobb är det en bra idé att skapa det med den senaste kompatibilitetsnivån. Starta din jobb design som förlitar sig på de senaste beteendena, för att undvika ökad förändring och komplexitet senare.

## <a name="set-the-compatibility-level"></a>Ange kompatibilitetsnivå

Du kan ange kompatibilitetsnivån för ett Stream Analytics jobb i Azure Portal eller med hjälp av [anropet skapa jobb REST API](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel).

Så här uppdaterar du kompatibilitetsnivån för jobbet i Azure Portal:

1. Använd [Azure Portal](https://portal.azure.com) för att leta upp Stream Analytics jobb.
2. **Stoppa** jobbet innan du uppdaterar kompatibilitetsnivån. Du kan inte uppdatera kompatibilitetsnivån om jobbet är i ett körnings läge.
3. Under **Konfigurera** rubrik väljer du **kompatibilitetsnivå**.
4. Välj det värde för kompatibilitetsnivå som du vill använda.
5. Välj **Spara** längst ned på sidan.

![Stream Analytics kompatibilitetsnivå i Azure Portal](media/stream-analytics-compatibility-level/stream-analytics-compat-level-1-2.png)

När du uppdaterar kompatibilitetsnivån verifierar T-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån.

## <a name="compatibility-level-12"></a>Kompatibilitetsnivå 1,2

Följande större ändringar introduceras i kompatibilitetsnivån 1,2:

###  <a name="amqp-messaging-protocol"></a>AMQP meddelande protokoll

**1,2 nivå**: Azure Stream Analytics använder meddelande protokollet [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) för att skriva till Service Bus köer och ämnen. Med AMQP kan du skapa hybrid program mellan plattformar med ett öppet standard protokoll.

### <a name="geospatial-functions"></a>Geospatiala funktioner

**Tidigare nivåer:** Azure Stream Analytics använda geografi beräkningar.

**1,2-nivå:** Med Azure Stream Analytics kan du beräkna geometriska projekterade geo-koordinater. Signaturen för geospatiala funktioner ändras inte. Deras semantik är dock något annorlunda, vilket ger mer exakt beräkning än tidigare.

Azure Stream Analytics stöder Geospatial referens data indexering. Referens data som innehåller geospatiala element kan indexeras för en snabbare kopplings beräkning.

De uppdaterade geospatiala funktionerna använder geospatialt Expressiveness (well) geospatialt format. Du kan ange andra geospatiala komponenter som inte tidigare har stöd för interjson.

Mer information finns i [uppdateringar av geospatiala funktioner i Azure Stream Analytics – molnet och IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallell frågekörning för inmatade källor med flera partitioner

**Tidigare nivåer:** Azure Stream Analytics frågor krävde att PARTITIONER BY-satsen ska användas för att parallellisera frågor över partitioner för indatakälla.

**1,2-nivå:** Om frågans logik kan vara parallellt över partitioner med indatakälla skapar Azure Stream Analytics separata instanser och kör beräkningar parallellt.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Intern Mass-API-integrering med CosmosDB-utdata

**Tidigare nivåer:** Upsert beteendet *infogades eller sammanfogades*.

**1,2-nivå:** Intern Mass-API-integrering med CosmosDB-utdata maximerar data flödet och hanterar begränsnings begär Anden effektivt. Mer information finns [på sidan Azure Stream Analytics utdata till Azure Cosmos DB](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12).

Upsert beteende är *Infoga eller Ersätt*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset vid skrivning till SQL-utdata

**Tidigare nivåer:** [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql) -typerna justerades till UTC.

**1,2-nivå:** DateTimeOffset är inte längre justerad.

### <a name="long-when-writing-to-sql-output"></a>Långt vid skrivning till SQL-utdata

**Tidigare nivåer:** Värdena trunkerades baserat på måltypen.

**1,2-nivå:** Värden som inte passar i måltypen hanteras enligt principen för utgående fel.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Post-och mat ris serialisering vid skrivning till SQL-utdata

**Tidigare nivåer:** Poster skrevs som "Record" och matriser skrevs som "matris".

**1,2-nivå:** Poster och matriser serialiseras i JSON-format.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikt validering av prefix för functions

**Tidigare nivåer:** Det finns ingen strikt verifiering av funktions prefix.

**1,2-nivå:** Azure Stream Analytics har en strikt validering av funktions prefix. Om du lägger till ett prefix i en inbyggd funktion uppstår ett fel. Stöds till exempel `myprefix.ABS(…)` inte.

Att lägga till ett prefix i inbyggda mängder resulterar också i fel. Stöds till exempel `myprefix.SUM(…)` inte.

Om du använder prefixet "system" för användardefinierade funktioner resulterar det i fel.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Tillåt inte matris och objekt som nyckel egenskaper i Cosmos DB output adapter

**Tidigare nivåer:** Matris-och objekt typer stöds som en nyckel egenskap.

**1,2-nivå:** Matris-och objekt typer stöds inte längre som en nyckel egenskap.

## <a name="compatibility-level-11"></a>Kompatibilitetsnivå 1,1

Följande större ändringar introduceras i kompatibilitetsnivån 1,1:

### <a name="service-bus-xml-format"></a>Service Bus XML-format

**1,0-nivå:** Azure Stream Analytics använda DataContractSerializer, så att meddelande innehållet innehöll XML-taggar. Exempel:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1,1-nivå:** Meddelande innehållet innehåller data strömmen direkt utan ytterligare taggar. Exempelvis: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Bevara Skift läges känslighet för fält namn

**1,0-nivå:** Fält namn ändrades till gemener när de bearbetas av Azure Stream Analytics-motorn.

**1,1 nivå:** Skift läges känslighet är bestående för fält namn när de bearbetas av Azure Stream Analyticss motorn.

> [!NOTE]
> Kvarhållning av Skift läge – känslighet är inte tillgängligt ännu för Stream-analys jobb som hanteras med hjälp av gräns miljön. Detta innebär att alla fält namn konverteras till gemener om jobbet finns på gränsen.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1,0-nivå:** CREATE TABLE kommandot filtrerade inte händelser med NaN (inte ett tal. Till exempel oändligt, oändlighet) i en flytt ALS kolumn typ eftersom de ligger utanför det dokumenterade intervallet för dessa tal.

**1,1-nivå:** Med CREATE TABLE kan du ange ett starkt schema. Stream Analyticss motorn verifierar att data överensstämmer med det här schemat. Med den här modellen kan kommandot filtrera händelser med NaN-värden.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Inaktivera automatisk konvertering av datetime-strängar till DateTime-typ vid ingress för JSON

**1,0-nivå:** JSON-parsern konverterar automatiskt sträng värden med datum-/tids-/zon information till DATETIME-typ vid ingångs datum så att värdet omedelbart förlorar sin ursprungliga formatering och tids zons information. Eftersom detta görs i ingress, även om fältet inte användes i frågan, konverteras det till UTC DateTime.

**1,1-nivå:** Det finns ingen automatisk konvertering av sträng värden med datum-/tids-/zon information till DATETIME-typen. Därför behålls tids zons informationen och den ursprungliga formateringen. Om t. ex. fältet NVARCHAR (MAX) används i frågan som en del av ett DATETIME-uttryck (till exempel funktionen DATEADD) konverteras den till DATETIME-typ för att utföra beräkningen och den förlorar sitt ursprungliga form.

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Stream Analytics resurs hälsa](./stream-analytics-troubleshoot-query.md)
