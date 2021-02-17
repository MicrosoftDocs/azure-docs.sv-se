---
title: 'Uppgradera till den aktuella Azure Monitor logg aviserings-API: et'
description: Lär dig hur du växlar till logg aviseringar ScheduledQueryRules-API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 55f433786ed9dd40b08bb64395a6bbc50800add4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623027"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Uppgradera till den aktuella logg aviserings-API: n från äldre Log Analytics aviserings-API

> [!NOTE]
> Den här artikeln är endast relevant för Azures offentliga (**inte** till Azure Government eller Azure Kina-molnet).

> [!NOTE]
> När en användare väljer att växla inställningen till det aktuella [scheduledQueryRules-API: et](/rest/api/monitor/scheduledqueryrules) går det inte att återgå till den äldre [äldre Log Analytics varnings-API: et](../platform/api-alerts.md).

Tidigare använde användare den [äldre Log Analytics aviserings-API: et](../platform/api-alerts.md) för att hantera logg aviserings regler. Aktuella arbets ytor använder [ScheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules). I den här artikeln beskrivs fördelarna och processen för att växla från det äldre API: t till det aktuella API: et.

## <a name="benefits"></a>Fördelar

- En mall för att skapa aviserings regler (tidigare behövde tre separata mallar).
- Enkelt API för både Log Analytics arbets ytor eller Application Insights resurser.
- [Stöd för PowerShell-cmdletar](../platform/alerts-log.md#managing-log-alerts-using-powershell).
- Justering av allvarlighets grader med alla andra aviserings typer.
- Möjlighet att skapa en [logg avisering mellan flera arbets ytor](../log-query/cross-workspace-query.md) som omfattar flera externa resurser som Log Analytics arbets ytor eller Application Insights resurser.
- Användare kan ange dimensioner för att dela upp aviseringar med hjälp av parametern "agg regering".
- Logg aviseringar har en utökad period på upp till två dagars data (tidigare än en dag).

## <a name="impact"></a>Påverkan

- Alla nya regler måste skapas/redige ras med aktuellt API. Se [exempel användning via Azure-resurs mal len](alerts-log-create-templates.md) och [exempel användning via PowerShell](../platform/alerts-log.md#managing-log-alerts-using-powershell).
- Eftersom regler blir Azure Resource Manager spårade resurser i det aktuella API: et och måste vara unika, kommer regel resurs-ID att ändras till den här strukturen: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Visnings namnen för aviserings regeln förblir oförändrade.

## <a name="process"></a>Process

Processen för att växla är inte interaktiv och kräver inte manuella steg, i de flesta fall. Aviserings reglerna stoppas eller stoppas inte, under eller efter växeln.
Det här anropet för att växla alla aviserings regler som är kopplade till den aktuella Log Analytics arbets ytan:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Med begär ande texten som innehåller nedanstående JSON:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Här är ett exempel på hur du använder [ARMClient](https://github.com/projectkudu/ARMClient), ett kommando rads verktyg med öppen källkod som fören klar anropet av API-anropet ovan:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Om växeln lyckas är svaret:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Kontrol lera växlings status för arbets ytan

Du kan också använda det här API-anropet för att kontrol lera växlar status:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Du kan också använda [ARMClient](https://github.com/projectkudu/ARMClient) -verktyget:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om Log Analytics-arbetsytan växlades till [scheduledQueryRules-API: et](/rest/api/monitor/scheduledqueryrules)är svaret:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Om Log Analytics arbets ytan inte växlades är svaret:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Monitor-logg aviseringar](../platform/alerts-unified-log.md).
- Lär dig hur du [hanterar dina logg aviseringar med hjälp av API: et](alerts-log-create-templates.md).
- Lär dig hur du [hanterar logg aviseringar med hjälp av PowerShell](../platform/alerts-log.md#managing-log-alerts-using-powershell).
- Lär dig mer om [Azures aviserings upplevelse](../platform/alerts-overview.md).
