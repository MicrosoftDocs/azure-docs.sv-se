---
title: Lägg till taggar till digitala dubbla
titleSuffix: Azure Digital Twins
description: Se hur du implementerar taggar på digitala dubbla
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9a1a55bdf21b74116450ca32f66d891f1aa206d3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045418"
---
# <a name="add-tags-to-digital-twins"></a>Lägg till taggar till digitala dubbla 

Du kan använda begreppet taggar för att ytterligare identifiera och kategorisera digitala dubbla. I synnerhet kanske användarna vill replikera taggar från befintliga system, till exempel [Haystack-Taggar](https://project-haystack.org/doc/TagModel), inom sina Azures digitala dubbla instanser. 

Det här dokumentet beskriver mönster som kan användas för att implementera taggar på digitala dubbla.

Taggar läggs först till som egenskaper i [modellen](concepts-models.md) som beskriver en digital, dubbel. Den egenskapen ställs sedan in på den dubbla när den skapas baserat på modellen. Sedan kan taggarna användas i [frågor](concepts-query-language.md) för att identifiera och filtrera dina dubbla.

## <a name="marker-tags"></a>Markör Taggar 

En **märkpenn-tagg** är en enkel sträng som används för att markera eller kategorisera en digital, t. ex. "blå" eller "röd". Den här strängen är taggens namn och markör taggarna har inget meningsfullt värde – taggen är signifikant, precis som dess förekomst (eller frånvaro). 

### <a name="add-marker-tags-to-model"></a>Lägg till markör Taggar i modell 

Markör taggar är modellerade som en [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -karta från `string` till `boolean` . Det booleska värdet `mapValue` ignoreras eftersom förekomsten av taggen är allt det är viktigt. 

Här är ett utdrag från en dubbel modell som implementerar en märkpenn-tagg som en egenskap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Lägg till markör Taggar i digitala dubbla

När `tags` egenskapen är en del av en digital-enhets modell kan du ställa in markören i den digitala dubbla genom att ange värdet för den här egenskapen. 

Här är ett exempel som fyller markören `tags` för tre dubbla:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Fråga med markör Taggar

När Taggar har lagts till i digitala dubbla, kan taggarna användas för att filtrera de dubbla i frågorna. 

Här är en fråga för att hämta alla dubbla som har taggats som "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Du kan också kombinera taggar för mer komplexa frågor. Här är en fråga för att hämta alla dubbla som är rundade och inte röda: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Värde koder 

En **Value-tagg** är ett nyckel/värde-par som används för att ge varje tagg ett värde, till exempel `"color": "blue"` eller `"color": "red"` . När en Value-tagg har skapats kan den också användas som en markör-tagg genom att ignorera taggens värde. 

### <a name="add-value-tags-to-model"></a>Lägg till värde Taggar i modellen 

Värde koderna modelleras som en [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -karta från `string` till `string` . Både `mapKey` och `mapValue` är viktiga. 

Här är ett utdrag från en dubbel modell som implementerar en Value-tagg som en egenskap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Lägg till värde Taggar i digitala dubbla

Precis som med markör taggar kan du ange värde tag gen i ett digitalt värde genom att ange värdet för den här `tags` egenskapen från modellen. Om du vill använda en värdekod som en markör etikett kan du ange `tagValue` fältet till det tomma sträng värdet ( `""` ). 

Här är ett exempel som fyller i värdet `tags` för tre dubbla:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Observera att `red` och `purple` används som markör Taggar i det här exemplet.

### <a name="query-with-value-tags"></a>Fråga med värde Taggar

Precis som med markör taggar kan du använda värde taggar för att filtrera de dubbla i frågorna. Du kan också använda värde Taggar och markör Taggar tillsammans.

I exemplet ovan `red` används som en märkpenn-tagg. Kom ihåg att det här är en fråga för att hämta alla dubbla som har taggats som "Red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Här är en fråga för att hämta alla entiteter som är små (värde tag gen) och inte rött: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Nästa steg

Läs mer om att utforma och hantera digitala dubbla modeller:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)

Läs mer om att skicka frågor till den dubbla grafen:
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)