---
title: Snabb korrigering för rådgivare-rekommendationer
description: Utför Mass reparation med snabb korrigering i Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90968596"
---
# <a name="quick-fix-remediation-for-advisor"></a>Snabb korrigering för Advisor
**Snabb korrigering** möjliggör ett snabbare och enklare sätt att åtgärda på flera resurser. Den ger möjlighet till Mass reparationer av resurser och hjälper dig att optimera dina prenumerationer snabbare med reparationer i skala för dina resurser.
Funktionen är endast tillgänglig för vissa rekommendationer, via Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Steg för att använda snabb korrigering

1. I listan över rekommendationer som har etiketten **snabb korrigering** klickar du på rekommendationen.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Skärm bild av Azure Advisor som visar snabb korrigerings etiketter i rekommendationerna.}":::
   
   *Priserna i bilden är endast till exempel syfte*

2. På sidan rekommendations information visas en lista över resurser som du har den här rekommendationen för. Välj alla resurser som du vill åtgärda för rekommendationen.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="Skärm bild av fönstret påverkade resurser med list objekt och knappen snabb korrigering markerat.":::
   
   *Priserna i bilden är endast till exempel syfte*

3. När du har valt resurserna klickar du på knappen **snabb korrigering** för att återställa.

   > [!NOTE]
   > Några av de listade resurserna kan vara inaktiverade eftersom du inte har rätt behörighet för att ändra dem.
   
   > [!NOTE]
   > Om det finns andra konsekvenser kan du, utöver de fördelar som anges i Advisor, meddelas i upplevelsen för att hjälpa dig att fatta välgrundade beslut om reparation.
   
4. Du får ett meddelande om reparations åtgärden. Du får ett fel meddelande om det finns resurser som inte åtgärdas och resurser i det valda läget i vyn resurs lista.  


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
