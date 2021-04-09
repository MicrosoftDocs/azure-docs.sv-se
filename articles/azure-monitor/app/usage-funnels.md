---
title: Azure Application Insights-trattar
description: Lär dig hur du kan använda trattar för att upptäcka hur kunderna interagerar med ditt program.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: c09667e0493fc39e8a2679a698f06301bab6ba45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024956"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Upptäck hur kunder använder ditt program med Application Insights-trattar

Att förstå kund upplevelsen är av yttersta vikt för ditt företag. Om ditt program omfattar flera steg, måste du veta om de flesta kunder går igenom hela processen eller om de avslutar processen vid en viss tidpunkt. Förloppet mellan en serie steg i ett webb program kallas för en *tratt*. Du kan använda Azure Application Insights-trattar för att få insikter om dina användare, och för att övervaka steg-för-steg-omräknings takt. 

## <a name="create-your-funnel"></a>Skapa din tratt
Innan du skapar din tratt bestämmer du den fråga som du vill besvara. Du kanske till exempel vill veta hur många användare som visar start sidan, visar en kund profil och skapar en biljett. I det här exemplet vill ägarna till det Fabrikam-fiber företag som vill veta hur många procent av kunderna som kunde skapa en kund biljett.

Här följer de steg som du vidtar för att skapa en tratt.

1. I verktyget Application Insights trattar väljer du **nytt**.
1. I den nedrullningsbara menyn **tidsintervall** väljer du de **senaste 90 dagarna**. Välj antingen **Mina trattar** eller **delade trattar**.
1. Välj **index** i list rutan **steg 1** . 
1. I listan **steg 2** väljer du **kund**.
1. I listan **steg 3** väljer du **skapa**.
1. Lägg till ett namn i tratten och välj **Spara**.

Följande skärm bild visar ett exempel på vilken typ av data som skapas av tratt-verktyget. Fabrikam-ägare kan se att under de senaste 90 dagarna, 54,3 procent av sina kunder som besökte start sidan skapade en kund biljett. De kan också se att 2 700 av sina kunder kommer till indexet från start sidan. Detta kan tyda på ett uppdaterings problem.


![Skärm bild av trattdiagram-verktyget med data](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Trattens funktioner
Föregående skärm bild innehåller fem markerade områden. Dessa är funktioner i trattar. I följande lista beskrivs mer om varje motsvarande del i skärm bilden:
1. Om din app är samplad visas en banderoll. Om du väljer banderollen öppnas ett kontext fönster som förklarar hur du inaktiverar sampling. 
2. Du kan exportera din tratt till [Power BI](./export-power-bi.md).
3. Välj ett steg om du vill visa mer information till höger. 
4. Det historiska konverterings diagrammet visar konverterings kurserna under de senaste 90 dagarna. 
5. Förstå användarna bättre genom att komma åt användar verktyget. Du kan använda filter i varje steg. 

## <a name="next-steps"></a>Nästa steg
  * [Översikt över användning](usage-overview.md)
  * [Användare, sessioner och händelser](usage-segmentation.md)
  * [Kvarhållning](usage-retention.md)
  * [Arbetsböcker](../visualize/workbooks-overview.md)
  * [Lägg till användar kontext](./usage-overview.md)
  * [Exportera till Power BI](./export-power-bi.md)