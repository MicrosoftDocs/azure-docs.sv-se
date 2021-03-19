---
title: Skapa aviseringar för Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du konfigurerar Azure-aviseringar för att meddela om status för ändringar som upptäckts av Ändringsspårning och inventering.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594513"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Skapa aviseringar för Ändringsspårning och inventering

Aviseringar i Azure meddelar dig om resultat från Runbook-jobb, service Health-problem eller andra scenarier som är relaterade till ditt Automation-konto. Azure Automation innehåller inte förkonfigurerade aviserings regler, men du kan skapa egna baserat på de data som genereras. Den här artikeln innehåller vägledning om hur du skapar aviserings regler utifrån ändringar som identifieras av Ändringsspårning och inventering.

Om du inte är bekant med Azure Monitor aviseringar, se [Översikt över aviseringar i Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) innan du börjar. Om du vill veta mer om aviseringar som använder logg frågor, se [logg aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Skapa avisering

I följande exempel visas att filen **c:\Windows\System32\drivers\etc\hosts** har ändrats på en dator. Filen är viktig eftersom Windows använder den för att matcha värdnamn med IP-adresser. Den här åtgärden åsidosätter DNS och kan leda till anslutnings problem. Det kan också leda till omdirigering av trafik till skadliga eller på annat sätt farliga webbplatser.

![Diagram som visar ändringar i värd filen](./media/configure-alerts/changes.png)

Vi ska använda det här exemplet för att diskutera stegen för att skapa aviseringar vid en ändring.

1. På sidan **ändrings spårning** från ditt Automation-konto väljer du **Log Analytics**.

2. Leta efter innehålls ändringar i **värd** filen med frågan i loggar Sök `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Den här frågan söker efter innehålls ändringar för filer med fullständigt kvalificerade Sök vägs namn som innehåller ordet `hosts` . Du kan också fråga efter en viss fil genom att ändra Sök vägs delen till dess fullständigt kvalificerade form, till exempel med hjälp av `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. När frågan returnerar sina resultat väljer du **ny varnings regel** i logg sökningen för att öppna sidan **skapa avisering** . Du kan också navigera till den här sidan via **Azure Monitor** i Azure Portal.

4. Kontrol lera frågan igen och ändra aviserings logiken. I det här fallet vill du att aviseringen ska utlösas om det inte finns någon ändring som upptäcks på alla datorer i miljön.

    ![Ändra till fråga för att spåra ändringar av hosts-filen](./media/configure-alerts/change-query.png)

5. När aviserings logiken har angetts tilldelar du åtgärds grupper för att utföra åtgärder som svar på utlösningen av aviseringen. I det här fallet ställer vi in e-postmeddelanden som ska skickas och ett ITSM-kvitto (IT Service Management) skapas.

Följ stegen nedan för att ställa in aviseringar så att du kan se status för en uppdaterings distribution. Om du är nybörjare på Azure-aviseringar kan du läsa [Översikt över Azure-aviseringar](../../azure-monitor/alerts/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurera åtgärds grupper för dina aviseringar

När du har konfigurerat dina aviseringar kan du konfigurera en åtgärds grupp, som är en grupp åtgärder som ska användas över flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, Runbooks, Webhooks och mycket mer. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../../azure-monitor/alerts/action-groups.md).

1. Välj en avisering och välj sedan **Skapa ny** under **Åtgärds grupper**.

2. Ange ett fullständigt namn och ett kort namn för åtgärds gruppen. Uppdateringshantering använder det korta namnet när meddelanden skickas med den angivna gruppen.

3. Under **åtgärder** anger du ett namn som anger åtgärden, till exempel **e-postavisering**.

4. För **Åtgärds typ** väljer du lämplig typ, till exempel **e-post/SMS-meddelande/push/röst**.

5. Välj Penn ikonen för att redigera åtgärds informationen.

6. Fyll i fönstret för åtgärds typen. Om du till exempel använder **e-post/SMS-meddelande/push/röst** för att skicka ett e-postmeddelande, anger du ett namn på åtgärden, markerar kryss rutan **e-** postadress, anger en giltig e-postadress och väljer sedan **OK**.

    ![Konfigurera en e-poståtgärdsgrupp](./media/configure-alerts/configure-email-action-group.png)

7. I fönstret Lägg till åtgärdsgrupp väljer du **OK**.

8. För e-postaviseringar kan du anpassa e-postmeddelandets ämne. Välj **Anpassa åtgärder** under **Skapa regel** och välj sedan **e-postämne**.

9. När du är klar väljer du **Skapa varningsregel**.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

* Lär dig mer om [logg frågor](../../azure-monitor/logs/log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.

* Hantera [användning och kostnader med Azure Monitor loggar](../../azure-monitor/logs/manage-cost-storage.md) beskriver hur du styr dina kostnader genom att ändra din data lagrings period och hur du analyserar och varnar för din data användning.