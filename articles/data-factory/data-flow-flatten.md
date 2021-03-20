---
title: Förenkla omvandling i data flöde för mappning
description: Normalisera hierarkiska data med hjälp av den sammanslagna omvandlingen
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "81413678"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Förenkla omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd den sammanslagna omvandlingen för att ta mat ris värden i hierarkiska strukturer, till exempel JSON och avregistrera dem i enskilda rader. Den här processen kallas för denormalisering.

## <a name="configuration"></a>Konfiguration

Den sammanslagna omvandlingen innehåller följande konfigurations inställningar

![Förenklings inställningar](media/data-flow/flatten1.png "Förenklings inställningar")

### <a name="unroll-by"></a>Avregistrera av

Välj en matris som ska avregistreras. Utdata kommer att ha en rad per objekt i varje matris. Om den avregistrera efter matrisen i inmatnings raden är null eller tom, finns det en rad matning med värden som är null.

### <a name="unroll-root"></a>Avregistrera rot

Som standard registrerar den sammanslagna omvandlingen en matris längst upp i hierarkin som den finns i. Du kan också välja en matris som avregistrerad rot. Den avåterställda roten måste vara en matris med komplexa objekt som antingen är eller innehåller en avregistrering av matrisen. Om du väljer en avrullnings rot innehåller utdata minst en rad per objekt i den avbildade roten. Om inmatnings raden inte har några objekt i den expanderade roten, tas den bort från utdata. Om du väljer en avställnings rot skrivs alltid ett mindre än eller lika många rader med standard beteendet.

### <a name="flatten-mapping"></a>Förenkla mappning

På samma sätt som Välj omvandling väljer du projektion av den nya strukturen från inkommande fält och den avnormaliserade matrisen. Om en denormaliserad matris mappas blir kolumnen utdata samma datatyp som matrisen. Om den avbildas efter matris är en matris med komplexa objekt som innehåller undermatriser, kommer mappningen av ett objekt i subarry att generera en matris.

Kontrol lera dina mappnings resultat på fliken Granska och för hands versionen av data.

## <a name="examples"></a>Exempel

Se följande JSON-objekt för följande exempel på den sammanslagna omvandlingen

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Ingen Avregistrerings rot med sträng mat ris

| Avregistrera av | Avregistrera rot | Projektion |
| --------- | ----------- | ---------- |
| varor. kunder | Inget | name <br> kund = varor. kund |

#### <a name="output"></a>Utdata

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Ingen Avregistrerings rot med komplex matris

| Avregistrera av | Avregistrera rot | Projektion |
| --------- | ----------- | ---------- |
| varor. order. levererat. orderItems | Inget | name <br> Ordernr = varor. order. ordernr <br> itemName = varor. order. levererat. orderItems. itemName <br> itemQty = varor. Orders. levererat. orderItems. itemQty <br> plats = plats |

#### <a name="output"></a>Utdata

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Samma rot som en Avregistrerings mat ris

| Avregistrera av | Avregistrera rot | Projektion |
| --------- | ----------- | ---------- |
| varor. order | varor. order | name <br> varor. order. levererat. orderItems. itemName <br> varor. kunder <br> location |

#### <a name="output"></a>Utdata

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Avregistrera rot med komplex matris

| Avregistrera av | Avregistrera rot | Projektion |
| --------- | ----------- | ---------- |
| varor. order. levererat. orderItem | varor. order |name <br> Ordernr = varor. order. ordernr <br> itemName = varor. order. levererat. orderItems. itemName <br> itemQty = varor. Orders. levererat. orderItems. itemQty <br> plats = plats |

#### <a name="output"></a>Utdata

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exempel

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Nästa steg

* Använd [Pivot-transformeringen](data-flow-pivot.md) för att pivotera rader till kolumner.
* Använd [unpivot-transformeringen](data-flow-unpivot.md) för att pivotera kolumner till rader.
