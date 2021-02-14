---
title: Skapa rullande Window trigger-beroenden
description: Lär dig hur du skapar beroende för en rullande fönster utlösare i Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361476"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Skapa ett beroende för utlösare för rullande fönster
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller steg för att skapa ett beroende på en utlösare för rullande fönster. Allmän information om utlösare för rullande fönster finns i [så här skapar du utlösare för rullande Window](how-to-create-tumbling-window-trigger.md).

För att skapa en beroende kedja och se till att en utlösare körs endast när en annan utlösare har körts i data fabriken, använder du den här avancerade funktionen för att skapa ett rullande fönster beroende.

En demonstration om hur du skapar beroende pipelines i Azure Data Factory med utlösare för rullande fönster finns på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Skapa ett beroende i Data Factory gränssnittet

Om du vill skapa ett beroende på en utlösare väljer du **Utlösare > avancerad > nytt** och väljer sedan utlösaren så att den är beroende av lämplig förskjutning och storlek. Välj **Slutför** och publicera data Factory-ändringarna för att beroendena ska börja gälla.

![Beroende skapande](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Beroende skapande")

## <a name="tumbling-window-dependency-properties"></a>Egenskaper för rullande Window-beroende

En utlösare för rullande fönster med ett beroende har följande egenskaper:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Följande tabell innehåller en lista med attribut som behövs för att definiera ett rullande fönster-beroende.

| **Egenskapens namn** | **Beskrivning**  | **Typ** | **Obligatoriskt** |
|---|---|---|---|
| typ  | Alla befintliga rullande fönster-utlösare visas i den här List rutan. Välj utlösaren att ta beroende av.  | TumblingWindowTriggerDependencyReference eller SelfDependencyTumblingWindowTriggerReference | Ja |
| offset | Förskjutning av beroende utlösare. Ange ett värde i intervall format och både negativa och positiva förskjutningar tillåts. Den här egenskapen är obligatorisk om utlösaren är beroende av sig själv och i alla andra fall är den valfri. Self-Dependency måste alltid vara en negativ förskjutning. Om inget värde anges är fönstret detsamma som själva utlösaren. | Tidsintervall<br/>(hh: mm: SS) | Själv-beroende: Ja<br/>Övrigt: Nej |
| ikoner | Storlek på fönstret beroende rullande. Ange ett positivt TimeSpan-värde. Den här egenskapen är valfri. | Tidsintervall<br/>(hh: mm: SS) | Inga  |

> [!NOTE]
> En utlösare för rullande fönster kan vara beroende av högst fem andra utlösare.

## <a name="tumbling-window-self-dependency-properties"></a>Rullande-fönster egenskaper för Self-Dependency

I scenarier där utlösaren inte ska fortsätta till nästa fönster förrän föregående fönster har slutförts, skapar du ett självtestat. En självberoende utlösare som är beroende av att de tidigare körningarna av sig själv inom den gångna timmen har de egenskaper som anges i följande kod.

> [!NOTE]
> Om den utlösta pipelinen är beroende av utdata från pipelines i tidigare utlösta fönster rekommenderar vi att du endast använder rullande Window trigger Self-Dependency. Om du vill begränsa parallell utlösare, anger du maximimum-utlösaren samtidighet.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Användnings scenarier och exempel

Nedan visas illustrationer av scenarier och användning av rullande-fönster egenskaper för beroende.

### <a name="dependency-offset"></a>Beroende förskjutning

![Förskjutnings exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Förskjutnings exempel")

### <a name="dependency-size"></a>Beroende storlek

![Storleks exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Storleks exempel")

### <a name="self-dependency"></a>Själv beroende

![Själv beroende](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Själv beroende")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Beroende av en annan utlösare för rullande fönster

En daglig telemetri bearbetnings jobb beroende på ett annat dagligt jobb som sammanställer de senaste sju dagarna och genererar löpande fönster i sju dagar:

![Beroende exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Beroende exempel")

### <a name="dependency-on-itself"></a>Beroende av sig själv

Ett dagligt jobb utan luckor i jobbets utdata:

![Självbetjänings exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Självbetjänings exempel")

## <a name="monitor-dependencies"></a>Övervaka beroenden

Du kan övervaka beroende kedjan och motsvarande fönster från sidan utlösare för att köra övervakning. Gå till  **övervakning > trigger-körningar**. Om en utlösare för rullande fönster har beroenden kommer Utlösarens namn att innehålla en hyperlänk till vyn över beroende övervakning.  

![Övervaka utlösarkörningar](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Övervaka Utlösare körs – hela rullande fönster beroende vy")

Klicka dig igenom Utlösarens namn för att Visa utlösare beroenden. Den högra panelen visar detaljerad körnings information för utlösare, till exempel RunID, fönster tid, status och så vidare.

![Övervaka listvy för beroenden](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Övervaka listvy för beroenden")

Du kan se status för beroenden och Windows för varje beroende utlösare. Om en av beroende utlösare Miss lyckas måste du köra om den för att den beroende utlösaren ska köras.

En utlösare för rullande fönster väntar på beroenden i _sju dagar_ innan tids gränsen uppnåddes. Efter sju dagar går det inte att köra utlösaren.

Om du vill visa ett mer visuellt objekt för utlösare för beroende väljer du vyn Gantt-schema.

![Övervaka beroende Gantt-diagram](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Övervaka beroende Gantt chart View")

Transparenta rutor visar beroende fönster för varje nedströms beroende utlösare, medan fyllda färgade rutor ovan visar att enskilda fönster körs. Här följer några tips för att tolka vyn Gantt-diagram:

* Den transparenta rutan återges blått när beroende fönster är i väntande eller kör tillstånd
* När alla fönster har slutförts för en beroende utlösare blir den transparenta rutan grön
* Den transparenta rutan återges röd när vissa beroende fönster Miss lyckas. Leta efter en fylld röd ruta för att identifiera körnings fönstret

Om du vill köra ett fönster i Gantt-schemat igen, väljer du rutan heldragen färg för fönstret och en åtgärds panel visas med information och kör om alternativ

## <a name="next-steps"></a>Nästa steg

* Läs [om hur du skapar en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md)
