---
title: Avisera om problem i Azure Cloud Services att använda Azure-diagnostik-integrering med Azure Application insikter | Microsoft Docs
description: Övervaka för problem som startfel, krascher och åter användning av roll i Azure Cloud Services med Azure Application insikter
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309298"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Avisera om problem i Azure Cloud Services med Azure Diagnostics-integrering med Azure Application Insights

I den här artikeln beskriver vi hur du ställer in aviserings regler som övervakar för problem som startfel, krascher och återkallning av roller i Azure Cloud Services (webb-och arbets roller).

Metoden som beskrivs i den här artikeln är baserad på [Azure-diagnostik-integrering med Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)och de nyligen utgivna [logg aviseringarna för Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) funktioner.

## <a name="define-a-base-query"></a>Definiera en bas fråga

För att komma igång definierar vi en bas fråga som hämtar Windows händelse logg händelser från Windows Azure-kanalen, som samlas in i Application Insights som spårnings poster.
Dessa poster kan användas för att identifiera olika problem i Azure Cloud Services, t. ex. startfel, körnings fel och åter användnings slingor.

> [!NOTE]
> Bas frågan nedan kontrollerar om det finns problem i ett tids fönster på 30 minuter och en svars tid på 10 minuter för att mata in telemetri-poster. Dessa standardinställningar kan konfigureras när du ser anpassa.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Sök efter angivna händelse-ID: n

När du har hämtat Windows händelse logg händelser kan du identifiera specifika problem genom att kontrol lera deras respektive händelse-ID och meddelande egenskaper (se exemplen nedan).
Kombinera bara bas frågan ovan med en av frågorna nedan och Använd den kombinerade frågan när du definierar logg varnings regeln.

> [!NOTE]
> I exemplen nedan identifieras ett problem om fler än tre händelser påträffas under den analyserade tids perioden. Standardvärdet kan konfigureras för att ändra känslighet för varnings regeln.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Skapa en avisering

I navigerings menyn i Application Insights resursen går du till **aviseringar** och väljer sedan **ny aviserings regel**.

![Skärm bild av Skapa regel](./media/proactive-cloud-services/001.png)

I fönstret **Skapa regel** , under avsnittet **definiera aviserings villkor** , klickar du på **Lägg till kriterier** och väljer sedan **anpassad loggs ökning**.

![Skärm bild av definiera villkors villkor för avisering](./media/proactive-cloud-services/002.png)

I rutan **Sök fråga** klistrar du in den kombinerade fråga som du för beredde i föregående steg.

Fortsätt sedan till rutan **tröskelvärde** och ange värdet till 0. Du kan eventuellt justera **fälten** **period** och frekvens.
Klicka på **Klar**.

![Skärm bild av fråga om att konfigurera signal logik](./media/proactive-cloud-services/003.png)

Under avsnittet **definiera aviserings information** anger du ett **namn** och en **Beskrivning** för varnings regeln och anger dess **allvarlighets grad**.
Kontrol lera också att knappen **Aktivera regel vid skapande** har angetts till **Ja**.

![Aviserings information om skärm bild](./media/proactive-cloud-services/004.png)

Under avsnittet **definiera åtgärds grupp** kan du välja en befintlig **Åtgärds grupp** eller skapa en ny.
Du kan välja att låta åtgärds gruppen innehålla flera olika typer av åtgärder.

![Åtgärds grupp för skärm bild](./media/proactive-cloud-services/005.png)

När du har definierat åtgärds gruppen bekräftar du ändringarna och klickar på **skapa aviserings regel**.

## <a name="next-steps"></a>Nästa steg

Läs mer om att identifiera automatiskt:

[Fel avvikelser](./proactive-failure-diagnostics.md) 
 [Minnes läckor](./proactive-potential-memory-leak.md) 
 [Prestanda avvikelser](./proactive-performance-diagnostics.md)

