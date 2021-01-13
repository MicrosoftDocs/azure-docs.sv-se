---
title: Exempel på telemetri processorer – Azure Monitor Application Insights för Java
description: Exempel som illustrerar telemetri-processorer i Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146463"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Exempel på telemetri processorer – Azure Monitor Application Insights för Java

## <a name="includeexclude-samples"></a>Inkludera/exkludera exempel

### <a name="1-include-spans"></a>1. inkludera spänner

I följande exempel visas intervallen för attributen processor. Alla andra intervall som inte matchar egenskaperna bearbetas inte av den här processorn.

Följande villkor uppfylls för en matchning:
* Intervall namnet måste vara lika med "spana" eller "spanB" 

Följande är en span som matchar inkluderings-egenskaperna och processor åtgärderna tillämpas.
* Span1 namn: ' spana ' attribut: {kuvert: dev, test_request: 123, credit_card: 1234}
* Span2-namn: ' spanB ' attribut: {multimiljö: dev, test_request: false}
* Span3 namn: ' spana ' attribut: {kuvert: 1, test_request: dev, credit_card: 1234}

Följande omfång matchar inte de inkluderande egenskaperna och processor åtgärderna tillämpas inte.
* Span4-namn: ' spanC ' attribut: {multimiljö: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. exkludera spänner

Följande visar exkluderade intervall för den här attributens processor. Alla intervall som matchar egenskaperna bearbetas inte av den här processorn.

Följande villkor uppfylls för en matchning:
* Intervall namnet måste vara lika med "spana" eller "spanB" 

Följande är en span som matchar exkluderings egenskaperna och processor åtgärderna tillämpas inte.
* Span1 namn: ' spana ' attribut: {kuvert: dev, test_request: 123, credit_card: 1234}
* Span2-namn: ' spanB ' attribut: {multimiljö: dev, test_request: false}
* Span3 namn: ' spana ' attribut: {kuvert: 1, test_request: dev, credit_card: 1234}

Följande omfång stämmer inte överens med exkluderings egenskaperna och processor åtgärderna tillämpas.
* Span4-namn: ' spanC ' attribut: {multimiljö: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti-intervall

Följande visar exkluderade intervall för den här attributens processor. Alla intervall som matchar egenskaperna bearbetas inte av den här processorn.

Följande villkor uppfylls för en matchning:
* Ett attribut ("kuvert", "dev") måste finnas i intervallet för en matchning.
* Så länge det finns ett attribut med nyckeln test_request i intervallet finns det en matchning.

Följande är en span som matchar exkluderings egenskaperna och processor åtgärderna tillämpas inte.
* Span1 namn: spanB-attribut: {multi: dev, test_request: 123, credit_card: 1234}
* Span2 namn: ' spana ' attribut: {kuvert: dev, test_request: false}

Följande omfång stämmer inte överens med exkluderings egenskaperna och processor åtgärderna tillämpas.
* Span3 namn: ' spanB ' attribut: {kuvert: 1, test_request: dev, credit_card: 1234}
* Span4-namn: ' spanC ' attribut: {multimiljö: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. selektiv bearbetning

Följande visar hur du anger en uppsättning span-egenskaper som anger vilka som omfattar den här processorn som ska tillämpas på. `include`Egenskaperna anger vilka som ska tas med och `exclude` egenskaperna filtrerar bort ytterligare över intervall som inte ska bearbetas.

Med konfigurationen nedan matchar följande intervall egenskaperna och processor åtgärderna tillämpas:

* Span1 namn: spanB-attribut: {multimiljö: produktion, test_request: 123, credit_card: 1234, redact_trace: "falskt"}
* Span2 namn: ' spana ' attribut: {kuvert: mellanlagring, test_request: falskt, redact_trace: true}

Följande sträcker matchar inte inkluderings-egenskaperna och processor åtgärderna tillämpas inte:
* Span3 namn: spanB-attribut: {multimiljö: produktion, test_request: true, credit_card: 1234, redact_trace: falskt}
* Span4-namn: ' spanC ' attribut: {multimiljö: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Exempel på attribut processor

### <a name="insert"></a>Infogning

Följande infogar ett nytt attribut {"Attribute1": "attributeValue1"} som sträcker sig över var nyckeln "Attribute1" inte finns.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Infoga från en annan nyckel

Följande använder värdet från attributet "anotherkey" för att infoga ett nytt attribut {"newKey": "värde från attributet" anotherkey "} till intervall där nyckeln" newKey "inte finns. Om attributet anotherkey inte finns infogas inget nytt attribut i spans.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Uppdatera

Följande uppdaterar attributet till {"DB. Secret": "förbortredigeringed"} och uppdaterar attributet artavbildningsfil med värdet från attributet foo. Sträcker sig över utan attributet "artavbildningsfil" ändras inte.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Ta bort

Följande visar hur du tar bort attribut med nyckeln credit_card.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hash

Följande visar hash-värden för befintliga attribut.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extrahera

I följande exempel demonstreras användningen av regex för att skapa nya attribut baserat på värdet för ett annat attribut.
Till exempel har http. URL = http://example.com/path?queryParam1=value1 , queryParam2 = värde2 följande attribut kommer att infogas:
* httpProtocol: http
* httpDomain: example.com
* httpPath: sökväg
* httpQueryParams: queryParam1 = värde1, queryParam2 = värde2
* värdet http. URL ändras inte.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

I följande exempel visas hur du bearbetar intervall som har ett intervall namn som matchar regexp-mönster.
Den här processorn tar bort attributet "token" och kommer att obfuscate "Password"-attributet i intervall där intervall namn matchar "auth \* ". och där intervall namnet inte matchar "login \* ".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Exempel på intervall processor

### <a name="name-a-span"></a>Namnge ett intervall

I följande exempel anges värdena för attributet "DB. svc", "operation" och "ID" för att skapa det nya namnet på intervallet, i den ordningen, åtskiljda med värdet "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Extrahera attribut från intervall namn

Låt oss anta att namnet på det angivna omfånget är/API/v1/Document/12345678/Update. Genom att använda följande resultat i utmatnings intervallets namn/api/v1/document/{documentId}/update läggs ett nytt attribut "documentId" = "12345678" till intervallet.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Extrahera attribut från intervall namn med inkludera och exkludera

Följande visar hur du byter namn på intervall namnet till {operation_website} och lägger till attributet {Key: operation_website, value: oldSpanName} när intervallet har följande egenskaper:
- Intervall namnet innehåller/var som helst i strängen.
- Intervall namnet är inte ' donot/Change '.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```