---
title: Lagrings översikt – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om data lagring i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 67ab4c8cf079adaf3b38cdcc30abeec43cd4612f
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505203"
---
# <a name="data-storage"></a>Datalagring

I den här artikeln beskrivs data lagring i Azure Time Series Insights Gen2. Den täcker värme och kall, data tillgänglighet och bästa praxis.

## <a name="provisioning"></a>Etablering

När du skapar en Azure Time Series Insights Gen2-miljö har du följande alternativ:

* Kall data lagring:
  * Skapa en ny Azure Storage-resurs i den prenumeration och region som du har valt för din miljö.
  * Bifoga ett redan befintligt Azure Storage-konto. Det här alternativet är endast tillgängligt genom att distribuera från en Azure Resource Manager- [mall](/azure/templates/microsoft.timeseriesinsights/allversions)och inte visas i Azure Portal.
* Varm data lagring:
  * Ett varmt arkiv är valfritt och kan aktive ras eller inaktive ras under eller efter etablerings tiden. Om du väljer att aktivera varmt Arkiv vid ett senare tillfälle och det redan finns data i ditt kall arkiv kan du [läsa avsnittet nedan](concepts-storage.md#warm-store-behavior) för att förstå det förväntade beteendet. Lagrings tiden för den varma lagrings platsen kan konfigureras för 7 till 31 dagar och detta kan också justeras efter behov.

När en händelse matas in, indexeras den i både varm lagring (om aktive rad) och kall lagring.

[![Lagrings översikt](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Som ägare av Azure Blob Storage-kontot där data från kyl lagret finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten inkluderar Skriv-och borttagnings behörigheter. Redigera inte eller ta bort de data som Azure Time Series Insights Gen2 skrivningar, eftersom det kan orsaka data förlust.

## <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights Gen2 partitioner och indexerar data för optimala prestanda för frågor. Data blir tillgängliga för frågor från både varm (om aktive rad) och kall lagring när den har indexerats. Mängden data som matas in och data flödes hastigheten per partition kan påverka tillgängligheten. Granska [begränsningarna för data flödet](./concepts-streaming-ingress-throughput-limits.md) för händelse källan och [bästa praxis](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) för bästa prestanda. Du kan också konfigurera en fördröjnings [avisering](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) för att bli meddelad om din miljö har problem med att bearbeta data.

> [!IMPORTANT]
> Du kan uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om du får en betydande fördröjning utöver 60 sekunder kan du skicka ett support ärende via Azure Portal.

## <a name="warm-store"></a>Varm lagring

Data i det varmt arkivet är bara tillgängliga via [Time Series-API: er](./concepts-query-overview.md), [Azure Time Series Insights TSD-Utforskare](./concepts-ux-panels.md)eller Power BI- [anslutningen](./how-to-connect-power-bi.md). Frågor om varma butiker är kostnads fria och det finns ingen kvot, men det finns en [gräns på 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) samtidiga begär Anden.

### <a name="warm-store-behavior"></a>Varmt lagrings beteende

* När den är aktive rad dirigeras alla data som strömmas till din miljö till din varma lagring, oavsett händelsens tidsstämpel. Observera att pipeline-inmatnings pipelinen skapas för nästan real tids strömning och inmatning av historiska händelser [stöds inte](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* Kvarhållningsperioden beräknas baserat på när händelsen indexerades i varmt Arkiv, inte händelsens tidsstämpel. Det innebär att data inte längre är tillgängliga i varmt Arkiv efter att kvarhållningsperioden har förflutit, även om händelsens tidsstämpel är för framtiden.
  * Exempel: en händelse med 10 dagars väder prognoser matas in och indexeras i en varm lagrings behållare som kon figurer ATS med 7 dagars kvarhållningsperiod. Efter 7 dagars tid är förutsägelsen inte längre tillgänglig i varmt lager, men kan frågas från kall.
* Om du aktiverar varmt lager på en befintlig miljö som redan har nyligen använda data som har indexerats i kall lagring, Tänk på att din varma lagring inte kommer att fyllas i igen med dessa data.
* Om du precis har aktiverat varmt Arkiv och har problem med att visa dina senaste data i Utforskaren kan du tillfälligt växla mellan frågor och svar från varma butiker:

   [![Inaktivera varma frågor](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Kall lagring

I det här avsnittet beskrivs Azure Storage information som är relevant för Azure Time Series Insights Gen2.

En utförlig beskrivning av Azure Blob Storage finns i Introduktion till [Storage-blobbar](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>Ditt kalla lagrings konto

Azure Time Series Insights Gen2 behåller upp till två kopior av varje händelse i ditt Azure Storage-konto. En kopia lagrar händelser sorterade efter Inhämtnings tid, och tillåter alltid åtkomst till händelser i en tidsordnad sekvens. Med tiden skapar Azure Time Series Insights Gen2 också en ompartitionerad kopia av data för att optimera för att genomföra frågor.

Alla dina data lagras oändligt i ditt Azure Storage-konto.

#### <a name="writing-and-editing-blobs"></a>Skriva och redigera blobbar

För att säkerställa frågornas prestanda och data tillgänglighet ska du inte redigera eller ta bort blobbar som Azure Time Series Insights Gen2 skapar.

#### <a name="accessing-cold-store-data"></a>Åtkomst till data för kall lagring

Förutom att komma åt dina data från API: erna för [Azure Time Series Insights Explorer](./concepts-ux-panels.md) och [Time Series](./concepts-query-overview.md), kanske du också vill komma åt dina data direkt från Parquet-filerna som lagras i kyl lagret. Du kan till exempel läsa, transformera och rensa data i en Jupyter Notebook och sedan använda den för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

Om du vill komma åt data direkt från ditt Azure Storage-konto behöver du Läs behörighet till det konto som används för att lagra dina Azure Time Series Insights Gen2-data. Du kan sedan läsa valda data baserat på skapande tiden för den Parquet-fil som finns i `PT=Time` mappen som beskrivs nedan i avsnittet [fil format för Parquet](#parquet-file-format-and-folder-structure) .  Mer information om hur du aktiverar Läs åtkomst till ditt lagrings konto finns i [Hantera åtkomst till dina lagrings konto resurser](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Borttagning av data

Ta inte bort dina Azure Time Series Insights Gen2-filer. Hantera endast relaterade data i Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumn fil format med öppen källkod som utformats för effektiv lagring och prestanda. Azure Time Series Insights Gen2 använder Parquet för att aktivera tids serie-ID-baserade frågeresultat i stor skala.

Mer information om filtypen Parquet finns i [Parquet-dokumentationen](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 lagrar kopior av dina data på följande sätt:

* Den första kopian partitioneras efter inmatnings tiden och lagrar data ungefär i den ordning de anländer. Dessa data finns i `PT=Time` mappen:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra ompartitionerade kopian grupperas efter Time Series-ID: n och finns i `PT=TsId` mappen:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Tidsstämpeln i BLOB-namnen i `PT=Time` mappen motsvarar ankomst tiden för data till Azure Time Series Insights Gen2 och inte tidsstämpeln för händelserna.

Data i `PT=TsId` mappen kommer att optimeras för frågan över tid och är inte statisk. Vid ompartitionering kan vissa händelser finnas i flera blobbar. Namngivningen av Blobbarna i den här mappen är inte garanterat oförändrad.

I allmänhet, om du behöver komma åt data direkt via Parquet-filer, använder du `PT=Time` mappen.  Med framtida funktioner kan du få effektiv åtkomst till `PT=TsId` mappen.

> [!NOTE]
>
> * `<YYYY>` mappar till en 4-siffrig års representation.
> * `<MM>` mappar till en tvåsiffrig månads representation.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tids stämplings representation med fyrsiffrigt år ( `YYYY` ), tvåsiffrig månad ( `MM` ), tvåsiffrig dag (), tvåsiffrig timme (), tvåsiffrig `DD` `HH` minut () `MM` , tvåsiffrig sekund ( `SS` ) och tresiffrig MS-siffrig MS ( `fff` ).

Azure Time Series Insights Gen2-händelser mappas till Parquet-filinnehållet på följande sätt:

* Varje händelse mappas till en enda rad.
* Varje rad **innehåller kolumnen tidsstämpel med** en tids stämpling för händelsen. Egenskapen Time-stämpel är aldrig null. Den är som standard **händelse i kö** om egenskapen tidstämpel inte anges i händelse källan. Den lagrade tidstämpeln är alltid i UTC.
* Varje rad innehåller Time Series-ID: n (TSID) som definieras när Azure Time Series Insights Gen2-miljön skapas. TSID-egenskapens namn inkluderar `_string` suffixet.
* Alla andra egenskaper som skickas som telemetridata mappas till kolumn namn som slutar med `_bool` (Boolean), `_datetime` (tidstämpel), `_long` (Long), ( `_double` Double), `_string` (sträng) eller `dynamic` (dynamisk), beroende på egenskaps typen.  Mer information finns i om [vilka data typer som stöds](./concepts-supported-data-types.md).
* Det här mappnings schemat gäller för den första versionen av fil formatet som refereras till som **V = 1** och lagras i Bask-mappen med samma namn. När den här funktionen utvecklas kan det här mappnings schemat ändras och referens namnet ökas.

## <a name="next-steps"></a>Nästa steg

* Läs om [data modellering](./concepts-model-overview.md).

* Planera din [Azure Time Series Insights Gen2-miljö](./how-to-plan-your-environment.md).
