---
title: Mallfunktioner
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att hämta värden, arbeta med strängar och siffror och hämta distributions information.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: dbda5c635a5189158424363c341b269d331a5fdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707327"
---
# <a name="arm-template-functions"></a>ARM-mallens funktioner

I den här artikeln beskrivs alla funktioner som du kan använda i en Azure Resource Manager-mall (ARM-mall). Information om hur du använder funktioner i din mall finns i [syntax för mallar](template-expressions.md).

Information om hur du skapar dina egna funktioner finns i [användardefinierade funktioner](template-syntax.md#functions).

De flesta funktioner fungerar på samma sätt när de distribueras till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. Några funktioner kan inte användas i alla omfång. De anges i listorna nedan.

<a id="array" aria-hidden="true"></a>
<a id="concatarray" aria-hidden="true"></a>
<a id="contains" aria-hidden="true"></a>
<a id="createarray" aria-hidden="true"></a>
<a id="empty" aria-hidden="true"></a>
<a id="first" aria-hidden="true"></a>
<a id="intersection" aria-hidden="true"></a>
<a id="last" aria-hidden="true"></a>
<a id="length" aria-hidden="true"></a>
<a id="min" aria-hidden="true"></a>
<a id="max" aria-hidden="true"></a>
<a id="range" aria-hidden="true"></a>
<a id="skip" aria-hidden="true"></a>
<a id="take" aria-hidden="true"></a>
<a id="union" aria-hidden="true"></a>

## <a name="any-function"></a>Alla funktioner

[Funktionen any](template-functions-any.md) är tillgänglig i bicep för att hjälpa till att lösa problem med data typs varningar.

## <a name="array-functions"></a>Matrisfunktioner

Resource Manager innehåller flera funktioner för att arbeta med matriser.

* [matris](template-functions-array.md#array)
* [concat](template-functions-array.md#concat)
* [ingår](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [tomt](template-functions-array.md#empty)
* [förstagångskörningen](template-functions-array.md#first)
* [överlappning](template-functions-array.md#intersection)
* [pågå](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [bekräftat](template-functions-array.md#max)
* [intervall](template-functions-array.md#range)
* [Ignorera](template-functions-array.md#skip)
* [gå](template-functions-array.md#take)
* [Union](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true"></a>
<a id="equals" aria-hidden="true"></a>
<a id="less" aria-hidden="true"></a>
<a id="lessorequals" aria-hidden="true"></a>
<a id="greater" aria-hidden="true"></a>
<a id="greaterorequals" aria-hidden="true"></a>

## <a name="comparison-functions"></a>Jämförelsefunktioner

Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [coalesce](template-functions-comparison.md#coalesce)
* [är lika med](template-functions-comparison.md#equals)
* [minskad](template-functions-comparison.md#less)
* [mindre än eller lika med](template-functions-comparison.md#lessorequals)
* [större än](template-functions-comparison.md#greater)
* [större än eller lika med](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true"></a>
<a id="parameters" aria-hidden="true"></a>
<a id="variables" aria-hidden="true"></a>

## <a name="date-functions"></a>Datumfunktioner

Resource Manager innehåller följande funktioner för att arbeta med datum.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funktioner för distributions värde

Resource Manager innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [spridningen](template-functions-deployment.md#deployment)
* [miljö](template-functions-deployment.md#environment)
* [komponentparametrar](template-functions-deployment.md#parameters)
* [användarvariabler](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true"></a>
<a id="bool" aria-hidden="true"></a>
<a id="if" aria-hidden="true"></a>
<a id="not" aria-hidden="true"></a>
<a id="or" aria-hidden="true"></a>

## <a name="logical-functions"></a>Logiska funktioner

Resource Manager innehåller följande funktioner för att arbeta med logiska villkor:

* [and](template-functions-logical.md#and)
* [boolesk](template-functions-logical.md#bool)
* [!](template-functions-logical.md#false)
* [eventuella](template-functions-logical.md#if)
* [Ogiltigt](template-functions-logical.md#not)
* [eller](template-functions-logical.md#or)
* [värdet](template-functions-logical.md#true)

<a id="add" aria-hidden="true"></a>
<a id="copyindex" aria-hidden="true"></a>
<a id="div" aria-hidden="true"></a>
<a id="float" aria-hidden="true"></a>
<a id="int" aria-hidden="true"></a>
<a id="minint" aria-hidden="true"></a>
<a id="maxint" aria-hidden="true"></a>
<a id="mod" aria-hidden="true"></a>
<a id="mul" aria-hidden="true"></a>
<a id="sub" aria-hidden="true"></a>

## <a name="numeric-functions"></a>Numeriska funktioner

Resource Manager innehåller följande funktioner för att arbeta med heltal:

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [bekräftat](template-functions-numeric.md#max)
* [rest](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Build](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true"></a>

## <a name="object-functions"></a>Objektfunktioner

Resource Manager innehåller flera funktioner för att arbeta med objekt.

* [ingår](template-functions-object.md#contains)
* [createObject](template-functions-object.md#createobject)
* [tomt](template-functions-object.md#empty)
* [överlappning](template-functions-object.md#intersection)
* [utgör](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [null](template-functions-object.md#null)
* [Union](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true"></a>
<a id="listkeys" aria-hidden="true"></a>
<a id="list" aria-hidden="true"></a>
<a id="providers" aria-hidden="true"></a>
<a id="reference" aria-hidden="true"></a>
<a id="resourcegroup" aria-hidden="true"></a>
<a id="resourceid" aria-hidden="true"></a>
<a id="subscription" aria-hidden="true"></a>
<a id="subscriptionResourceId" aria-hidden="true"></a>
<a id="tenantResourceId" aria-hidden="true"></a>

## <a name="resource-functions"></a>Resursfunktioner

Resource Manager tillhandahåller följande funktioner för att hämta resurs värden:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [Listnycklar](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [lista](template-functions-resource.md#list)
* [pickZones](template-functions-resource.md#pickzones)
* [finansiär](template-functions-resource.md#providers)
* [förhållande](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) -kan bara användas i distributioner till en resurs grupp.
* [resourceId](template-functions-resource.md#resourceid) -kan användas i valfri omfattning, men giltiga parametrar ändras beroende på omfattningen.
* [prenumeration](template-functions-resource.md#subscription) – kan bara användas i distributioner till en resurs grupp eller prenumeration.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true"></a>
<a id="base64tojson" aria-hidden="true"></a>
<a id="base64tostring" aria-hidden="true"></a>
<a id="concat" aria-hidden="true"></a>
<a id="containsstring" aria-hidden="true"></a>
<a id="datauri" aria-hidden="true"></a>
<a id="datauritostring" aria-hidden="true"></a>
<a id="emptystring" aria-hidden="true"></a>
<a id="endswith" aria-hidden="true"></a>
<a id="firststring" aria-hidden="true"></a>
<a id="guid" aria-hidden="true"></a>
<a id="indexof" aria-hidden="true"></a>
<a id="laststring" aria-hidden="true"></a>
<a id="lastindexof" aria-hidden="true"></a>
<a id="lengthstring" aria-hidden="true"></a>
<a id="padleft" aria-hidden="true"></a>
<a id="replace" aria-hidden="true"></a>
<a id="skipstring" aria-hidden="true"></a>
<a id="split" aria-hidden="true"></a>
<a id="startswith" aria-hidden="true"></a>
<a id="string" aria-hidden="true"></a>
<a id="substring" aria-hidden="true"></a>
<a id="takestring" aria-hidden="true"></a>
<a id="tolower" aria-hidden="true"></a>
<a id="toupper" aria-hidden="true"></a>
<a id="trim" aria-hidden="true"></a>
<a id="uniquestring" aria-hidden="true"></a>
<a id="uri" aria-hidden="true"></a>
<a id="uricomponent" aria-hidden="true"></a>
<a id="uricomponenttostring" aria-hidden="true"></a>

## <a name="string-functions"></a>Strängfunktioner

Resource Manager innehåller följande funktioner för att arbeta med strängar:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [ingår](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [tomt](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [förstagångskörningen](template-functions-string.md#first)
* [formatering](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [pågå](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [bytt](template-functions-string.md#replace)
* [Ignorera](template-functions-string.md#skip)
* [del](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [sträng](template-functions-string.md#string)
* [under sträng](template-functions-string.md#substring)
* [gå](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [reducera](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade och kapslade mallar när du distribuerar Azure-resurser](linked-templates.md).
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [resurs upprepning i arm-mallar](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
