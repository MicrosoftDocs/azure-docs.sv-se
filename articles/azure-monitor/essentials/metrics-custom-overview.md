---
title: Anpassade mått i Azure Monitor (förhandsversion)
description: Lär dig mer om anpassade mått Azure Monitor och hur de modelleras.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: bd7f19df5eed87f2fb02af4b5f2577340bcbfd60
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812110"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Anpassade mått i Azure Monitor (förhandsversion)

När du distribuerar resurser och program i Azure bör du börja samla in telemetri för att få insikter om deras prestanda och hälsa. Azure gör vissa mått tillgängliga för dig redan från start. Dessa mått kallas [standard eller plattform.](./metrics-supported.md) De är dock begränsade till sin natur. 

Du kanske vill samla in vissa anpassade prestandaindikatorer eller affärsspecifika mått för att ge djupare insikter. Dessa **anpassade** mått kan samlas in via programtelemetri, en agent som körs på dina Azure-resurser eller till och med ett övervakningssystem utanför systemet som skickas direkt till Azure Monitor. När de har publicerats på Azure Monitor kan du bläddra, fråga och varna om anpassade mått för dina Azure-resurser och program sida vid sida med de standardmått som genereras av Azure.

Azure Monitor anpassade mått är aktuella i offentlig förhandsversion. 

## <a name="methods-to-send-custom-metrics"></a>Metoder för att skicka anpassade mått

Anpassade mått kan skickas till Azure Monitor via flera metoder:
- Instrumentera programmet med hjälp av Azure Application Insights SDK och skicka anpassad telemetri till Azure Monitor. 
- Installera Azure Monitor Agent (förhandsversion) på din virtuella [Windows-](../agents/azure-monitor-agent-overview.md) eller Linux Azure-dator och använd en [datainsamlingsregel](../agents/data-collection-rule-azure-monitor-agent.md) för att skicka prestandaräknare till Azure Monitor mått.
- Installera Tillägget Windows Azure Diagnostics (WAD) på din [](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)virtuella [Azure-dator,](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)VM-skalningsuppsättning, klassiska [virtuella](../essentials/collect-custom-metrics-guestos-vm-classic.md)dator eller klassiska [Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) och skicka prestandaräknare till Azure Monitor. 
- Installera [InfluxData Telegraf-agenten](../essentials/collect-custom-metrics-linux-telegraf.md) på din virtuella Azure Linux-dator och skicka mått med hjälp Azure Monitor plugin-programmet för utdata.
- Skicka anpassade mått [direkt till Azure Monitor REST API](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Prismodell och kvarhållning

På [Azure Monitor för information](https://azure.microsoft.com/pricing/details/monitor/) om när fakturering kommer att aktiveras för anpassade mått och måttfrågor. Specifik prisinformation för alla mått, inklusive anpassade mått och måttfrågor, finns på den här sidan. Sammanfattningsvis kostar det inte att mata in standardmått (plattformsmått) i Azure Monitor Metrics Store, men anpassade mått medför kostnader när de blir allmänt tillgängliga. Mått-API-frågor medför kostnader.

Anpassade mått bevaras under samma [tid som plattformsmåtten](../essentials/data-platform-metrics.md#retention-of-metrics). 

> [!NOTE]  
> Mått som skickas till Azure Monitor via Application Insights SDK debiteras som indata i loggen. De debiteras bara ytterligare måttavgifter om Application Insights aktivera [aviseringar för anpassade måttdimensioner](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) har valts. Den här kryssrutan skickar data till Azure Monitor metrics-databasen med hjälp av API:et för anpassade mått för att tillåta mer komplexa aviseringar.  Läs mer om [Application Insights prismodellen](../app/pricing.md#pricing-model) och [priserna i din region.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Så här skickar du anpassade mått

När du skickar anpassade mått Azure Monitor måste varje datapunkt eller värde som rapporteras innehålla följande information.

### <a name="authentication"></a>Autentisering
För att skicka anpassade mått till Azure Monitor behöver den entitet som skickar måttet en giltig Azure Active Directory-token (Azure AD) i **bearer-huvudet** för begäran. Det finns några sätt att hämta en giltig bearer-token:
1. [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Ger en identitet till en Azure-resurs, till exempel en virtuell dator. Hanterad tjänstidentitet (MSI) har utformats för att ge resurser behörighet att utföra vissa åtgärder. Ett exempel är att låta en resurs generera mått om sig själv. En resurs, eller dess MSI, kan beviljas behörigheter för **Övervakningsmåttutgivare** för en annan resurs. Med den här behörigheten kan MSI även generera mått för andra resurser.
2. [Azure AD-tjänstens huvudnamn](../../active-directory/develop/app-objects-and-service-principals.md). I det här scenariot kan ett Azure AD-program eller en tjänst tilldelas behörigheter för att generera mått om en Azure-resurs.
För att autentisera begäran verifierar Azure Monitor programtoken med hjälp av offentliga Azure AD-nycklar. Den befintliga **rollen Övervakningsmåttutgivare** har redan den här behörigheten. Den är tillgänglig i Azure Portal. Tjänstens huvudnamn, beroende på vilka resurser som det skickar anpassade mått för, kan ges rollen **Övervakningsmåttutgivare** i det omfång som krävs. Exempel är en prenumeration, resursgrupp eller en specifik resurs.

> [!TIP]  
> När du begär en Azure AD-token för att generera anpassade mått ser du till att målgruppen eller resursen som token begärs för är `https://monitoring.azure.com/` . Se till att inkludera avslutande "/".

### <a name="subject"></a>Ämne
Den här egenskapen samlar in vilket Azure-resurs-ID som det anpassade måttet rapporteras för. Den här informationen kodas i URL:en för api-anropet som görs. Varje API kan bara skicka måttvärden för en enskild Azure-resurs.

> [!NOTE]  
> Du kan inte generera anpassade mått mot resurs-ID:t för en resursgrupp eller prenumeration.


### <a name="region"></a>Region
Den här egenskapen samlar in vilken Azure-region som resursen som du skickar mått för distribueras i. Mått måste genereras till samma regionslutpunkt Azure Monitor regionen som resursen distribueras i. Anpassade mått för en virtuell dator som distribueras i USA, västra måste till exempel skickas till den regionala slutpunkten Azure Monitor USA, västra. Regioninformationen kodas också i URL:en för API-anropet.

> [!NOTE]  
> Under den offentliga förhandsversionen är anpassade mått endast tillgängliga i en delmängd av Azure-regioner. En lista över regioner som stöds dokumenteras i ett senare avsnitt i den här artikeln.
>
>

### <a name="timestamp"></a>Timestamp
Varje datapunkt som skickas Azure Monitor måste markeras med en tidsstämpel. Den här tidsstämpeln samlar in det DateTime-värde som måttvärdet mäts eller samlas in vid. Azure Monitor accepterar måttdata med tidsstämplar så långt som 20 minuter under de senaste och 5 minuter i framtiden. Tidsstämpeln måste vara i ISO 8601-format.

### <a name="namespace"></a>Namnområde
Namnrymder är ett sätt att kategorisera eller gruppera liknande mått tillsammans. Genom att använda namnrymder kan du uppnå isolering mellan grupper av mått som kan samla in olika insikter eller prestandaindikatorer. Du kan till exempel ha ett namnområde med namnet **contosomemorymetrics** som spårar minnesanvändningsmått som profilerar din app. Ett annat namnområde **som kallas contosoapptransaction** kan spåra alla mått om användartransaktioner i ditt program.

### <a name="name"></a>Name
**Namn** är namnet på måttet som rapporteras. Vanligtvis är namnet tillräckligt beskrivande för att hjälpa till att identifiera vad som mäts. Ett exempel är ett mått som mäter antalet minnesbyte som används på en viss virtuell dator. Det kan ha ett måttnamn som **Minnesbyte som används.**

### <a name="dimension-keys"></a>Dimensionsnycklar
En dimension är ett nyckel- eller värdepar som hjälper till att beskriva ytterligare egenskaper för måttet som samlas in. Genom att använda de ytterligare egenskaperna kan du samla in mer information om måttet, vilket ger djupare insikter. Måttet Minnesbyte som används kan till exempel ha en dimensionsnyckel med namnet **Process** som visar hur många byte minne varje process på en virtuell dator förbrukar.  Med hjälp av den här nyckeln kan du filtrera måttet för att se hur mycket minnesspecifika processer som används eller för att identifiera de fem främsta processerna efter minnesanvändning.
Dimensioner är valfria, alla mått kan inte ha dimensioner. Ett anpassat mått kan ha upp till 10 dimensioner.

### <a name="dimension-values"></a>Dimensionsvärden
När du rapporterar en måttdatapunkt finns det ett motsvarande dimensionsvärde för varje dimensionsnyckel på måttet som rapporteras. Du kanske till exempel vill rapportera det minne som används av ContosoApp på den virtuella datorn:

* Måttnamnet är **Minnesbyte i Använd**.
* Dimensionsnyckeln är **Process**.
* Dimensionsvärdet skulle vara **ContosoApp.exe**.

När du publicerar ett måttvärde kan du bara ange ett enda dimensionsvärde per dimensionsnyckel. Om du samlar in samma minnesanvändning för flera processer på den virtuella datorn kan du rapportera flera måttvärden för den tidsstämpeln. Varje måttvärde skulle ange ett annat dimensionsvärde för **dimensionsnyckeln** Process.
Dimensioner är valfria, alla mått kan inte ha dimensioner. Om ett måttinlägg definierar dimensionsnycklar är motsvarande dimensionsvärden obligatoriska.

### <a name="metric-values"></a>Måttvärden
Azure Monitor alla mått med en minuts kornighetsintervall. Vi förstår att ett mått kan behöva samplas flera gånger under en viss minut. Ett exempel är CPU-användning. Eller så kan den behöva mätas för många diskreta händelser. Ett exempel är svarstider för inloggningstransaktioner. Om du vill begränsa antalet rådatavärden som du måste generera och betala för i Azure Monitor kan du föra samman lokalt och generera värdena:

* **Min:** Det minsta observerade värdet från alla urval och mätningar under minuten.
* **Max:** Det högsta observerade värdet från alla urval och mätningar under minuten.
* **Summa:** Summering av alla observerade värden från alla urval och mätningar under minuten.
* **Antal:** Antalet urval och mätningar som tagits under minuten.

Om det till exempel finns fyra inloggningstransaktioner i din app under en viss minut kan de resulterande uppmätta svarstiderna för var och en vara följande:

|Transaktion 1|Transaktion 2|Transaktion 3|Transaktion 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Den resulterande måttpublicering som ska Azure Monitor blir då följande:
* Min: 4
* Max: 16
* Summa: 40
* Antal: 4

Om programmet inte kan föra samman lokalt och behöver generera varje diskret exempel eller händelse omedelbart vid insamling kan du generera rådatamåttvärdena. Varje gång en inloggningstransaktion sker i din app publicerar du till exempel ett mått för att Azure Monitor med bara ett enda mått. Så för en inloggningstransaktion som tog 12 ms skulle måttpublicering vara följande:
* Min: 12
* Max: 12
* Summa: 12
* Antal: 1

Med den här processen kan du generera flera värden för samma mått plus dimensionskombination under en viss minut. Azure Monitor sedan alla rådata som genereras under en viss minut och aggregerar dem tillsammans.

### <a name="sample-custom-metric-publication"></a>Exempel på anpassad måttpublicering
I följande exempel skapar du ett anpassat mått med namnet **Minnesbyte** i Använd under måttnamnrymdens **minnesprofil** för en virtuell dator. Måttet har en enda dimension som kallas **Process**. För den angivna tidsstämpeln skickar vi måttvärden för två olika processer:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, diagnostiktillägget och InfluxData Telegraf-agenten har redan konfigurerats för att generera måttvärden mot rätt regionsslutpunkt och föra med alla föregående egenskaper i varje enskilt objekt.
>
>

## <a name="custom-metric-definitions"></a>Anpassade måttdefinitioner
Du behöver inte definiera ett anpassat mått i förväg Azure Monitor innan det genereras. Varje publicerad måttdatapunkt innehåller namnrymd, namn och dimensionsinformation. Så första gången ett anpassat mått skickas till Azure Monitor skapas en måttdefinition automatiskt. Den här måttdefinitionen kan sedan upptäckas för alla resurser som måttet genereras mot via måttdefinitionerna.

> [!NOTE]  
> Azure Monitor har ännu inte stöd för att **definiera enheter** för ett anpassat mått.

## <a name="using-custom-metrics"></a>Använda anpassade mått
När anpassade mått har skickats till Azure Monitor kan du bläddra bland dem via Azure Portal och köra frågor mot dem via Azure Monitor REST-API:er. Du kan också skapa aviseringar om dem för att meddela dig när vissa villkor är uppfyllda.

> [!NOTE]
> Du måste vara läsare eller deltagare för att kunna visa anpassade mått. Se [Övervakningsläsare.](../../role-based-access-control/built-in-roles.md#monitoring-reader) 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Bläddra bland dina anpassade mått via Azure Portal
1.    Gå till [Azure-portalen](https://portal.azure.com).
2.    Välj **fönstret** Övervaka.
3.    Välj **Mått**.
4.    Välj en resurs som du har skapat anpassade mått mot.
5.    Välj måttnamnrymden för ditt anpassade mått.
6.    Välj det anpassade måttet.

> [!NOTE]
> Se [Komma igång med Azure Metrics Explorer](./metrics-getting-started.md) mer information om hur du visar mått i Azure Portal.

## <a name="supported-regions"></a>Regioner som stöds
Under den offentliga förhandsversionen är möjligheten att publicera anpassade mått endast tillgänglig i en delmängd av Azure-regioner. Den här begränsningen innebär att mått endast kan publiceras för resurser i en av de regioner som stöds. Mer [information om Azure-regioner](https://azure.microsoft.com/global-infrastructure/geographies/) finns i Geografiska områden i Azure. Koden för Azure-regionen som används i slutpunkterna nedan är bara namnet på regionen där blanksteg har tagits bort. I följande tabell visas en uppsättning Azure-regioner som stöds för anpassade mått. Den visar också motsvarande slutpunkter som mått för resurser i dessa regioner ska publiceras till:

|Azure-region |Prefix för regional slutpunkt|
|---|---|
| Alla offentliga molnregioner | https://<azure_region_code>.monitoring.azure.com |
| **Azure Government** | |
| US Gov, Arizona | https: \/ /usgovarizona.monitoring.azure.us |
| **Kina** | |
| Kina, östra 2 | https: \/ /chinaeast2.monitoring.azure.cn |

## <a name="latency-and-storage-retention"></a>Svarstid och kvarhållning av lagring

Det kan ta upp till 2 till 3 minuter att lägga till ett helt nytt mått eller en ny dimension som läggs till i ett mått. Väl i systemet bör data visas inom mindre än 30 sekunder 99 % av tiden. 

Om du tar bort ett mått eller tar bort en dimension kan det ta en vecka till en månad innan ändringen tas bort från systemet.

## <a name="quotas-and-limits"></a>Kvoter och begränsningar
Azure Monitor följande användningsgränser för anpassade mått:

|Kategori|Gräns|
|---|---|
|Aktiva tidsserier/prenumerationer/region|50 000|
|Dimensionsnycklar per mått|10|
|Stränglängd för måttnamnrymder, måttnamn, dimensionsnycklar och dimensionsvärden|256 tecken|

En aktiv tidsserie definieras som en unik kombination av mått, dimensionsnyckel eller dimensionsvärde som har haft måttvärden publicerade under de senaste 12 timmarna.

Överväg följande mått för att förstå gränsen på 50 000 tidsserier:

*Serversvarstid* med dimensioner: *Region,* *Avdelning,* *CustomerID*

Om du har 10 regioner, 20 avdelningar och 100 kunder med det här måttet får du 10 x 20 x 100 = 2 000 tidsserier. 

Om du har 100 regioner, 200 avdelningar och 2 000 kunder 100 x 200 x 2000 = 40 000 000 tidsserier, vilket är långt över gränsen enbart för det här måttet. 

Återigen gäller den här gränsen inte för ett enskilt mått. Den är för summan av alla sådana mått i en prenumeration och region.  

## <a name="design-limitations-and-considerations"></a>Designbegränsningar och -överväganden

**Använd inte Application Insights** för granskning – Application Insights-telemetripipelinen är optimerad för att minimera prestandapåverkan och begränsa nätverkstrafiken från att övervaka ditt program. Därför begränsas eller tas exempel (tar bara en procentandel av din telemetri och ignorerar resten) om den ursprungliga datauppsättningen blir för stor. På grund av det här beteendet kan du inte använda det i granskningssyfte eftersom vissa poster sannolikt kommer att tas bort. 

**Mått med en variabel i namnet – Använd inte** en variabel som en del av måttnamnet, använd en konstant i stället. Varje gång variabeln ändrar sitt värde Azure Monitor att generera ett nytt mått, vilket snabbt kommer att nå gränserna för antalet mått. När utvecklarna vill inkludera en variabel i måttnamnet vill de i allmänhet spåra flera tidsserier inom ett mått och använda dimensioner i stället för variabelmåttnamn. 

**Måttdimensioner med** hög kardinalitet – mått med för många giltiga värden i en dimension (en "hög kardinalitet") är mycket mer sannolika att träffa gränsen på 50 000. I allmänhet bör du aldrig använda ett ständigt föränderligt värde i en dimension eller ett måttnamn. Tidsstämpeln ska till exempel ALDRIG vara en dimension. Server, kund eller produkt-ID kan användas, men bara om du har ett mindre antal av var och en av dessa typer. Som ett test kan du fråga dig själv om du någonsin skulle skapa diagram över sådana data i en graf.  Om du har 10 eller kanske till och med 100 servrar kan det vara bra att se alla i ett diagram för jämförelse. Men om du har 1 000 skulle det resulterande diagrammet förmodligen vara svårt om inte omöjligt att läsa. Bästa praxis är att behålla det till färre än 100 giltiga värden. Upp till 300 är ett grått område.  Om du behöver gå igenom den här mängden använder du Azure Monitor anpassade loggar i stället.   

Om du har en variabel i namnet eller en dimension med hög kardinalitet kan följande inträffa:
- Mått blir otillförlitliga på grund av begränsning
- Metrics Explorer fungerar inte
- Aviseringar och meddelanden blir oförutsägbara
- Kostnaderna kan öka oväntat – Microsoft debiterar inte när de anpassade måtten med dimensioner är i offentlig förhandsversion. Men när avgifterna startar i framtiden debiteras du oväntade avgifter. Planen är att debitera för förbrukning av mått baserat på antalet övervakade tidsserier och antalet API-anrop som görs.  

## <a name="next-steps"></a>Nästa steg
Använd anpassade mått från olika tjänster: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Skaluppsättning för virtuella datorer](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassisk)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuell Linux-dator med Telegraf-agenten](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST-API](./metrics-store-custom-rest-api.md)
 - [Klassisk Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
