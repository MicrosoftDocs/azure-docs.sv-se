---
title: Vägledning om prestanda och skalbarhet för kopieringsaktivitet
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopierings aktiviteten.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: 1c166b99243e5a6ee576100b8470aa38b9535c7a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387673"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Vägledning om prestanda och skalbarhet för kopieringsaktivitet

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ibland vill du utföra en storskalig datamigrering från data Lake eller Enterprise Data Warehouse (ANALYSLÖSNINGAR) till Azure. Andra gånger vill du mata in stora mängder data från olika källor till Azure för stor data analys. I varje fall är det viktigt att uppnå optimala prestanda och skalbarhet.

Azure Data Factory (ADF) tillhandahåller en mekanism för att mata in data. ADF har följande fördelar:

* Hanterar stora mängder data
* Är starkt genomförd
* Är kostnads effektiv

Dessa fördelar gör att ADF passar bra för data tekniker som vill skapa skalbara data inmatnings pipeliner som är mycket presterande.

När du har läst den här artikeln kan du svara på följande frågor:

* Vilken nivå av prestanda och skalbarhet kan jag använda för att använda ADF Copy-aktivitet för data migration och scenarier för data inmatning?
* Vilka steg ska jag utföra för att justera prestandan för en ADF Copy-aktivitet?
* Vilka videokanaligt prestanda optimerings rattar kan jag använda för att optimera prestanda för en enskild kopierings aktivitet?
* Vad är andra faktorer utanför ADF att överväga när du optimerar kopierings prestanda?

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet kan du läsa [översikten kopiera aktivitet](copy-activity-overview.md) innan du läser den här artikeln.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiera prestanda och skalbarhet som går att nå med ADF

ADF erbjuder en server lös arkitektur som tillåter parallellitet på olika nivåer.

Med den här arkitekturen kan du utveckla pipeliner som maximerar data flödet för data flödet i din miljö. Dessa pipeliner använder fullt ut följande resurser:

* Nätverksbandbredd
* Lagrings-/utdata-åtgärder per sekund (IOPS) och bandbredd

Den här fullständiga användningen innebär att du kan beräkna det totala data flödet genom att mäta det minsta data flöde som är tillgängligt med följande resurser:

* Käll data lager
* Måldatalager
* Nätverks bandbredd mellan käll-och mål data lager

I tabellen nedan beräknas kopieringens varaktighet. Varaktigheten baseras på data storleken och bandbredds gränsen för din miljö.

&nbsp;

| Data storlek/ <br/> bredd | 50 Mbit/s    | 100 Mbit/s  | 500 Mbit/s  | 1 Gbit/s   | 5 Gbit/s   | 10 Gbit/s  | 50 Gbit/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 timmar    | 2,3 timmar   | 0,5 timmar   | 0,2 timmar  | 0,05 timmar | 0,02 timmar | 0,0 timmar   |
| **1 TB**                    | 46,6 timmar   | 23,3 timmar  | 4,7 timmar   | 2,3 timmar  | 0,5 timmar  | 0,2 timmar  | 0,05 timmar  |
| **10 TB**                   | 19,4 dagar  | 9,7 dagar  | 1,9 dagar  | 0,9 dagar | 0,2 dagar | 0,1 dagar | 0,02 dagar |
| **100 TB**                  | 194,2 dagar | 97,1 dagar | 19,4 dagar | 9,7 dagar | 1,9 dagar | 1 dag    | 0,2 dagar  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |
| | |  | | |  | | |

ADF-kopian är skalbar på olika nivåer:

![så här skalar ADF-kopiering](media/copy-activity-performance/adf-copy-scalability.png)

* ADF-kontroll flödet kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](control-flow-for-each-activity.md).

* En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser.
  * När du använder Azure integration Runtime (IR) kan du ange [upp till 256 data integrerings enheter (DIUs)](#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt.
  * När du använder IR med egen värd kan du vidta någon av följande metoder:
    * Skala upp datorn manuellt.
    * Skala ut till flera datorer ([upp till 4 noder](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) och en enda kopierings aktivitet kommer att partitionera dess fil uppsättning på alla noder.

* En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar [parallellt](#parallel-copy).

## <a name="performance-tuning-steps"></a>Prestanda justerings steg

Utför följande steg för att justera prestandan för din Azure Data Factory-tjänst med kopierings aktiviteten:

1. **Hämta en test data uppsättning och upprätta en bas linje.**

    Under utvecklingen testar du din pipeline med hjälp av kopierings aktiviteten mot ett representativt data exempel. Den data uppsättning du väljer ska representera dina typiska data mönster tillsammans med följande attribut:

    * Mappstruktur
    * Fil mönster
    * Data schema

    Och din data uppsättning bör vara tillräckligt stor för att utvärdera kopierings prestanda. En felfri storlek tar minst 10 minuter innan kopierings aktiviteten slutförs. Samla in körnings information och prestanda egenskaper efter [Kopiera aktivitets övervakning](copy-activity-monitoring.md).

2. **Maximera prestanda för en enskild kopierings aktivitet**:

    Vi rekommenderar att du först maximerar prestandan med en enda kopierings aktivitet.

    * **Om kopierings aktiviteten körs på en _Azure_ integration Runtime:**

        Börja med standardvärden för [data integrerings enheter (DIU)](#data-integration-units) och inställningar för [parallell kopiering](#parallel-copy) .

    * **Om kopierings aktiviteten körs på en _egen värd_ för integration Runtime:**

        Vi rekommenderar att du använder en dedikerad dator som värd för IR. Datorn måste vara separat från den server som är värd för data lagret. Börja med standardvärden för inställningen för [parallell kopiering](#parallel-copy) och Använd en enda nod för IR med egen värd.

    Gör en prestandatest. Anteckna de prestanda som uppnås. Inkludera de faktiska värden som används, till exempel DIUs och parallella kopior. Information om hur du samlar in körnings resultat och prestanda inställningar som används hittar du i [Kopiera aktivitets övervakning](copy-activity-monitoring.md) . Lär dig hur du [felsöker kopierings aktivitetens prestanda](copy-activity-performance-troubleshooting.md) för att identifiera och lösa Flask halsen.

    Iterera för att utföra ytterligare prestandatest körningar efter fel söknings-och justerings vägledningen. När en enskild kopierings aktivitet körs kan inte uppnå bättre data flöde, Överväg om du vill maximera det sammanställda data flödet genom att köra flera kopior samtidigt. Det här alternativet beskrivs i nästa numrerade punkt.

3. **Maximera sammanställd genom strömning genom att köra flera kopior samtidigt:**

    Nu har du maximerat prestandan för en enskild kopierings aktivitet. Om du ännu inte har uppnått de övre gränserna för data flödet i din miljö kan du köra flera kopierings aktiviteter parallellt. Du kan köra parallellt genom att använda ADF-styrningens flödes konstruktioner. En sådan konstruktion är [för varje slinga](control-flow-for-each-activity.md). Mer information finns i följande artiklar om Solution templates:

    * [Kopiera filer från flera containrar](solution-template-copy-files-multiple-containers.md)
    * [Migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Mass kopiering med en kontroll tabell](solution-template-bulk-copy-with-control-table.md)

4. **Expandera konfigurationen till hela data uppsättningen.**

    När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och pipelinen för att hantera hela data uppsättningen.

## <a name="troubleshoot-copy-activity-performance"></a>Felsöka prestanda för kopierings aktivitet

Följ [prestanda justerings stegen](#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. Och lär dig hur du felsöker varje kopierings aktivitets körnings prestanda problem i Azure Data Factory från [fel sökning av prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopiera funktioner för prestanda optimering

Azure Data Factory innehåller följande funktioner för prestanda optimering:

* [Dataintegrationsenheter](#data-integration-units)
* [Skalbarhet för integration runtime med egen värd](#self-hosted-integration-runtime-scalability)
* [Parallell kopia](#parallel-copy)
* [Mellanlagrad kopia](#staged-copy)

### <a name="data-integration-units"></a>Dataintegrationsenheter

En data integrerings enhet (DIU) är ett mått som representerar kraften hos en enskild enhet i Azure Data Factory. Kraften är en kombination av processor, minne och tilldelning av nätverks resurser. DIU gäller bara för [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime). DIU gäller inte för [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime). [Mer information finns här](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Skalbarhet för integration runtime med egen värd

Du kanske vill vara värd för en ökande samtidiga arbets belastning. Eller så kanske du vill uppnå högre prestanda i din nuvarande arbets belastnings nivå. Du kan förbättra skalningen av bearbetningen med följande metoder:

* Du kan skala _upp_ IR med egen värd genom att öka antalet [samtidiga jobb](create-self-hosted-integration-runtime.md#scale-up) som kan köras på en nod.  
Skala upp fungerar bara om nodens processor och minne är mindre än fullt utnyttjade.
* Du kan skala _ut_ IR med egen värd genom att lägga till fler noder (datorer).

Mer information finns i:

* [Kopiera aktivitet prestanda optimerings funktioner: egen värd för integration runtime-skalbarhet](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Skapa och konfigurera en integration runtime med egen värd: skalnings överväganden](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Parallell kopia

Du kan ställa in `parallelCopies` egenskapen för att ange vilken parallellitet du vill att kopierings aktiviteten ska använda. Tänk på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten. Trådarna körs parallellt. Trådarna läses antingen från källan eller skrivs till dina data lager för mottagare. [Läs mer](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Mellanlagrad kopia

En data kopierings åtgärd kan skicka data _direkt_ till data lagret för mottagare. Alternativt kan du välja att använda Blob Storage som ett _interimistiskt_ mellanlagrings lager. [Läs mer](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Nästa steg

Se andra artiklar om kopierings aktiviteter:

* [Översikt över kopierings aktivitet](copy-activity-overview.md)
* [Felsöka prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md)
* [Kopiera aktivitets prestanda optimerings funktioner](copy-activity-performance-features.md)
* [Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md)
* [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)
