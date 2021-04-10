---
title: Förstå hur etablering integreras med Azure Monitor loggar i Azure Active Directory.
description: Förstå hur etablering integreras med Azure Monitor loggar i Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561576"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Förstå hur etablering integreras med Azure Monitor loggar

Etableringen integreras med Azure Monitor loggar och Log Analytics. Med Azure-övervakning kan du göra saker som att skapa arbets böcker, även kallade instrument paneler, lagra etablerings loggar i 30 + dagar och skapa anpassade frågor och aviseringar. Den här artikeln beskriver hur etablering av loggar integreras med Azure Monitor loggar. Mer information om hur etablering av loggar fungerar i allmänhet finns i [etablerings loggar](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Aktivera etablerings loggar

Du bör redan vara bekant med Azure-övervakning och Log Analytics. Om inte, hoppa över för att lära dig mer om dem och gå sedan tillbaka till Lär dig mer om program etablerings loggar. Läs mer om Azure-övervakning i [Azure Monitor översikt](../../azure-monitor/overview.md). Mer information om Azure Monitor loggar och Log Analytics finns i [Översikt över logg frågor i Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

När du har konfigurerat Azure-övervakning kan du aktivera loggar för program etablering. Alternativet finns på sidan **diagnostikinställningar** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Åtkomst till diagnostikinställningar" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Aktivera program etablerings loggar" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Om du nyss har skapat en arbets yta kan det ta lite tid innan du kan skicka loggar till den. Om du får ett fel meddelande om att prenumerationen inte är registrerad för att använda *Microsoft. Insights* kan du kontrol lera igen om några minuter.
 
## <a name="understanding-the-data"></a>Förstå data
Den underliggande data strömmen som etableringen skickar loggar till är nästan identisk. Azure Monitor loggar får nästan samma data ström som Azure Portal UI och Azure API. Det finns bara några **skillnader** i logg fälten som beskrivs i följande tabell. Mer information om de här fälten finns i [lista provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Azure Monitor-loggar   |Azure Portal användar gränssnitt   |Azure-API |
|----------|-----------|------------|
|errorDescription |orsak |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor-arbetsböcker

Azure Monitor arbets böcker ger en flexibel arbets yta för data analys. De innehåller också information om hur du skapar omfattande visuella rapporter i Azure Portal. Mer information finns i [Översikt över Azure Monitor-arbetsböcker](../../azure-monitor/visualize/workbooks-overview.md).

Program etableringen levereras med en uppsättning färdiga arbets böcker. Du hittar dem på sidan arbets böcker. Om du vill visa data måste du se till att alla filter (timeRange, jobID, appName) är ifyllda. Du måste också kontrol lera att du har skapat en app, annars finns det inga data i loggarna.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Arbets böcker för program etablering" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Instrument panel för program etablering" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Anpassade frågor

Du kan skapa anpassade frågor och visa data på Azure-instrumentpaneler. Mer information finns i [skapa och dela instrument paneler för Log Analytics data](../../azure-monitor/logs/get-started-queries.md). Ta också en titt på [Översikt över logg frågor i Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

Här följer några exempel på hur du kommer igång med program etablering.

Fråga loggarna efter en användare baserat på deras ID i käll systemet:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Sammanfattande antal per ErrorCode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Sammanfatta antal händelser per dag enligt åtgärd:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Ta 100 händelser och egenskaper för projekt nyckel:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Anpassade aviseringar

Med Azure Monitor kan du konfigurera anpassade aviseringar så att du får meddelanden om viktiga händelser som rör etablering. Till exempel kanske du vill få en avisering om toppar i fel. Eller så kanske toppar i inaktive ras eller tas bort. Ett annat exempel på var du kanske vill bli aviserad är brist på någon etablering, vilket tyder på att något är fel.

Mer information om aviseringar finns i [svara på händelser med Azure Monitor aviseringar](../../azure-monitor/alerts/tutorial-response.md).

Avisera när det finns en ökning av felen. Ersätt jobID med jobID för ditt program.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Avisera när det finns en ökning av felen." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Det kan finnas ett problem som gjorde att etablerings tjänsten slutade köras. Använd följande avisering för att identifiera när det inte finns några etablerings händelser under ett angivet tidsintervall.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Det kan finnas ett problem som gjorde att etablerings tjänsten slutade köras." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Avisera när det finns en insamling i inaktivitet eller borttagningar.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Avisera när det finns en insamling i inaktivitet eller borttagningar." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Communitybidrag

Vi tar en öppen källkod och community-baserad metod för program etablerings frågor och instrument paneler. Om du har skapat en fråga, en avisering eller en arbets bok som du tror att andra kan vara användbar, måste du publicera den till [AzureMonitorCommunity GitHub-lagrings platsen](https://github.com/microsoft/AzureMonitorCommunity). Ta sedan bort ett e-postmeddelande med en länk. Vi ska granska och publicera den till tjänsten så att andra kan dra nytta av det. Du kan kontakta oss på provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Nästa steg

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Kom igång med frågor i Azure Monitor loggar](../../azure-monitor/logs/get-started-queries.md)
- [Skapa och hantera aviserings grupper i Azure Portal](../../azure-monitor/alerts/action-groups.md)
- [Installera och Använd Log Analytics-vyerna för Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [API för etablerings loggar](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)