---
title: Språk stöd – API för textanalys
titleSuffix: Azure Cognitive Services
description: En lista över naturliga språk som stöds av API för textanalys. I den här artikeln förklaras vilka språk som stöds för varje åtgärd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: 9e257209060396fbf45a4a24c38bc6950acb5168
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387034"
---
# <a name="text-analytics-api-v3-language-support"></a>Språk stöd för API för textanalys v3 

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment-analysis)

| Språk              | Språkkod | v3-stöd | Startar v3-modell version: |              Kommentarer |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` även godkänd |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Engelska               |     `en`      |     ✓      |         2019-10-01         |                    |
| Franska                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Tyska                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Italienska               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japanska              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreanska                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norska (bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` även godkänd |
| Spanska               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turkiska               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Utåsikts utvinning (v 3.1 – endast för hands version)

| Språk              | Språkkod | Från och med v3 modell version: |              Kommentarer |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engelska               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Igenkänning av namngivna enheter (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Endast entiteter "person", "plats" och "organisation" returneras för språk som marker ATS med *.

| Språk               | Språkkod | v3-stöd | Från och med v3 modell version: |       Kommentarer        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabiska                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` även godkänd |
| Chinese-Traditional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Tjeckiska                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Danska                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Nederländska                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Engelska                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finska               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Franska                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Tyska                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebreiska                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Ungerska             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italienska               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japanska              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreanska                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norska (bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` även godkänd |
| Polska                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` även godkänd |
| Ryska              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spanska               |     `es`      |     ✓       |               2020-04-01        |                    |
| Svenska               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turkiska               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Extrahering av nyckel fraser](#tab/key-phrase-extraction)

| Språk              | Språkkod |  v3-stöd | Tillgängligt från och med v3-modell version: |       Kommentarer        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Danska                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Nederländska                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Engelska               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Finska               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Franska                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Tyska                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Italienska               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japanska              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Koreanska                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norska (bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` även godkänd |
| Polska                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugisiska (Portugal) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` även godkänd |
| Ryska               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Spanska               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Svenska               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Länkning av entitet](#tab/entity-linking)

| Språk | Språkkod |  v3-stöd | Tillgängligt från och med v3-modell version: | Kommentarer |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Engelska  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spanska  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Personligt identifierbar information (PII)](#tab/pii)

| Språk               | Språkkod | v3-stöd | Från och med v3 modell version: |       Kommentarer        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` även godkänd |
| Engelska                |     `en`      |     ✓      |               2020-07-01        |                    |
| Franska                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Tyska                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italienska               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japanska              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreanska                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` även godkänd |
| Spanska               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language-detection)

API för textanalys kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk och returnera identifierade språk med namn och kod. Textanalys Språkidentifiering språk kods parametrarna uppfyller [BCP-47-](https://tools.ietf.org/html/bcp47) standarden med de flesta av dem som överensstämmer med [ISO-639-1-](https://www.iso.org/iso-639-language-codes.html) identifierare. 

Om du har innehåll som uttrycks på ett mindre vanligt språk kan du prova Språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown` .

| Språk | Språkkod | v3-stöd | Tillgängligt från och med v3-modell version: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanska|`sq`|✓|    |
|Amhariska|`am`|✓|2021-01-05|
|Arabiska|`ar`|✓|    |
|Armeniska|`hy`|✓|    |
|Assamesiska|`as`|✓|2021-01-05|
|Azerbajdzjanska|`az`|✓|2021-01-05|
|Baskiska|`eu`|✓|    |
|Vitryska|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bosniska|`bs`|✓|2020-09-01|
|Bulgariska|`bg`|✓|    |
|Burmesiska|`my`|✓|    |
|Katalanska|`ca`|✓|    |
|Centrala kambodjanska|`km`|✓|    |
|Kinesiska|`zh`|✓|    |
|Kinesiska, förenklad|`zh_chs`|✓|    |
|Kinesiska, traditionell|`zh_cht`|✓|    |
|Korsikanska|`co`|✓|2021-01-05|
|Kroatiska|`hr`|✓|    |
|Tjeckiska|`cs`|✓|    |
|Danska|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Nederländska|`nl`|✓|    |
|Engelska|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estniska|`et`|✓|    |
|Fijian|`fj`|✓|2020-09-01|
|Finska|`fi`|✓|    |
|Franska|`fr`|✓|    |
|Galiciska|`gl`|✓|    |
|Georgiska|`ka`|✓|    |
|Tyska|`de`|✓|    |
|Grekiska|`el`|✓|    |
|Gujarati|`gu`|✓|    |
|Haitian|`ht`|✓|    |
|Haus|`ha`|✓|2021-01-05|
|Hebreiska|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Ungerska|`hu`|✓|    |
|Isländska|`is`|✓|    |
|IBO|`ig`|✓|2021-01-05|
|Indonesiska|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Iriska|`ga`|✓|    |
|Italienska|`it`|✓|    |
|Japanska|`ja`|✓|    |
|Javanesiska|`jv`|✓|2021-01-05|
|Kannada|`kn`|✓|    |
|Kazakiska|`kk`|✓|2020-09-01|
|Rwanda|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Koreanska|`ko`|✓|    |
|Kurdisk|`ku`|✓|    |
|Laos|`lo`|✓|    |
|Latin|`la`|✓|    |
|Lettiska|`lv`|✓|    |
|Litauiska|`lt`|✓|    |
|Luxemburgiska|`lb`|✓|2021-01-05|
|Makedonska|`mk`|✓|    |
|Madagaskisk|`mg`|✓|2020-09-01|
|Malajiska|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltesiska|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Mongoliska|`mn`|✓|2021-01-05|
|Nepal|`ne`|✓|2021-01-05|
|Norska|`no`|✓|    |
|Norska (nynorsk)|`nn`|✓|    |
|Odia|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Persiska|`fa`|✓|    |
|Polska|`pl`|✓|    |
|Portugisiska|`pt`|✓|    |
|Punjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Rumänska|`ro`|✓|    |
|Ryska|`ru`|✓|    |
|Samoan|`sm`|✓|2020-09-01|
|Serbiska|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Sinhala|`si`|✓|    |
|Slovakiska|`sk`|✓|    |
|Slovenska|`sl`|✓|    |
|Somaliska|`so`|✓|    |
|Spanska|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Swahili|`sw`|✓|    |
|Svenska|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitian|`ty`|✓|2020-09-01|
|Tadzjikiska|`tg`|✓|2021-01-05|
|Tamilska|`ta`|✓|    |
|Tatariska|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Thailändska|`th`|✓|    |
|Tibetanska|`bo`|✓|2021-01-05|
|Tigrinja|`ti`|✓|2021-01-05|
|Tonganska|`to`|✓|2020-09-01|
|Turkiska|`tr`|✓|2021-01-05|
|Turkmeniska|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Yoruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Se även

* [Vad är API för textanalys?](overview.md)   
