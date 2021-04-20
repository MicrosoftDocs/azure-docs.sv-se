---
title: Lagringsöversikt – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om datalagring i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725034"
---
# <a name="data-storage"></a>Datalagring

I den här artikeln beskrivs datalagring i Azure Time Series Insights Gen2. Den omfattar varm och kall, datatillgänglighet och bästa praxis.

## <a name="provisioning"></a>Etablering

När du skapar Azure Time Series Insights Gen2-miljö har du följande alternativ:

* Kall datalagring:
  * Skapa en Azure Storage resurs i den prenumeration och region som du har valt för din miljö.
  * Bifoga ett befintligt Azure Storage konto. Det här alternativet är endast tillgängligt genom att Azure Resource Manager [från](/azure/templates/microsoft.timeseriesinsights/allversions)en mall och visas inte i Azure Portal.
* Varm datalagring:
  * Ett varmt lager är valfritt och kan aktiveras eller inaktiveras under eller efter etableringen. Om du bestämmer dig för att aktivera varm lagring vid ett [](concepts-storage.md#warm-store-behavior) senare tillfälle och det redan finns data i kalllagret kan du läsa det här avsnittet nedan för att förstå det förväntade beteendet. Den varma lagringstiden för datalagring kan konfigureras för 7 till 31 dagar, och detta kan också justeras efter behov.

När en händelse matas in indexeras den i både varm lagring (om aktiverad) och kalllager.

[![Lagringsöversikt](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Som ägare till Azure Blob Storage-kontot där kalllagringsdata finns har du fullständig åtkomst till alla data i kontot. Den här åtkomsten omfattar skriv- och borttagningsbehörigheter. Redigera inte eller ta bort de data som Azure Time Series Insights Gen2 skriver eftersom det kan orsaka dataförlust.

## <a name="data-availability"></a>Datatillgänglighet

Azure Time Series Insights Gen2-partitioner och indexerar data för optimala frågeprestanda. Data blir tillgängliga för frågor från både varmt (om aktiverat) och kalllager efter att de har indexerats. Mängden data som matas in och dataflödet per partition kan påverka tillgängligheten. Granska dataflödesbegränsningarna [för händelsekällan](./concepts-streaming-ingress-throughput-limits.md) [och bästa praxis för](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) bästa prestanda. Du kan också konfigurera en [fördröjningsavisering](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) för att få ett meddelande om din miljö har problem med att bearbeta data.

> [!IMPORTANT]
> Du kan uppleva en period på upp till 60 sekunder innan data blir tillgängliga. Om svarstiden är längre än 60 sekunder kan du skicka en supportbiljett via Azure Portal.

## <a name="warm-store"></a>Varm lagring

Data i ditt varma lager är endast tillgängliga via [Time Series Query-API:er,](./concepts-query-overview.md) [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md)eller Power BI [Connector](./how-to-connect-power-bi.md). Varmlagerfrågor är kostnadsfria och det finns ingen kvot, men det finns en [gräns på 30 samtidiga](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) begäranden.

### <a name="warm-store-behavior"></a>Beteende för varm lagring

* När det här alternativet är aktiverat dirigeras alla data som strömmas till din miljö till din varma lagring, oavsett händelsetidsstämpeln. Observera att pipelinen för strömningsinmatning är byggd för direktuppspelning nästan i realtid och inmatning av historiska händelser [stöds inte.](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)
* Kvarhållningsperioden beräknas baserat på när händelsen indexerades i varmlagring, inte händelsetidsstämpeln. Det innebär att data inte längre är tillgängliga i varmlagring när kvarhållningsperioden har förflutit, även om händelsestämpeln gäller för framtiden.
  * Exempel: en händelse med väderprognoser på 10 dagar matas in och indexeras i en varm lagringscontainer som konfigurerats med en kvarhållningsperiod på 7 dagar. Efter sju dagar är förutsägelsen inte längre tillgänglig i varm lagring, men den kan efterfrågas från kall lagring.
* Om du aktiverar varmlagring i en befintlig miljö som redan har nyligen indexerade data i kall lagring bör du observera att ditt varma lager inte fylls med dessa data igen.
* Om du precis har aktiverat varm lagring och har problem med att visa dina senaste data i Utforskaren kan du tillfälligt stänga av varmlagerfrågor:

   [![Inaktivera varma frågor](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Kall butik

I det här avsnittet Azure Storage information som är relevant Azure Time Series Insights Gen2.

En grundlig beskrivning av Azure Blob Storage finns i introduktionen [till Storage-blobar.](../storage/blobs/storage-blobs-introduction.md)

### <a name="your-cold-storage-account"></a>Ditt kalllagringskonto

Azure Time Series Insights Gen2 behåller upp till två kopior av varje händelse i ditt Azure Storage konto. En kopia lagrar händelser sorterade efter inmatningstid, vilket alltid ger åtkomst till händelser i en tidsbeställd sekvens. Med tiden skapar Azure Time Series Insights Gen2 även en ompartitionerad kopia av data för att optimera för prestandafrågor.

Alla dina data lagras på obestämd tid i ditt Azure Storage konto.

> [!WARNING]
> Begränsa inte offentlig Internetåtkomst till en hubb eller händelsekälla som används av Time Series Insights, annars bryts den nödvändiga anslutningen.

#### <a name="writing-and-editing-blobs"></a>Skriva och redigera blobar

För att säkerställa frågeprestanda och datatillgänglighet ska du inte redigera eller ta bort några blobar som Azure Time Series Insights Gen2 skapar.

#### <a name="accessing-cold-store-data"></a>Åtkomst till kalllagerdata

Förutom att komma åt dina data från [Azure Time Series Insights Explorer](./concepts-ux-panels.md) och Time [Series Query](./concepts-query-overview.md)API:er kan du även komma åt dina data direkt från Parquet-filer som lagras i kalllagret. Du kan till exempel läsa, transformera och rensa data i en Jupyter Notebook och sedan använda dem för att träna din Azure Machine Learning-modell i samma Spark-arbetsflöde.

För att komma åt data direkt Azure Storage ditt konto behöver du läsbehörighet till det konto som används för att lagra dina Azure Time Series Insights Gen2-data. Du kan sedan läsa valda data baserat på skapandetiden för Parquet-filen i mappen som beskrivs nedan i `PT=Time` [avsnittet Parquet-filformat.](#parquet-file-format-and-folder-structure)  Mer information om hur du aktiverar läsåtkomst till ditt lagringskonto finns i [Hantera åtkomst till dina lagringskontoresurser.](../storage/blobs/anonymous-read-access-configure.md)

#### <a name="data-deletion"></a>Databorttagning

Ta inte bort dina Azure Time Series Insights Gen2-filer. Hantera relaterade data inifrån Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-filformat och mappstruktur

Parquet är ett kolumnfilformat med öppen källkod som utformats för effektiv lagring och prestanda. Azure Time Series Insights Gen2 använder Parquet för att aktivera Time Series ID-baserade frågeprestanda i stor skala.

Mer information om Parquet-filtypen finns i [Parquet-dokumentationen.](https://parquet.apache.org/documentation/latest/)

Azure Time Series Insights Gen2 lagrar kopior av dina data på följande sätt:

* Den första första kopian partitioneras efter inmatningstid och lagrar data ungefär i ankomstordning. Dessa data finns i `PT=Time` mappen :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Den andra, ompartitionerade kopian, grupperas efter Time Series-ID:er och finns i `PT=TsId` mappen :

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Tidsstämpeln i blobnamnen i mappen motsvarar ankomsttiden för data till Azure Time Series Insights Gen2 och inte `PT=Time` tidsstämpeln för händelserna.

Data i mappen `PT=TsId` optimeras för frågor över tid och är inte statiska. Under ompartitionering kan vissa händelser förekomma i flera blobar. Namngivningen av blobarna i den här mappen är inte garanterad att vara densamma.

Om du i allmänhet behöver komma åt data direkt via Parquet-filer använder du `PT=Time` mappen .  Framtida funktioner ger effektiv åtkomst till `PT=TsId` mappen.

> [!NOTE]
>
> * `<YYYY>` mappar till en fyrsiffrig årsrepresentation.
> * `<MM>` mappar till en tvåsiffrig månadsrepresentation.
> * `<YYYYMMDDHHMMSSfff>` mappar till en tidsstämpelrepresentation med fyrsiffrig årstid ( ), tvåsiffrig månad ( ), dag med två siffror ( ), tvåsiffrig timme ( ), tvåsiffrig minut ( ), tvåsiffrig sekund ( ) och tresiffrig `YYYY` `MM` `DD` `HH` `MM` `SS` millisekund ( `fff` ).

Azure Time Series Insights Gen2-händelser mappas till Parquet-filinnehåll på följande sätt:

* Varje händelse mappar till en enda rad.
* Varje rad innehåller **tidsstämpelkolumnen** med en händelsetidsstämpel. Tidsstämpelegenskapen är aldrig null. Standardvärdet är den **tid då händelsen förskeddes** om tidsstämpelegenskapen inte har angetts i händelsekällan. Den lagrade tidsstämpeln finns alltid i UTC.
* Varje rad innehåller kolumnerna för tidsserie-ID (TSID) enligt definitionen när Azure Time Series Insights Gen2-miljön skapas. TSID-egenskapsnamnet innehåller `_string` suffixet .
* Alla andra egenskaper som skickas som telemetridata mappas till kolumnnamn som slutar med `_bool` (boolesk), `_datetime` (tidsstämpel), `_long` (lång), `_double` (dubbel), (sträng) eller (dynamisk), beroende på `_string` `dynamic` egenskapstyp.  Mer information finns i Datatyper [som stöds.](./concepts-supported-data-types.md)
* Det här mappningsschemat gäller för den första versionen av filformatet, refererad som **V=1**, och lagras i basmappen med samma namn. När den här funktionen utvecklas kan mappningsschemat ändras och referensnamnet ökas.

## <a name="next-steps"></a>Nästa steg

* Läs mer [om datamodellering.](./concepts-model-overview.md)

* Planera din [Azure Time Series Insights Gen2-miljö](./how-to-plan-your-environment.md).
