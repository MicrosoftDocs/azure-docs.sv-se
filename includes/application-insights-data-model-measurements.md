---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187101"
---
Samling med anpassade mått. Använd den här samlingen för att rapportera namngivna mått som är associerade med telemetri-objektet. Typiska användnings fall är:
- storleken på nytto bandets nytto Last
- antalet köobjekt som bearbetas av begär ande telemetri
- tiden kunden tog för att slutföra steget i steg-för-steg-åtgärd för slut för ande av händelser.

Du kan fråga efter [anpassade mått](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) i Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Anpassade mått är associerade med telemetri-objektet som de tillhör. De är föremål för sampling med telemetri-objektet som innehåller dessa mått. Om du vill spåra ett mått som har ett värde som är oberoende av andra typer av telemetri använder du [mått telemetri](../articles/azure-monitor/app/api-custom-events-metrics.md).

Max nyckel längd: 150
