---
title: Lägga till taggar i digital twins
titleSuffix: Azure Digital Twins
description: Se hur du implementerar taggar på digitala tvillingar
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376723"
---
# <a name="add-tags-to-digital-twins"></a>Lägga till taggar i digital twins 

Du kan använda begreppet taggar för att ytterligare identifiera och kategorisera dina digitala tvillingpar. I synnerhet kanske användarna vill replikera taggar från befintliga system, till exempel [Taggstack](https://project-haystack.org/doc/TagModel), inom sina Azure Digital Twins instanser. 

Det här dokumentet beskriver mönster som kan användas för att implementera taggar på digitala tvillingar.

Taggar läggs först till som egenskaper i modellen [som](concepts-models.md) beskriver en digital tvilling. Egenskapen anges sedan för tvillingen när den skapas baserat på modellen. Därefter kan taggarna användas i frågor [för att](concepts-query-language.md) identifiera och filtrera dina tvillingar.

## <a name="marker-tags"></a>Markörtaggar 

En **markörtagg** är en enkel sträng som används för att markera eller kategorisera en digital tvilling, till exempel "blå" eller "röd". Den här strängen är taggens namn och markörtaggar har inget meningsfullt värde – taggen är viktig bara av dess närvaro (eller frånvaro). 

### <a name="add-marker-tags-to-model"></a>Lägga till markörtaggar i modellen 

Markörtaggar modelleras som en [DTDL-karta](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) från `string` till `boolean` . Det `mapValue` booleska ignoreras eftersom förekomsten av taggen är allt som är viktigt. 

Här är ett utdrag från en tvillingmodell som implementerar en markörtagg som en egenskap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Lägga till markörtaggar i digitala tvillingar

När egenskapen är en del av en digital tvillings modell kan du ange markörtaggen i den digitala tvillingen genom att `tags` ange värdet för den här egenskapen. 

Här är ett exempel som fyller i markören `tags` för tre tvillingar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Här är ett kodexempel på hur du anger markören för `tags` en tvilling med hjälp av [.NET SDK:](/dotnet/api/overview/azure/digitaltwins/client)

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Fråga med markörtaggar

När taggarna har lagts till i digitaltvillingarna kan taggarna användas för att filtrera tvillingarna i frågor. 

Här är en fråga för att hämta alla tvillingar som har taggats som "röda": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Du kan också kombinera taggar för mer komplexa frågor. Här är en fråga för att hämta alla tvillingar som är runda och inte röda: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Värdetaggar 

En **värdetagg** är ett nyckel/värde-par som används för att ge varje tagg ett värde, till exempel `"color": "blue"` eller `"color": "red"` . När en värdetagg har skapats kan den också användas som en markörtagg genom att ignorera taggens värde. 

### <a name="add-value-tags-to-model"></a>Lägga till värdetaggar i modellen 

Värdetaggar modelleras som en [DTDL-karta](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) från `string` till `string` . Både och `mapKey` är `mapValue` viktiga. 

Här är ett utdrag från en tvillingmodell som implementerar en värdetagg som en egenskap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Lägga till värdetaggar i digital twins

Precis som med markörtaggar kan du ange värdetaggen i en digital tvilling genom att ange värdet för `tags` den här egenskapen från modellen. Om du vill använda en värdetagg som en markörtagg kan du ange `tagValue` fältet till det tomma strängvärdet ( `""` ). 

Här är ett exempel som fyller i värdet `tags` för tre tvillingar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Observera att `red` och `purple` används som markörtaggar i det här exemplet.

### <a name="query-with-value-tags"></a>Fråga med värdetaggar

Precis som med markörtaggar kan du använda värdetaggar för att filtrera tvillingarna i frågor. Du kan också använda värdetaggar och markörtaggar tillsammans.

I exemplet ovan `red` används som en markörtagg. Kom ihåg att det här är en fråga för att hämta alla tvillingar som har taggats som "röda": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Här är en fråga för att hämta alla entiteter som är små (värdetagg) och inte röda: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Nästa steg

Läs mer om att utforma och hantera digitala tvillingmodeller:
* [*Så här hanterar du DTDL-modeller*](how-to-manage-model.md)

Läs mer om att köra frågor mot tvillingdiagrammet:
* [*Så här frågar du tvillingdiagrammet*](how-to-query-graph.md)
