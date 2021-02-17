---
title: Skapa Resource Health aviseringar med hjälp av Azure Portal
description: Skapa en avisering med Azure Portal som meddelar dig när dina Azure-resurser blir otillgängliga.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594624"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Konfigurera resurshälsoaviseringar med Azure-portalen

Den här artikeln visar hur du ställer in aktivitets logg aviseringar för resurs hälso meddelanden med hjälp av Azure Portal.

Azure Resource Health håller dig informerad om aktuella och historiska hälso status för dina Azure-resurser. Azure Resource Health-avisering kan meddela dig i nästan realtid när dessa resurser har en ändring i sin hälsostatus. Skapa Resource Health aviseringar program mässigt tillåta att användare skapar och anpassar aviseringar i flera.

Resurs hälso meddelanden lagras i [Azure aktivitets loggen](../azure-monitor/essentials/platform-logs-overview.md) med den stora mängden information som lagras i aktivitets loggen. det finns ett separat användar gränssnitt som gör det enklare att visa och konfigurera aviseringar om resurs hälso meddelanden.
Du kan få en avisering när Azure-resursen skickar resurs hälso meddelanden till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

* Prenumerationen som påverkas.
* Resurs (er) typ (er) som påverkas.
* Den eller de resurs grupper som påverkas.
* Resurs (er) som påverkas.
* Händelse status för resurs (er) som påverkas.
* Resurs (er) påverkade status.
* Anledning (er) typ (er) av resurs (er) som påverkas.

Du kan också konfigurera vem aviseringen ska skickas till:

* Välj en befintlig åtgärds grupp.
* Skapa en ny åtgärds grupp (som kan användas för framtida aviseringar).

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

Information om hur du konfigurerar aviserings aviseringar för resurs hälsa med hjälp av Azure Resource Manager mallar finns i [Resource Manager-mallar](./resource-health-alert-arm-template-guide.md).
Resource Health avisering med Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Resource Health avisering med hjälp av Azure Portal

1. I Azure- [portalen](https://portal.azure.com/)väljer du **service Health**.

    ![Service Health val](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. I avsnittet **Resource Health** väljer du **service Health**.
3. Välj **Lägg till resurs hälso avisering** och fyll i fälten.
4. Under aviserings mål väljer du den **prenumeration**, de **resurs typer**, **resurs grupper** och den **resurs** som du vill bli aviserad om.

    ![Val av val av mål](./media/resource-health-alert-monitor-guide/alert-target.png)

5. Under aviserings villkor väljer du:
    1. **Händelse status** som du vill bli aviserad om. Allvarlighets grad för händelsen: aktiv, löst, pågår, uppdaterat
    2. Den **resurs status** som du vill bli aviserad om. Resurs status för händelsen: tillgänglig, ej tillgänglig, okänd, degraderad
    3. Den **orsaks typ** som du vill bli aviserad om. Orsaken till händelsen: plattform initierad, användare som initierade ![ varnings villkor val](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. Under definiera aviserings information anger du följande information:
    1. **Namn på varnings regel**: namnet på den nya varnings regeln.
    2. **Beskrivning**: beskrivningen av den nya varnings regeln.
    3. **Spara avisering till resurs grupp**: Välj den resurs grupp där du vill spara den nya regeln.
7. Under **Åtgärds grupp** i den nedrullningsbara menyn anger du den åtgärds grupp som du vill tilldela till den nya varnings regeln. Du kan också [skapa en ny åtgärds grupp](../azure-monitor/alerts/action-groups.md) och tilldela den till den nya regeln. Om du vill skapa en ny grupp väljer du + **ny grupp**.
8. Om du vill aktivera reglerna efter att du har skapat dem väljer du **Ja** för alternativet **Aktivera regel vid skapande** .
9. Välj **Skapa varningsregel**.

Den nya varnings regeln för aktivitets loggen skapas och ett bekräftelse meddelande visas i det övre högra hörnet i fönstret.
Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om [hur du hanterar aktivitets logg regler](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:

* [Översikt över Azure Resource Health](Resource-health-overview.md)
* [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)

Skapa Service Health aviseringar:

* [Konfigurera aviseringar för Service Health](./alerts-activity-log-service-notifications-portal.md) 
* [Händelse schema för Azure aktivitets logg](../azure-monitor/essentials/activity-log-schema.md)
* [Konfigurera resurshälsoaviseringar med Resource Manager-mallar](./resource-health-alert-arm-template-guide.md)
