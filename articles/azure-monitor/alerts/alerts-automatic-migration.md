---
title: Förstå hur processen för automatisk migrering för dina Azure Monitor klassiska aviseringar fungerar
description: Lär dig hur processen för automatisk migrering fungerar.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 14118b69b46bafa76889f53152438e2cb2ab4e96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625846"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Förstå processen för automatisk migrering för dina klassiska aviserings regler

Som [tidigare](../platform/monitoring-classic-retirement.md)har meddelats kommer de klassiska aviseringarna i Azure monitor att dras tillbaka för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna. Som en del av indragnings processen finns [ett Migreringsverktyg](alerts-using-migration-tool.md) i Azure Portal för kunder att utlösa migrering själva.
Den här artikeln vägleder dig genom processen för automatisk migrering och hjälper dig att lösa eventuella problem som du kan köra i.

  > [!NOTE]
  > Den här artikeln gäller endast för det offentliga Azure-molnet. Borttagnings processen för Azure Monitor klassiska aviseringar i Azure Government molnet och Azure Kina 21Vianet kommer att meddelas vid ett senare tillfälle.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>Vad händer när processen för automatisk migrering sker?

- Från och med **1 September 2019** kommer kunder inte att kunna skapa några nya regler för klassisk avisering förutom [vissa mått](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).
- För undantag kan kunden fortsätta att skapa nya klassiska aviserings regler och använda sina klassiska aviseringar fram till ytterligare ett meddelande.
- Från och med **1 September 2019** aktive ras migrering av klassiska aviseringar i batchar för kunder som har klassiska aviseringar.
- Precis som med verktyget för frivillig migrering kommer vissa klassiska aviserings regler som inte går migrera att lämnas som de är. Dessa klassiska varnings regler kommer att fortsätta att stödjas fram till ytterligare meddelande. Alla ogiltiga regler för klassisk avisering tas dock bort eftersom de inte fungerar.
Eventuella klassiska varnings regler som övervakar borttagna mål resurser eller på [mått som inte längre stöds](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) betraktas som ogiltiga.
- När migreringen för din prenumeration startar, om det inte finns några problem, ska migreringen slutföras inom en timme. Kunder kan övervaka status för migreringen på [bladet migrering i Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Prenumerations ägare får ett e-postmeddelande om att migreringen har slutförts.
- Om det uppstår några problem under migreringen får prenumerations ägarna också ett e-postmeddelande som informerar dem om samma. Kunder kan använda flyttnings bladet för att se all information om problemet.
- Om en åtgärd krävs från kunder, t. ex. genom att tillfälligt inaktivera ett resurs lås eller ändra en princip tilldelning, måste kunderna lösa eventuella problem. Om problemen inte löses av kan du inte garantera att migreringen av dina klassiska aviseringar är korrekt.

    > [!NOTE]
    > Om du inte vill vänta på att processen för automatisk migrering ska starta kan du fortfarande aktivera migreringen med hjälp av Migreringsverktyget.

## <a name="important-things-to-note"></a>Viktiga saker att tänka på

Migreringsprocessen konverterar de klassiska varnings reglerna till nya, motsvarande varnings regler och skapar åtgärds grupper. Vid förberedelse bör du vara medveten om följande punkter:

- Meddelande nytto Last formaten för nya varnings regler skiljer sig från de klassiska varnings reglerna eftersom de har stöd för fler funktioner. Om du har Logi Kap par kan de Runbooks eller webhookar som utlöses av den klassiska varnings regeln sluta fungera som förväntat när migreringen är klar på grund av skillnader i meddelande nytto laster. [Lär dig hur du förbereder migreringen](alerts-prepare-migration.md).

- Vissa klassiska varnings regler kan inte migreras med verktyget. [Lär dig vilka regler som inte kan migreras och vad du kan göra med dem](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Migreringsprocessen påverkar inte utvärderingen av dina klassiska aviserings regler. De fortsätter att köra och skicka aviseringar tills de har migrerats och de nya varnings reglerna börjar gälla.

## <a name="what-if-the-automatic-migration-fails"></a>Vad händer om den automatiska migreringen Miss lyckas?

När processen för automatisk migrering Miss lyckas får prenumerations ägarna ett e-postmeddelande om problemet. Du kan använda flyttnings bladet i Azure Monitor om du vill se en fullständig information om problemet.

I [fel söknings guiden](alerts-understand-migration.md#common-problems-and-remedies) finns information om eventuella problem som kan uppstå under migreringen.

  > [!NOTE]
  > Om en åtgärd krävs från kunder, t. ex. genom att tillfälligt inaktivera ett resurs lås eller ändra en princip tilldelning, måste kunderna lösa eventuella problem. Om problemen inte löses av kan du inte garantera att migreringen av dina klassiska aviseringar är klar.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migrering](alerts-prepare-migration.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)
