---
title: Migrera Azure Monitor varnings regler
description: Lär dig hur du använder verktyget för frivillig migrering för att migrera dina klassiska aviserings regler.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 28ccdde85f2873839fbe977c3c991177ac8bb3bb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621904"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Använd verktyget för frivillig migrering för att migrera dina klassiska aviserings regler

Som [tidigare](../platform/monitoring-classic-retirement.md)har meddelats kommer de klassiska aviseringarna i Azure monitor att dras tillbaka för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna. Ett Migreringsverktyg var tillgängligt i Azure Portal till kunder som använde klassiska aviserings regler och som vill utlösa migrering själva. Den här artikeln förklarar hur du använder det Migreringsverktyg, som även kommer att användas för återstående aviseringar som väntar på ytterligare meddelande.

## <a name="benefits-of-new-alerts"></a>Fördelar med nya aviseringar

Klassiska aviseringar ersätts med nya, enhetliga aviseringar i Azure Monitor. Den nya varnings plattformen har följande fördelar:

- Du kan få aviseringar på en mängd olika flerdimensionella mått för [många fler Azure-tjänster](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- De nya måtten Alerts stöder [varnings regler för flera resurser](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) som avsevärt minskar behovet av att hantera många regler.
- Den enhetliga meddelande mekanismen, som stöder:
  - [Åtgärds grupper](../platform/action-groups.md), en modulär meddelande funktion som fungerar med alla nya aviserings typer (statistik, logg och aktivitets logg).
  - Nya aviserings metoder som SMS, röst och ITSM-anslutningsprogram.
- Den [enhetliga aviserings upplevelsen](../platform/alerts-overview.md) ger alla aviseringar om olika signaler (mått, logg och aktivitets logg) till en enda plats.

## <a name="before-you-migrate"></a>Innan du migrerar

Migreringsprocessen konverterar de klassiska varnings reglerna till nya, motsvarande varnings regler och skapar åtgärds grupper. Vid förberedelse bör du vara medveten om följande punkter:

- Både meddelandets nytto Last format och API: er för att skapa och hantera nya varnings regler skiljer sig från de klassiska varnings reglerna eftersom de har stöd för fler funktioner. [Lär dig hur du förbereder migreringen](alerts-prepare-migration.md).

- Vissa klassiska varnings regler kan inte migreras med verktyget. [Lär dig vilka regler som inte kan migreras och vad du kan göra med dem](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Migreringsprocessen påverkar inte utvärderingen av dina klassiska aviserings regler. De fortsätter att köra och skicka aviseringar tills de har migrerats och de nya varnings reglerna börjar gälla.

## <a name="how-to-use-the-migration-tool"></a>Använda migreringsverktyget

Följ dessa steg om du vill utlösa migreringen av dina klassiska varnings regler i Azure Portal:

1. I [Azure Portal](https://portal.azure.com)väljer du **övervaka**.

1. Välj **aviseringar** och välj sedan **Hantera aviserings regler** eller **Visa klassiska aviseringar**.

1. Välj **migrera till nya regler** för att gå till landnings sidan för migrering. Den här sidan visar en lista över alla dina prenumerationer och deras migrerings status:

    ![Skärm bild som visar sidan Migrera aviserings regler.](media/alerts-using-migration-tool/migration-landing.png "Migrera regler")

    Alla prenumerationer som kan migreras med hjälp av verktyget markeras som **redo för migrering**.

    > [!NOTE]
    > Migrations verktyget distribueras i faser till alla prenumerationer som använder klassiska aviserings regler. I de tidiga faserna av distributionen kan du se vissa prenumerationer som marker ATS som ej redo för migrering.

1. Välj en eller flera prenumerationer och välj sedan **Förhandsgranska migrering**.

    Den resulterande sidan visar information om klassiska aviserings regler som kommer att migreras för en prenumeration i taget. Du kan också välja **Hämta migrerings information för den här prenumerationen** om du vill ha mer information i CSV-format.

    ![Skärm bild som visar sidan Migrera aviserings regler med en länk för att hämta information om migreringen för den här prenumerationen och du kan ange e-post för migrerings avisering.](media/alerts-using-migration-tool/migration-preview.png "Förhandsgranska migrering")

1. Ange en eller flera e-postadresser som ska meddelas om migreringens status. Du får ett e-postmeddelande när migreringen är klar eller om någon åtgärd krävs från dig.

1. Välj **Starta migrering**. Läs informationen som visas i bekräftelse dialog rutan och bekräfta att du är redo att starta migreringsprocessen.

    > [!IMPORTANT]
    > När du har initierat migreringen för en prenumeration kan du inte redigera eller skapa klassiska aviserings regler för den prenumerationen. Den här begränsningen garanterar att inga ändringar i de klassiska varnings reglerna går förlorade under migreringen till de nya reglerna. Även om du inte kan ändra de klassiska varnings reglerna fortsätter de fortfarande att köras och för att tillhandahålla aviseringar tills de har migrerats. När migreringen har slutförts för din prenumeration kan du inte längre använda de klassiska varnings reglerna.

    ![Skärm bild som visar ett meddelande om migreringen, inklusive viktig information med länkar för att lära dig mer innan du fortsätter.](media/alerts-using-migration-tool/migration-confirm.png "Bekräfta start migrering")

1. När migreringen är klar, eller om en åtgärd krävs från dig, får du ett e-postmeddelande till de adresser som du angav tidigare. Du kan också kontrol lera statusen regelbundet på sidan migration vilplan i portalen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Varför är min prenumeration listad som ej redo för migrering?

Migrations verktyget distribueras till kunder i faser. I de tidiga faserna kan de flesta eller alla prenumerationer markeras som **ej redo för migrering**. 

När en prenumeration blir klar för migrering får prenumerations ägaren ett e-postmeddelande som talar om att verktyget är tillgängligt. Behåll ett öga för det här meddelandet.

### <a name="who-can-trigger-the-migration"></a>Vem kan utlösa migreringen?

Användare som har rollen övervaknings deltagare tilldelad till dem på prenumerations nivå kan utlösa migreringen. [Lär dig mer om rollbaserad åtkomst kontroll i Azure för migreringsprocessen](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Hur lång tid tar migreringen?

Migreringen har slutförts för de flesta prenumerationer på under en timme. Du kan hålla koll på migreringens förlopp på sidan migration vilplan. Under migreringen bör du vara säker på att aviseringarna fortfarande körs i det klassiska varnings systemet eller i det nya.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Vad kan jag göra om jag stöter på ett problem under migreringen?

I [fel söknings guiden](alerts-understand-migration.md#common-problems-and-remedies) finns information om eventuella problem som kan uppstå under migreringen. Om någon åtgärd krävs från dig för att slutföra migreringen får du ett meddelande till e-postadresserna du angav när du konfigurerade verktyget.

## <a name="next-steps"></a>Nästa steg

- [Förbereda för migrering](alerts-prepare-migration.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)
