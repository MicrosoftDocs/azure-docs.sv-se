---
title: Kom igång med logg frågor i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att komma igång med att skriva logg frågor i Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: c87b70c74a5de9cc4b6c48935b93fdd13295f0f8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052338"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Kom igång med loggfrågor i Azure Monitor

> [!NOTE]
> Du kan arbeta med den här övningen i din egen miljö om du samlar in data från minst en virtuell dator. Om du inte gör det använder du vår [demo miljö](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), som innehåller massor av exempel data.  Om du redan vet hur du frågar i KQL, men bara behöver skapa användbara frågor baserat på resurs typ (er), kan du läsa avsnittet [sparade exempel frågor](../logs/example-queries.md).

I den här självstudien får du lära dig att skriva logg frågor i Azure Monitor. Du får lära dig att:

- Förstå frågans struktur
- Sortera frågeresultat
- Filtrera frågeresultat
- Ange ett tidsintervall
- Välj vilka fält som ska inkluderas i resultaten
- Definiera och Använd anpassade fält
- Sammanställda och grupp resultat

En själv studie kurs om hur du använder Log Analytics i Azure Portal finns i [Kom igång med Azure Monitor Log Analytics](./log-analytics-tutorial.md).<br>
Mer information om logg frågor i Azure Monitor finns i [Översikt över logg frågor i Azure Monitor](../logs/log-query-overview.md).

Följ tillsammans med en video version av den här kursen nedan:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Skriva en ny fråga

Frågor kan inledas med antingen ett tabell namn eller kommandot *search* . Du måste börja med ett tabell namn, eftersom det definierar en tydlig omfattning för frågan och förbättrar både frågans prestanda och relevansen för resultatet.

> [!NOTE]
> Frågespråket Kusto som används i Azure Monitor är skiftlägeskänsligt. Språkets nyckelord skrivs vanligtvis med gemener. När du använder namn på tabeller eller kolumner i en fråga, se till att använda rätt Skift läge, som du ser i rutan schema.

### <a name="table-based-queries"></a>Tabellbaserade frågor

Azure Monitor organiserar loggdata i form av tabeller som var och en består av flera kolumner. Alla tabeller och kolumner visas i rutan schema i Log Analytics i Analytics-portalen. Identifiera en tabell som du är intresse rad av och ta en titt på data:

```Kusto
SecurityEvent
| take 10
```

Frågan som visas ovan returnerar 10 resultat från tabellen *SecurityEvent* , i någon speciell ordning. Det här är ett mycket vanligt sätt att ta en titt på en tabell och förstå dess struktur och innehåll. Nu ska vi kontrol lera hur det är byggt:

* Frågan börjar med tabell namnet *SecurityEvent* – den här delen definierar frågans omfång.
* Pipe-tecknet (|) separerar kommandon, så utdata från den första i indata för följande kommando. Du kan lägga till valfritt antal skickas-element.
* Efter denna pipe finns kommandot **ta** , som returnerar ett angivet antal godtyckliga poster från tabellen.

Vi kunde faktiskt köra frågan även utan att lägga till `| take 10` – som fortfarande är giltig, men den kan returnera upp till 10 000 resultat.

### <a name="search-queries"></a>Sökfrågor

Sök frågor är mindre strukturerade och passar i allmänhet bättre för att hitta poster som innehåller ett särskilt värde i någon av kolumnerna:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Den här frågan söker efter poster som innehåller frasen "kryptografisk" i *SecurityEvent* -tabellen. Av dessa poster returneras och visas 10 poster. Om vi utelämnar `in (SecurityEvent)` delen och bara kör så fortsätter `search "Cryptographic"` sökningen över *alla* tabeller, vilket skulle ta längre tid och vara mindre effektivt.

> [!WARNING]
> Sök frågor är vanligt vis långsammare än tabellbaserade frågor eftersom de måste bearbeta mer data. 

## <a name="sort-and-top"></a>Sortera och överst
Det kan vara praktiskt att **ta** emot några poster, men resultaten är markerade och visas inte i någon viss ordning. Om du vill hämta en ordnad vy kan du **Sortera** efter den önskade kolumnen:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Det kan returnera för många resultat, men kan också ta lite tid. Frågan ovan sorterar *hela* SecurityEvent-tabellen efter kolumnen TimeGenerated. Analys portalen begränsar sedan visningen så att endast 10 000 poster visas. Den här metoden är naturligtvis inte optimal.

Det bästa sättet att bara hämta de senaste 10 posterna är att använda **överst**, som sorterar hela tabellen på Server sidan och sedan returnerar de översta posterna:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Fallande sorterings ordning är standard sorterings ordningen, så vi utesluter vanligt vis argumentet **DESC** . Utdata kommer att se ut så här:

![Topp 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Där: filtrera på ett villkor
Filter, som de anges efter namn, filtrerar data efter ett angivet villkor. Detta är det vanligaste sättet att begränsa frågeresultaten till relevant information.

Om du vill lägga till ett filter i en fråga använder du operatorn **WHERE** följt av ett eller flera villkor. Följande fråga returnerar till exempel bara *SecurityEvent* -poster där _Level_ är lika med _8_:

```Kusto
SecurityEvent
| where Level == 8
```

När du skriver filter villkor kan du använda följande uttryck:

| Uttryck | Beskrivning | Exempel |
|:---|:---|:---|
| == | Kontrol lera likhet<br>(Skift läges känsligt) | `Level == 8` |
| =~ | Kontrol lera likhet<br>(inte Skift läges känsligt) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Kontrol lera olikhet<br>(båda uttrycken är identiska) | `Level != 4` |
| *and*, *or* | Krävs mellan villkor| `Level == 16 or CommandLine != ""` |

Om du vill filtrera efter flera villkor kan du antingen använda **och**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

eller rör flera **där** elementen ett efter den andra:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Värden kan ha olika typer, så du kan behöva omvandla dem för att utföra jämförelse av rätt typ. Till exempel är kolumnen SecurityEvent *nivå* av typen sträng, så du måste omvandla den till en numerisk typ, till exempel *int* eller *Long*, innan du kan använda numeriska operatorer på den: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Ange ett tidsintervall

### <a name="time-picker"></a>Tids väljare

Tids väljaren är bredvid knappen Kör och anger att vi bara vill fråga efter poster från de senaste 24 timmarna. Detta är standard tidsintervallet som används för alla frågor. Om du bara vill hämta poster från den senaste timmen väljer du _senaste timmen_ och kör frågan igen.

![Tidväljaren](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tids filter i fråga

Du kan också definiera ett eget tidsintervall genom att lägga till ett tids filter i frågan. Det är bäst att placera tids filtret omedelbart efter tabell namnet: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

I ovanstående tids filter  `ago(30m)` innebär "30 minuter sedan" så den här frågan returnerar bara poster från de senaste 30 minuterna. Andra tidsenheter inkluderar dagar (2D), minuter (25m) och sekunder (tiotal).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt och utöka: Välj och beräkna kolumner

Använd **Project** för att välja vilka kolumner som ska ingå i resultaten:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Föregående exempel genererar följande utdata:

![Frågeresultat för projekt](media/get-started-queries/project.png)

Du kan också använda **Project** för att byta namn på kolumner och definiera nya. I följande exempel används Project för att göra följande:

* Välj endast de ursprungliga kolumnerna för *dator* och *TimeGenerated* .
* Visar *aktivitets* kolumnen som *EventDetails*.
* Skapa en ny kolumn med namnet *EventCode*. Funktionen **substring ()** används för att bara hämta de första fyra tecknen från fältet aktivitet.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**utöka** behåller alla ursprungliga kolumner i resultat uppsättningen och definierar ytterligare dem. Följande fråga använder **utöka** för att lägga till kolumnen *EventCode* . Observera att den här kolumnen kanske inte visas i slutet av tabellen, vilket innebär att du skulle behöva expandera informationen i en post för att visa den.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Sammanfatta: aggregerade grupper av rader
Använd **sammanfatta** för att identifiera grupper av poster, enligt en eller flera kolumner, och tillämpa agg regeringar för dem. Den vanligaste användningen av **Sammanfattning** är *Count*, som returnerar antalet resultat i varje grupp.

Följande fråga granskar alla *perf* -poster från den senaste timmen, grupperar dem efter *ObjectName* och räknar posterna i varje grupp: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Ibland är det klokt att definiera grupper efter flera dimensioner. Varje unik kombination av dessa värden definierar en separat grupp:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

En annan vanlig användning är att utföra matematiska eller statistiska beräkningar på varje grupp. Följande beräknar till exempel den genomsnittliga *CounterValue* för varje dator:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Tyvärr är resultatet av frågan meningslöst eftersom vi blandade olika prestanda räknare. För att göra detta mer meningsfullt bör du beräkna genomsnittet separat för varje kombination av *CounterName* och *dator*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Sammanfatta per tids kolumn
Grupp resultat kan också baseras på en tids kolumn eller ett annat kontinuerligt värde. Om du bara sammanfattar `by TimeGenerated` flera gånger skapas grupper för varje enskild millisekund över tidsintervallet, eftersom dessa är unika värden. 

Om du vill skapa grupper baserat på kontinuerliga värden, är det bäst att dela upp intervallet i hanterbara enheter med hjälp av **lager**. Följande fråga analyserar *prestanda* poster som mäter ledigt minne (*Tillgängliga megabyte*) på en speciell dator. Den beräknar genomsnitts värdet för varje timme-period under de senaste 7 dagarna:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Om du vill göra resultatet tydligare väljer du att visa det som ett tids diagram med tillgängligt minne över tid:

![Fråga efter minne över tid](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du använder sträng data i en logg fråga med att [arbeta med strängar i Azure Monitor logg frågor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Lär dig mer om att aggregera data i en logg fråga med [avancerade agg regeringar i Azure Monitor logg frågor](/azure/data-explorer/write-queries#advanced-aggregations).
- Lär dig hur du kopplar data från flera tabeller med [kopplingar i Azure Monitor logg frågor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Hämta dokumentation om hela Kusto-frågespråket i [språk referens för KQL](/azure/kusto/query/).