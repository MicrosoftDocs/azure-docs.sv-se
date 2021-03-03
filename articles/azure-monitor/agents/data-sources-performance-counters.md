---
title: Samla in prestanda data källor för Windows och Linux med Log Analytics-agenten i Azure Monitor
description: Prestanda räknare samlas in av Azure Monitor för att analysera prestanda för Windows-och Linux-agenter.  Den här artikeln beskriver hur du konfigurerar insamling av prestanda räknare för både Windows-och Linux-agenter, information om de lagras i arbets ytan och hur du analyserar dem i Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: c06123b33c7f467e12742cf6180d821e647b5115
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711560"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Samla in prestanda data källor för Windows och Linux med Log Analytics agent
Prestanda räknare i Windows och Linux ger inblick i prestanda för maskin varu komponenter, operativ system och program.  Azure Monitor kan samla in prestanda räknare från Log Analytics agenter med frekventa intervall för NRT-analys (nära real tid), förutom att aggregera prestanda data för analys och rapportering på längre sikt.

> [!IMPORTANT]
> Den här artikeln beskriver hur du samlar in prestanda data med [Log Analytics agent](./log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](../agents/agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

![Prestandaräknare](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurera prestanda räknare
Konfigurera prestanda räknare från [Data-menyn i avancerade inställningar](../agents/agent-data-sources.md#configuring-data-sources) för arbets ytan Log Analytics.

När du först konfigurerar Windows-eller Linux-prestandaräknare för en ny arbets yta får du möjlighet att snabbt skapa flera vanliga räknare.  De listas med en kryssruta bredvid varje.  Se till att alla räknare som du vill skapa först är markerade och klicka sedan på **Lägg till de valda prestanda räknarna**.

För prestanda räknare i Windows kan du välja en angiven instans för varje prestanda räknare. För prestanda räknare för Linux används instansen för varje räknare som du väljer för alla underordnade räknare för den överordnade räknaren. I följande tabell visas de vanliga instanser som är tillgängliga för prestanda räknare för både Linux och Windows.

| Instansnamn | Beskrivning |
| --- | --- |
| \_Totalt |Totalt antal instanser |
| \* |Alla instanser |
| (/&#124;/var) |Matchar instanser med namnet:/eller/var |

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

![Konfigurera Windows-prestandaräknare](media/data-sources-performance-counters/configure-windows.png)

Följ den här proceduren om du vill lägga till en ny Windows-prestandaräknare som ska samlas in. Observera att v2 Windows-prestandaräknare inte stöds.

1. Skriv namnet på räknaren i text rutan i *\counter (format objekt) (instans)*.  När du börjar skriva visas en matchande lista med vanliga räknare.  Du kan antingen välja en räknare från listan eller ange en egen.  Du kan också returnera alla instanser för en viss räknare genom att ange *object\counter*.  

    När du samlar in SQL Server prestanda räknare från namngivna instanser börjar alla namngivna instans räknare starta med *MSSQL $* och följt av namnet på instansen.  Om du till exempel vill samla in förhållandet för träff kvoten för loggen för alla databaser från databasen prestanda objekt för namngiven SQL-instans INST2 anger du `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Klicka **+** eller tryck på **RETUR** för att lägga till räknaren i listan.
3. När du lägger till en räknare används standardvärdet 10 sekunder för **samplings intervallet**.  Du kan ändra det till ett högre värde på upp till 1800 sekunder (30 minuter) om du vill minska lagrings kraven för insamlade prestanda data.
4. När du är klar med att lägga till räknare klickar du på knappen **Spara** längst upp på skärmen för att spara konfigurationen.

### <a name="linux-performance-counters"></a>Linux-prestandaräknare

![Konfigurera Linux-prestandaräknare](media/data-sources-performance-counters/configure-linux-1.png)

Följ den här proceduren om du vill lägga till en ny Linux-prestandaräknare som ska samlas in.

1. Skriv namnet på räknaren i text rutan i *\counter (format objekt) (instans)*.  När du börjar skriva visas en matchande lista med vanliga räknare.  Du kan antingen välja en räknare från listan eller ange en egen.  
1. Klicka **+** eller tryck på **RETUR** för att lägga till räknaren i listan över andra räknare för objektet.
1. Alla räknare för ett objekt använder samma **samplings intervall**.  Standardvärdet är 10 sekunder.  Du ändrar detta till ett högre värde på upp till 1800 sekunder (30 minuter) om du vill minska lagrings kraven för insamlade prestanda data.
1. När du är klar med att lägga till räknare klickar du på knappen **Spara** längst upp på skärmen för att spara konfigurationen.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurera Linux-prestandaräknare i konfigurations filen
I stället för att konfigurera Linux-prestandaräknare med hjälp av Azure Portal har du möjlighet att redigera konfigurationsfiler på Linux-agenten.  Prestanda mått som samlas in styrs av konfigurationen i **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Varje objekt eller kategori av prestanda mått som ska samlas in bör definieras i konfigurations filen som ett enda `<source>` element. Syntaxen följer mönstret nedan.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Parametrarna i det här elementet beskrivs i följande tabell.

| Parametrar | Beskrivning |
|:--|:--|
| objekt \_ namn | Objekt namn för samlingen. |
| instans- \_ regex |  Ett *reguljärt uttryck* som definierar vilka instanser som ska samlas in. Värdet: `.*` anger alla instanser. Om du bara vill samla in processor mått för den \_ totala instansen kan du ange `_Total` . Om du bara vill samla in process mått för crond-eller sshd-instanser kan du ange: `(crond\|sshd)` . |
| räknar \_ namn \_ regex | Ett *reguljärt uttryck* som definierar vilka räknare (för objektet) som ska samlas in. Om du vill samla in alla räknare för objektet anger du: `.*` . Om du till exempel bara vill samla in växlings utrymmes räknare för minnesobjektet kan du ange: `.+Swap.+` |
| interval | Den frekvens med vilken objektets räknare samlas in. |


I följande tabell visas de objekt och räknare som du kan ange i konfigurations filen.  Det finns ytterligare räknare som är tillgängliga för vissa program enligt beskrivningen i [samla in prestanda räknare för Linux-program i Azure Monitor](data-sources-linux-applications.md).

| Objekt namn | Räknarens namn |
|:--|:--|
| Logisk disk | Kostnads fri noder i procent |
| Logisk disk | Ledigt utrymme i procent |
| Logisk disk | % Använda noder i procent |
| Logisk disk | Använt utrymme i procent |
| Logisk disk | Disk-lästa byte/s |
| Logisk disk | Disk läsningar/SEK |
| Logisk disk | Disk överföringar/SEK |
| Logisk disk | Disk-skrivna byte/s |
| Logisk disk | Disk skrivningar/SEK |
| Logisk disk | Lediga megabyte |
| Logisk disk | Logisk disk byte/SEK |
| Minne | Tillgängligt minne i procent |
| Minne | Tillgängligt växlings utrymme i procent |
| Minne | Använt minne i procent |
| Minne | Använt växlings utrymme i procent |
| Minne | Tillgängligt minne i megabyte |
| Minne | Tillgängliga megabyte växlings utrymme |
| Minne | Sid läsningar/s |
| Minne | Sid skrivningar/SEK |
| Minne | Sidor/s |
| Minne | Använt megabyte växlings utrymme |
| Minne | Använt minne i MB |
| Nätverk | Totalt antal överförda byte |
| Nätverk | Totalt antal mottagna byte |
| Nätverk | Totalt antal byte |
| Nätverk | Totalt antal överförda paket |
| Nätverk | Totalt antal mottagna paket |
| Nätverk | Totalt antal mottagna mottagnings fel |
| Nätverk | Totalt antal TX-fel |
| Nätverk | Totalt antal kollisioner |
| Fysisk disk | Medel s/disk läsning |
| Fysisk disk | Medel s/disk överföring |
| Fysisk disk | Medel s/disk skrivning |
| Fysisk disk | Fysisk disk-byte/SEK |
| Process | PCT privilegie rad tid |
| Process | PCT-användar tid |
| Process | Använt minne i KB |
| Process | Virtuellt delat minne |
| Processor | DPC-tid i procent |
| Processor | Inaktivitetstid i procent |
| Processor | % Avbrotts tid |
| Processor | % I/o-vänte tid |
| Processor | % Trevligt tid |
| Processor | Privilegie rad tid i procent |
| Processor | % processortid |
| Processor | Användar tid i procent |
| System | Ledigt fysiskt minne |
| System | Ledigt utrymme i växlingsfiler |
| System | Ledigt virtuellt minne |
| System | Processer |
| System | Storlek lagrad i växlingsfiler |
| System | Drifttid |
| System | Användare |


Följande är standard konfigurationen för prestanda mått.

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in alla angivna prestanda räknare med angivet exempel intervall på alla agenter som har den räknaren installerad.  Data sammanställs inte och rå data är tillgängliga i alla logg frågor för den varaktighet som anges i Log Analytics-arbetsytan.

## <a name="performance-record-properties"></a>Egenskaper för prestanda post
Prestanda poster har en typ av **prestanda** och har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Datorn som händelsen samlades in från. |
| CounterName |Namn på prestanda räknaren |
| CounterPath |Fullständig sökväg till räknaren i formulär \\ \\ \<Computer> \\ objekts räknaren (instans) \\ . |
| CounterValue |Räknarens numeriska värde. |
| InstanceName |Namn på händelse instansen.  Tom om ingen instans. |
| ObjectName |Namn på prestandaobjektet |
| SourceSystem |Typ av agent som data samlades in från. <br><br>OpsManager – Windows-agent, antingen direkt anslutning eller SCOM <br> Linux – alla Linux-agenter  <br> AzureStorage – Azure-diagnostik |
| TimeGenerated |Datum och tid då data samplades. |

## <a name="sizing-estimates"></a>Storleks uppskattningar
 En grov uppskattning för insamling av en viss räknare vid 10 sekunders intervall är ungefär 1 MB per dag per instans.  Du kan beräkna lagrings kraven för en viss räknare med följande formel.

> 1 MB x (antal räknare) x (antal agenter) x (antal instanser)

## <a name="log-queries-with-performance-records"></a>Logga frågor med prestanda poster
Följande tabell innehåller olika exempel på logg frågor som hämtar prestanda poster.

| Söka i data | Beskrivning |
|:--- |:--- |
| Prest |Alla prestanda data |
| Perf &#124; där dator = = "Min Dator" |Alla prestanda data från en viss dator |
| Perf &#124; där CounterName = = "Aktuell diskkölängd" |Alla prestanda data för en viss räknare |
| Perf &#124; WHERE ObjectName = = "processor" och CounterName = = "% processor tid" och InstanceName = = "_Total" &#124; sammanfatta AVGCPU = AVG (CounterValue) efter dator |Genomsnittlig CPU-belastning på alla datorer |
| Perf &#124; där CounterName = = "% processor tid" &#124; sammanfatta AggregatedValue = Max (CounterValue) efter dator |Maximal processor användning på alla datorer |
| Perf &#124; WHERE ObjectName = = "logisk disk" och CounterName = = "Aktuell diskkölängd" och dator = = "" &#124; sammanfatta AggregatedValue = AVG (CounterValue) efter instans namn |Genomsnittlig Kölängd för aktuell disk i alla instanser av en viss dator |
| Perf &#124; där CounterName = = "disk överföringar/s" &#124; sammanfatta AggregatedValue = percentil (CounterValue, 95) efter dator |95 percentil av disk överföringar/SEK över alla datorer |
| Perf &#124; där CounterName = = "% processor tid" och InstanceName = = "_Total" &#124; sammanfatta AggregatedValue = AVG (CounterValue) per bin (TimeGenerated, 1H), dator |Genomsnittlig CPU-användning per timme på alla datorer |
| Perf &#124; där dator = = "Min Dator" och CounterName startswith_cs "%" och InstanceName = = "_Total" &#124; sammanfatta AggregatedValue = percentil (CounterValue, 70) per bin (TimeGenerated, 1H), CounterName | Varje timme 70 percentil med varje% procentuell räknare för en viss dator |
| Perf &#124; där CounterName = = "% processor tid" och InstanceName = = "_Total" och dator = = "Min Dator" &#124; sammanfatta ["min (CounterValue)"] = min (CounterValue). ["AVG (CounterValue)"] = AVG (CounterValue), ["percentile75 (CounterValue)"] = percentil (CounterValue, 75), ["Max (CounterValue)"] = Max (CounterValue) per bin (TimeGenerated, 1H), dator |Genomsnittligt per timme, lägsta, högsta och 75 CPU-användning för en speciell dator |
| Perf &#124; WHERE ObjectName = = "MSSQL $ INST2: databaser" och InstanceName = = "Master" | Alla prestanda data från databasens prestanda objekt för Master-databasen från den namngivna SQL Server-instansen INST2.  




## <a name="next-steps"></a>Nästa steg
* [Samla in prestanda räknare från Linux-program](data-sources-linux-applications.md) , inklusive MySQL och Apache HTTP server.
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.  
* Exportera insamlade data till [Power BI](../visualize/powerbi.md) för ytterligare visualiseringar och analyser.