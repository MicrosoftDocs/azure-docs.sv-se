---
title: Arbets bok för avbrott, SLA och avbrott – Application Insights
description: Beräkna och rapportera service avtal för webbtest i ett enda fönster ruta över dina Application Insights-resurser och Azure-prenumerationer.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714059"
---
# <a name="downtime-sla-and-outages-workbook"></a>Arbets bok för avbrott, SLA och avbrott

Vi presenterar ett enkelt sätt att beräkna och rapportera SLA (service nivå avtal) för webbtester via en enda ruta i glaset över dina Application Insights-resurser och Azure-prenumerationer. Rapporten stillestånds tid och avbrott innehåller kraftfulla, färdiga frågor och data visualiseringar för att förbättra förståelsen av kundens anslutning, typisk svars tid för programmet och erfarenhet av drift tid.

Mallen för SLA-arbetsböcker är tillgänglig via arbets boks galleriet i Application Insights resurs eller via fliken tillgänglighet genom att välja **SLA-rapporter** överst.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Skärm bild av fliken tillgänglighet med SLA-rapporter markerade." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Skärm bild av arbets bokens galleri med arbets avbrott och drift avbrott." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibel parameter

De parametrar som anges i arbets boken påverkar resten av rapporten.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Skärm bild av fliken avbrott/underhålls parametrar i arbets boken stillestånds tid och avbrott." lightbox ="./media/sla-report/outages.png":::

`Subscriptions``App Insights Resources`parametrarna,, och `Web Test` avgör dina resurs alternativ på hög nivå. Dessa parametrar baseras på Log Analytics-frågor och används i varje rapport fråga.

`Failure Threshold` och `Outage Window` gör att du kan fastställa egna kriterier för ett tjänst avbrott, till exempel villkoret för tillgänglighets avisering för App Insights baserat på misslyckad plats räknare under en vald period. Det typiska tröskelvärdet är tre platser i ett fönster med fem minuter.

`Maintenance Period` gör att du kan välja den typiska underhålls frekvensen och `Maintenance Window` är en datetime-selektor för ett exempel på en underhålls period. Alla data som inträffar under den angivna perioden ignoreras i resultatet.

`Availability Target 9s` anger målets nior-mål från två nior till fem nior.

## <a name="overview-page"></a>Översiktssidan

Översikts sidan innehåller information på hög nivå om din totala service avtal (exklusive underhålls perioder om det har definierats), slut punkt till slut punkt för avbrott och avbrotts tid för programmet. Avbrotts instanser definieras av när ett test börjar fungera tills det lyckas baserat på dina avbrotts parametrar. Om ett test börjar Miss lyckas kl. 8:00 och lyckas med 10:00 am, anses hela data perioden vara samma avbrott.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" Översikts sidan för GIF som visar översikts tabellen efter test." lightbox="./media/sla-report/overview.gif":::

Du kan också undersöka det längsta avbrott som inträffat under rapporterings perioden.

Vissa tester kan länkas tillbaka till sin Application Insights-resurs för ytterligare undersökning, men det är bara möjligt i [arbets ytans baserade Application Insights-resurs](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Stillestånds tid, avbrott och haverier

Fliken avbrott **och drift stopp** innehåller information om totala avbrotts instanser och den totala tids gränsen uppdelad efter test. Fliken **fel per plats** innehåller en geo-karta över misslyckade test platser för att identifiera eventuella anslutnings områden för problem.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF av fliken avbrott och drift avbrott och fliken plats på fliken plats i arbets boken stillestånds tid och strömavbrott." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Redigera rapporten

Du kan redigera rapporten precis som andra [Azure Monitor arbets böcker](../visualize/workbooks-overview.md). Du kan anpassa frågorna och visualiseringarna baserat på ditt teams behov.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF om du väljer knappen Redigera för att ändra visualiseringen till ett cirkel diagram." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Frågorna kan köras i [Log Analytics](../logs/log-analytics-overview.md) och användas i andra rapporter eller instrument paneler. Ta bort parameter begränsningen och återanvänd kärn frågan.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF för logg fråga." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Åtkomst och delning

Rapporten kan delas med dina team, ledarskap eller fästa på en instrument panel för att användas. Användaren måste ha Läs behörighet/åtkomst till den program insikts-resurs där den faktiska arbets boken lagras.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Skärm bild av dela den här mallen." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Nästa steg

- [Tips för optimering av Log Analytics-fråga](../logs/query-optimization.md).
- Lär dig hur du [skapar ett diagram i arbets böcker](../visualize/workbooks-chart-visualizations.md).
- Lär dig hur du övervakar din webbplats med [tillgänglighets test](monitor-web-app-availability.md).