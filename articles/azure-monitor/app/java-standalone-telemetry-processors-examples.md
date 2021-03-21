---
title: Telemetri processor exempel – Azure Monitor Application Insights för Java
description: Utforska exempel som visar telemetri-processorer i Azure Monitor Application Insights för Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734595"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Telemetri processor exempel – Azure Monitor Application Insights för Java

Den här artikeln innehåller exempel på telemetri-processorer i Application Insights för Java. Du hittar exempel för att inkludera och exkludera konfigurationer. Du hittar också exempel för attribut processorer och intervall processorer.
## <a name="include-and-exclude-samples"></a>Inkludera och exkludera exempel

I det här avsnittet får du se hur du tar med och exkluderar omfång. Du kommer också att se hur du utesluter flera intervall och använder selektiv bearbetning.
### <a name="include-spans"></a>Inkludera spänner

Det här avsnittet visar hur du inkluderar spanar för en attribut processor. Intervall som inte matchar egenskaperna bearbetas inte av processorn.

En matchning kräver att intervall namnet är lika med `spanA` eller `spanB` . 

Dessa omfattar matchning av include-egenskaperna och processor åtgärderna tillämpas:
* Span1 namn: ' spana ' attribut: {kuvert: dev, test_request: 123, credit_card: 1234}
* Span2-namn: ' spanB ' attribut: {multimiljö: dev, test_request: false}
* Span3 namn: ' spana ' attribut: {kuvert: 1, test_request: dev, credit_card: 1234}

Det här intervallet matchar inte include-egenskaperna och processor åtgärderna tillämpas inte:
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

### <a name="exclude-spans"></a>Exkludera spänner

Det här avsnittet visar hur du utesluter intervall för en attribut processor. Intervall som matchar egenskaperna bearbetas inte av den här processorn.

En matchning kräver att intervall namnet är lika med `spanA` eller `spanB` .

Följande spänner matchar exkluderings egenskaperna och processor åtgärderna tillämpas inte:
* Span1 namn: ' spana ' attribut: {kuvert: dev, test_request: 123, credit_card: 1234}
* Span2-namn: ' spanB ' attribut: {multimiljö: dev, test_request: false}
* Span3 namn: ' spana ' attribut: {kuvert: 1, test_request: dev, credit_card: 1234}

Det här intervallet matchar inte exkluderings egenskaperna och processor åtgärderna tillämpas:
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

### <a name="exclude-spans-by-using-multiple-criteria"></a>Uteslut spanar genom att använda flera villkor

Det här avsnittet visar hur du utesluter intervall för en attribut processor. Intervall som matchar egenskaperna bearbetas inte av den här processorn.

En matchning kräver att följande villkor uppfylls:
* Ett attribut (till exempel `env` eller `dev` ) måste finnas i intervallet.
* Intervallet måste ha ett attribut som har en nyckel `test_request` .

Följande spänner matchar exkluderings egenskaperna och processor åtgärderna tillämpas inte.
* Span1 namn: spanB-attribut: {multi: dev, test_request: 123, credit_card: 1234}
* Span2 namn: ' spana ' attribut: {kuvert: dev, test_request: false}

Följande omfång stämmer inte överens med exkluderings egenskaperna och processor åtgärderna tillämpas:
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

### <a name="selective-processing"></a>Selektiv bearbetning

I det här avsnittet visas hur du anger den uppsättning span-egenskaper som anger vilka som omfattar den här processorn som ska tillämpas på. Include-egenskaperna anger vilka intervall som ska bearbetas. Exkluderings egenskaperna filtrerar ut intervall som inte ska bearbetas.

I följande konfiguration sträcker sig dessa över egenskaperna och processor åtgärderna tillämpas:

* Span1 namn: spanB-attribut: {multimiljö: produktion, test_request: 123, credit_card: 1234, redact_trace: "falskt"}
* Span2 namn: ' spana ' attribut: {kuvert: mellanlagring, test_request: falskt, redact_trace: true}

Dessa sträcker matchar inte include-egenskaperna och processor åtgärderna används inte:
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

Följande exempel infogar det nya attributet `{"attribute1": "attributeValue1"}` i intervall där nyckeln `attribute1` inte finns.

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

I följande exempel används värdet från attributet `anotherkey` för att infoga det nya attributet `{"newKey": "<value from attribute anotherkey>"}` i intervall där nyckeln `newKey` inte finns. Om attributet `anotherkey` inte finns infogas inget nytt attribut i intervall.

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

I följande exempel uppdateras attributet till `{"db.secret": "redacted"}` . Attributet uppdateras `boo` med hjälp av attributet Value från `foo` . Sträcker sig över att attributet `boo` inte ändras.

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

I följande exempel visas hur du tar bort ett attribut som har nyckeln `credit_card` .

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

I följande exempel visas hur du hashar befintliga attributvärden.

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

Följande exempel visar hur du använder ett reguljärt uttryck (regex) för att skapa nya attribut baserat på värdet för ett annat attribut.
Till exempel anges `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` följande attribut:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *ingen* ändring

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

I följande exempel visas hur du bearbetar intervall som har ett intervall namn som matchar regex-mönster.
Den här processorn tar bort `token` attributet. Det obfuscates `password` attributet i intervall där intervall namnet matchar `auth.*` och var intervall namnet inte matchar `login.*` .

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

I följande exempel anges värdena för attribut `db.svc` , `operation` och `id` . Den bildar det nya namnet på intervallet genom att använda dessa attribut, i den ordningen, separerade med värdet `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Extrahera attribut från ett intervall namn

Låt oss anta att namnet på det angivna intervallet är `/api/v1/document/12345678/update` . Följande exempel resulterar i namnet på utmatnings intervallet `/api/v1/document/{documentId}/update` . Det lägger till det nya attributet `documentId=12345678` i intervallet.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Extrahera attribut från ett intervall namn med hjälp av include och exclude

I följande exempel visas hur du ändrar intervall namnet till `{operation_website}` . Det lägger till ett attribut med nyckel `operation_website` och värde `{oldSpanName}` när intervallet har följande egenskaper:
- Intervall namnet innehåller `/` var som helst i strängen.
- Intervall namnet är inte `donot/change` .
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
