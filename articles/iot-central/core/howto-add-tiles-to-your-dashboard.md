---
title: Konfigurera till din Azure IoT Central instrumentpanel | Microsoft Docs
description: Som byggare kan du lära dig hur du konfigurerar Azure IoT Central för program med paneler.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864261"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera instrumentpanelen för programmet

**Instrumentpanelen** är den första sidan som visas när du ansluter till ett IoT Central program. Om du skapar ditt program från en av de branschfokuserade [programmallarna](./concepts-app-templates.md)har programmet en fördefinierad instrumentpanel att starta. Om du skapar ditt program från en anpassad [programmall visar](./concepts-app-templates.md)instrumentpanelen några tips för att komma igång.

> [!TIP]
> Användare kan [skapa flera instrumentpaneler](howto-create-personal-dashboards.md) utöver standardinstrumentpanelen för program. Dessa instrumentpaneler kan vara personliga endast för användaren eller delas mellan alla användare av programmet.  

## <a name="add-tiles"></a>Lägg till paneler

Följande skärmbild visar instrumentpanelen i ett program som skapats från **mallen Anpassat** program. Om du vill anpassa den aktuella instrumentpanelen väljer **du Redigera** för att lägga till en anpassad personlig eller delad instrumentpanel genom att välja **Ny:**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Instrumentpanel för program baserat på den anpassade programmallen":::

När du har **valt** Redigera **eller Ny** är instrumentpanelen i *redigeringsläge.* Du kan använda verktygen på panelen Redigera **instrumentpanel för** att lägga till paneler på instrumentpanelen och anpassa och ta bort paneler på själva instrumentpanelen. Om du till exempel vill lägga till **en telemetripanel** för att visa aktuell temperatur som rapporterats av en eller flera enheter:

1. Välj en **enhetsgrupp** och välj sedan dina enheter i **listrutan Enheter** som ska visas på panelen. Nu visas tillgänglig telemetri, egenskaper och kommandon från enheterna.

1. Om det behövs använder du listrutan för att välja ett telemetrivärde som ska visas på panelen. Du kan lägga till fler objekt i panelen genom att **välja + Telemetri**, **+ Egenskap** eller **+ Molnegenskap**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Lägga till en telemetripanel för temperatur på instrumentpanelen":::

När du har valt alla värden som ska visas på panelen klickar du på Lägg **till panel.** Panelen visas nu på instrumentpanelen där du kan ändra visualiseringen, ändra storlek på den, flytta den och konfigurera den.

När du har lagt till och anpassa paneler  på instrumentpanelen väljer du Spara för att spara ändringarna på instrumentpanelen, vilket tar dig ur redigeringsläget.

## <a name="customize-tiles"></a>Anpassa paneler

Om du vill redigera en panel måste du vara i redigeringsläge.  Vilka anpassningsalternativ som är tillgängliga beror på [paneltypen](#tile-types):

* Med ikonikonen för ikonikonen på en panel kan du ändra visualiseringen. Visualiseringar omfattar linjediagram, stapeldiagram, cirkeldiagram, senast kända värden, kpi:er (kpi:er), heatmaps och kartor.

* Med kvadratikonen kan du ändra storlek på panelen.

* Med kugghjulsikonen kan du konfigurera visualiseringen. För en visualisering av linjediagram kan du till exempel välja att visa förklaringen och axlarna och välja det tidsintervall som ska ritas.


## <a name="tile-types"></a>Paneltyper

I följande tabell beskrivs de olika typerna av paneler som du kan lägga till på en instrumentpanel:

| Panel             | Description |
| ---------------- | ----------- |
| Markdown         | Markdown-paneler är klickbara paneler som visar en rubrik- och beskrivningstext formaterad med markdown. URL:en kan vara en relativ länk till en annan sida i programmet eller en absolut länk till en extern webbplats.|
| Bild            | Bildpaneler visar en anpassad bild och kan vara klickbara. URL:en kan vara en relativ länk till en annan sida i programmet eller en absolut länk till en extern webbplats.|
| Etikett            | Etikettpaneler visar anpassad text på en instrumentpanel. Du kan välja storleken på texten. Använd en etikettpanel för att lägga till relevant information på instrumentpanelen, till exempel beskrivningar, kontaktuppgifter eller hjälp.|
| Antal            | Antal paneler visar antalet enheter i en enhetsgrupp.|
| Karta              | Kartpaneler visar platsen för en eller flera enheter på en karta. Du kan också visa upp till 100 punkter i en enhets platshistorik. Du kan till exempel visa en exempelväg där en enhet har varit den senaste veckan.|
| KPI              |  KPI-paneler visar aggregerade telemetrivärden för en eller flera enheter under en tidsperiod. Du kan till exempel använda den för att visa den högsta temperatur och det tryck som har nåtts för en eller flera enheter under den senaste timmen.|
| Linjediagram       | Linjediagramspaneler ritar ett eller flera aggregerade telemetrivärden för en eller flera enheter under en tidsperiod. Du kan till exempel visa ett linjediagram för att rita genomsnittlig temperatur och tryck på en eller flera enheter under den senaste timmen.|
| Stapeldiagram        | Stapeldiagramspaneler ritar ett eller flera aggregerade telemetrivärden för en eller flera enheter under en tidsperiod. Du kan till exempel visa ett stapeldiagram för att visa genomsnittlig temperatur och tryck för en eller flera enheter under den senaste timmen.|
| Cirkeldiagram        | Cirkeldiagrampaneler visar ett eller flera aggregerade telemetrivärden för en eller flera enheter under en tidsperiod.|
| Termisk karta         | Paneler med heat map visar information om en eller flera enheter, som representeras som färger.|
| Senaste kända värde | De senaste kända värdepanelerna visar de senaste telemetrivärdena för en eller flera enheter. Du kan till exempel använda den här panelen för att visa de senaste värdena för temperatur, tryck och luftfuktighet för en eller flera enheter. |
| Händelsehistorik    | Paneler för händelsehistorik visar händelser för en enhet under en tidsperiod. Du kan till exempel använda den för att visa alla öppna och stänga händelser för en eller flera enheter under den senaste timmen.|
| Egenskap         |  Egenskapspaneler visar det aktuella värdet för egenskaper och molnegenskaper för en eller flera enheter. Du kan till exempel använda den här panelen för att visa enhetsegenskaper, till exempel tillverkare eller version av inbyggd programvara för en enhet. |

För närvarande kan du lägga till upp till 10 enheter till paneler som stöder flera enheter.

### <a name="customizing-visualizations"></a>Anpassa visualiseringar

Som standard visar linjediagram data över en tidsperiod. Det valda tidsperioden delas upp i 50 bucketar med samma storlek och enhetsdata aggregeras sedan per bucket för att ge 50 datapunkter över det valda tidsperioden. Om du vill visa rådata kan du ändra ditt val för att visa de senaste 100 värdena. Om du vill ändra intervallet eller välja visualisering av rådata använder du listrutan Visningsintervall på **panelen Konfigurera** diagram.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Ändra visningsintervallet för ett linjediagram":::

För paneler som visar aggregeringsvärden väljer du kugghjulsikonen bredvid telemetritypen i panelen **Konfigurera diagram** för att välja aggregeringen. Du kan välja mellan medelvärde, summa, maximum, minimum och antal.

För linjediagram, stapeldiagram och cirkeldiagram kan du anpassa färgen på de olika telemetrivärdena. Välj paletteikonen bredvid den telemetri som du vill anpassa:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Ändra färg på ett telemetrivärde":::

För paneler som visar strängegenskaper eller telemetrivärden kan du välja hur texten ska visas. Om enheten till exempel lagrar en URL i en strängegenskap kan du visa den som en klickbar länk. Om URL:en refererar till en bild kan du rendera bilden i en senast känd värde- eller egenskapspanel. Om du vill ändra hur en sträng visas väljer du kugghjulsikonen bredvid telemetritypen eller egenskapen i panelkonfigurationen:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Ändra hur en sträng visas på en panel":::

För **numeriska** KPI:er,  **senast** kända värden och egenskapspaneler kan du använda villkorsstyrd formatering för att anpassa färgen på panelen baserat på dess aktuella värde. Om du vill lägga till **villkorsstyrd** formatering väljer du Konfigurera på panelen och sedan ikonen **Villkorsstyrd** formatering bredvid det värde som ska anpassas:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Skärmbild som visar hur du hittar konfigurationsalternativet för en panel och sedan ikonen för villkorsstyrd formatering":::

Lägg till regler för villkorsstyrd formatering:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Skärmbild som visar regler för villkorsstyrd formatering för genomsnittligt flöde. Det finns tre regler – mindre än 20 är gröna, mindre än 50 är gula och allt över 50 är rött":::
   
Följande skärmbild visar effekten av regeln för villkorsstyrd formatering:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Skärmbild som visar den röda bakgrundsfärgen på panelen Genomsnittligt vattenflöde. Numret på panelen är 50,54":::

### <a name="tile-formatting"></a>Formatering av "panel"
Med den här funktionen, som är tillgänglig i panelerna KPI, LKV och Egenskap, kan användarna justera teckenstorleken, välja decimalprecision, förkorta numeriska värden (till exempel formatera 1 700 som 1,7K) eller omsluta strängvärden i sina paneler.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Panelformat":::

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar Azure IoT Central standardprograminstrumentpanelen kan du lära dig [hur du skapar en personlig instrumentpanel.](howto-create-personal-dashboards.md)
