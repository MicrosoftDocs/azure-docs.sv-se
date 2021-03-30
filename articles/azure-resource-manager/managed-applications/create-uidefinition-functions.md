---
title: Skapa funktioner för UI-definition
description: Beskriver de funktioner som används när du konstruerar GRÄNSSNITTs definitioner för Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040987"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition-funktioner

Den här artikeln innehåller en översikt över de funktioner som stöds för CreateUiDefinition.

## <a name="function-syntax"></a>Syntax för funktion

Om du vill använda en funktion omger du anropet med hakparenteser. Exempel:

```json
"[function()]"
```

Strängar och andra funktioner kan refereras till som parametrar för en funktion, men strängarna måste omges av enkla citat tecken. Exempel:

```json
"[fn1(fn2(), 'demo text')]"
```

I förekommande fall kan du referera till egenskaperna för resultatet av en funktion med hjälp av punkt operatorn. Exempel:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Samlingsfunktioner

Dessa funktioner kan användas med samlingar, t. ex. JSON-strängar, matriser och objekt.

* [ingår](create-ui-definition-collection-functions.md#contains)
* [tomt](create-ui-definition-collection-functions.md#empty)
* [Synkroniseringsfilter](create-ui-definition-collection-functions.md#filter)
* [förstagångskörningen](create-ui-definition-collection-functions.md#first)
* [pågå](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [Mappa](create-ui-definition-collection-functions.md#map)
* [Ignorera](create-ui-definition-collection-functions.md#skip)
* [del](create-ui-definition-collection-functions.md#split)
* [gå](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Konverteringsfunktioner

Dessa funktioner kan användas för att konvertera värden mellan JSON-datatyper och-kodningar.

* [boolesk](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [sträng](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Datumfunktioner

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Logiska funktioner

Dessa funktioner kan användas i villkor. Vissa funktioner kanske inte stöder alla typer av JSON-data.

* [and](create-ui-definition-logical-functions.md#and)
* [coalesce](create-ui-definition-logical-functions.md#coalesce)
* [är lika med](create-ui-definition-logical-functions.md#equals)
* [större än](create-ui-definition-logical-functions.md#greater)
* [större än eller lika med](create-ui-definition-logical-functions.md#greaterorequals)
* [eventuella](create-ui-definition-logical-functions.md#if)
* [minskad](create-ui-definition-logical-functions.md#less)
* [mindre än eller lika med](create-ui-definition-logical-functions.md#lessorequals)
* [Ogiltigt](create-ui-definition-logical-functions.md#not)
* [eller](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Matematiska funktioner

* [add](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [maximal](create-ui-definition-math-functions.md#floor)
* [bekräftat](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [rest](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [slump](create-ui-definition-math-functions.md#rand)
* [intervall](create-ui-definition-math-functions.md#range)
* [Build](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Referensfunktioner

* [om](create-ui-definition-referencing-functions.md#basics)
* [sökvägen](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [sätt](create-ui-definition-referencing-functions.md#steps)
* [Prenumerera](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Strängfunktioner

* [concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [bytt](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [under sträng](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).
