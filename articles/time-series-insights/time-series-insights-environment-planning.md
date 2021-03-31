---
title: Planera din gen1-miljö – Azure Time Series Insights | Microsoft Docs
description: Metod tips för att förbereda, konfigurera och distribuera din Azure Time Series Insights gen1-miljö.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016215"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Planera din Azure Time Series Insights gen1-miljö

> [!CAUTION]
> Det här är en gen1-artikel.

I den här artikeln beskrivs hur du planerar din Azure Time Series Insights gen1-miljö baserat på dina förväntade ingångs takt och dina krav på data lagring.

## <a name="video"></a>Video

**Titta på den här videon om du vill veta mer om datakvarhållning i Azure Time Series Insights och hur du planerar för den**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bästa praxis

För att komma igång med Azure Time Series Insights är det bäst om du vet hur mycket data du förväntar dig att pusha per minut och hur länge du behöver lagra dina data.  

Läs [Azure Time Series Insights prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/)för mer information om kapacitet och kvarhållning för båda Azure Time Series Insights SKU: er.

Om du vill planera Azure Time Series Insightss miljön för långsiktig framgång bör du tänka på följande attribut:

- [Lagringskapacitet](#storage-capacity)
- [Datakvarhållningstid](#data-retention)
- [Ingress-kapacitet](#ingress-capacity)
- [Forma dina händelser](#shape-your-events)
- [Se till att du har referens data på plats](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Lagringskapacitet

Som standard behåller Azure Time Series Insights data baserat på mängden lagring som du etablerar (enheter &#215; mängden lagrings utrymme per enhet) och ingress.

## <a name="data-retention"></a>Datakvarhållning

Du kan ändra tids inställningen för **datakvarhållning** i Azure Time Series Insightss miljön. Du kan aktivera upp till 400 dagars kvarhållning.

Azure Time Series Insights har två lägen:

- Ett läge optimerar för de mest aktuella data. Den tillämpar en princip för att **Rensa gamla data** som lämnar de senaste data som är tillgängliga med instansen. Det här läget är aktiverat som standard.
- Den andra optimerar data så att de förblir under de konfigurerade gräns värdena. **Pausa** ingångar förhindrar att nya data inaktive ras när det har valts som **lagrings gränsen har överskridits**.

Du kan justera kvarhållning och växla mellan de två lägena på miljöns konfigurations sida i Azure Portal.

> [!IMPORTANT]
> Du kan konfigurera högst 400 dagar av datakvarhållning i din Azure Time Series Insights gen1-miljö.

### <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. I [Azure Portal](https://portal.azure.com)väljer du din Time Series Insights miljö.

1. I fönstret **Time Series Insights miljö** under **Inställningar** väljer du **lagrings konfiguration**.

1. Ange ett värde mellan 1 och 400 i rutan **data lagrings tid (i dagar)** .

   [![Konfigurera kvarhållning](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Om du vill veta mer om hur du implementerar en lämplig bevarande princip för data kan du läsa [Konfigurera kvarhållning](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Ingress-kapacitet

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Miljö planering

Den andra arean för att fokusera på att planera Azure Time Series Insightss miljön är ingångs kapacitet. Den dagliga ingångs lagringen och händelse kapaciteten mäts per minut i block om 1 KB. Den största tillåtna paket storleken är 32 KB. Data paket som är större än 32 KB trunkeras.

Du kan öka kapaciteten för en S1-eller S2-SKU till 10 enheter i en enda miljö. Du kan inte migrera från en S1-miljö till en S2. Du kan inte migrera från en S2-miljö till S1.

För ingress-kapacitet ska du först fastställa de totala ingångs värden som du behöver per månad. Ta sedan reda på vad dina behov per minut är.

Begränsning och latens spelar en roll i per minut-kapacitet. Om du har en insamling i dina data ingångar som varar mindre än 24 timmar kan Azure Time Series Insights "fånga upp" vid en ingångs frekvens på två gånger enligt de priser som anges i tabellen ovan.

Om du till exempel har en enda S1-SKU intränger data med 720 händelser per minut och data hastigheterna i mindre än en timme till en hastighet av 1 440 händelser eller mindre, det finns ingen märkbar fördröjning i din miljö. Men om du överskrider 1 440 händelser per minut i mer än en timme, kommer du troligen att uppleva svars tid i data som visualiseras och är tillgängliga för frågor i din miljö.

Du kanske inte vet i förväg hur mycket data du förväntar dig att skicka. I det här fallet kan du hitta datatelemetri för [azure IoT Hub](../iot-hub/monitor-iot-hub.md) och [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) i din Azure Portal prenumeration. Telemetrin kan hjälpa dig att avgöra hur du ska etablera din miljö. Använd fönstret **mått** i Azure Portal för respektive händelse källa för att visa dess telemetri. Om du förstår dina evenemangs käll mått kan du effektivare planera och etablera din Azure Time Series Insightss miljö.

### <a name="calculate-ingress-requirements"></a>Beräkna ingress-krav

Så här beräknar du dina ingångs krav:

- Kontrol lera att din ingångs kapacitet överskrider din genomsnittliga genomsnitt per minut och att din miljö är tillräckligt stor för att hantera dina förväntade ingångar som motsvarar två gånger din kapacitet i mindre än en timme.

- Om ingångs toppar inträffar som är längre än 1 timme, använder du mängden insamling i genomsnitt. Etablera en miljö med kapaciteten för att hantera insamling av kostnader.

### <a name="mitigate-throttling-and-latency"></a>Minimera begränsning och latens

Om du vill ha mer information om hur du förhindrar begränsning och svars tider kan du läsa mer om [svars tid och begränsningar](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Forma dina händelser

Det är viktigt att se till att du skickar händelser till Azure Time Series Insights stöder storleken på den miljö som du håller på att konfigurera. (Omvänt kan du mappa storleken på miljön till hur många händelser Azure Time Series Insights läsa och storleken på varje händelse.) Det är också viktigt att tänka på de attribut som du kanske vill använda för att segmentera och filtrera efter när du frågar dina data.

> [!TIP]
> Läs igenom JSON Forms-dokumentationen när du [skickar händelser](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Kontrol lera att du har referens data

En *referens data uppsättning* är en samling objekt som utökar händelserna från din händelse källa. Azure Time Series Insights ingress-motorn kopplar varje händelse från din händelse källa till motsvarande datarad i referens data uppsättningen. Den förhöjda händelsen är sedan tillgänglig för fråga. Kopplingen baseras på **primär nyckel** kolumnerna som definieras i din referens data uppsättning.

> [!NOTE]
> Referens data är inte anslutna retroaktivt. Endast aktuella och framtida ingångs data matchas och kopplas till referens data uppsättningen när den har kon figurer ATS och laddats upp. Om du planerar att skicka en stor mängd historiska data till Azure Time Series Insights och inte först ladda upp eller skapa referens data i Azure Time Series Insights kan du behöva göra om arbetet (Tips: inte roligt).  

Om du vill veta mer om hur du skapar, överför och hanterar referens data i Azure Time Series Insights läser du vår [dokumentation om referens data uppsättning](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Kom igång genom [att skapa en ny Azure Time Series Insights miljö i Azure Portal](time-series-insights-get-started.md).

- Lär dig hur du [lägger till en Event Hubs händelse källa](./how-to-ingest-data-event-hub.md) i Azure Time Series Insights.

- Läs om hur du [konfigurerar en händelse källa för IoT Hub](./how-to-ingest-data-iot-hub.md).