---
title: Förstå hur processen för automatisk migrering för dina Azure Monitor klassiska aviseringar fungerar
description: Lär dig hur processen för automatisk migrering fungerar.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734867"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Förstå processen för automatisk migrering för dina klassiska aviserings regler

Som [tidigare](monitoring-classic-retirement.md)har meddelats kommer de klassiska aviseringarna i Azure monitor att dras tillbaka för offentliga moln användare, men fortfarande i begränsad användning till och med **31 maj 2021**. Klassiska aviseringar för Azure Government molnet och Azure Kina 21Vianet kommer att dra tillbaka den **29 februari 2024**.

[Ett Migreringsverktyg](alerts-using-migration-tool.md) är tillgängligt i Azure Portal för kunderna att utlösa migrering själva. I den här artikeln förklaras processen för automatisk migrering i offentliga moln, som startar efter 31 maj 2021. Den innehåller också information om problem och lösningar som du kan köra.

## <a name="important-things-to-note"></a>Viktiga saker att tänka på

Migreringsprocessen konverterar de klassiska varnings reglerna till nya, motsvarande varnings regler och skapar åtgärds grupper. Vid förberedelse bör du vara medveten om följande punkter:

- Meddelande nytto Last formaten för nya varnings regler skiljer sig från nytto laster i de klassiska varnings reglerna eftersom de har stöd för fler funktioner. Om du har en klassisk aviserings regel med Logic Apps, Runbooks eller Webhooks kan de sluta fungera som förväntat efter migreringen, på grund av olikheter i nytto lasten. [Lär dig hur du förbereder migreringen](alerts-prepare-migration.md).

- Vissa klassiska varnings regler kan inte migreras med verktyget. [Lär dig vilka regler som inte kan migreras och vad du kan göra med dem](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Vad händer när processen för automatisk migrering sker i offentliga moln?

- Från och med 31 maj 2021 kommer du inte att kunna skapa några nya klassiska varnings regler och migrering av klassiska aviseringar utlöses i batchar.
- Eventuella klassiska varnings regler som övervakar borttagna mål resurser eller på [mått som inte längre stöds](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) betraktas som ogiltiga.
- De klassiska varnings reglerna som är ogiltiga kommer att tas bort någon gång efter 31 maj 2021.
- När migreringen för prenumerationen har startat bör den slutföras inom en timme. Kunder kan övervaka status för migreringen i [migrations verktyget i Azure Monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Prenumerations ägare får ett e-postmeddelande om att migreringen lyckades eller misslyckades.

    > [!NOTE]
    > Om du inte vill vänta på att processen för automatisk migrering ska starta kan du fortfarande aktivera migreringen med hjälp av Migreringsverktyget.

## <a name="what-if-the-automatic-migration-fails"></a>Vad händer om den automatiska migreringen Miss lyckas?

När processen för automatisk migrering Miss lyckas får prenumerations ägarna ett e-postmeddelande om problemet. Du kan använda Migreringsverktyget i Azure Monitor om du vill se en fullständig information om problemet. I [fel söknings guiden](alerts-understand-migration.md#common-problems-and-remedies) finns information om eventuella problem som kan uppstå under migreringen.

  > [!NOTE]
  > Om en åtgärd krävs från kunder, t. ex. genom att tillfälligt inaktivera ett resurs lås eller ändra en princip tilldelning, måste kunderna lösa eventuella problem. Om problemen inte löses av kan du inte garantera att migreringen av dina klassiska aviseringar är klar.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migrering](alerts-prepare-migration.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)