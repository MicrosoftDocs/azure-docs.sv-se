---
title: Kontinuerlig export av telemetri från Application Insights | Microsoft Docs
description: Exportera diagnostik-och användnings data till lagring i Microsoft Azure och ladda ned dem därifrån.
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: e7831123834df9186310453106c50261373160ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737043"
---
# <a name="export-telemetry-from-application-insights"></a>Exportera telemetri från Application Insights
Vill du behålla din telemetri längre än standard kvarhållningsperioden? Eller bearbeta den på ett visst sätt? Kontinuerlig export är idealisk för detta. De händelser som visas i Application Insights-portalen kan exporteras till lagring i Microsoft Azure i JSON-format. Därifrån kan du hämta dina data och skriva vilken kod du behöver för att bearbeta den.  

> [!IMPORTANT]
> Kontinuerlig export är föråldrad. [Migrera till en arbets yta-baserad Application Insights resurs](convert-classic-resource.md) om du vill använda [diagnostikinställningar](#diagnostic-settings-based-export) för att exportera telemetri.

> [!NOTE]
> Löpande export stöds bara för klassiska Application Insights-resurser. [Arbetsytebaserade Application Insights-resurser](./create-workspace-resource.md) måste använda [diagnostikinställningar](./create-workspace-resource.md#export-telemetry).
>

Innan du konfigurerar kontinuerlig export finns det några alternativ som du kanske vill tänka på:

* Med knappen Exportera högst upp på fliken mått eller Sök kan du överföra tabeller och diagram till ett Excel-kalkylblad.

* [Analytics](../logs/log-query-overview.md) har ett kraftfullt frågespråk för telemetri. Det kan också exportera resultat.
* Om du vill [utforska dina data i Power BI](./export-power-bi.md)kan du göra det utan att använda kontinuerlig export.
* Med [data åtkomst REST API](https://dev.applicationinsights.io/) kan du komma åt telemetri program mässigt.
* Du kan också komma åt konfigurations [kontinuerlig export via PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

När kontinuerlig export kopierar dina data till lagrings utrymmet (där det kan fortsätta så länge som du vill), är det fortfarande tillgängligt i Application Insights för den normala [kvarhållningsperioden](./data-retention-privacy.md).

## <a name="supported-regions"></a>Regioner som stöds

Kontinuerlig export stöds i följande regioner:

* Sydostasien
* Kanada, centrala
* Indien, centrala
* Europa, norra
* Storbritannien, södra
* Australien, östra
* Japan, östra
* Sydkorea, centrala
* Frankrike, centrala
* Asien, östra
* USA, västra
* USA, centrala
* USA, östra 2
* USA, södra centrala
* USA, västra 2
* Sydafrika, norra
* USA, norra centrala
* Brasilien, södra
* Schweiz, norra
* Australien, sydöstra
* Storbritannien, västra
* Tyskland, västra centrala
* Schweiz, västra
* Australien, centrala 2
* Förenade Arabemiraten Central
* Brasilien, sydöstra
* Australien, centrala
* Förenade Arabemiraten, norra
* Östra Norge
* Japan, västra

> [!NOTE]
> Program som redan har kon figurer ATS i **Västeuropa** och **östra USA** stöds, men onboarding av nya program i dessa regioner stöds inte.

## <a name="continuous-export-advanced-storage-configuration"></a>Kontinuerlig export, avancerad lagrings konfiguration

Kontinuerlig export **stöder inte** följande funktioner/konfigurationer för Azure Storage:

* Använda [VNet/Azure Storage brand väggar](../../storage/common/storage-network-security.md) tillsammans med Azure Blob Storage.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Skapa en löpande export

> [!NOTE]
> Ett program kan inte exportera fler än 3 TB data per dag. Om fler än 3 TB per dag exporteras kommer exporten att inaktive ras. Om du vill exportera utan begränsning använder du [diagnostiska inställningar baserade på export](#diagnostic-settings-based-export).

1. I Application Insights resurs för din app under Konfigurera till vänster öppnar du kontinuerlig export och väljer **Lägg till**:

2. Välj de data typer för telemetri som du vill exportera.

3. Skapa eller Välj ett [Azure Storage-konto](../../storage/common/storage-introduction.md) där du vill lagra data. Mer information om pris alternativ för lagring finns på [sidan med officiella priser](https://azure.microsoft.com/pricing/details/storage/).

     Klicka på Lägg till, exportera mål, lagrings konto och skapa sedan en ny butik eller Välj en befintlig butik.

    > [!Warning]
    > Som standard kommer lagrings platsen att ställas in på samma geografiska region som din Application Insights-resurs. Om du lagrar i en annan region kan du debiteras överförings avgifter.

4. Skapa eller Välj en behållare i lagrings utrymmet.

> [!NOTE]
> När du har skapat exporten kommer nyligen inmatade data att börja flöda till Azure Blob Storage. Vid kontinuerlig export överförs endast nya telemetri som skapas/matas in efter det att kontinuerlig export har Aktiver ATS. Data som fanns innan du aktiverade den löpande exporten exporteras inte, och det finns inget ordinarie sätt att exportera tidigare skapade data retroaktivt med löpande export.

Det kan finnas en fördröjning på ungefär en timme innan data visas i lagrings utrymmet.

När den första exporten är klar hittar du en struktur som liknar följande i din Azure Blob Storage-behållare: (detta varierar beroende på vilka data som samlas in.)

|Name | Beskrivning |
|:----|:------|
| [Tillgänglighet](export-data-model.md#availability) | Reports för [webb test för tillgänglighet](./monitor-web-app-availability.md).  |
| [Händelse](export-data-model.md#events) | Anpassade händelser som genererats av [TrackEvent ()](./api-custom-events-metrics.md#trackevent). 
| [Undantag](export-data-model.md#exceptions) |Rapporterar [undantag](./asp-net-exceptions.md) på servern och i webbläsaren.
| [Meddelanden](export-data-model.md#trace-messages) | Skickas av [TrackTrace](./api-custom-events-metrics.md#tracktrace)och av [loggnings korten](./asp-net-trace-logs.md).
| [Mått](export-data-model.md#metrics) | Genereras av Metric API-anrop.
| [PerformanceCounters](export-data-model.md) | Prestanda räknare som samlats in av Application Insights.
| [Begäranden](export-data-model.md#requests)| Skickat av [TrackRequest](./api-custom-events-metrics.md#trackrequest). Standardmodulerna använder detta för att rapportera Server svars tid, mätt på servern.| 

### <a name="to-edit-continuous-export"></a>Redigera kontinuerlig export

Klicka på löpande export och välj det lagrings konto som ska redige ras.

### <a name="to-stop-continuous-export"></a>Stoppa kontinuerlig export

Stoppa exporten genom att klicka på Inaktivera. När du klickar på Aktivera igen kommer exporten att starta om med nya data. Du får inte de data som kom in i portalen när export har inaktiverats.

Om du vill stoppa exporten permanent tar du bort den. Om du gör det tas inte data bort från lagrings platsen.

### <a name="cant-add-or-change-an-export"></a>Kan du inte lägga till eller ändra en export?
* Om du vill lägga till eller ändra exporter behöver du åtkomstbehörighet som ägare, deltagare eller Application Insights-deltagare. [Lär dig mer om roller][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Vilka händelser får du?
Exporterade data är den råa telemetri som vi tar emot från ditt program, förutom att vi lägger till plats data som vi beräknar från klientens IP-adress.

Data som har tagits bort från [sampling](./sampling.md) ingår inte i exporterade data.

Andra beräknade mått tas inte med. Vi exporterar till exempel inte genomsnittlig processor användning, men vi exporterar den obehandlade som genomsnittet beräknas från.

Datan innehåller även resultatet av alla [webb test för tillgänglighet](./monitor-web-app-availability.md) som du har konfigurerat.

> [!NOTE]
> **Ta.** Om ditt program skickar mycket data kan du använda samplingsfunktionen och bara skicka en delmängd av den telemetri som genereras. [Läs mer om sampling.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Granska data
Du kan kontrol lera lagringen direkt i portalen. Klicka på Start i menyn längst till vänster längst upp där det står "Azure-tjänster" Välj **lagrings konton**, Välj lagrings kontots namn på sidan Översikt och välj **blobbar** under tjänster och välj sedan container namnet.

Öppna **vyn** och **Cloud Explorer** för att kontrol lera Azure Storage i Visual Studio. (Om du inte har det meny kommandot måste du installera Azure SDK: öppna dialog rutan **nytt projekt** , expandera Visual C#/Cloud och välj **Hämta Microsoft Azure SDK för .net**.)

När du öppnar BLOB Store visas en behållare med en uppsättning BLOB-filer. URI: n för varje fil som härleds från Application Insights resurs namn, Instrumentation-nyckel, telemetri-typ/datum/tid. (Resurs namnet är bara gemener och instrument knappen utesluter bindestreck.)

![Inspektera BLOB Store med ett lämpligt verktyg](./media/export-telemetry/04-data.png)

Datumet och tiden är UTC och när telemetri deponerades i lagret – inte den tidpunkt då den genererades. Så om du skriver kod för att ladda ned data kan den flytta linjärt genom data.

Här är en form av sökvägen:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Var

* `blobCreationTimeUtc` är tiden då blobben skapades i den interna mellanlagringen
* `blobDeliveryTimeUtc` är tiden när blobben kopieras till export destinations lagringen

## <a name="data-format"></a><a name="format"></a> Data format
* Varje BLOB är en textfil som innehåller flera ' \n '-avgränsade rader. Den innehåller Telemetrin som bearbetas under en tids period på ungefär hälften en minut.
* Varje rad representerar en data punkt för telemetri, till exempel en begäran eller sid visning.
* Varje rad är ett oformaterat JSON-dokument. Om du vill visa raderna öppnar du blobben i Visual Studio och väljer **Redigera**  >  **Avancerat**  >  **format fil**:

   ![Visa telemetri med ett lämpligt verktyg](./media/export-telemetry/06-json.png)

Tids längden är i Tick, där 10 000 Tick = 1 MS. Dessa värden visar till exempel en tid på 1 ms för att skicka en begäran från webbläsaren, 3 MS för att ta emot den och 1,8 s för att bearbeta sidan i webbläsaren:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Detaljerad data modell referens för egenskaps typerna och värdena.](export-data-model.md)

## <a name="processing-the-data"></a>Bearbetar data
I en liten skala kan du skriva kod för att hämta data, läsa dem i ett kalkyl blad och så vidare. Exempel:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Ett större kod exempel finns i [använda en arbets roll][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Ta bort dina gamla data
Du ansvarar för att hantera lagrings kapaciteten och ta bort gamla data vid behov.

## <a name="if-you-regenerate-your-storage-key"></a>Om du återskapar din lagrings nyckel...
Om du ändrar lagringsnyckel slutar den löpande exporten att fungera. Du ser ett meddelande i ditt Azure-konto.

Öppna fliken Löpande export och redigera exporten. Redigera exportmålet men använd samma lagring. Bekräfta med OK.

Den löpande exporten startas om.

## <a name="export-samples"></a>Exportera exempel

* [Exportera till SQL med Stream Analytics][exportasa]
* [Stream Analytics exempel 2](export-stream-analytics.md)

På större skalor bör du överväga [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-kluster i molnet. HDInsight tillhandahåller en mängd olika tekniker för att hantera och analysera stora data och du kan använda den för att bearbeta data som har exporter ATS från Application Insights.

## <a name="q--a"></a>Frågor och svar
* *Men alla jag vill ha en enstaka hämtning av ett diagram.*  

    Ja, du kan göra det. Klicka på **Exportera data** överst på fliken.
* *Jag har konfigurerat en export, men det finns inga data i mitt arkiv.*

    Fick Application Insights ta emot telemetri från din app sedan du konfigurerade exporten? Du får bara nya data.
* *Jag försökte konfigurera en export, men nekades åtkomst*

    Om kontot ägs av din organisation måste du vara medlem i gruppen ägare eller deltagare.
* *Kan jag exportera direkt till min egen lokala lagrings plats?*

    Nej, tyvärr. Vår export motor fungerar för närvarande bara med Azure Storage för tillfället.  
* *Finns det någon gräns för mängden data som du lagrar i mitt arkiv?*

    Nej. Vi håller på att skicka data till tills du tar bort exporten. Vi slutar om vi träffar de yttre gränserna för Blob Storage, men det är ganska stort. Det är upp till dig att styra hur mycket lagrings utrymme du använder.  
* *Hur många blobbar ska jag se i lagringen?*

  * För alla data typer som du har valt att exportera, skapas en ny BLOB varje minut (om data är tillgängliga).
  * Dessutom allokeras ytterligare diskpartitioner för program med hög trafik. I det här fallet skapar varje enhet en BLOB varje minut.
* *Jag återskapade nyckeln till min lagring eller ändrat namnet på behållaren, och nu fungerar inte exporten.*

    Redigera exporten och öppna fliken Exportera destination. Lämna samma lagrings utrymme valt som tidigare och bekräfta genom att klicka på OK. Exporten kommer att startas om. Om ändringen var inom de senaste dagarna går det inte att förlora data.
* *Kan jag pausa exporten?*

    Ja. Klicka på Inaktivera.

## <a name="code-samples"></a>Kodexempel

* [Stream Analytics exempel](export-stream-analytics.md)
* [Exportera till SQL med Stream Analytics][exportasa]
* [Detaljerad data modell referens för egenskaps typerna och värdena.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Diagnostiska inställningar baserade på export

Diagnostiska inställningar som baseras på export använder ett annat schema än kontinuerlig export. Den har också stöd för funktioner som kontinuerlig export inte liknar:

* Azure Storage-konton med VNet, brand väggar och privata länkar.
* Exportera till Event Hub.

Migrera till diagnostiska inställningar baserat på export:

1. Inaktivera aktuell löpande export.
2. [Migrera program till arbets ytan-baserad](convert-classic-resource.md).
3. [Aktivera export av diagnostikinställningar](create-workspace-resource.md#export-telemetry). Välj **diagnostikinställningar > Lägg till diagnostikinställningar** inifrån din Application Insights-resurs.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

