---
title: Övervaka Azure blockchain service (ABS)
description: Övervaka Azure blockchain-tjänsten via Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594923"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Övervaka Azure blockchain-tjänsten via Azure Monitor  

Kunder som kör blockchain-scenarier i produktions klass på ABS (Azure blockchain service) är avgörande för att övervaka resurserna för tillgänglighet, prestanda och åtgärder. Den här artikeln beskriver övervaknings data som genererats av Azure blockchain-tjänsten och hur en kan använda de olika funktionerna och integreringarna i Azure Monitor för att analysera och varna för, för att hantera miljöer för produktions miljön.  

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure blockchain service skapar övervaknings data med hjälp av Azure Monitor, som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser. Mer information om Azure Monitor finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in från Azure blockchain-tjänsten och tillhandahålla exempel för att konfigurera data insamling och analysera data med Azure-verktyg.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Övervaka data som samlas in från Azure blockchain-tjänsten  

Azure blockchain-tjänsten samlar in samma typ av övervaknings data som andra Azure-resurser, som beskrivs i [övervaknings data](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) från Azure-resurser. Se [övervaka data referens för Azure blockchain-tjänsten](#monitor-azure-blockchain-service-data-reference) om du vill ha en detaljerad referens för de loggar och mått som skapats av tjänsten Azure blockchain.

Översikts sidan i Azure Portal för varje Azure blockchain service member-resurs innehåller en kort vy över transaktionerna, inklusive begär Anden som hanteras och bearbetade block. En del av dessa data samlas in automatiskt och är tillgängliga för analys när du har skapat Azure blockchain-tjänstens medlems resurs, medan du kan aktivera ytterligare data insamling med ytterligare konfiguration.

## <a name="diagnostic-settings"></a>Diagnostikinställningar  

Plattforms mått och aktivitets loggen samlas in automatiskt, men du måste skapa en diagnostisk inställning för att samla in resurs loggar eller vidarebefordra dem utanför Azure Monitor. Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../../azure-monitor/essentials/diagnostic-settings.md) .

När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure blockchain-tjänsten visas nedan.

**Blockchain-proxy** – Välj kategorin om du vill övervaka ngnix-proxy-loggarna. All kund transaktions information finns i gransknings-och fel söknings syfte.  

**Blockchain program loggar** – Välj kategorin för att hämta loggar för det blockchain-program som hanteras av den hanterade tjänsten. För en ABS-Quorum-medlem är dessa loggar till exempel loggarna från själva kvorumet.  

**Mått begär Anden**: Välj alternativet för att samla in mått data från Azure Cosmos dB till målen i den diagnostiska inställningen, som samlas in automatiskt i Azure-mått. Samla in Mät data med resurs loggar för att analysera båda typerna av data och skicka mått data utanför Azure Monitor.

## <a name="analyze-metric-data"></a>Analysera mått data  

Du kan analysera mått för Azure blockchain service med Metrics Explorer, gå till fliken mått under avsnittet övervakning på bladet ABS-resurs. Mer information om hur du använder verktyget finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) . De fullständiga måtten för Azure blockchain-tjänsten finns i namn området Azure blockchain service standard mått.

Du kan använda **Node** -dimensionen när du lägger till ett filter eller delar upp mått, som i princip innehåller mått värden per Transaction-noder och validator-noder i ABS-medlemmen.

## <a name="analyze-log-data"></a>Analysera loggdata

Här följer några frågor som du kan ange i logg Sök fältet för att övervaka dina Azure blockchain service-medlemmar. Dessa frågor fungerar med det [nya språket](../../azure-monitor/logs/log-query-overview.md).

Om du vill fråga fel villkoren i blockchain program loggar använder du nedanstående fråga:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Om du vill fråga fel villkoren i blockchain proxy-loggar använder du nedanstående fråga  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Du kan använda de tids filter som är tillgängliga i Azure-loggarna för att filtrera frågan under ett särskilt tidsintervall.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Övervaka data referens för Azure blockchain-tjänsten  

Den här artikeln innehåller en referens till de logg-och mått data som samlas in för att analysera prestanda och tillgänglighet för Azure blockchain-tjänsten.  

### <a name="resource-logs"></a>Resursloggar

Alla resurs loggar delar ett gemensamt schema på översta nivån med få unika egenskaper som är specifika för blockchain-tjänsten. Du kan referera till schemat för [resurs loggar på den översta nivån](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). information om de Azure blockchain-tjänstespeciella egenskaperna beskrivs nedan  

I följande tabell visas egenskaperna för Azure blockchain proxy-loggar när de samlas in i Azure Monitor loggar eller Azure Storage.  


| Egenskapsnamn  | Description |
|:---|:---|
| time | Datum och tid (UTC) när åtgärden utfördes. |
| resourceID  | Azure blockchain-Tjänsteresursen för vilka loggar är aktiverade.  |
| category  |För Azure blockchain-tjänsten är de värden som är möjliga **Proxylogs** och **Applicationlogs**. |
| operationName  | Namnet på åtgärden som representeras av den här händelsen.   |
| Loggnings nivå  | Som standard aktiverar Azure blockchain service **informations** logg nivå.   |
| NodeLocation  | Azure-region där blockchain-medlemmen har distribuerats.  |
| BlockchainNodeName  | Namnet på noden för den Azure blockchain service-medlem som åtgärden utförs på.   |
| EthMethod  | Metoden, som anropas av det underliggande blockchain-protokollet, i kvorum, kan vara eth_sendTransactions, eth_getBlockByNumber osv.  |
| Agent  | Användar agenten som agerar för en användares räkning, t. ex. webb läsar-Mozilla, Edge osv. Exempel på värden är: "Mozilla/5.0 (Linux x64) node.js/8.16.0 V8/6.2.414.77"  |
| Kod   | HTTP-felkoder. Vanligt vis är 4XX och 5XX fel villkor.  |
| NodeHost  | Nodens DNS-namn.   |
| RequestMethodName | HTTP-metoden heter, de möjliga värdena här används för att skapa medlemmar, hämta information om befintlig medlem, ta bort för borttagnings medlem, uppdaterings uppdatering av medlemmar.   |
| BlockchainMemberName  | Azure blockchain-tjänstens medlems namn tillhandahålls av användaren.  |
| Konsortium | Namnet på konsortiet som anges av användaren.   |
| Fjärransluten  | IP-adressen för klienten där begäran kommer.  |
| RequestSize  | Storlek på begäran som gjorts i byte.  |
| RequestTime  | Varaktigheten för begäran i millisekunder.|




I följande tabell visas egenskaperna för program loggarna för Azure blockchain.


| Egenskapsnamn  | Description |
|:---|:---|
| time | Datum och tid (UTC) när åtgärden utfördes. |
| resourceID  | Azure blockchain-Tjänsteresursen för vilka loggar är aktiverade.|
| category  |För Azure blockchain-tjänsten är det möjliga värdet **Proxylogs** och **Applicationlogs**.  |
| operationName  | Namnet på åtgärden som representeras av den här händelsen.   |
| Loggnings nivå  | Som standard aktiverar Azure blockchain service **informations** logg nivå.   |
| NodeLocation  | Azure-region där blockchain-medlemmen har distribuerats.  |
| BlockchainNodeName  | Namnet på noden för den Azure blockchain service-medlem som åtgärden utförs på.   |
| BlockchainMessage    | Det här fältet innehåller den blockchain program logg som är de databaserade loggarna. För ABS-kvorum skulle detta ha kvorumloggen. Den innehåller information om vilken typ av loggpost som är information, fel, varning och en sträng som ger mer information om åtgärden som utförs.   |
| TenantID    | Den landsspecifika innehavaren av Azure blockchain-tjänsten. Formatet för det här fältet är https://westlake-rp-prod . <region> . cloudapp.azure.com där region anger Azure-regionen för den distribuerade medlemmen.       |
| SourceSystem   | Systemet fyller i loggarna, i det här fallet är det **Azure**.    |



### <a name="metrics"></a>Mått

I följande tabeller visas de plattforms mått som samlas in för Azure blockchain-tjänsten. Alla mått lagras i namn områdets **Azure blockchain service standard-** mått.

En lista över alla Azure Monitor mått som stöds (inklusive Azure blockchain-tjänsten) finns i [Azure Monitor mått som stöds](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Blockchain mått

Följande tabell innehåller en lista över blockchain-mått som samlas in för Azure blockchain-tjänstens medlems resurs.


| Måttnamn | Enhet  |  Sammansättningstyp| Description   |
|---|---|---|---|
| Väntande transaktioner   | Antal  |  Genomsnitt | Antalet transaktioner som väntar på att bli används.   |
| Bearbetade block   | Antal  | Sum  |  Antalet behandlade block under varje tidsintervall. För närvarande är block storleken 5 sekunder, vilket innebär att varje nod i minuten bearbetar 12 block och 60 block på 5 minuter.   |
|Bearbetade transaktioner    | Antal  | Sum  | Antalet bearbetade transaktioner i ett block.    |
|Köade transaktioner    |  Antal | Genomsnitt  | Antalet transaktioner som inte kan används direkt. Det kan bero på att de togs emot i rätt ordning och att framtiden väntar på att en tidigare transaktion ska komma. Eller så kan det vara två transaktioner som har samma nummer endast en gång (nonce) och samma gas värde, och därför kan det andra inte vara används.   |

### <a name="connection-metrics"></a>Anslutnings mått  

I följande tabell visas de olika anslutnings mått som samlas in för Azure blockchain-tjänstens medlems resurs. Detta är NGINX.


| Måttnamn | Enhet  |  Sammansättningstyp| Description |
|---|---|---|---|
| Godkända anslutningar   | Antal  |  Sum | Det totala antalet godkända klient anslutningar.   |
| Aktiva anslutningar  | Antal  | Genomsnitt  |  Aktuellt antal aktiva klient anslutningar, inklusive väntande anslutningar.    |
|Hanterade anslutningar    | Antal  | Sum  | Det totala antalet hanterade anslutningar. Vanligt vis är parametervärdet detsamma som godkända anslutningar om vissa resurs gränser har nåtts.     |
|Hanterade begär Anden     |  Antal | Sum  | Det totala antalet klient begär Anden.  |


### <a name="performance-metrics"></a>Prestandamått

I följande tabell visas de prestanda mått som samlas in för varje nod i Azure blockchain member-resursen.  


| Måttnamn | Enhet  |  Sammansättningstyp| Description   |
|---|---|---|---|
| Procent andel CPU-användning   | Procent  |  Max | Procent andel av CPU-användningen.     |
| Lästa byte i IO   | Kilobyte   | Sum  |  Summan av IO-lästa byte över alla noder i blockchain-medlems resursen.      |
|Skrivna byte i IO     | Kilobyte   | Sum  | Summan av IO-skrivningar i byte för alla noder i blockchain-medlems resursen.     |
|Minnes gräns       |  GB   | Genomsnitt    | Maximalt minne tillgängligt för blockchain-processen per nod. |
|Minnesanvändning     | GB  |  Genomsnitt | Genomsnittligt minne som används i genomsnitt för alla noder.  |
| Minnes användnings procent     | Procent   | Genomsnitt  |  Procent andelen minne som används i genomsnitt för alla noder.       |
|Lagrings användning      | GB   | Genomsnitt  | Den GB lagrings utrymme som används i genomsnitt för alla noder.       |


## <a name="next-steps"></a>Nästa steg

Läs mer om [Blockchain Data Manager](./data-manager.md) för att avbilda och transformera blockchain-Data till Azure Event Grid.
