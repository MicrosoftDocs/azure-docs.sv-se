---
title: Systemvariabler i Azure Data Factory
description: I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: fc6b2e4c944394d811abc19f70aeb34a0ae3c9a4
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127676"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.

## <a name="pipeline-scope"></a>Pipeline-omfång
Dessa systemvariabler kan refereras var som helst i pipeline-JSON.

| Variabelnamn | Description |
| --- | --- |
| @pipeline(). DataFactory |Namnet på den data fabrik som pipelinen körs i |
| @pipeline(). Pipeline |Namn på pipelinen |
| @pipeline(). RunId |ID för den aktuella pipeline-körningen |
| @pipeline(). TriggerType |Typen av utlösare som anropade pipelinen (till exempel `ScheduleTrigger` , `BlobEventsTrigger` ). En lista över utlösnings typer som stöds finns [i pipeline-körning och utlösare i Azure Data Factory](concepts-pipeline-execution-triggers.md). Utlösaren `Manual` anger att pipelinen utlöstes manuellt. |
| @pipeline(). TriggerId|ID för utlösaren som anropade pipelinen |
| @pipeline(). TriggerName|Namnet på utlösaren som anropade pipelinen |
| @pipeline(). TriggerTime|Tiden för den Utlös ande körning som anropade pipelinen. Detta är tiden då utlösaren **faktiskt** startade för att anropa pipelinen och kan skilja sig något från den schemalagda tiden för utlösaren.  |

>[!NOTE]
>Utlös ande datum/tid system variabler (i både pipeliner och utlösnings omfång) returnera UTC-datum i ISO 8601-format, till exempel `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Intervall för schema utlösare
Dessa systemvariabler kan refereras var som helst i Utlösar-JSON för utlösare av typen [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Variabelnamn | Description |
| --- | --- |
| @trigger().scheduledTime |Tiden då utlösaren schemalades för att anropa pipeline-körningen. |
| @trigger(). StartTime |Tiden då utlösaren **faktiskt** startade för att anropa pipeline-körningen. Detta kan skilja sig något från den schemalagda tiden för utlösaren. |

## <a name="tumbling-window-trigger-scope"></a>Utlösare för rullande fönster
Dessa systemvariabler kan refereras var som helst i Utlösar-JSON för utlösare av typen [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Variabelnamn | Description |
| --- | --- |
| @trigger(). outputs. windowStartTime |Början av fönstret som är associerat med utlösarens körning. |
| @trigger(). outputs. windowEndTime |Slutet av fönstret som är kopplat till utlösarens körning. |
| @trigger().scheduledTime |Tiden då utlösaren schemalades för att anropa pipeline-körningen. |
| @trigger(). StartTime |Tiden då utlösaren **faktiskt** startade för att anropa pipeline-körningen. Detta kan skilja sig något från den schemalagda tiden för utlösaren. |

## <a name="event-based-trigger-scope"></a>Händelsebaserade utlösnings omfång
Dessa systemvariabler kan refereras var som helst i Utlösar-JSON för utlösare av typen [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Variabelnamn | Description |
| --- | --- |
| @triggerBody(). fileName  |Namnet på filen vars skapande eller borttagning orsakade utlösaren för brand.   |
| @triggerBody(). mappnamn  |Sökväg till den mapp som innehåller filen som anges av `@triggerBody().fileName` . Det första segmentet i mappsökvägen är namnet på Azure Blob Storage-behållaren.  |
| @trigger(). StartTime |Tiden då utlösaren startade för att anropa pipeline-körningen. |

## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns i [Expression language & Functions](control-flow-expression-language-functions.md).
