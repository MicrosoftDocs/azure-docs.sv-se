---
title: Azure Monitor för Azure-cache för Redis | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för Azure Redis Cache-funktionen, som ger cache-ägare en snabb förståelse för prestanda-och användnings problem.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573459"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Utforska Azure Monitor för Azure cache för Redis

För alla Azure-cache för Redis-resurser ger Azure Monitor för Azure cache för Redis en enhetlig, interaktiv vy av:

- Övergripande prestanda
- Fel
- Kapacitet
- Drifts hälsa

Den här artikeln hjälper dig att förstå fördelarna med den här nya övervaknings miljön. Det visar också hur du ändrar och anpassar upplevelsen så att den passar organisationens unika behov.

## <a name="introduction"></a>Introduktion

Innan du börjar uppleva bör du förstå hur Azure Monitor för Azure cache för Redis visuellt presenterar information.

Den ger:

- I **skalnings perspektiv** för Azure cache för Redis-resurser på en enda plats i alla dina prenumerationer. Du kan selektivt begränsa till de prenumerationer och resurser som du vill utvärdera.

- **Analys** av en viss Azure-cache för Redis-resurs. Du kan diagnostisera problem och se en detaljerad analys av användning, fel, kapacitet och åtgärder. Välj någon av dessa kategorier för att se en detaljerad vy över relevant information.  

- **Anpassning** av den här funktionen, som är byggd ovanpå Azure Monitor mallar för arbets böcker. Med den här funktionen kan du ändra vilka mått som visas och ändra eller ange tröskelvärden som överensstämmer med dina gränser. Du kan spara ändringarna i en anpassad arbets bok och sedan fästa arbets boks diagram på Azure-instrumentpaneler.

Den här funktionen kräver inte att du aktiverar eller konfigurerar något. Azure cache för Redis-information samlas in som standard.

>[!NOTE]
>Det finns ingen avgift för att få åtkomst till den här funktionen. Du debiteras bara för Azure Monitor viktiga funktioner som du konfigurerar eller aktiverar, enligt beskrivningen på sidan [pris information för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Visa användnings-och prestanda mått för Azure cache för Redis

Gör så här om du vill visa användning och prestanda för dina lagrings konton i alla dina prenumerationer:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter **övervakare** och välj **övervaka**.

    ![Sökrutan med ordet "Monitor" och Sök resultatet för tjänster som visar "övervaka" med en hastighets mätare](./media/cosmosdb-insights-overview/search-monitor.png)

1. Välj **Azure-cache för Redis**. Om det här alternativet inte finns väljer du **mer**  >  **Azure-cache för Redis**.

### <a name="overview"></a>Översikt

I **Översikt** visar tabellen interaktiv Azure-cache för Redis-mått. Du kan filtrera resultaten baserat på de alternativ du väljer i följande List rutor:

- **Prenumerationer**: endast prenumerationer som har en Azure-cache för Redis-resursen visas.  

- **Azure-cache för Redis**: du kan välja alla, en delmängd eller en enda Azure-cache för Redis-resursen.

- **Tidsintervall**: som standard visar tabellen de senaste fyra timmarna med information baserat på motsvarande val.

Det finns en panelen räknare under List rutorna. Panelen visar det totala antalet Azure cache för Redis-resurser i de valda prenumerationerna. Villkorsstyrda färg koder eller värme kartor för arbets boks kolumner rapporterar transaktions mått. Den djupaste färgen representerar det högsta värdet. Ljusare färger motsvarar lägre värden.

Om du väljer en nedrullningsbar listruta bredvid någon av Azure-cachen för Redis-resurser visas en nedbrytning av prestanda måtten på den enskilda resurs nivån.

![Skärm bild av översikts upplevelsen](./media/redis-cache-insights-overview/overview.png)

När du väljer resurs namnet Azure cache för Redis markerat i blått visas standard **översikts** tabellen för det associerade kontot. Den visar dessa kolumner:

- **Använt minne**
- **Använt minne i procent**
- **Serverbelastning**
- **Tidslinje för serverbelastning**
- **Processor**
- **Anslutna klienter**
- **Cachemissar**
- **Fel (max)**

### <a name="operations"></a>Operations

När du väljer **åtgärder** överst på sidan öppnas **Åtgärds** tabellen i arbets boks mal len. Den visar dessa kolumner:

- **Totalt antal åtgärder**
- **Tidslinje för totalt antal åtgärder**
- **Åtgärder per sekund**
- **Hämtningar**
- **Uppsättningar**

![Skärm bild av drifts upplevelsen](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Användning

När du väljer **användning** överst på sidan öppnas **användnings** tabellen för arbets boks mal len. Den visar dessa kolumner:

- **Cacheläsning**
- **Tidslinje för cacheläsning**
- **Cacheskrivning**
- **Cacheträffar**
- **Cachemissar**

![Skärm bild av användnings upplevelsen](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Fel

När du väljer **felen** överst på sidan öppnas tabellen med **felaktiga** tabeller för arbets boks mal len. Den visar dessa kolumner:

- **Totalt antal fel**
- **Redundans/fel**
- **UnresponsiveClient/fel**
- **RDB/fel**
- **AOF/fel**
- **Export/fel**
- **DataLoss/fel**
- **Import/fel**

![Skärm bild av problem med en uppdelning efter HTTP-typ av begäran](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Mått definitioner

Om du vill ha en fullständig lista över mått definitionerna som utgör arbets böckerna kan du läsa [artikeln om tillgängliga mått och rapporterings intervall](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Visa från en Azure-cache för Redis-resurs

Få åtkomst till Azure Monitor för Azure cache för Redis direkt från en enskild resurs:

1. I Azure Portal väljer du Azure cache för Redis.

2. I listan väljer du en enskild Azure-cache för Redis-resurs. I avsnittet övervakning väljer du insikter.

    ![Skärm bild av meny alternativ med orden "Insights" markerade i en röd ruta](./media/redis-cache-insights-overview/insights.png)

Dessa vyer är också tillgängliga genom att välja resurs namnet för en Azure-cache för Redis-resurs från arbets boken Azure Monitors nivå.

### <a name="resource-level-overview"></a>Översikt över resurs nivå

I arbets boken **Översikt** för Azure Redis cache visas flera prestanda mått som ger dig till gång till:

- Interaktiva prestanda diagram som visar de viktigaste detaljerna som rör Azure cache för Redis-prestanda.

- Mått och status paneler som markerar Shard-prestanda, totalt antal anslutna klienter och övergripande svars tid.

![Skärm bild av instrument panelen översikt som visar information om processor prestanda, använt minne, anslutna klienter, fel, förfallna nycklar och avlägsnade nycklar](./media/redis-cache-insights-overview/resource-overview.png)

Om du väljer någon av de andra flikarna för **prestanda** eller **åtgärder** öppnas respektive arbets bok.

### <a name="resource-level-performance"></a>Prestanda på resurs nivå

![Skärm bild av diagram över resurs prestanda](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Åtgärder på resurs nivå

![Skärm bild av diagram över resurs åtgärder](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Fäst, exportera och expandera

Om du vill fästa mått avsnitt på en [Azure-instrumentpanel](../../azure-portal/azure-portal-dashboards.md)väljer du kartnålen i det övre högra hörnet i avsnittet.

![Ett mått avsnitt med kartnåls symbolen markerad](./media/cosmosdb-insights-overview/pin.png)

Om du vill exportera data till ett Excel-format väljer du nedåtpilen till vänster om symbolen för kartnålen.

![En markerad export-arbetsbok-symbol](./media/cosmosdb-insights-overview/export.png)

Om du vill visa eller dölja alla vyer i en arbets bok väljer du expansions symbolen till vänster om export symbolen.

![En markerad symbol för att utöka arbets boken](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Anpassa Azure Monitor för Azure cache för Redis

Eftersom den här upplevelsen har skapats ovanpå Azure Monitor arbetsboksmall kan du välja **Anpassa**  >  **Redigera**  >  **Spara** för att spara en kopia av den ändrade versionen i en anpassad arbets bok.

![Ett kommando fält med anpassning markerad](./media/cosmosdb-insights-overview/customize.png)

Arbets böcker sparas i en resurs grupp i antingen **Mina rapporter** -eller **delade rapporter** -avsnittet. **Mina rapporter** är bara tillgängligt för dig. **Delade rapporter** är tillgängliga för alla som har åtkomst till resurs gruppen.

När du har sparat en anpassad arbets bok går du till arbets boks galleriet för att öppna den.

![Ett kommando fält med Galleri markerat](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Felsökning

Information om fel sökning finns i [artikeln](troubleshoot-workbooks.md)om att hitta en dedikerad arbets bok baserad insikter.

## <a name="next-steps"></a>Nästa steg

* Konfigurera [mått aviseringar](../alerts/alerts-metric.md) och [meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications-portal.md) för att konfigurera automatiserade aviseringar som hjälper till att identifiera problem.

* Lär dig mer om scenarier som arbets böcker stöder, hur du skapar och anpassar rapporter med mera genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../visualize/workbooks-overview.md).
