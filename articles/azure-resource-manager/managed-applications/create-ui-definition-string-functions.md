---
title: Skapa UI definitions sträng funktioner
description: Beskriver de sträng funktioner som används när du konstruerar GRÄNSSNITTs definitioner för Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099912"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition-sträng funktioner

Dessa funktioner används med JSON-strängar.

## <a name="concat"></a>concat

Sammanfogar en eller flera strängar.

Om till exempel utdata för `element1` IF `"Contoso"` , returnerar det här exemplet strängen `"Demo Contoso app"` :

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Anger om en sträng slutar med ett värde.

Följande exempel returnerar true.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Genererar en globalt unik sträng (GUID).

Följande exempel returnerar ett värde som `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Returnerar den första positionen för ett värde i en sträng eller-1 om det inte hittas.

I följande exempel returneras 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Returnerar den sista positionen för ett värde i en sträng eller-1 om det inte går att hitta.

Följande exempel returnerar 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>bytt

Returnerar en sträng i vilken alla förekomster av den angivna strängen i den aktuella strängen ersätts med en annan sträng.

Följande exempel returnerar `"Contoso.com web app"` :

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Anger om en sträng börjar med ett värde.

Följande exempel returnerar true.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>under sträng

Returnerar del strängen för den angivna strängen. Under strängen börjar vid det angivna indexet och har den angivna längden.

Följande exempel returnerar `"web"` :

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Returnerar en sträng som har konverterats till gemener.

Följande exempel returnerar `"contoso"` :

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Returnerar en sträng konverterad till versaler.

Följande exempel returnerar `"CONTOSO"` :

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).

