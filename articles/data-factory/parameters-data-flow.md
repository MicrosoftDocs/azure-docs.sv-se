---
title: Parametrisera mappningsdataflöden
description: Lär dig hur du parameteriserar ett mappningsdataflöde från datafabrikspipelines
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725718"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrisera mappningsdataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mappning av dataflöden i Azure Data Factory Azure Synapse Analytics stöder användning av parametrar. Definiera parametrar i dataflödesdefinitionen och använd dem i dina uttryck. Parametervärdena anges av den anropande pipelinen via aktiviteten Kör Dataflöde kör. Du har tre alternativ för att ange värden i dataflödesaktivitetsuttrycken:

* Använda språket för pipelinekontrollflödesuttryck för att ange ett dynamiskt värde
* Använda språket för dataflödesuttryck för att ange ett dynamiskt värde
* Använd något av uttrycksspråken för att ange ett statiskt literalvärde

Använd den här funktionen för att göra dina dataflöden generella, flexibla och återanvändbara. Du kan parametrisera dataflödesinställningar och uttryck med dessa parametrar.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Skapa parametrar i ett mappningsdataflöde

Om du vill lägga till parametrar i dataflödet klickar du på den tomma delen av dataflödesarbetsytan för att se de allmänna egenskaperna. I inställningsfönstret visas fliken **Parameter**. Välj **Ny** för att generera en ny parameter. För varje parameter måste du tilldela ett namn, välja en typ och eventuellt ange ett standardvärde.

![Skapa Dataflöde parametrar](media/data-flow/create-params.png "Skapa Dataflöde parametrar")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Använda parametrar i ett mappningsdataflöde 

Parametrar kan refereras till i alla dataflödesuttryck. Parametrar börjar med $ och är oföränderliga. Du hittar listan över tillgängliga parametrar i Expression Builder under **fliken** Parametrar.

![Skärmbild som visar tillgängliga parametrar på fliken Parametrar.](media/data-flow/parameter-expression.png "Uttryck för dataflödesparameter")

Du kan snabbt lägga till ytterligare parametrar genom att **välja Ny parameter** och ange namn och typ.

![Skärmbild som visar parametrarna på fliken Parametrar med nya parametrar tillagda.](media/data-flow/new-parameter-expression.png "Uttryck för dataflödesparameter")

## <a name="assign-parameter-values-from-a-pipeline"></a>Tilldela parametervärden från en pipeline

När du har skapat ett dataflöde med parametrar kan du köra det från en pipeline med aktiviteten Kör Dataflöde körning. När du har lagt till aktiviteten på pipelinearbetsytan visas tillgängliga dataflödesparametrar på fliken Parametrar **för** aktiviteten.

När du tilldelar parametervärden kan du använda antingen [språket för pipelineuttryck](control-flow-expression-language-functions.md) eller [dataflödesuttrycket baserat](data-flow-expression-functions.md) på Spark-typer. Varje mappningsdataflöde kan ha valfri kombination av parametrar för pipeline- och dataflödesuttryck.

![Skärmbild som visar fliken Parametrar med Dataflöde valt för värdet för myparam.](media/data-flow/parameter-assign.png "Ange en Dataflöde parameter")

### <a name="pipeline-expression-parameters"></a>Parametrar för pipelineuttryck

Med parametrar för pipelineuttryck kan du referera till systemvariabler, funktioner, pipelineparametrar och variabler som liknar andra pipeline-aktiviteter. När du klickar **på Pipeline-uttryck** öppnas ett sidnavigeringsnavigering där du kan ange ett uttryck med hjälp av uttrycksverktyget.

![Skärmbild som visar uttrycksverktygets fönster.](media/data-flow/parameter-pipeline.png "Ange en Dataflöde parameter")

När du refererar till dem utvärderas pipelineparametrar och sedan används deras värde i uttrycksspråket för dataflöde. Pipelineuttryckstypen behöver inte matcha parametertypen för dataflödet. 

#### <a name="string-literals-vs-expressions"></a>Stränglitteraler jämfört med uttryck

När du tilldelar en parameter för pipelineuttryck av typen sträng läggs standardcitat till och värdet utvärderas som en literal. Om du vill läsa parametervärdet som ett dataflödesuttryck markerar du uttrycksrutan bredvid parametern .

![Skärmbild som visar fönstret DataflödesparametrarUttryck som valts för en parameter.](media/data-flow/string-parameter.png "Ange en Dataflöde parameter")

Om dataflödesparametern `stringParam` refererar till en pipelineparameter med värdet `upper(column1)` . 

- Om uttrycket är markerat `$stringParam` utvärderas värdet för column1 med versaler.
- Om uttrycket inte är markerat (standardbeteende)  `$stringParam` utvärderas till `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Skicka in tidsstämplar

I språket för pipelineuttryck kan systemvariabler som och funktioner som returnera tidsstämplar som strängar i `pipeline().TriggerTime` `utcNow()` formatet "yyyy-MM-dd T HH:mm:ss" i formatet "yyyy-MM-dd \' T \' HH:mm:ss". SSSSSZ". Om du vill konvertera dessa till dataflödesparametrar av typen tidsstämpel använder du stränginterpolation för att inkludera önskad tidsstämpel i en `toTimestamp()` funktion. Om du till exempel vill konvertera pipelinens utlösartid till en dataflödesparameter kan du använda `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Skärmbild som visar fliken Parametrar där du kan ange en utlösartid.](media/data-flow/parameter-timestamp.png "Ange en Dataflöde parameter")

> [!NOTE]
> Dataflöden har endast stöd för upp till 3 millisekunders siffror. Funktionen `left()` används för att trimma ytterligare siffror.

#### <a name="pipeline-parameter-example"></a>Exempel på pipelineparameter

Säg att du har en `intParam` heltalsparameter som refererar till en pipelineparameter av typen String, `@pipeline.parameters.pipelineParam` . 

![Skärmbild som visar fliken Parametrar med parametrarna stringParam och intParam.](media/data-flow/parameter-pipeline-2.png "Ange en Dataflöde parameter")

`@pipeline.parameters.pipelineParam` tilldelas värdet `abs(1)` vid körning.

![Skärmbild som visar fliken Parametrar med värdet för ett b:er (1) valt.](media/data-flow/parameter-pipeline-4.png "Ange en Dataflöde parameter")

När `$intParam` refereras till i ett uttryck som en härledd kolumn utvärderas `abs(1)` returen `1` . 

![Skärmbild som visar kolumnvärdet.](media/data-flow/parameter-pipeline-3.png "Ange en Dataflöde parameter")

### <a name="data-flow-expression-parameters"></a>Parametrar för dataflödesuttryck

Om **du väljer Dataflödesuttryck** öppnas dataflödesuttrycksverktyget. Du kommer att kunna referera till funktioner, andra parametrar och alla definierade schemakolumner i dataflödet. Det här uttrycket utvärderas som det är när det refereras till.

> [!NOTE]
> Om du skickar ett ogiltigt uttryck eller refererar till en schemakolumn som inte finns i transformeringen utvärderas parametern till null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Skicka ett kolumnnamn som en parameter

Ett vanligt mönster är att skicka in ett kolumnnamn som ett parametervärde. Om kolumnen definieras i dataflödesschemat kan du referera till den direkt som ett stränguttryck. Om kolumnen inte har definierats i schemat använder du `byName()` funktionen . Kom ihåg att typutforma kolumnen med en typ av typ som `toString()` .

Om du till exempel vill mappa en strängkolumn baserat på en parameter kan du lägga till en härledd `columnName` kolumnomvandling som är lika med `toString(byName($columnName))` .

![Skicka ett kolumnnamn som en parameter](media/data-flow/parameterize-column-name.png "Skicka ett kolumnnamn som en parameter")

## <a name="next-steps"></a>Nästa steg
* [Köra dataflödesaktivitet](control-flow-execute-data-flow-activity.md)
* [Kontrollflödesuttryck](control-flow-expression-language-functions.md)
