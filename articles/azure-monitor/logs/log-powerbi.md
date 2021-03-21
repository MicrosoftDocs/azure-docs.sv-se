---
title: Log Analytics integrering med Power BI och Excel
description: Skicka resultat från Log Analytics till Power BI
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 894b87bfa17a59db20b424199a4021ad75b182fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041067"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics integration med Power BI

Den här artikeln fokuserar på sätt att mata in data från Log Analytics i Microsoft Power BI för att skapa mer visuellt tilltalande rapporter och instrument paneler. 

## <a name="background"></a>Bakgrund 

Azure Monitor loggar är en plattform som tillhandahåller en komplett lösning för att mata in loggar. [Azure Monitor Log Analytics](../platform/data-platform.md#) är gränssnittet för att skicka frågor till dessa loggar. Mer information om hela Azure Monitor data plattform inklusive Log Analytics finns i [Azure Monitor data plattform](../data-platform.md). 

Microsoft Power BI är Microsofts data visualiserings plattform. Mer information om hur du kommer igång finns i [Power BI start sidan](https://powerbi.microsoft.com/). 


I allmänhet kan du använda kostnads fria Power BI-funktioner för att integrera och skapa visuellt tilltalande rapporter och instrument paneler.

Fler avancerade funktioner kan kräva att du köper ett Power BI Pro-eller Premium-konto. Dessa funktioner omfattar bland annat: 
 - Dela ditt arbete 
 - schemalagda uppdateringar
 - Power BI-appar 
 - data flöden och stegvis uppdatering 

Mer information finns i [Läs mer om Power BI priser och funktioner](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Integrera frågor  

Power BI använder [fr åga](/powerquery-m/power-query-m-language-specification/) -frågespråket som huvud språk för frågor. 

Log Analytics frågor kan exporteras till M och används i Power BI direkt. När du har kört en lyckad fråga väljer du **exporten till Power BI (M-fråga)** från knappen **Exportera** i Log Analytics gränssnittets övre åtgärds fält.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Log Analytics fråga som visar menyn för export alternativ PULLDOWN" border="true":::

Log Analytics skapar en txt-fil som innehåller M-koden som kan användas direkt i Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Ansluta dina loggar till en data uppsättning 

En Power BI data uppsättning är en data källa som är klar för rapportering och visualisering. Om du vill ansluta en Log Analytics-fråga till en data uppsättning kopierar du M-koden som exporter ATS från Log Analytics till en tom fråga i Power BI. 

Mer information finns i [förstå Power BI data uppsättningar](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Samla in data med Power BI data flöden 

Med Power BI data flöden kan du också samla in och lagra data. Mer information finns i [Power BI data flöden](/power-bi/service-dataflows-overview).

Ett data flöde är en typ av "moln ETL" som är utformad för att hjälpa dig att samla in och prepa dina data. En data uppsättning är en modell som är utformad för att hjälpa dig att ansluta olika entiteter och modeller efter dina behov.

## <a name="incremental-refresh"></a>Inkrementell uppdatering 

Både Power BI data uppsättningar och Power BI data flöden har ett alternativ för stegvis uppdatering. Power BI data flöden och Power BI data uppsättningar har stöd för den här funktionen, men du behöver Power BI Premium för att använda den.  


Stegvis uppdatering kör små frågor och uppdaterar mindre mängder data per körning i stället för att mata in alla data igen och igen när du kör frågan. Du har möjlighet att spara stora mängder data, men lägga till en ny data ökning varje gång frågan körs. Det här beteendet är idealiskt för att längre köra rapporter.

Power BI stegvis uppdatering är beroende av att ett *datetime* -värde har arkiverats i resultat uppsättningen. Innan du konfigurerar stegvis uppdatering bör du kontrol lera att din Log Analytics frågeresultat innehåller minst ett *datetime* -värde. 

Mer information och hur du konfigurerar stegvis uppdatering finns i [Power BI data uppsättningar och stegvis uppdatering](/power-bi/service-premium-incremental-refresh) och [Power BI data flöden och stegvis uppdatering](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Rapporter och instrumentpaneler

När dina data har skickats till Power BI kan du fortsätta att använda Power BI för att skapa rapporter och instrument paneler.

Mer information finns i [den här guiden om hur du skapar din första Power BI modell och rapport](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>Excel-integration

Du kan använda samma M-integrering som används i Power BI för att integrera med ett Excel-kalkylblad. Mer information finns i den här [guiden om hur du integrerar med Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) och klistrar sedan in M-frågan som exporter ats från Log Analytics.

Ytterligare information hittar du i [integrera Log Analytics och Excel](log-excel.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med [Log Analytics frågor](./log-query-overview.md).