---
title: Uppgradera Log Analytics instrument panels visualiseringar
description: Lär dig hur du uppgraderar Log Analytics instrument panels visualiseringar med frågor som kan ge kraftfulla insikter.
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 139241d679007bf72680f91a1518a1ef90cc7efc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700634"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Uppgradera Log Analytics instrument panels visualiseringar

I februari 2020 introducerade vi en förbättrad visualiserings teknik. Förbättra och förbättra din förmåga att visualisera frågeresultat och uppnå kraftfulla insikter, snabbt. 

Du kan läsa mer om den här uppgraderingen i den här [Azure-uppdateringen](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Den här nya visualiserings tekniken är på väg att skapa nya och förbättrade upplevelser runt frågeresultatet. 

## <a name="dashboards-in-azure"></a>Instrument paneler i Azure

Azure-instrumentpaneler är ett sätt att visualisera statusen för hela Azure-arbetsytan. De är utformade för att ge en enda panel med glas till din status för din Azure-egendom och tillåta en rad olika genvägar till vanliga åtgärder. 

Mer information finns i [Azure-instrumentpaneler](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>Uppgradera Log Analytics instrument panels delar

Den nya visualiserings tekniken behandlar några vanliga problem med den gamla implementeringen och introducerar några nya funktioner för fästa Log Analytics delar: 

- **Samma tillgängliga typer** – alla visualiserings typer som är tillgängliga i Log Analytics är tillgängliga som fästa delar på instrument paneler.

- **Konsekvent utseende och känsla** – visualiseringen och känslan av de fästa delarna är nu nästan identiska med de i Log Analytics. Skillnaderna beror på optimeringar som kräver diskreta skillnader i data innehållet i det visuella objektet. Se övervägande delen av det här dokumentet för mer information om skillnaderna.

- **Knapp beskrivningar och etiketter** – nya, fästa Log Analytics visualiseringar med stöd knapp beskrivningar. Cirkel-och ring diagram stöder nu etiketter.

- **Interaktiva förklaringar** – genom att klicka på visualiserings förklaringen kan du lägga till/ta bort dimensioner från det fästa visuella objektet som i Log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Steg 1 – uppgraderings meddelande för valbarhet

När en Log Analytics fästa del kan uppgraderas visas *ett nytt meddelande* om att det finns Log Analytics fästa delar på instrument paneler som gör det möjligt för användarna att uppgradera sin visualisering. Om du vill uppleva de nya visualiseringarna för att uppgradera valda visualiseringar på instrument panelen.

 
![Marginal](media/dashboard-upgrade/update-message-1.png)
 
![Skärm bild som visar hur du uppdaterar panelens visualisering.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> När instrument panelen har publicerats går det inte att ångra uppgraderingen. Ändringar tas dock bort om du navigerar bort från instrument panelen utan att publicera på nytt.  

När du har klickat på den kommer visualiseringen att uppdateras till den nya tekniken. Diskreta skillnader i visualiseringen kan komma att justeras med deras utseende och känsla i Log Analytics.

När visualiseringarna har uppgraderats måste du publicera om instrument panelen för att ändringen ska börja gälla.

![Skärm bild som visar uppgraderade visualiseringar.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Steg 2 – migrering av alla instrument paneler

När en inledande opt-in-period är över, kommer Log Analytics-teamet att uppgradera alla instrument paneler i systemet. Genom att justera alla Azure-instrumentpaneler kan teamet introducera fler visualiseringar och uppleva förbättringar på tavlan.

## <a name="considerations"></a>Överväganden

Log Analytics visualiseringar som fästs på en instrument panel har en viss funktion som är utformad för optimal upplevelse. Granska följande design aspekter när du fäster en visualisering på en instrument panel.

### <a name="query-time-scope---30-day-limit"></a>Tidsintervall för fråga – 30 dagars gräns

Eftersom instrumentpaneler kan innehålla flera visualiseringar från flera frågor är tidsintervallet för en enskild fäst fråga begränsat till 30 dagar. En enskild fråga kan bara köras på ett tidsintervall som är mindre än eller lika med 30 dagar. Den här begränsningen är att säkerställa en rimlig inläsnings tid för instrument panelen.

### <a name="query-data-values---25-values-and-other-grouping"></a>Fråga efter data värden-25 värden och annan gruppering

Instrument paneler kan vara visuellt täta och komplexa. För att minska kognitiv belastning när du visar en instrument panel optimerar vi visualiseringarna genom att begränsa visningen till 25 olika data typer. Om det finns fler än 25 Log Analytics optimerar data. Den visar individuellt de 25 typerna med de flesta data som separata och grupperar sedan de återstående värdena till ett annat värde. Följande diagram visar sådana fall.  

![Skärm bild som visar en instrument panel med 25 olika data typer.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Uppdatering av instrument panelen vid inläsning

Instrument paneler uppdateras vid inläsning. Alla frågor som rör instrument panelens fästa Log Analytics visualiseringar utförs och instrument panelen uppdateras när den har lästs in. Om sidan instrument panel förblir öppen, uppdateras data i instrument panelen var 60: e minut.

## <a name="next-steps"></a>Nästa steg

[Skapa och dela instrument paneler i Log Analytics](../visualize/tutorial-logs-dashboards.md)