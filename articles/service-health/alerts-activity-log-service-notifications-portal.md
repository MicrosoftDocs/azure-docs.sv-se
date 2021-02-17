---
title: Ta emot aktivitets logg aviseringar på Azure Service Notifications med hjälp av Azure Portal
description: Lär dig hur du använder Azure Portal för att ställa in aktivitets logg aviseringar för meddelanden om tjänstens hälso tillstånd med hjälp av Azure Portal.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570181"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Skapa aktivitets logg aviseringar för tjänst meddelanden med hjälp av Azure Portal
## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder Azure Portal för att ställa in aktivitets logg aviseringar för meddelanden om tjänstens hälso tillstånd med hjälp av Azure Portal.  

Meddelanden om tjänst hälsa lagras i [aktivitets loggen i Azure](../azure-monitor/essentials/platform-logs-overview.md). Med tanke på den stora mängden information som lagras i aktivitets loggen finns ett separat användar gränssnitt som gör det enklare att visa och konfigurera aviseringar om meddelanden om tjänstens hälsa. 

Du kan få en avisering när Azure skickar meddelanden om tjänst hälsa till din Azure-prenumeration. Du kan konfigurera aviseringen baserat på:

- Hälso tillstånds klassen för tjänsten (tjänste problem, planerat underhåll, hälso rekommendationer, säkerhets rekommendationer).
- Prenumerationen som påverkas.
- Den eller de tjänster som påverkas.
- De regioner som påverkas.

> [!NOTE]
> Aviseringar för tjänst hälsa skickar inte aviseringar för resurs hälso händelser.

Du kan också konfigurera vem aviseringen ska skickas till:

- Välj en befintlig åtgärds grupp.
- Skapa en ny åtgärds grupp (som kan användas för framtida aviseringar).

Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

Information om hur du konfigurerar aviseringar om tjänstens hälso tillstånd med hjälp av Azure Resource Manager mallar finns i [Resource Manager-mallar](../azure-monitor/alerts/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Titta på en video om hur du konfigurerar din första Azure Service Health avisering

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Skapa Service Health avisering med Azure Portal
1. I [portalen](https://portal.azure.com)väljer du **service Health**.

    ![Tjänsten "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. I avsnittet **aviseringar** väljer du **hälso aviseringar**.

    ![Fliken "hälso aviseringar"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Välj **Lägg till tjänstens hälso tillstånds avisering** och fyll i fälten.

    ![Kommandot "skapa tjänstens hälso avisering"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Välj den **prenumeration**, de **tjänster** och **regioner** som du vill bli aviserad om.

    [![Dialog rutan Lägg till aktivitets logg avisering](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Den här prenumerationen används för att spara aktivitets logg aviseringen. Aviserings resursen distribueras till den här prenumerationen och övervakar händelser i aktivitets loggen för den.

5. Välj de **händelse typer** som du vill bli aviserad om: *Service problem*, *planerat underhåll*, *hälso rekommendationer* och *säkerhets rådgivning*.

6. Klicka på **Välj åtgärds grupp** för att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md).


7. Definiera aviserings informationen genom att ange ett namn och en **Beskrivning** för **varnings regeln** .

8. Välj den **resurs grupp** där du vill att aviseringen ska sparas.



Inom några minuter är aviseringen aktiv och börjar utlösas baserat på de villkor du angav när du skapade.

Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md). Information om webhook-schemat för aktivitets logg aviseringar finns i [Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [metod tips för att konfigurera Azure Service Health aviseringar](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Lär dig hur du [ställer in mobila push-meddelanden för Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Lär dig hur du [konfigurerar webhook-meddelanden för befintliga problem hanterings system](service-health-alert-webhook-guide.md).
- Läs mer om [meddelanden om tjänst hälsa](service-notifications.md).
- Lär dig mer om [begränsning av meddelande frekvens](../azure-monitor/alerts/alerts-rate-limiting.md).
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Få en [Översikt över aktivitets logg aviseringar](../azure-monitor/alerts/alerts-overview.md)och lär dig hur du tar emot aviseringar.
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
