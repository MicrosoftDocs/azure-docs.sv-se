---
title: Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java
description: Så här konfigurerar du telemetri-processorer i Azure Monitor Application Insights för Java
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7fd53c77b64e028ffad25c8fa7a9eefd95439513
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387164"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetri-processorer (för hands version) – Azure Monitor Application Insights för Java

> [!NOTE]
> Den här funktionen är fortfarande under för hands version.

Java 3,0-agenten för Application Insights har nu funktioner för att bearbeta telemetridata innan data exporteras.

### <a name="some-use-cases"></a>Några användnings fall:
 * Maskera känsliga data
 * Lägg till anpassade dimensioner villkorligt
 * Uppdatera telemetri-namnet som används för agg regering och visning

### <a name="supported-processors"></a>Processorer som stöds:
 * Attribut processor
 * Spänn processor

## <a name="to-get-started"></a>För att komma igång

Skapa en konfigurations fil med namnet `applicationinsights.json` och placera den i samma katalog som `applicationinsights-agent-***.jar` , med följande mall.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Inkludera/exkludera spänner

Attributet processor och span-processorn exponerar alternativet att tillhandahålla en uppsättning egenskaper för ett intervall att matcha mot, för att fastställa om intervallet ska tas med eller undantas från processorn. För att konfigurera det här alternativet under `include` och/eller `exclude` minst ett `matchType` och ett av `spanNames` eller `attributes` krävs. Konfigurationen inkludera/exkludera stöds för att ha mer än ett angivet villkor. Alla angivna villkor måste utvärderas till sant för att en matchning ska inträffa. 

**Obligatoriskt fält**: 
* `matchType` styr hur objekt i `spanNames` och `attributes` matriser tolkas. Möjliga värden är `regexp` eller `strict`. 

**Valfria fält**: 
* `spanNames` måste matcha minst ett av objekten. 
* `attributes` anger listan över attribut som ska matchas mot. Alla dessa attribut måste matcha exakt för att en matchning ska ske.

> [!NOTE]
> Om både `include` och `exclude` anges, `include` kontrol leras egenskaperna innan `exclude` egenskaperna.

#### <a name="sample-usage"></a>Exempel användning

Följande visar hur du anger en uppsättning span-egenskaper som anger vilka som omfattar den här processorn som ska tillämpas på. `include`Egenskaperna anger vilka som ska tas med och `exclude` egenskaperna filtrerar bort ytterligare över intervall som inte ska bearbetas.

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
            "svcA",
            "svcB"
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

Med ovanstående konfiguration matchar följande intervall egenskaperna och processor åtgärderna tillämpas:

* Span1 namn: svcB-attribut: {multimiljö: produktion, test_request: 123, credit_card: 1234, redact_trace: "falskt"}

* Span2 namn: ' svcA ' attribut: {kuvert: mellanlagring, test_request: falskt, redact_trace: true}

Följande sträcker matchar inte inkluderings-egenskaperna och processor åtgärderna tillämpas inte:

* Span3 namn: svcB-attribut: {multimiljö: produktion, test_request: true, credit_card: 1234, redact_trace: falskt}

* Span4-namn: ' svcC ' attribut: {multimiljö: dev, test_request: false}

## <a name="attribute-processor"></a>Attribut processor 

Attributens processor ändrar attributen för ett intervall. Det kan också användas för att inkludera/exkludera intervall.
Den tar en lista med åtgärder som utförs i den ordning som anges i konfigurations filen. De åtgärder som stöds är:

* `insert` : Infogar ett nytt attribut i intervall där nyckeln inte redan finns
* `update` : Uppdaterar ett attribut i intervall där nyckeln finns
* `delete` : Tar bort ett attribut från ett intervall
* `hash`   : Hash-värden (SHA1) ett befintligt attributvärde

För åtgärder `insert` och `update`
* `key` krävs
* en av `value` eller `fromAttribute` krävs
* `action` måste anges.

För `delete` åtgärden,
* `key` krävs
* `action`: `delete` krävs.

För `hash` åtgärden,
* `key` krävs
* `action` : `hash` krävs.

Listan med åtgärder kan sammanställas för att skapa avancerade scenarier, till exempel attribut för att fylla på, kopiera värden till en ny nyckel, bortredigering känslig information.

#### <a name="sample-usage"></a>Exempel användning

I följande exempel visas hur du infogar nycklar/värden i sträcker:

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
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

I följande exempel visas hur du konfigurerar processorn så att den bara uppdaterar befintliga nycklar i ett attribut:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
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

## <a name="span-processors"></a>Intervall processorer

Spänn processorn ändrar antingen intervall namnet eller attributen för ett intervall baserat på intervall namnet. Det kan också användas för att inkludera/exkludera intervall.

### <a name="name-a-span"></a>Namnge ett intervall

Följande inställning krävs som en del av avsnittet Name:

* `fromAttributes`: Attributvärdet för nycklarna används för att skapa ett nytt namn i den ordning som anges i konfigurationen. Alla attributändringar måste anges i intervallet för att processorn ska kunna byta namn på den.

Du kan konfigurera följande inställningar:

* `separator`: En sträng som anges kommer att användas för att dela värden
> [!NOTE]
> Om namnbytet är beroende av attribut som ändras av attributets processor, se till att intervallet processor anges efter attributets processor i pipeline-specifikationen.

#### <a name="sample-usage"></a>Exempel användning

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

Använder en lista över reguljära uttryck för att matcha intervall namn mot och extrahera attribut från det baserat på under uttryck. Måste anges under `toAttributes` avsnittet.

Följande inställningar krävs:

`rules` : En lista med regler för att extrahera attributvärden från intervall namn. Värdena i intervall namnet ersätts av extraherade attributnamn. Varje regel i listan är regex-mönster sträng. Intervall namnet kontrol leras mot regex. Om regex matchar extraheras alla namngivna under uttryck i regex som attribut och läggs till i intervallet. Varje under uttrycks namn blir ett attributnamn och en matchad del av under uttryck blir attributvärdet. Den matchade delen i intervall namnet ersätts av ett extraherat attributnamn. Om attributen redan finns i intervallet kommer de att skrivas över. Processen upprepas för alla regler i den ordning som de anges. Varje efterföljande regel fungerar på det intervall namn som är resultatet när föregående regel har bearbetats.

#### <a name="sample-usage"></a>Exempel användning

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