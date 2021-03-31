---
title: Avancerad filtrering – Azure Event Grid IoT Edge | Microsoft Docs
description: Avancerad filtrering i Event Grid på IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001056"
---
# <a name="advanced-filtering"></a>Avancerad filtrering
Med Event Grid kan du ange filter för alla egenskaper i JSON-nyttolasten. Dessa filter modelleras som `AND` en uppsättning villkor, med varje yttre villkor som har valfria inre `OR` villkor. För varje `AND` villkor anger du följande värden:

* `OperatorType` – Typen av jämförelse.
* `Key` – JSON-sökvägen till egenskapen som filtret ska tillämpas på.
* `Value` – Referensvärdet som filtret körs mot (eller) `Values` – den uppsättning referens värden som filtret körs mot.

## <a name="json-syntax"></a>JSON-syntax

JSON-syntaxen för ett avancerat filter är följande:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtrering av mat ris värden

Event Grid stöder inte filtrering på en matris med värden idag. Om en inkommande händelse har ett mat ris värde för nyckeln för avancerad filter, Miss lyckas motsvarande åtgärd. Den inkommande händelsen slutar matcha inte med händelse prenumerationen.

## <a name="and-or-not-semantics"></a>OCH-inte semantiskt

Observera att i JSON-exemplet ovan `AdvancedFilters` är en matris. Tänk på varje `AdvancedFilter` mat ris element som ett `AND` villkor.

För operatörer som har stöd för flera värden (till exempel `NumberIn` ,, `NumberNotIn` `StringIn` osv.) behandlas varje värde som ett `OR` villkor. En kommer att `StringBeginsWith("a", "b", "c")` matcha alla sträng värden som börjar med antingen `a` eller `b` eller `c` .

> [!CAUTION]
> Operatorerna NOT- `NumberNotIn` och `StringNotIn` fungerar som-och-villkor för varje värde som anges i `Values` fältet.
>
> Om du inte gör det blir filtret ett Accept-All filtrerat och manipulations syftet med filtreringen.

## <a name="floating-point-rounding-behavior"></a>Avrundat beteende för svävande punkt

Event Grid använder `decimal` .net-typen för att hantera alla numeriska värden. De numeriska värden som anges i JSON för händelse prenumerationen omfattas inte av beteendet för flytt ALS avrundning.

## <a name="case-sensitivity-of-string-filters"></a>Skift läges känslighet för sträng filter

Alla sträng jämförelser är Skift läges känsliga. Det finns inget sätt att ändra det här beteendet idag.

## <a name="allowed-advanced-filter-keys"></a>Tillåtna avancerade filter nycklar

`Key`Egenskapen kan antingen vara en välkänd egenskap på den översta nivån eller vara en JSON-sökväg med flera punkter, där varje punkt visar steg i ett kapslat JSON-objekt.

Event Grid har ingen särskild betydelse för `$` tecknen i nyckeln, till skillnad från JSONPath-specifikationen.

### <a name="event-grid-schema"></a>Event Grid-schema

För händelser i Event Grid schemat:

* ID
* Avsnitt
* Ämne
* Typ
* DataVersion
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Anpassat händelse schema

Det finns ingen begränsning för `Key` i det anpassade händelse schemat eftersom Event Grid inte tillämpar något kuvert schema på nytto lasten.

## <a name="numeric-single-value-filter-examples"></a>Numeriska filter exempel med flera värden

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Exempel på numeriskt intervall värde filter

* Numberin
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Sträng intervall – exempel på värde filter

* StringContains
* StringBeginsWith
* StringEndsWith
* Strängin
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Booleska exempel på ett värde filter

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
