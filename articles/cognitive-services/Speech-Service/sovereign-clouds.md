---
title: Suveräna moln – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder suveräna moln
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: 63caac7871df9d999ca5a5f1b8c036ae7014231c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98061760"
---
# <a name="speech-services-in-sovereign-clouds"></a>Tal tjänster i suveräna moln

## <a name="azure-government-united-states"></a>Azure Government (USA)

Endast tillgängligt för amerikanska myndigheter och deras partner. Se mer information om Azure Government [här](../../azure-government/documentation-government-welcome.md) [.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Områdena**
  - US Gov, Arizona
  - US Gov, Virginia
- **Tillgängliga pris nivåer:**
  - Kostnads fri (F0) och standard (S0). Se mer information [här](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Funktioner som stöds:**
  - Tal till text
    - Anpassad tal (för ljud modell (AM) och språk modell (LM) anpassning)
      - [Speech Studio](https://speech.azure.us/)
  - Text till tal
    - Standard röst
    - Neurala röst
  - Tal översättare
- **Funktioner som inte stöds:**
  - Anpassad röst
- **Språk som stöds:**
  - Se listan över språk som stöds [här](language-support.md)

### <a name="endpoint-information"></a>Slut punkts information

Det här avsnittet innehåller information om slut punkts information för tal tjänster för användning med [tal-SDK](speech-sdk.md), [tal-till-text-REST API](rest-speech-to-text.md)och [text till tal-REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Tal tjänster REST API

Tal tjänster REST API slut punkter i Azure Government har följande format:

|  REST API typ/åtgärd | Slut punkts format |
|--|--|
| Åtkomsttoken | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Tal till text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Tal till text REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text-till-tal (REST API)](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Ersätt `<REGION_IDENTIFIER>` med den identifierare som matchar regionen för din prenumeration från den här tabellen:

|                     | Regions-ID |
|--|--|
| **US Gov, Arizona**  | `usgovarizona` |
| **US Gov, Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

För tal-SDK i suveräna moln måste du använda en instansiering av `SpeechConfig` klassen eller `--host` alternativet för [tal CLI](spx-overview.md). (Du kan också använda instansiering av "från slut punkt" och `--endpoint` Alternativ för tal CLI).

`SpeechConfig` klassen ska instansieras så här:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Tal-CLI ska användas som detta (Observera `--host` alternativet):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Ersätt `subscriptionKey` med din tal resurs nyckel. Ersätt `usGovHost` med uttrycket som matchar det obligatoriska tjänst erbjudandet och region för din prenumeration från den här tabellen:

|  Region/tjänst erbjudande | Värd uttryck |
|--|--|
| **US Gov, Arizona** | |
| Tal till text | `wss://usgovarizona.stt.speech.azure.us` |
| Text till tal | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov, Virginia** | |
| Tal till text | `wss://usgovvirginia.stt.speech.azure.us` |
| Text till tal | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure Kina

Tillgängligt för organisationer med en affärs närvaro i Kina. Se mer information om Azure Kina [här.](/azure/china/overview-operations) 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Områdena**
  - Kina, östra 2
- **Tillgängliga pris nivåer:**
  - Kostnads fri (F0) och standard (S0). Se mer information [här](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Funktioner som stöds:**
  - Tal till text
    - Anpassad tal (för ljud modell (AM) och språk modell (LM) anpassning)
      - [Speech Studio](https://speech.azure.cn/)
  - Text till tal
    - Standard röst
    - Neurala röst
  - Tal översättare
- **Funktioner som inte stöds:**
  - Anpassad röst
- **Språk som stöds:**
  - Se listan över språk som stöds [här](language-support.md)

### <a name="endpoint-information"></a>Slut punkts information

Det här avsnittet innehåller information om slut punkts information för tal tjänster för användning med [tal-SDK](speech-sdk.md), [tal-till-text-REST API](rest-speech-to-text.md)och [text till tal-REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Tal tjänster REST API

Tal tjänster REST API slut punkter i Azure Kina har följande format:

|  REST API typ/åtgärd | Slut punkts format |
|--|--|
| Åtkomsttoken | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Tal till text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Tal till text REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text-till-tal (REST API)](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Ersätt `<REGION_IDENTIFIER>` med den identifierare som matchar regionen för din prenumeration från den här tabellen:

|                     | Regions-ID |
|--|--|
| **Kina, östra 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech SDK

För tal-SDK i suveräna moln måste du använda en instansiering av `SpeechConfig` klassen eller `--host` alternativet för [tal CLI](spx-overview.md). (Du kan också använda instansiering av "från slut punkt" och `--endpoint` Alternativ för tal CLI).

`SpeechConfig` klassen ska instansieras så här:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Tal-CLI ska användas som detta (Observera `--host` alternativet):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Ersätt `subscriptionKey` med din tal resurs nyckel. Ersätt `azCnHost` med uttrycket som matchar det obligatoriska tjänst erbjudandet och region för din prenumeration från den här tabellen:

|  Region/tjänst erbjudande | Värd uttryck |
|--|--|
| **Kina, östra 2** | |
| Tal till text | `wss://chinaeast2.stt.speech.azure.cn` |
| Text till tal | `https://chinaeast2.tts.speech.azure.cn` |