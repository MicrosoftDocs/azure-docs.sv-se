---
title: DatetimeV2 fördefinierade entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller datetimeV2 för inbyggd entitet i Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013079"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2-fördefinierad entitet för en LUIS-app

Den **datetimeV2** fördefinierade entiteten extraherar datum-och tids värden. Dessa värden löses i ett standardiserat format för klient program att använda. När ett uttryck har ett datum eller en tid som inte är fullständig, innehåller LUIS _både tidigare och framtida värden_ i slut punkts svaret. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller datetimeV2 till program avsikterna.

## <a name="types-of-datetimev2"></a>Typer av datetimeV2
DatetimeV2 hanteras från [identifierare-text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) lagringsplatsen.

## <a name="example-json"></a>Exempel-JSON

Följande uttryck och dess partiella JSON-svar visas nedan.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3-svar](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Egenskapsnamn |Egenskaps typ och beskrivning|
|---|---|
|Entitet|**String** -text extraherad från uttryck med typ av datum, tid, datum intervall eller tidsintervall.|
|typ|**sträng** – en av under [typerna för datetimeV2](#subtypes-of-datetimev2)
|Start|**int** – indexet i uttryck som entiteten börjar på.|
|endIndex|**int** – indexet i uttryck som entiteten slutar på.|
|lösning|Har en `values` matris som har ett, två eller fyra [matchnings värden](#values-of-resolution).|
|slut|Slutvärdet för en tid, eller ett datum intervall, i samma format som `value` . Används endast om `type` är `daterange` , `timerange` eller `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Under typer av datetimeV2

Den fördefinierade **datetimeV2** -entiteten har följande under typer och exempel på dem finns i tabellen som följer:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Lösnings värden
* Matrisen har ett-element om datumet eller tiden i uttryck är fullständigt angivna och entydiga.
* Matrisen har två element om datetimeV2-värdet är tvetydigt. Tvetydighet innehåller brist på angivet år, tid eller tidsintervall. Exempel finns i [tvetydiga datum](#ambiguous-dates) . När tiden är tvetydig för förmiddag eller P.M. ingår båda värdena.
* Matrisen har fyra element om uttryck har två element med tvetydighet. Denna tvetydighet innehåller element som har:
  * Ett datum-eller datum intervall som är tvetydigt för år
  * Ett tids-eller tidsintervall som är oklart till A.M. eller P.M. Till exempel 3:00 april tredje.

Varje element i `values` matrisen kan ha följande fält:

|Egenskapsnamn|Beskrivning av egenskap|
|--|--|
|Timex|tid, datum eller datum intervall uttryckt i TIMEX-format som följer [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601) och TIMEX3-attribut för anteckningen med TimeML-språket.|
|rest|term som används för att beskriva hur du använder värdet, till exempel `before` , `after` .|
|typ|Under typen, som kan vara något av följande:,,,,,, `datetime` `date` `time` `daterange` `timerange` `datetimerange` `duration` `set` .|
|värde|**Valfritt.** Ett DateTime-objekt i formatet ÅÅÅÅ-MM-DD (Date), HH: mm: SS (Time) ÅÅÅÅ-MM-dd HH: mm: SS (datetime). Om `type` är `duration` , är värdet antalet sekunder (varaktighet) <br/> Används endast om `type` är `datetime` eller `date` , `time` eller "varaktighet.|

## <a name="valid-date-values"></a>Giltiga datum värden

**DatetimeV2** stöder datum mellan följande intervall:

| Min | Max |
|----------|-------------|
| 1 januari 1900   | 31 december 2099 |

## <a name="ambiguous-dates"></a>Tvetydiga datum

Om datumet kan vara tidigare än eller senare innehåller LUIS båda värdena. Ett exempel är en uttryck som innehåller månaden och datumet utan året.

Till exempel, med följande uttryck:

`May 2nd`

* Om dagens datum är maj tredje 2017 ger LUIS både "2017-05-02" och "2018-05-02" som värden.
* När dagens datum är den 1 maj 2017 ger LUIS både "2016-05-02" och "2017-05-02" som värden.

I följande exempel visas upplösningen för entiteten "2 maj". Den här lösningen förutsätter att dagens datum är ett datum mellan den 2 maj 2017 och den 1 maj 2018.
Fält med `X` i `timex` fältet är delar av det datum som inte uttryckligen anges i uttryck.

## <a name="date-resolution-example"></a>Exempel på datum matchning


Följande uttryck och dess partiella JSON-svar visas nedan.

`May 2nd`

#### <a name="v3-response"></a>[V3-svar](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Matchnings exempel för datum intervall för numeriskt datum

`datetimeV2`Entiteten extraherar datum-och tidsintervall. `start`Fälten och `end` anger början och slutet av intervallet. Luis för uttryck `May 2nd to May 5th` innehåller **DateRange** -värden för både innevarande år och nästa år. Värdena i `timex` fältet `XXXX` visar hur året är tvetydigt. `P3D` anger att tids perioden är tre dagar lång.

Följande uttryck och dess partiella JSON-svar visas nedan.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3-svar](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Exempel på matchnings datum intervall för veckodag

I följande exempel visas hur LUIS använder **datetimeV2** för att lösa uttryck `Tuesday to Thursday` . I det här exemplet är det aktuella datumet juni 19. LUIS innehåller **DateRange** -värden för båda datum intervallen före och efter det aktuella datumet.

Följande uttryck och dess partiella JSON-svar visas nedan.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3-svar](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Tvetydig tid
Matrisen Values har två tids element om tiden eller tidsintervallet är tvetydigt. När det finns en tvetydig tid har värdena både A.M. och P.M. angivelser.

## <a name="time-range-resolution-example"></a>Exempel på tids intervalls matchning

DatetimeV2 JSON-svaret har ändrats i API v3. I följande exempel visas hur LUIS använder **datetimeV2** för att lösa uttryck som har ett tidsintervall.

Ändringar från API v2:
* `datetimeV2.timex.type` Egenskapen returneras inte längre eftersom den returneras på den överordnade nivån `datetimev2.type` .
* `datetimeV2.value`Egenskapen har bytt namn till `datetimeV2.timex` .

Följande uttryck och dess partiella JSON-svar visas nedan.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3-svar](#tab/5-1)

Följande JSON- `verbose` parameter har angetts till `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/5-2)

Följande JSON- `verbose` parameter har angetts till `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Exempel på tids upplösning

Följande uttryck och dess partiella JSON-svar visas nedan.

`8am`

#### <a name="v3-response"></a>[V3-svar](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Föråldrad fördefinierad datetime

Den `datetime` fördefinierade entiteten är inaktuell och ersätts av **datetimeV2**.

`datetime`Utför följande steg för att ersätta med `datetimeV2` i Luis-appen:

1. Öppna fönstret **entiteter** i Luis-webbgränssnittet.
2. Ta bort **datetime** -förbyggda entiteten.
3. Klicka på **Lägg till fördefinierad entitet**
4. Välj **datetimeV2** och klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [dimension](luis-reference-prebuilt-dimension.md), [e-](luis-reference-prebuilt-email.md) postentiteter och [nummer](luis-reference-prebuilt-number.md).

