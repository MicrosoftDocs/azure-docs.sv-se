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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: a0cd968c90a27d6f1ae79d08fb2bd027169233db
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133794"
---
# <a name="text-analytics-api-v3-language-support"></a>Språk stöd för API för textanalys v3 

#### <a name="sentiment-analysis"></a>[Attitydanalys](#tab/sentiment-analysis)

| Språk              | Språkkod | v2-stöd | v3-stöd | Startar v3-modell version: |              Kommentarer |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` även godkänd |
| Chinese-Traditional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Danska               |     `da`      |     ✓      |            |                            |                    |
| Nederländska                 |     `nl`      |     ✓      |            |                            |                    |
| Engelska               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finska               |     `fi`      |     ✓      |            |                            |                    |
| Franska                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Tyska                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grekiska                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italienska               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japanska              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreanska                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norska (bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polska                |     `pl`      |     ✓      |            |                            |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` även godkänd |
| Ryska               |     `ru`      |     ✓      |            |                            |                    |
| Spanska               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svenska               |     `sv`      |     ✓      |            |                            |                    |
| Turkiska               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Utåsikts utvinning (v 3.1 – endast för hands version)

| Språk              | Språkkod | Från och med v3 modell version: |              Kommentarer |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engelska               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Igenkänning av namngivna enheter (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 stöder för närvarande bara engelska och spanska språk. Om du anropar NER v3 med ett annat språk returneras v 2.1-API: et, förutsatt att språket stöds i version 2,1.
> * v 2.1 returnerar bara den fullständiga uppsättningen tillgängliga entiteter för de engelska, kinesiska, enkla, franska, tyska och spanska språken.  Entiteterna "person", "plats" och "organisation" returneras för de andra språk som stöds.

| Språk               | Språkkod | v 2.1-stöd | v3-stöd | Från och med v3 modell version: |       Kommentarer        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabiska                |     `ar`      |     ✓      |            |                                 |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |            |                                 | `zh` även godkänd |
| Chinese-Traditional   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Tjeckiska                 |     `cs`      |     ✓      |            |                                 |                    |
| Danska                |     `da`      |     ✓      |            |                                 |                    |
| Nederländska                 |     `nl`      |     ✓      |            |                                 |                    |
| Engelska                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finska               |     `fi`      |     ✓      |            |                                 |                    |
| Franska                 |     `fr`      |     ✓      |            |                                 |                    |
| Tyska                 |     `de`      |     ✓      |            |                                 |                    |
| Hebreiska                |     `he`      |     ✓      |            |                                 |                    |
| Ungerska             |     `hu`      |     ✓      |            |                                 |                    |
| Italienska               |     `it`      |     ✓      |            |                                 |                    |
| Japanska              |     `ja`      |     ✓      |            |                                 |                    |
| Koreanska                |     `ko`      |     ✓      |            |                                 |                    |
| Norska (bokmål)   |     `no`      |     ✓      |            |                                 | `nb` även godkänd |
| Polska                |     `pl`      |     ✓      |            |                                 |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` även godkänd |
| Ryska              |     `ru`      |     ✓      |            |                                 |                    |
| Spanska               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Svenska               |     `sv`      |     ✓      |            |                                 |                    |
| Turkiska               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extrahering av nyckel fraser](#tab/key-phrase-extraction)

| Språk              | Språkkod | v2-stöd | v3-stöd | Tillgängligt från och med v3-modell version: |       Kommentarer        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Danska                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Nederländska                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Engelska               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finska               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Franska                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Tyska                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italienska               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japanska              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreanska                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norska (bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` även godkänd |
| Polska                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugisiska (Brasilien)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugisiska (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` även godkänd |
| Ryska               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spanska               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Svenska               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Länkning av entitet](#tab/entity-linking)

| Språk | Språkkod | v2-stöd | v3-stöd | Tillgängligt från och med v3-modell version: | Kommentarer |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Engelska  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spanska  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Språkidentifiering](#tab/language-detection)

API för textanalys kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk och returnera identifierade språk med namn och kod. Textanalys Språkidentifiering språk kods parametrarna uppfyller [BCP-47-](https://tools.ietf.org/html/bcp47) standarden med de flesta av dem som överensstämmer med [ISO-639-1-](https://www.iso.org/iso-639-language-codes.html) identifierare. 

Om du har innehåll som uttrycks på ett mindre vanligt språk kan du prova Språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown` .

| Språk | Språkkod | v3-stöd | Tillgängligt från och med v3-modell version: |
|:-|:-:|:-:|:-:|
| Afrikaans | `af` | ✓ |  |
| Albanska | `sq` | ✓ |  |
| Arabiska | `ar` | ✓ |  |
| Armeniska | `hy` | ✓ |  |
| Baskiska | `eu` | ✓ |  |
| Vitryska | `be` | ✓ |  |
| Bengali | `bn` | ✓ |  |
| Bosniska | `bs` | ✓ | 2020-09-01 |
| Bulgariska | `bg` | ✓ |  |
| Burmesiska | `my` | ✓ |  |
| Katalanska, valencianska | `ca` | ✓ |  |
| Centrala kambodjanska | `km` | ✓ |  |
| Kinesiska | `zh` | ✓ |  |
| Kinesiska, förenklad | `zh_chs` | ✓ |  |
| Kinesiska, traditionell | `zh_cht` | ✓ |  |
| Kroatiska | `hr` | ✓ |  |
| Tjeckiska | `cs` | ✓ |  |
| Danska | `da` | ✓ |  |
| Dari | `prs` | ✓ | 2020-09-01 |
| Divehi, Dhivehi, Maldivian | `dv` | ✓ |  |
| Holländska, flamländska | `nl` | ✓ |  |
| Engelska | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Estniska | `et` | ✓ |  |
| Fijian | `fj` | ✓ | 2020-09-01 |
| Finska | `fi` | ✓ |  |
| Franska | `fr` | ✓ |  |
| Galiciska | `gl` | ✓ |  |
| Georgiska | `ka` | ✓ |  |
| Tyska | `de` | ✓ |  |
| Grekiska | `el` | ✓ |  |
| Gujarati | `gu` | ✓ |  |
| Haitian, Haitian Creole | `ht` | ✓ |  |
| Hebreiska | `he` | ✓ |  |
| Hindi | `hi` | ✓ |  |
| Hmong Daw | `mww` | ✓ | 2020-09-01 |
| Ungerska | `hu` | ✓ |  |
| Isländska | `is` | ✓ |  |
| Indonesiska | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Iriska | `ga` | ✓ |  |
| Italienska | `it` | ✓ |  |
| Japanska | `ja` | ✓ |  |
| Kannada | `kn` | ✓ |  |
| Kazakiska | `kk` | ✓ | 2020-09-01 |
| Koreanska | `ko` | ✓ |  |
| Kurdisk | `ku` | ✓ |  |
| Laos | `lo` | ✓ |  |
| Latin | `la` | ✓ |  |
| Lettiska | `lv` | ✓ |  |
| Litauiska | `lt` | ✓ |  |
| Makedonska | `mk` | ✓ |  |
| Madagaskisk | `mg` | ✓ | 2020-09-01 |
| Malajiska | `ms` | ✓ |  |
| Malayalam | `ml` | ✓ |  |
| Maltesiska | `mt` | ✓ |  |
| Maori | `mi` | ✓ | 2020-09-01 |
| Marathi | `mr` | ✓ | 2020-09-01 |
| Norska | `no` | ✓ |  |
| Norska (nynorsk) | `nn` | ✓ |  |
| Odia | `or` | ✓ |  |
| Afghanska, Pushto | `ps` | ✓ |  |
| Persiska | `fa` | ✓ |  |
| Polska | `pl` | ✓ |  |
| Portugisiska | `pt` | ✓ |  |
| Punjabi, Panjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Rumänska, Moldavien, moldaviska | `ro` | ✓ |  |
| Ryska | `ru` | ✓ |  |
| Samoan | `sm` | ✓ | 2020-09-01 |
| Serbiska | `sr` | ✓ |  |
| Sinhala, Sinhalese | `si` | ✓ |  |
| Slovakiska | `sk` | ✓ |  |
| Slovenska | `sl` | ✓ |  |
| Somaliska | `so` | ✓ |  |
| Spanska, kastiliansk | `es` | ✓ |  |
| Swahili | `sw` | ✓ |  |
| Svenska | `sv` | ✓ |  |
| Tagalog | `tl` | ✓ |  |
| Tahitian | `ty` | ✓ | 2020-09-01 |
| Tamilska | `ta` | ✓ |  |
| Telugu | `te` | ✓ |  |
| Thailändska | `th` | ✓ |  |
| Tonganska | `to` | ✓ | 2020-09-01 |
| Turkiska | `tr` | ✓ |  |
| Ukrainska | `uk` | ✓ |  |
| Urdu | `ur` | ✓ |  |
| Uzbekiska | `uz` | ✓ |  |
| Vietnamesiska | `vi` | ✓ |  |
| Walesiska | `cy` | ✓ |  |
| Jiddish | `yi` | ✓ |  |
| Yucatec Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>Se även

* [Vad är API för textanalys?](overview.md)   
