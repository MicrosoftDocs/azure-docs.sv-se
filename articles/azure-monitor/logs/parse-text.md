---
title: Parsa text data i Azure Monitor loggar | Microsoft Docs
description: Beskriver olika alternativ för att parsa loggdata i Azure Monitor poster när data matas in och när de hämtas i en fråga, jämför de relativa fördelarna för var och en.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 29393126f94d77dbced0ef896616b945443184fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625259"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Parsa text data i Azure Monitor loggar
Vissa loggdata som samlas in av Azure Monitor innehåller flera informations delar i en enda egenskap. Att parsa dessa data i flera egenskaper gör det enklare att använda i frågor. Ett vanligt exempel är en [anpassad logg](../agents/data-sources-custom-logs.md) som samlar in en hel logg post med flera värden i en enda egenskap. Genom att skapa separata egenskaper för de olika värdena kan du söka efter och aggregera dem.

I den här artikeln beskrivs olika alternativ för att parsa loggdata i Azure Monitor när data matas in och när de hämtas i en fråga, jämför de relativa fördelarna för var och en.


## <a name="parsing-methods"></a>Tolknings metoder
Du kan parsa data antingen vid inläsnings tiden när data samlas in eller vid tidpunkten när data analyseras med en fråga. Varje strategi har unika fördelar enligt beskrivningen nedan.

### <a name="parse-data-at-collection-time"></a>Parsa data vid samlings tid
När du parsar data vid samlings tiden konfigurerar du [anpassade fält](../logs/custom-fields.md) som skapar nya egenskaper i tabellen. Frågor behöver inte inkludera någon tolknings logik och använder bara dessa egenskaper som andra fält i tabellen.

Fördelarna med den här metoden är följande:

- Enklare att fråga efter insamlade data eftersom du inte behöver inkludera analys kommandon i frågan.
- Bättre frågeresultat eftersom frågan inte behöver parsas.
 
Nack delarna med den här metoden är följande:

- Måste definieras i förväg. Det går inte att inkludera data som redan har samlats in.
- Om du ändrar tolknings logiken kommer den endast att gälla för nya data.
- Färre tolknings alternativ än vad som är tillgängligt i frågor.
- Ökar svars tiden för insamling av data.
- Det kan vara svårt att hantera fel.


### <a name="parse-data-at-query-time"></a>Parsa data vid tid för fråga
När du parsar data vid tidpunkten för frågor inkluderar du logik i din fråga för att analysera data i flera fält. Själva tabellen ändras inte.

Fördelarna med den här metoden är följande:

- Gäller för alla data, inklusive data som redan har samlats in.
- Ändringar i logiken kan tillämpas direkt på alla data.
- Flexibla tolknings alternativ inklusive fördefinierad logik för specifika data strukturer.
 
Nack delarna med den här metoden är följande:

- Kräver mer komplexa frågor. Detta kan minimeras genom att använda [funktioner för att simulera en tabell](#use-function-to-simulate-a-table).
- Måste replikera logik för parsning i flera frågor. Kan dela en del logik via functions.
- Kan skapa overhead när du kör komplex logik mot mycket stora post uppsättningar (miljard tals poster).

## <a name="parse-data-as-its-collected"></a>Parsa data när de samlas in
Information om hur du tolkar data när de samlas in finns i [skapa anpassade fält i Azure Monitor](../logs/custom-fields.md) . Detta skapar anpassade egenskaper i tabellen som kan användas av frågor precis som andra egenskaper.

## <a name="parse-data-in-query-using-patterns"></a>Parsa data i fråga med hjälp av mönster
När de data som du vill parsa kan identifieras av ett mönster som upprepas i poster, kan du använda olika operatorer i [Kusto-frågespråket](/azure/kusto/query/) för att extrahera specifika data till en eller flera nya egenskaper.

### <a name="simple-text-patterns"></a>Enkla text mönster

Använd operatorn [parsa](/azure/kusto/query/parseoperator) i frågan för att skapa en eller flera anpassade egenskaper som kan extraheras från ett sträng uttryck. Du anger det mönster som ska identifieras och namnen på de egenskaper som ska skapas. Detta är särskilt användbart för data med nyckel värdes strängar med ett formulär liknande _nyckel = värde_.

Överväg en anpassad logg med data i följande format.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Följande fråga tolkar dessa data i enskilda egenskaper. Raden med _projektet_ läggs till för att endast returnera de beräknade egenskaperna och inte _RawData_, vilket är den enskilda egenskapen som innehåller hela posten från den anpassade loggen.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Följande är ett annat exempel som avbryter användar namnet för ett UPN i _AzureActivity_ -tabellen.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguljära uttryck
Om dina data kan identifieras med ett reguljärt uttryck kan du använda [funktioner som använder reguljära uttryck](/azure/kusto/query/re2) för att extrahera enskilda värden. I följande exempel används [Extract](/azure/kusto/query/extractfunction) för att dela upp _UPN_ -fältet från _AzureActivity_ -poster och sedan returnera distinkta användare.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

För att möjliggöra effektiv parsning i stor skala, Azure Monitor använder re2-versionen av reguljära uttryck som liknar men inte identiska med några av de andra varianterna av reguljära uttryck. Mer information finns i [syntaxen för re2-uttrycket](https://aka.ms/kql_re2syntax) .


## <a name="parse-delimited-data-in-a-query"></a>Parsa avgränsade data i en fråga
Avgränsade data separerar fält med ett gemensamt Character, till exempel ett kommatecken i en CSV-fil. Använd funktionen [Split](/azure/kusto/query/splitfunction) för att parsa avgränsade data med hjälp av en avgränsare som du anger. Du kan använda detta med [Extend](/azure/kusto/query/extendoperator) -operatorn för att returnera alla fält i data eller för att ange enskilda fält som ska ingå i utdata.

> [!NOTE]
> Eftersom Split returnerar ett dynamiskt objekt kan resultaten behöva skickas explicit till data typer, till exempel en sträng som ska användas i operatorer och filter.

Överväg en anpassad logg med data i följande CSV-format.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Följande fråga tolkar dessa data och sammanfattar med två av de beräknade egenskaperna. Den första raden delar upp egenskapen  _RawData_ i en sträng mat ris. Var och en av de följande raderna ger ett namn för enskilda egenskaper och lägger till dem i utdata med hjälp av funktioner för att konvertera dem till rätt datatyp.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Parsa fördefinierade strukturer i en fråga
Om dina data är formaterade i en känd struktur kan du kanske använda en av funktionerna i [Kusto-frågespråket](/azure/kusto/query/) för att parsa fördefinierade strukturer:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL-fråga](/azure/kusto/query/parseurlqueryfunction)
- [Filsökväg](/azure/kusto/query/parsepathfunction)
- [Användar agent](/azure/kusto/query/parse-useragentfunction)
- [Versions sträng](/azure/kusto/query/parse-versionfunction)

I följande exempel fråga parsar fältet _Egenskaper_ i _AzureActivity_ -tabellen som är strukturerad i JSON. Resultatet sparas i en dynamisk egenskap med namnet _parsedProp_, som innehåller det enskilda namngivna värdet i JSON. Dessa värden används för att filtrera och sammanfatta frågeresultaten.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Dessa analys funktioner kan vara processor intensiva, så de bör endast användas när frågan använder flera egenskaper från de formaterade data. Annars blir enkel mönster matchnings bearbetning snabbare.

I följande exempel visas fördelningen av TGT-typen för domänkontrollanten. Typen finns bara i fältet EventData, som är en XML-sträng, men inga andra data från det här fältet behövs. I det här fallet används [parse](/azure/kusto/query/parseoperator) för att hämta de data som krävs.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Använd funktionen för att simulera en tabell
Du kan ha flera frågor som utför samma parsning av en viss tabell. I det här fallet [skapar du en funktion](../logs/functions.md) som returnerar parsade data i stället för att replikera tolknings logiken i varje fråga. Du kan sedan använda funktions Ali Aset i stället för den ursprungliga tabellen i andra frågor.

Överväg det kommaavgränsade anpassade logg exemplet ovan. För att kunna använda parsade data i flera frågor, skapar du en funktion med hjälp av följande fråga och sparar den med aliaset _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Du kan nu använda aliaset _MyCustomCSVLog_ i stället för det faktiska tabell namnet i frågor som följande.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.
