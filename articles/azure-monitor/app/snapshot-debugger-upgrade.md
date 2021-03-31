---
title: Uppgradera Azure Application insikter Snapshot Debugger
description: Så här uppgraderar du Snapshot Debugger för .NET-appar till den senaste versionen på Azure App Services eller via NuGet-paket
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77671400"
---
# <a name="upgrading-the-snapshot-debugger"></a>Uppgradera Snapshot Debugger

För att tillhandahålla bästa möjliga säkerhet för dina data, flyttas Microsoft från TLS 1,0 och TLS 1,1, som har visat sig vara sårbara för bestämda angripare. Om du använder en äldre version av plats tillägget måste en uppgradering fortsätta att fungera. Det här dokumentet beskriver de steg som krävs för att uppgradera din ögonblicks bilds fel sökare till den senaste versionen. Det finns två primära uppgraderings vägar beroende på om du har aktiverat Snapshot Debugger med ett plats tillägg eller om du använde en SDK-NuGet som har lagts till i ditt program. Både uppgraderings Sök vägar beskrivs nedan. 

## <a name="upgrading-the-site-extension"></a>Uppgradera webbplats tillägget

> [!IMPORTANT]
> Äldre versioner av Application Insights använt ett privat webbplats tillägg som kallas _Application Insights-tillägg för Azure App Service_. Den aktuella Application Insights upplevelsen aktive ras genom att ställa in appinställningar för att se till att ett förinstallerat plats tillägg visas.
> För att undvika konflikter, vilket kan leda till att platsen slutar fungera, är det viktigt att du tar bort tillägget för den privata platsen först. Se steg 4 nedan.

Om du har aktiverat fel sökning av ögonblicks bilder med hjälp av webbplats tillägget kan du uppgradera med hjälp av följande procedur:

1. Logga in på Azure-portalen.
2. Navigera till din resurs som har Application Insights och ögonblicks bilds fel sökning aktive rad. För en webbapp går du till exempel till App Service resurs:

   ![Skärm bild av enskild App Service resurs med namnet DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. När du har navigerat till din resurs klickar du på bladet tillägg och väntar på listan över tillägg som ska fyllas i:

   ![Skärm bild av App Service tillägg som visar Application Insights-tillägget för Azure App Service installerade](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Om någon version av _Application Insights-tillägget för Azure App Service_ är installerat väljer du den och klickar på ta bort. Bekräfta att **Ja** tar bort tillägget och vänta tills borttagningen har slutförts innan du fortsätter till nästa steg.

   ![Skärm bild av App Service tillägg som visar Application Insights tillägg för Azure App Service med knappen Ta bort markerad](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Gå till bladet översikt i resursen och klicka på Application Insights:

   ![Skärm bild av tre knappar. Knappen centrera med namnet Application Insights har valts](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Om det här är första gången du har sett Application Insights bladet för den här App Service, uppmanas du att aktivera Application Insights. Välj **aktivera Application Insights**.
 
   ![Skärm bild av första gången du får Application Insights bladet med knappen Aktivera Application Insights markerat](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. De aktuella Application Insights inställningarna visas. Om du inte vill ha möjlighet att ändra inställningarna kan du lämna dem som de är. Knappen **tillämpa** längst ned på bladet är inte aktive rad som standard och du måste växla en av inställningarna för att aktivera knappen. Du behöver inte ändra några faktiska inställningar, i stället för att ändra inställningen och sedan ändra tillbaka den direkt. Vi rekommenderar att du ställer in inställningen för profileraren och väljer **Använd**.

   ![Skärm bild av sidan Application Insights App Service konfiguration med knappen Använd markerad i rött](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. När du klickar på **Verkställ** uppmanas du att bekräfta ändringarna.

    > [!NOTE]
    > Platsen kommer att startas om som en del av uppgraderings processen.

   ![Skärm bild av App Service Använd övervaknings fråga. Text ruta visar meddelande: "Vi kommer nu att tillämpa ändringar i dina appdata och installera våra verktyg för att länka din Application Insights-resurs till webbappen. Då startas webbplatsen om. Vill du fortsätta? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Klicka på **Ja** om du vill tillämpa ändringarna och vänta tills processen har slutförts.

Platsen har nu uppgraderats och är redo att användas.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Uppgradera Snapshot Debugger med SDK/NuGet

Om programmet använder en version av `Microsoft.ApplicationInsights.SnapshotCollector` lägre version 1.3.1 måste den uppgraderas till en [nyare version](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) för att fortsätta att fungera.
