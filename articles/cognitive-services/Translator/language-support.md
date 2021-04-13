---
title: Språk stöd – översättare
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator stöder följande språk för text översättning med hjälp av NMT (neurala Machine Translation).
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: a195567117378a118b9bea8d15a7cb0a661d284a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303533"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Språk-och region stöd för text-och tal Översättning

Använd Translator för att översätta till och från något av de 90 text översättnings språken och dialekterna. Neurala Machine Translation (NMT) är den nya standarden för AI-drivna dator översättningar med hög kvalitet och är tillgänglig som standard med hjälp av v3 av Translator när ett neurala-system är tillgängligt.

Du kan också använda Translator tillsammans med anpassad översättare för att skapa neurala översättnings system som förstår terminologin som används i din verksamhet och bransch, och med Microsoft Speech service kan du lägga till tal översättning till din app.

[Lär dig mer om hur dator översättning fungerar](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Textöversättning
Text översättning är tillgängligt med översättnings åtgärden till eller från något av de språk som är tillgängliga i Translator. API: et erbjuder också språk identifiering med hjälp av åtgärden identifiera, transkriberingsspråk med hjälp av åtgärden translittererad och tvåspråkiga ord listor med hjälp av ord listan sökning och exempel åtgärder för ord listor. De tillgängliga språken för var och en av dessa åtgärder visas nedan. 

### <a name="translate"></a>Översätt

Translator stöder följande språk för text översättning av text. 

[Visa referens dokumentation för översättnings åtgärd](reference/v3-0-translate.md)

| Språk | Språkkod |
|:-|:-:|
| Afrikaans | `af` |
| Albanska | `sq` |
| Amhariska | `am` |
| Arabiska | `ar` |
| Armeniska | `hy` |
| Assamesiska | `as` |
| Azerbajdzjanska | `az` |
| Bangla | `bn` |
| Bosniska (latinsk) | `bs` |
| Bulgariska | `bg` |
| Kantonesiska (traditionell) | `yue` |
| Katalanska | `ca` |
| Kinesiska, förenklad | `zh-Hans` |
| Kinesiska, traditionell | `zh-Hant` |
| Kroatiska | `hr` |
| Tjeckiska | `cs` |
| Danska | `da` |
| Dari | `prs` |
| Nederländska | `nl` |
| Engelska | `en` |
| Estniska | `et` |
| Fijian | `fj` |
| Filipino | `fil` |
| Finska | `fi` |
| Franska | `fr` |
| Franska (Kanada) | `fr-ca` |
| Tyska | `de` |
| Grekiska | `el` |
| Gujarati | `gu` |
| Haitiska | `ht` |
| Hebreiska | `he` |
| Hindi | `hi` |
| Hmong Daw | `mww` |
| Ungerska | `hu` |
| Isländska | `is` |
| Indonesiska | `id` |
| Inuktitut | `iu` |
| Iriska | `ga` |
| Italienska | `it` |
| Japanska | `ja` |
| Kannada | `kn` |
| Kazakiska | `kk` |
| Kambodjanska | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Koreanska | `ko` |
| Kurdiska (Central) | `ku` |
| Kurdiska (norra) | `kmr` |
| Laos | `lo` |
| Lettiska | `lv` |
| Litauiska | `lt` |
| Madagaskisk | `mg` |
| Malajiska | `ms` |
| Malayalam | `ml` |
| Maltesiska | `mt` |
| Maori | `mi` |
| Marathi | `mr` |
| Myanmar | `my` |
| Nepal | `ne` |
| Norska | `nb` |
| Odia | `or` |
| Afghanska | `ps` |
| Persiska | `fa` |
| Polska | `pl` |
| Portugisiska (Brasilien) | `pt` |
| Portugisiska (Portugal) | `pt-pt` |
| Punjabi | `pa` |
| Queretaro Otomi | `otq` |
| Rumänska | `ro` |
| Ryska | `ru` |
| Samoan | `sm` |
| Serbiska (kyrillisk) | `sr-Cyrl` |
| Serbiska (latinsk) (Serbien) | `sr-Latn` |
| Slovakiska | `sk` |
| Slovenska | `sl` |
| Spanska | `es` |
| Swahili | `sw` |
| Svenska | `sv` |
| Tahitian | `ty` |
| Tamilska | `ta` |
| Telugu | `te` |
| Thailändska | `th` |
| Tigrinja | `ti` |
| Tonganska | `to` |
| Turkiska | `tr` |
| Ukrainska | `uk` |
| Urdu | `ur` |
| Vietnamesiska | `vi` |
| Walesiska | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> Språk koden är `pt` som standard `pt-br` portugisiska (Brasilien).

### <a name="detect"></a>Upptäcka

Translator identifierar följande språk för översättning och transkriberingsspråk.

[Visa referens dokumentation för identifierings åtgärd](reference/v3-0-detect.md)

| Språk | Språkkod |
|:-|:-:|
| Afrikaans | `af` |
| Albanska | `sq` |
| Arabiska | `ar` |
| Bulgariska | `bg` |
| Katalanska | `ca` |
| Kinesiska, förenklad | `zh-Hans` |
| Kinesiska, traditionell | `zh-Hant` |
| Kroatiska | `hr` |
| Tjeckiska | `cs` |
| Danska | `da` |
| Nederländska | `nl` |
| Engelska | `en` |
| Estniska | `et` |
| Finska | `fi` |
| Franska | `fr` |
| Tyska | `de` |
| Grekiska | `el` |
| Gujarati | `gu` |
| Haitiska | `ht` |
| Hebreiska | `he` |
| Hindi | `hi` |
| Ungerska | `hu` |
| Isländska | `is` |
| Indonesiska | `id` |
| Inuktitut | `iu` |
| Iriska | `ga` |
| Italienska | `it` |
| Japanska | `ja` |
| Klingon | `tlh-Latn` |
| Koreanska | `ko` |
| Kurdiska (Central) | `ku-Arab` |
| Lettiska | `lv` |
| Litauiska | `lt` |
| Malajiska | `ms` |
| Maltesiska | `mt` |
| Norska | `nb` |
| Afghanska | `ps` |
| Persiska | `fa` |
| Polska | `pl` |
| Portugisiska | `pt` |
| Rumänska | `ro` |
| Ryska | `ru` |
| Serbiska (kyrillisk) | `sr-Cyrl` |
| Serbiska (latinsk) (Serbien) | `sr-Latn` |
| Slovakiska | `sk` |
| Slovenska | `sl` |
| Spanska | `es` |
| Swahili | `sw` |
| Svenska | `sv` |
| Tahitian | `ty` |
| Thailändska | `th` |
| Turkiska | `tr` |
| Ukrainska | `uk` |
| Urdu | `ur` |
| Vietnamesiska | `vi` |
| Walesiska | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Transkribera

Metoden translittererad stöder följande språk. I "till/från", "<-->" anger att språket kan överföras från eller till något av de angivna skripten. "-->" anger att språket bara kan vara translittererad från ett skript till ett annat.

[Visa referens dokumentation om translittererad](reference/v3-0-translate.md)


| Språk    | Språkkod | Skript | Till/från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | `ar` | Arabiska `Arab` | <--> | Latin `Latn` |
| Assamesiska | `as` | Bengali `Beng` | <--> | Latin `Latn` |
| Bangla  | `bn` | Bengali `Beng` | <--> | Latin `Latn` |
|Vitryska| `be` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
|Bulgariska| `bg` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Kinesiska (förenklad) | `zh-Hans` | Kinesiska, förenklad `Hans`| <--> | Latin `Latn` |
| Kinesiska (förenklad) | `zh-Hans` | Kinesiska, förenklad `Hans`| <--> | Kinesiska, traditionell `Hant`|
| Kinesiska (traditionell) | `zh-Hant` | Kinesiska, traditionell `Hant`| <--> | Latin `Latn` |
| Kinesiska (traditionell) | `zh-Hant` | Kinesiska, traditionell `Hant`| <--> | Kinesiska, förenklad `Hans` |
|Grekiska| `el` | Grekiska `Grek`  | <--> | Latin `Latn` |
| Gujarati | `gu`  | Gujarati `Gujr` | <--> | Latin `Latn` |
| Hebreiska | `he` | Hebreiska `Hebr` | <--> | Latin `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latin `Latn` |
| Japanska | `ja` | Japanska `Jpan` | <--> | Latin `Latn` |
| Kannada | `kn` | Kannada `Knda` | <--> | Latin `Latn` |
|Kazakiska| `kk` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
|Koreanska| `ko` | Koreanska `Kore`  | <--> | Latin `Latn` |
|Kirgiziska| `ky` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
|Makedonska| `mk` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | <--> | Latin `Latn` |
| Marathi | `mr` | Devanagari `Deva` | <--> | Latin `Latn` |
|Mongoliska| `mn` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latin `Latn` |
|Persiska| `fa` | Arabiska `Arab`  | <--> | Latin `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
|Ryska| `ru` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Serbiska (kyrillisk) | `sr-Cyrl` | Kyrillisk `Cyrl`  | --> | Latin `Latn` |
| Serbiska (latinsk) (Serbien) | `sr-Latn` | Latin `Latn` | --> | Kyrillisk `Cyrl`|
|Sindhi| `sd` | Arabiska `Arab`  | <--> | Latin `Latn` |
|Sinhala| `si` | Sinhala `Sinh`  | <--> | Latin `Latn` |
|Tadzjikiska| `tg` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Tamilska | `ta` | Tamilska `Taml` | <--> | Latin `Latn` |
|Tatariska| `tt` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
| Telugu | `te` | Telugu `Telu` | <--> | Latin `Latn` |
| Thailändska | `th` | Thai `Thai` | --> | Latin `Latn` |
|Ukrainska| `uk` | Kyrillisk `Cyrl`  | <--> | Latin `Latn` |
|Urdu| `ur` | Arabiska `Arab`  | <--> | Latin `Latn` |

### <a name="dictionary"></a>Ordlista

Ord listan har stöd för följande språk till eller från engelska med hjälp av Sök-och exempel metoderna.

Visa referens dokumentation för [ord listornas sökning](reference/v3-0-dictionary-lookup.md) och exempel åtgärder för [ord listor](reference/v3-0-dictionary-examples.md) .

| Språk    | Språkkod |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Katalanska      | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`          |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Estniska      | `et`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Haitiska      | `ht`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungerska      | `hu`          |
| Isländska    | `is`  |
| Indonesiska      | `id`          |
| Italienska      | `it`          |
| Japanska      | `ja`          |
| Klingon      | `tlh`          |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Malajiska      | `ms`          |
| Maltesiska      | `mt`          |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska (Brasilien)     | `pt`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Serbiska (latinsk) (Serbien)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Swahili      | `sw`          |
| Svenska      | `sv`          |
| Tamilska      | `ta`          |
| Thailändska      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Få åtkomst till Translators språk lista program mässigt

Du kan hämta en lista över språk som stöds för Translator med hjälp av språk metoden. Du kan visa listan efter funktion, språkkod, språk kod och språk namn på engelska eller andra språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk görs tillgängliga.

[Visa referens dokumentation för språk åtgärder](reference/v3-0-languages.md)

## <a name="customization"></a>Anpassning

Följande språk är tillgängliga för anpassning till eller från engelska med hjälp av en [anpassad översättare](https://aka.ms/CustomTranslator).

| Språk    | Språkkod |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
|Katalanska|   `ca`    |
| Kinesiska, förenklad      | `zh-Hans`          |
|Kinesiska, traditionell|   `zh-Hant`   |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Engelska    | `en`     |
| Estniska      | `et`          |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Gujarati| `gu`    |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Ungerska      | `hu`          |
| Isländska | `is` |
| Indonesiska|   `id`    |
| Inuktitut|    `iu`    |
| Iriska | `ga`  |
| Italienska      | `it`          |
| Japanska      | `ja`          |
|Kannada|`kn`|
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Madagaskisk| `mg`    |
| Malajiska|    `ms` |
|Maltesiska|   `mt`    |
| Maori| `mi`  |
| Marathi| `mr`  |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska (Brasilien) | `pt` |
| Punjabi|`pa`|
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Samoan|   `sm`    |
| Serbiska (latinsk) (Serbien)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Swahili|  `sw`    |
| Svenska      | `sv`          |
|Tahitian|  `ty`    |
| Thailändska      | `th`          |
|Tonganska|    `to`    |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu| `ur`    |
| Vietnamesiska      | `vi`          |
| Walesiska | `cy` |

## <a name="speech-translation"></a>Talöversättning
Tal översättning är tillgängligt med hjälp av Translator med Cognitive Services Speech service. Visa [dokumentation om röst tjänster](../speech-service/index.yml) för att lära dig mer om hur du använder tal översättning och se alla [tillgängliga språk alternativ](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Tal till text
Konvertera tal till text för att översätta till valfritt språk. Tal-till-text används för tal till text översättning, eller för översättning av tal till tal när de används tillsammans med tal syntes.

| Språk    |
|:----------- |
|Arabiska|
|Kantonesiska (traditionell)|
|Katalanska|
|Kinesiska, förenklad|
|Kinesiska, traditionell|
|Danska|
|Nederländska|
|Engelska|
|Finska|
|Franska|
|Franska (Kanada)|
|Tyska|
|Gujarati|
|Hindi|
|Italienska|
|Japanska|
|Koreanska|
|Marathi|
|Norska|
|Polska|
|Portugisiska (Brasilien)|
|Portugisiska (Portugal)|
|Ryska|
|Spanska|
|Svenska|
|Tamilska|
|Telugu|
|Thailändska|
|Turkiska|

### <a name="text-to-speech"></a>Text till tal
Omvandla text till tal. Text till tal används för att lägga till ljud utmatning av översättnings resultat eller för tal till tal-översättning när de används med tal till text. 

| Språk |
|:-|
| Arabiska |
| Bulgariska |
| Kantonesiska (traditionell) |
| Katalanska |
| Kinesiska, förenklad |
| Kinesiska, traditionell |
| Kroatiska |
| Tjeckiska |
| Danska |
| Nederländska |
| Engelska |
| Finska |
| Franska |
| Franska (Kanada) |
| Tyska |
| Grekiska |
| Hebreiska |
| Hindi |
| Ungerska |
| Indonesiska |
| Italienska |
| Japanska |
| Koreanska |
| Malajiska |
| Norska |
| Polska |
| Portugisiska (Brasilien) |
| Portugisiska (Portugal) |
| Rumänska |
| Ryska |
| Slovakiska |
| Slovenska |
| Spanska |
| Svenska |
| Tamilska |
| Telugu |
| Thailändska |
| Turkiska |
| Vietnamesiska |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Visa språk listan på webbplatsen för Microsoft Translator

För en snabb titt på språken visar webbplatsen Microsoft Translator alla språk som stöds av Translator för översättning av text översättning och tal tjänster för tal översättning. Den här listan innehåller inte information som är specifik för utvecklare, till exempel språk koder.

[Se listan över språk](https://www.microsoft.com/translator/languages.aspx)
