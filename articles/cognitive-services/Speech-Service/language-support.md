---
title: Språk stöd – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Tal tjänsten har stöd för flera språk för konvertering från tal till text och text till tal, tillsammans med tal översättning. Den här artikeln innehåller en omfattande lista över språk stöd för tjänst funktionen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 43456655d057ee79ed347e4a94c008d825bfa639
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470242"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Språk-och röst stöd för tal tjänsten

Språk stöd varierar beroende på funktioner i tal tjänster. Följande tabeller sammanfattar språk stöd för [tal-till-text](#speech-to-text)-, [text-till-tal](#text-to-speech)-och [röst översättnings](#speech-translation) tjänst erbjudanden.

## <a name="speech-to-text"></a>Tal till text

Både Microsoft Speech SDK och REST API stöder följande språk (lokala). 

För att förbättra noggrannheten erbjuds anpassning för en delmängd av språken genom uppladdning av **ljud + mänskligt avskrifter** eller **relaterad text: meningar**. Stöd för anpassning av akustisk modell med **ljud-och medmärkta avskrifter** är begränsat till de enskilda bas modellerna i listan nedan. Andra bas modeller och språk kommer bara att använda texten i avskrifterna för att träna anpassade modeller precis som med **relaterad text: meningar**. Mer information om anpassning finns i [Kom igång med Custom Speech](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Språk                 | Locale (BCP-47) | Anpassningar  | [Språk identifiering](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabiska (Bahrain), modern standard  | `ar-BH` | Text                                   | Ja                           | 
| Arabiska (Egypten)                     | `ar-EG` | Text                                   | Ja                          |
| Arabiska (Irak)                      | `ar-IQ` | Text                                   |                           |
| Arabiska (Israel)                    | `ar-IL` | Text                                   |                           |
| Arabiska (Jordanien)                    | `ar-JO` | Text                                   |                           |
| Arabiska (Kuwait)                    | `ar-KW` | Text                                   |                           |
| Arabiska (Libanon)                   | `ar-LB` | Text                                   |                           |
| Arabiska (Oman)                      | `ar-OM` | Text                                   |                           |
| Arabiska (Qatar)                     | `ar-QA` | Text                                   |                           |
| Arabiska (Saudiarabien)              | `ar-SA` | Text                                   | Ja                          |
| Arabiska (tillstånd för Palestina)        | `ar-PS` | Text                                   |                           |
| Arabiska (Syrien)                     | `ar-SY` | Text                                   | Ja                          |
| Arabiska (Förenade Arabemiraten)      | `ar-AE` | Text                                   |                           |
| Bulgariska (Bulgarien)               | `bg-BG` | Text                                   |                           |
| Katalanska (Spanien)                    | `ca-ES` | Text                                   | Ja                          |
| Kinesiska (kantonesiska, traditionell)   | `zh-HK` | Ljud (20201015)<br>Text                 |        Ja                   |
| Kinesiska (mandariner, förenklad)     | `zh-CN` | Ljud (20200910)<br>Text                 |     Ja                      |
| Kinesiska (Taiwan-mandariner)       | `zh-TW` | Ljud (20190701, 20201015)<br>Text                 |           Ja                |
| Kroatiska (Kroatien)                 | `hr-HR` | Text                                   |                           |
| Tjeckiska (Tjeckien)             | `cs-CZ` | Text                                   |                           |
| Danska (Danmark)                   | `da-DK` | Text                                   | Ja                          |
| Nederländska (Nederländerna)                | `nl-NL` | Ljud (20201015)<br>Text                                   |    Ja                       |
| Engelska (Australien)                | `en-AU` | Ljud (20201019)<br>Text                 | Ja                          |
| Engelska (Kanada)                   | `en-CA` | Ljud (20201019)<br>Text                 | Ja                          |
| Engelska (Ghana)                    | `en-GH` | Text                                   |                           |
| Engelska (Hongkong)                | `en-HK` | Text                                   |                           |
| Engelska (Indien)                    | `en-IN` | Ljud (20200923)<br>Text                 | Ja                          |
| Engelska (Irland)                  | `en-IE` | Text                                   |                           |
| Engelska (Kenya)                    | `en-KE` | Text                                   |                           |
| Engelska (Nya Zeeland)              | `en-NZ` | Ljud (20201019)<br>Text                 |  Ja                         |
| Engelska (Nigeria)                  | `en-NG` | Text                                   |                           |
| Engelska (Filippinerna)              | `en-PH` | Text                                   |                           |
| Engelska (Singapore)                | `en-SG` | Text                                   |                           |
| Engelska (Sydafrika)             | `en-ZA` | Text                                   |                           |
| Engelska (Tanzania)                 | `en-TZ` | Text                                   |                           |
| Engelska (Storbritannien)           | `en-GB` | Ljud (20201019)<br>Text<br>Uttal| Ja                          |
| Engelska (USA)            | `en-US` | Ljud (20201019)<br>Text<br>Uttal| Ja                          |
| Estniska (Estland)                  | `et-EE` | Text                                   |                           |
| Filippinska (Filippinerna)             | `fil-PH`| Text                                   |                           |
| Finska (Finland)                  | `fi-FI` | Text                                   |     Ja                      |
| Franska (Kanada)                    | `fr-CA` | Ljud (20201015)<br>Text                 |     Ja                      |
| Franska (Frankrike)                    | `fr-FR` | Ljud (20201015)<br>Text<br>Uttal|      Ja                     |
| Franska (Schweiz)               | `fr-CH` | Text                                   |                           |
| Tyska (Österrike)                   | `de-AT` | Text                                   |                           |
| Tyska (Tyskland)                   | `de-DE` | Ljud (20190701, 20200619, 20201127)<br>Text<br>Uttal|  Ja                         |
| Grekiska (Grekland)                     | `el-GR` | Text                                   |                           |
| Gujarati (indiska)                  | `gu-IN` | Text                                   |                           |
| Hindi (Indien)                      | `hi-IN` | Ljud (20200701)<br>Text                 |     Ja                      |
| Ungerska (Ungern)                | `hu-HU` | Text                                   |                           |
| Indonesiska (Indonesien)             | `id-ID` | Text                                   |                           |
| Irländskt (Irland)                     | `ga-IE` | Text                                   |                           |
| Italienska (Italien)                    | `it-IT` | Ljud (20201016)<br>Text<br>Uttal|      Ja                     |
| Japanska (Japan)                   | `ja-JP` | Text                                   |      Ja                     |
| Koreanska (Korea)                     | `ko-KR` | Ljud (20201015)<br>Text                 |      Ja                     |
| Lettiska (Lettland)                   | `lv-LV` | Text                                   |                           |
| Litauiska (Litauen)             | `lt-LT` | Text                                   |                           |
| Malajiska (Malaysia)                    | `ms-MY` | Text                                   |                           |
| Maltesiska (Malta)                     | `mt-MT` | Text                                   |                           |
| Marathi (Indien)                    | `mr-IN` | Text                                   |                           |
| norska (Bokmål, Norge)         | `nb-NO` | Text                                   |     Ja                      |
| Polska (Polen)                    | `pl-PL` | Text                                   |       Ja                    |
| Portugisiska (Brasilien)                | `pt-BR` | Ljud (20190620, 20201015)<br>Text<br>Uttal|          Ja                 |
| Portugisiska (Portugal)              | `pt-PT` | Text                                   |             Ja              |
| Rumänska (Rumänien)                 | `ro-RO` | Text                                   |                           |
| Ryska (Ryssland)                   | `ru-RU` | Ljud (20200907)<br>Text                 |                Ja           |
| Slovakiska (Slovakien)                  | `sk-SK` | Text                                   |                           |
| Slovenska (Slovenien)               | `sl-SI` | Text                                   |                           |
| Spanska (Argentina)                | `es-AR` | Text                                   |                           |
| Spanska (Bolivia)                  | `es-BO` | Text                                   |                           |
| Spanska (Chile)                    | `es-CL` | Text                                   |                           |
| Spanska (Colombia)                 | `es-CO` | Text                                   |                           |
| Spanska (Costa Rica)               | `es-CR` | Text                                   |                           |
| Spanska (Kuba)                     | `es-CU` | Text                                   |                           |
| Spanska (Dominikanska republiken)       | `es-DO` | Text                                   |                           |
| Spanska (Ecuador)                  | `es-EC` | Text                                   |                           |
| Spanska (El Salvador)              | `es-SV` | Text                                   |                           |
| Spanska (Ekvatorialguinea)        | `es-GQ` | Text                                   |                           |
| Spanska (Guatemala)                | `es-GT` | Text                                   |                           |
| Spanska (Honduras)                 | `es-HN` | Text                                   |                           |
| Spanska (Mexiko)                   | `es-MX` | Ljud (20200907)<br>Text                 |    Ja                       |
| Spanska (Nicaragua)                | `es-NI` | Text                                   |                           |
| Spanska (Panama)                   | `es-PA` | Text                                   |                           |
| Spanska (Paraguay)                 | `es-PY` | Text                                   |                           |
| Spanska (Peru)                     | `es-PE` | Text                                   |                           |
| Spanska (Puerto Rico)              | `es-PR` | Text                                   |                           |
| Spanska (Spanien)                    | `es-ES` | Ljud (20201015)<br>Text                 |  Ja                         |
| Spanska (Uruguay)                  | `es-UY` | Text                                   |                           |
| Spanska (USA)                      | `es-US` | Text                                   |                           |
| Spanska (Venezuela)                | `es-VE` | Text                                   |                           |
| Svenska (Sverige)                   | `sv-SE` | Text                                   |   Ja                        |
| Tamiliska (Indien)                      | `ta-IN` | Text                                   |                           |
| Telugu (Indien)                     | `te-IN` | Text                                   |                           |
| Thailändska (Thailand)                    | `th-TH` | Text                                   |      Ja                     |
| Turkiska (Turkiet)                   | `tr-TR` | Text                                   |                           |
| Vietnamesiska (Vietnam)               | `vi-VN` | Text                                   |                           |

## <a name="text-to-speech"></a>Text till tal

Både Microsoft Speech SDK och REST-API: er har stöd för dessa röster, som var och en har stöd för ett särskilt språk och dialekt, som identifieras av nationella inställningar. Du kan också få en fullständig lista över språk och röster som stöds för varje region/slut punkt via [API: t för röster/listor](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Prissättningen varierar för standard-, anpassade och neurala röster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Neurala-röster

Neurala text till tal är en ny typ av tal syntes som drivs av djup neurala nätverk. När du använder en neurala-röst är syntetiskt tal nästan lätt att skilja från de mänskliga inspelningarna.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när användarna interagerar med AI-system.

> [!NOTE]
> Neurala-röster skapas från exempel som använder 24 kHz samplings frekvens.
> Alla röster kan sampla eller sampla till andra samplings frekvenser vid syntetiserar.


| Språk | Nationell inställning | Kön | Röst namn | Format stöd |
|---|---|---|---|---|
| Arabiska (Egypten) | `ar-EG` | Kvinna | `ar-EG-SalmaNeural` | Allmänt |
| Arabiska (Egypten) | `ar-EG` | Man | `ar-EG-ShakirNeural` <sup>Ny</sup> | Allmänt |
| Arabiska (Saudiarabien) | `ar-SA` | Kvinna | `ar-SA-ZariyahNeural` | Allmänt |
| Arabiska (Saudiarabien) | `ar-SA` | Man | `ar-SA-HamedNeural` <sup>Ny</sup> | Allmänt |
| Bulgariska (Bulgarien) | `bg-BG` | Kvinna | `bg-BG-KalinaNeural` | Allmänt |
| Bulgariska (Bulgarien) | `bg-BG` | Man | `bg-BG-BorislavNeural` <sup>Ny</sup> | Allmänt |
| Katalanska (Spanien) | `ca-ES` | Kvinna | `ca-ES-AlbaNeural` | Allmänt |
| Katalanska (Spanien) | `ca-ES` | Kvinna | `ca-ES-JoanaNeural` <sup>Ny</sup> | Allmänt |
| Katalanska (Spanien) | `ca-ES` | Man | `ca-ES-EnricNeural` <sup>Ny</sup> | Allmänt |
| Kinesiska (kantonesiska, traditionell) | `zh-HK` | Kvinna | `zh-HK-HiuGaaiNeural` | Allmänt |
| Kinesiska (kantonesiska, traditionell) | `zh-HK` | Kvinna | `zh-HK-HiuMaanNeural` <sup>Ny</sup> | Allmänt |
| Kinesiska (kantonesiska, traditionell) | `zh-HK` | Man | `zh-HK-WanLungNeural` <sup>Ny</sup> | Allmänt |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaoxiaoNeural` | Allmänt, flera röst format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaoyouNeural` | Röst för barn, optimerad för artikel-berättarröst |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Man | `zh-CN-YunyangNeural` | Optimerad för läsning av nyheter,<br /> flera röst typer som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Man | `zh-CN-YunyeNeural` | Optimerad för artikel-berättarröst  |
| Kinesiska (Taiwan-mandariner) | `zh-TW` | Kvinna | `zh-TW-HsiaoChenNeural` <sup>Ny</sup> | Allmänt |
| Kinesiska (Taiwan-mandariner) | `zh-TW` | Kvinna | `zh-TW-HsiaoYuNeural` | Allmänt |
| Kinesiska (Taiwan-mandariner) | `zh-TW` | Man | `zh-TW-YunJheNeural` <sup>Ny</sup> | Allmänt |
| Kroatiska (Kroatien) | `hr-HR` | Kvinna | `hr-HR-GabrijelaNeural` | Allmänt |
| Kroatiska (Kroatien) | `hr-HR` | Man | `hr-HR-SreckoNeural` <sup>Ny</sup> | Allmänt |
| Tjeckiska (Tjeckien) | `cs-CZ` | Kvinna | `cs-CZ-VlastaNeural` | Allmänt |
| Tjeckiska (Tjeckien) | `cs-CZ` | Man | `cs-CZ-AntoninNeural` <sup>Ny</sup> | Allmänt |
| Danska (Danmark) | `da-DK` | Kvinna | `da-DK-ChristelNeural` | Allmänt |
| Danska (Danmark) | `da-DK` | Man | `da-DK-JeppeNeural` <sup>Ny</sup> | Allmänt |
| Nederländska (Nederländerna) | `nl-NL` | Kvinna | `nl-NL-ColetteNeural` | Allmänt |
| Nederländska (Nederländerna) | `nl-NL` | Kvinna | `nl-NL-FennaNeural` <sup>Ny</sup> | Allmänt |
| Nederländska (Nederländerna) | `nl-NL` | Man | `nl-NL-MaartenNeural` <sup>Ny</sup> | Allmänt |
| Engelska (Australien) | `en-AU` | Kvinna | `en-AU-NatashaNeural` | Allmänt |
| Engelska (Australien) | `en-AU` | Man | `en-AU-WilliamNeural` | Allmänt |
| Engelska (Kanada) | `en-CA` | Kvinna | `en-CA-ClaraNeural` | Allmänt |
| Engelska (Kanada) | `en-CA` | Man | `en-CA-LiamNeural` <sup>Ny</sup> | Allmänt |
| Engelska (Indien) | `en-IN` | Kvinna | `en-IN-NeerjaNeural` | Allmänt |
| Engelska (Indien) | `en-IN` | Man | `en-IN-PrabhatNeural` <sup>Ny</sup> | Allmänt |
| Engelska (Irland) | `en-IE` | Kvinna | `en-IE-EmilyNeural` | Allmänt |
| Engelska (Irland) | `en-IE` | Man | `en-IE-ConnorNeural` <sup>Ny</sup> | Allmänt |
| Engelska (Storbritannien) | `en-GB` | Kvinna | `en-GB-LibbyNeural` | Allmänt |
| Engelska (Storbritannien) | `en-GB` | Kvinna | `en-GB-MiaNeural` | Allmänt |
| Engelska (Storbritannien) | `en-GB` | Man | `en-GB-RyanNeural` | Allmänt |
| Engelska (USA) | `en-US` | Kvinna | `en-US-AriaNeural` | Allmänt, flera röst format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Engelska (USA) | `en-US` | Kvinna | `en-US-JennyNeural` | Allmänt, flera röst format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Engelska (USA) | `en-US` | Man | `en-US-GuyNeural` | Allmänt, flera röst format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Finska (Finland) | `fi-FI` | Kvinna | `fi-FI-NooraNeural` | Allmänt |
| Finska (Finland) | `fi-FI` | Kvinna | `fi-FI-SelmaNeural` <sup>Ny</sup> | Allmänt |
| Finska (Finland) | `fi-FI` | Man | `fi-FI-HarriNeural` <sup>Ny</sup> | Allmänt |
| Franska (Kanada) | `fr-CA` | Kvinna | `fr-CA-SylvieNeural` | Allmänt |
| Franska (Kanada) | `fr-CA` | Man | `fr-CA-AntoineNeural` <sup>Ny</sup> | Allmänt |
| Franska (Kanada) | `fr-CA` | Man | `fr-CA-JeanNeural` | Allmänt |
| Franska (Frankrike) | `fr-FR` | Kvinna | `fr-FR-DeniseNeural` | Allmänt |
| Franska (Frankrike) | `fr-FR` | Man | `fr-FR-HenriNeural` | Allmänt |
| Franska (Schweiz) | `fr-CH` | Kvinna | `fr-CH-ArianeNeural` | Allmänt |
| Franska (Schweiz) | `fr-CH` | Man | `fr-CH-FabriceNeural` <sup>Ny</sup> | Allmänt |
| Tyska (Österrike) | `de-AT` | Kvinna | `de-AT-IngridNeural` | Allmänt |
| Tyska (Österrike) | `de-AT` | Man | `de-AT-JonasNeural` <sup>Ny</sup> | Allmänt |
| Tyska (Tyskland) | `de-DE` | Kvinna | `de-DE-KatjaNeural` | Allmänt |
| Tyska (Tyskland) | `de-DE` | Man | `de-DE-ConradNeural` | Allmänt |
| Tyska (Schweiz) | `de-CH` | Kvinna | `de-CH-LeniNeural` | Allmänt |
| Tyska (Schweiz) | `de-CH` | Man | `de-CH-JanNeural` <sup>Ny</sup> | Allmänt |
| Grekiska (Grekland) | `el-GR` | Kvinna | `el-GR-AthinaNeural` | Allmänt |
| Grekiska (Grekland) | `el-GR` | Man | `el-GR-NestorasNeural` <sup>Ny</sup> | Allmänt |
| Hebreiska (Israel) | `he-IL` | Kvinna | `he-IL-HilaNeural` | Allmänt |
| Hebreiska (Israel) | `he-IL` | Man | `he-IL-AvriNeural` <sup>Ny</sup> | Allmänt |
| Hindi (Indien) | `hi-IN` | Kvinna | `hi-IN-SwaraNeural` | Allmänt |
| Hindi (Indien) | `hi-IN` | Man | `hi-IN-MadhurNeural` <sup>Ny</sup> | Allmänt |
| Ungerska (Ungern) | `hu-HU` | Kvinna | `hu-HU-NoemiNeural` | Allmänt |
| Ungerska (Ungern) | `hu-HU` | Man | `hu-HU-TamasNeural` <sup>Ny</sup> | Allmänt |
| Indonesiska (Indonesien) | `id-ID` | Kvinna | `id-ID-GadisNeural` <sup>Ny</sup> | Allmänt |
| Indonesiska (Indonesien) | `id-ID` | Man | `id-ID-ArdiNeural` | Allmänt |
| Italienska (Italien) | `it-IT` | Kvinna | `it-IT-ElsaNeural` | Allmänt |
| Italienska (Italien) | `it-IT` | Kvinna | `it-IT-IsabellaNeural` | Allmänt |
| Italienska (Italien) | `it-IT` | Man | `it-IT-DiegoNeural` | Allmänt |
| Japanska (Japan) | `ja-JP` | Kvinna | `ja-JP-NanamiNeural` | Allmänt |
| Japanska (Japan) | `ja-JP` | Man | `ja-JP-KeitaNeural` | Allmänt |
| Koreanska (Korea) | `ko-KR` | Kvinna | `ko-KR-SunHiNeural` | Allmänt |
| Koreanska (Korea) | `ko-KR` | Man | `ko-KR-InJoonNeural` | Allmänt |
| Malajiska (Malaysia) | `ms-MY` | Kvinna | `ms-MY-YasminNeural` | Allmänt |
| Malajiska (Malaysia) | `ms-MY` | Man | `ms-MY-OsmanNeural` <sup>Ny</sup> | Allmänt |
| norska (Bokmål, Norge) | `nb-NO` | Kvinna | `nb-NO-IselinNeural` | Allmänt |
| norska (Bokmål, Norge) | `nb-NO` | Kvinna | `nb-NO-PernilleNeural` <sup>Ny</sup> | Allmänt |
| norska (Bokmål, Norge) | `nb-NO` | Man | `nb-NO-FinnNeural` <sup>Ny</sup> | Allmänt |
| Polska (Polen) | `pl-PL` | Kvinna | `pl-PL-AgnieszkaNeural` <sup>Ny</sup> | Allmänt |
| Polska (Polen) | `pl-PL` | Kvinna | `pl-PL-ZofiaNeural` | Allmänt |
| Polska (Polen) | `pl-PL` | Man | `pl-PL-MarekNeural` <sup>Ny</sup> | Allmänt |
| Portugisiska (Brasilien) | `pt-BR` | Kvinna | `pt-BR-FranciscaNeural` | Allmänt, flera röst format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Portugisiska (Brasilien) | `pt-BR` | Man | `pt-BR-AntonioNeural` | Allmänt |
| Portugisiska (Portugal) | `pt-PT` | Kvinna | `pt-PT-FernandaNeural` | Allmänt |
| Portugisiska (Portugal) | `pt-PT` | Kvinna | `pt-PT-RaquelNeural` <sup>Ny</sup> | Allmänt |
| Portugisiska (Portugal) | `pt-PT` | Man | `pt-PT-DuarteNeural` <sup>Ny</sup> | Allmänt |
| Rumänska (Rumänien) | `ro-RO` | Kvinna | `ro-RO-AlinaNeural` | Allmänt |
| Rumänska (Rumänien) | `ro-RO` | Man | `ro-RO-EmilNeural` <sup>Ny</sup> | Allmänt |
| Ryska (Ryssland) | `ru-RU` | Kvinna | `ru-RU-DariyaNeural` | Allmänt |
| Ryska (Ryssland) | `ru-RU` | Kvinna | `ru-RU-SvetlanaNeural` <sup>Ny</sup> | Allmänt |
| Ryska (Ryssland) | `ru-RU` | Man | `ru-RU-DmitryNeural` <sup>Ny</sup> | Allmänt |
| Slovakiska (Slovakien) | `sk-SK` | Kvinna | `sk-SK-ViktoriaNeural` | Allmänt |
| Slovakiska (Slovakien) | `sk-SK` | Man | `sk-SK-LukasNeural` <sup>Ny</sup> | Allmänt |
| Slovenska (Slovenien) | `sl-SI` | Kvinna | `sl-SI-PetraNeural` | Allmänt |
| Slovenska (Slovenien) | `sl-SI` | Man | `sl-SI-RokNeural` <sup>Ny</sup> | Allmänt |
| Spanska (Mexiko) | `es-MX` | Kvinna | `es-MX-DaliaNeural` | Allmänt |
| Spanska (Mexiko) | `es-MX` | Man | `es-MX-JorgeNeural` | Allmänt |
| Spanska (Spanien) | `es-ES` | Kvinna | `es-ES-ElviraNeural` | Allmänt |
| Spanska (Spanien) | `es-ES` | Man | `es-ES-AlvaroNeural` | Allmänt |
| Svenska (Sverige) | `sv-SE` | Kvinna | `sv-SE-HilleviNeural` | Allmänt |
| Svenska (Sverige) | `sv-SE` | Kvinna | `sv-SE-SofieNeural` <sup>Ny</sup> | Allmänt |
| Svenska (Sverige) | `sv-SE` | Man | `sv-SE-MattiasNeural` <sup>Ny</sup> | Allmänt |
| Tamiliska (Indien) | `ta-IN` | Kvinna | `ta-IN-PallaviNeural` | Allmänt |
| Tamiliska (Indien) | `ta-IN` | Man | `ta-IN-ValluvarNeural` <sup>Ny</sup> | Allmänt |
| Telugu (Indien) | `te-IN` | Kvinna | `te-IN-ShrutiNeural` | Allmänt |
| Telugu (Indien) | `te-IN` | Man | `te-IN-MohanNeural` <sup>Ny</sup> | Allmänt |
| Thailändska (Thailand) | `th-TH` | Kvinna | `th-TH-AcharaNeural` | Allmänt |
| Thailändska (Thailand) | `th-TH` | Kvinna | `th-TH-PremwadeeNeural` | Allmänt |
| Thailändska (Thailand) | `th-TH` | Man | `th-TH-NiwatNeural` <sup>Ny</sup> | Allmänt |
| Turkiska (Turkiet) | `tr-TR` | Kvinna | `tr-TR-EmelNeural` | Allmänt |
| Turkiska (Turkiet) | `tr-TR` | Man | `tr-TR-AhmetNeural` <sup>Ny</sup> | Allmänt |
| Vietnamesiska (Vietnam) | `vi-VN` | Kvinna | `vi-VN-HoaiMyNeural` | Allmänt |
| Vietnamesiska (Vietnam) | `vi-VN` | Man | `vi-VN-NamMinhNeural` <sup>Ny</sup> | Allmänt |

#### <a name="neural-voices-in-preview"></a>Neurala-röster i för hands versionen

Nedan finns neurala-röster i offentlig för hands version. 

| Språk                         | Nationell inställning  | Kön | Röst namn                             | Format stöd |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaohanNeural` | Allmänt, flera format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaomoNeural` | Allmänt, flera roll spel och format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaoruiNeural` | Senior-röst, flera stilar som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-XiaoxuanNeural` | Allmänt, flera roll spel och format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kinesiska (mandariner, förenklad) | `zh-CN` | Man   | `zh-CN-YunxiNeural` | Allmänt, flera format som är tillgängliga [med SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estniska (Estland) | `et-EE` | Kvinna | `et-EE-AnuNeural` | Allmänt |
| Estniska (Estland) | `et-EE` | Man | `et-EE-KertNeural` <sup>Ny</sup> | Allmänt |
| Irländskt (Irland) | `ga-IE` | Kvinna | `ga-IE-OrlaNeural` | Allmänt |
| Irländskt (Irland) | `ga-IE` | Man | `ga-IE-ColmNeural` <sup>Ny</sup> | Allmänt |
| Lettiska (Lettland) | `lv-LV` | Kvinna | `lv-LV-EveritaNeural` | Allmänt |
| Lettiska (Lettland) | `lv-LV` | Man | `lv-LV-NilsNeural` <sup>Ny</sup> | Allmänt |
| Litauiska (Litauen) | `lt-LT` | Kvinna | `lt-LT-OnaNeural` | Allmänt |
| Litauiska (Litauen) | `lt-LT` | Man | `lt-LT-LeonasNeural` <sup>Ny</sup> | Allmänt |
| Maltesiska (Malta) | `mt-MT` | Kvinna | `mt-MT-GraceNeural` | Allmänt |
| Maltesiska (Malta) | `mt-MT` | Man | `mt-MT-JosephNeural` <sup>Ny</sup> | Allmänt |

> [!IMPORTANT]
> Röster i offentlig för hands version är bara tillgängliga i tre tjänste regioner: USA, västra Europa och Sydostasien.

Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

Information om hur du kan konfigurera och justera neurala-röster, t. ex. tal format, finns i [tal syntes Markup Language](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> `en-US-JessaNeural`Rösten har ändrats till `en-US-AriaNeural` . Om du använde "Jessa" tidigare, konvertera till "Aria".

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaNeural)" i din begäran om tal syntes.

### <a name="standard-voices"></a>Standard röster

Det finns fler än 75 standard röster i över 45 språk och nationella inställningar, vilket gör att du kan konvertera text till syntetiskt tal. Mer information om regional tillgänglighet finns i [regioner](regions.md#standard-and-neural-voices).

> [!NOTE]
> Med två undantag skapas standard röster från exempel som använder en 16 kHz-samplings frekvens.
> En **-US-AriaRUS** och **en-US-GuyRUS-** röster skapas också från exempel som använder en 24 kHz-samplings frekvens.
> Alla röster kan sampla eller sampla till andra samplings frekvenser vid syntetiserar.

| Språk | Locale (BCP-47) | Kön | Röst namn |
|--|--|--|--|
| Arabiska (arabiska) | `ar-EG` | Kvinna | `ar-EG-Hoda`|
| Arabiska (Saudiarabien) | `ar-SA` | Man | `ar-SA-Naayf`|
| Bulgariska (Bulgarien) | `bg-BG` | Man | `bg-BG-Ivan`|
| Katalanska (Spanien) | `ca-ES` | Kvinna | `ca-ES-HerenaRUS`|
| Kinesiska (kantonesiska, traditionell) | `zh-HK` | Man | `zh-HK-Danny`|
| Kinesiska (kantonesiska, traditionell) | `zh-HK` | Kvinna | `zh-HK-TracyRUS`|
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-HuihuiRUS`|
| Kinesiska (mandariner, förenklad) | `zh-CN` | Man | `zh-CN-Kangkang`|
| Kinesiska (mandariner, förenklad) | `zh-CN` | Kvinna | `zh-CN-Yaoyao`|
| Kinesiska (Taiwan-mandariner) |  `zh-TW` | Kvinna | `zh-TW-HanHanRUS`|
| Kinesiska (Taiwan-mandariner) |  `zh-TW` | Kvinna | `zh-TW-Yating`|
| Kinesiska (Taiwan-mandariner) |  `zh-TW` | Man | `zh-TW-Zhiwei`|
| Kroatiska (Kroatien) | `hr-HR` | Man | `hr-HR-Matej`|
| Tjeckiska (Tjeckien) | `cs-CZ` | Man | `cs-CZ-Jakub`|
| Danska (Danmark) | `da-DK` | Kvinna | `da-DK-HelleRUS`|
| Nederländska (Nederländerna) | `nl-NL` | Kvinna | `nl-NL-HannaRUS`|
| Engelska (Australien) | `en-AU` | Kvinna | `en-AU-Catherine`|
| Engelska (Australien) | `en-AU` | Kvinna | `en-AU-HayleyRUS`|
| Engelska (Kanada) | `en-CA` | Kvinna | `en-CA-HeatherRUS`|
| Engelska (Kanada) | `en-CA` | Kvinna | `en-CA-Linda`|
| Engelska (Indien) | `en-IN` | Kvinna | `en-IN-Heera`|
| Engelska (Indien) | `en-IN` | Kvinna | `en-IN-PriyaRUS`|
| Engelska (Indien) | `en-IN` | Man | `en-IN-Ravi`|
| Engelska (Irland) | `en-IE` | Man | `en-IE-Sean`|
| Engelska (Storbritannien) | `en-GB` | Man | `en-GB-George`|
| Engelska (Storbritannien) | `en-GB` | Kvinna | `en-GB-HazelRUS`|
| Engelska (Storbritannien) | `en-GB` | Kvinna | `en-GB-Susan`|
| Engelska (USA) | `en-US` | Man | `en-US-BenjaminRUS`|
| Engelska (USA) | `en-US` | Man | `en-US-GuyRUS`|
| Engelska (USA) | `en-US` | Kvinna | `en-US-AriaRUS`|
| Engelska (USA) | `en-US` | Kvinna | `en-US-ZiraRUS`|
| Finska (Finland) | `fi-FI` | Kvinna | `fi-FI-HeidiRUS`|
| Franska (Kanada) | `fr-CA` | Kvinna | `fr-CA-Caroline`|
| Franska (Kanada) | `fr-CA` | Kvinna | `fr-CA-HarmonieRUS`|
| Franska (Frankrike) | `fr-FR` | Kvinna | `fr-FR-HortenseRUS`|
| Franska (Frankrike) | `fr-FR` | Kvinna | `fr-FR-Julie`|
| Franska (Frankrike) | `fr-FR` | Man | `fr-FR-Paul`|
| Franska (Schweiz) | `fr-CH` | Man | `fr-CH-Guillaume`|
| Tyska (Österrike) | `de-AT` | Man | `de-AT-Michael`|
| Tyska (Tyskland) | `de-DE` | Kvinna | `de-DE-HeddaRUS`|
| Tyska (Tyskland) | `de-DE` | Man | `de-DE-Stefan`|
| Tyska (Schweiz) | `de-CH` | Man | `de-CH-Karsten`|
| Grekiska (Grekland) | `el-GR` | Man | `el-GR-Stefanos`|
| Hebreiska (Israel) | `he-IL` | Man | `he-IL-Asaf`|
| Hindi (Indien) | `hi-IN` | Man | `hi-IN-Hemant`|
| Hindi (Indien) | `hi-IN` | Kvinna | `hi-IN-Kalpana`|
| Ungerska (Ungern) | `hu-HU` | Man | `hu-HU-Szabolcs`|
| Indonesiska (Indonesien) | `id-ID` | Man | `id-ID-Andika`|
| Italienska (Italien) | `it-IT` | Man | `it-IT-Cosimo`|
| Italienska (Italien) | `it-IT` | Kvinna | `it-IT-LuciaRUS`|
| Japanska (Japan) | `ja-JP` | Kvinna | `ja-JP-Ayumi`|
| Japanska (Japan) | `ja-JP` | Kvinna | `ja-JP-HarukaRUS`|
| Japanska (Japan) | `ja-JP` | Man | `ja-JP-Ichiro`|
| Koreanska (Korea) | `ko-KR` | Kvinna | `ko-KR-HeamiRUS`|
| Malajiska (Malaysia) | `ms-MY` | Man | `ms-MY-Rizwan`|
| norska (Bokmål, Norge) | `nb-NO` | Kvinna | `nb-NO-HuldaRUS`|
| Polska (Polen) | `pl-PL` | Kvinna | `pl-PL-PaulinaRUS`|
| Portugisiska (Brasilien) | `pt-BR` | Man | `pt-BR-Daniel`|
| Portugisiska (Brasilien) | `pt-BR` | Kvinna | `pt-BR-HeloisaRUS`|
| Portugisiska (Portugal) | `pt-PT` | Kvinna | `pt-PT-HeliaRUS`|
| Rumänska (Rumänien) | `ro-RO` | Man | `ro-RO-Andrei`|
| Ryska (Ryssland) | `ru-RU` | Kvinna | `ru-RU-EkaterinaRUS`|
| Ryska (Ryssland) | `ru-RU` | Kvinna | `ru-RU-Irina`|
| Ryska (Ryssland) | `ru-RU` | Man | `ru-RU-Pavel`|
| Slovakiska (Slovakien) | `sk-SK` | Man | `sk-SK-Filip`|
| Slovenska (Slovenien) | `sl-SI` | Man | `sl-SI-Lado`|
| Spanska (Mexiko) | `es-MX` | Kvinna | `es-MX-HildaRUS`|
| Spanska (Mexiko) | `es-MX` | Man | `es-MX-Raul`|
| Spanska (Spanien) | `es-ES` | Kvinna | `es-ES-HelenaRUS`|
| Spanska (Spanien) | `es-ES` | Kvinna | `es-ES-Laura`|
| Spanska (Spanien) | `es-ES` | Man | `es-ES-Pablo`|
| Svenska (Sverige) | `sv-SE` | Kvinna | `sv-SE-HedvigRUS`|
| Tamiliska (Indien) | `ta-IN` | Man | `ta-IN-Valluvar`|
| Telugu (Indien) | `te-IN` | Kvinna | `te-IN-Chitra`|
| Thailändska (Thailand) | `th-TH` | Man | `th-TH-Pattara`|
| Turkiska (Turkiet) | `tr-TR` | Kvinna | `tr-TR-SedaRUS`|
| Vietnamesiska (Vietnam) | `vi-VN` | Man | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa`Rösten har ändrats till `en-US-Aria` . Om du använde "Jessa" tidigare, konvertera till "Aria".

> [!TIP]
> Du kan fortsätta att använda den fullständiga tjänst namns mappningen, t. ex. "Microsoft Server Speech Text till tal Voice (en-US, AriaRUS)" i din begäran om tal syntes.

### <a name="customization"></a>Anpassning

Anpassad röst är tillgänglig i standard-och neurala-nivån. De språk som stöds är olika för dessa två nivåer. 

| Språk | Nationell inställning | Standard | Neurala |
|--|--|--|--|
| Kinesiska (mandariner, förenklad) | `zh-CN` | Ja | Ja |
| Kinesiska (mandariner, förenklad), engelsk tvåspråkig | `zh-CN` tvåspråkig | Ja | Ja |
| Engelska (Australien) | `en-AU` | Inga | Ja |
| Engelska (Indien) | `en-IN` | Ja | Ja |
| Engelska (Storbritannien) | `en-GB` | Ja | Ja |
| Engelska (USA) | `en-US` | Ja | Ja |
| Franska (Kanada) | `fr-CA` | Inga | Ja |
| Franska (Frankrike) | `fr-FR` | Ja | Ja |
| Tyska (Tyskland) | `de-DE` | Ja | Ja |
| Italienska (Italien) | `it-IT` | Ja | Ja |
| Japanska (Japan) | `ja-JP` | Inga | Ja |
| Koreanska (Korea) | `ko-KR` | Inga | Ja |
| Portugisiska (Brasilien) | `pt-BR` | Ja | Ja |
| Spanska (Mexiko) | `es-MX` | Ja | Ja |
| Spanska (Spanien) | `es-ES` | Inga | Ja |

Välj rätt språk version som matchar de tränings data du behöver för att träna en anpassad röst modell. Om till exempel de inspelnings data du har talas på engelska med en brittisk accent väljer du `en-GB` .

> [!NOTE]
> Vi stöder inte dubbelriktad modell utbildning i anpassad röst, förutom för Chinese-English bi-språk. Välj "kinesiskt kinesiskt tvåspråkig" om du vill träna en kinesisk röst som även kan tala med engelska. Chinese-English tvåspråkig modell träning med standard metoden är endast tillgänglig i Nord Europa och norra centrala USA. Anpassad röst utbildning för neurala finns i Storbritannien, södra och östra USA.

## <a name="speech-translation"></a>Talöversättning

API: et för **tal översättning** stöder olika språk för översättning av tal till tal och tal till text. Käll språket måste alltid vara från språk tabellen för tal till text. Vilka mål språk som är tillgängliga beror på om översättnings målet är tal eller text. Du kan översätta inkommande tal till fler än [60 språk](https://www.microsoft.com/translator/business/languages/). Det finns en delmängd av språk som är tillgängliga för [tal syntes](language-support.md#text-languages).

### <a name="text-languages"></a>Text språk

| Text språk           | Språkkod |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabiska                  | `ar`          |
| Bangla                  | `bn`          |
| Bosniska (latinsk)         | `bs`          |
| Bulgariska               | `bg`          |
| Kantonesiska (traditionell) | `yue`         |
| Katalanska                 | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`     |
| Kinesiska, traditionell     | `zh-Hant`     |
| Kroatiska                | `hr`          |
| Tjeckiska                   | `cs`          |
| Danska                  | `da`          |
| Nederländska                   | `nl`          |
| Engelska                 | `en`          |
| Estniska                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Finska                 | `fi`          |
| Franska                  | `fr`          |
| Tyska                  | `de`          |
| Grekiska                   | `el`          |
| Gujarati                | `gu`          |
| Haitiska          | `ht`          |
| Hebreiska                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Ungerska               | `hu`          |
| Indonesiska              | `id`          |
| Iriska                   | `ga`          |
| Italienska                 | `it`          |
| Japanska                | `ja`          |
| Kannada                 | `kn`          |
| Swahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Koreanska                  | `ko`          |
| Lettiska                 | `lv`          |
| Litauiska              | `lt`          |
| Madagaskisk                | `mg`          |
| Malajiska                   | `ms`          |
| Malayalam               | `ml`          |
| Maltesiska                 | `mt`          |
| Maori                   | `mi`          |
| Marathi                 | `mr`          |
| Norska               | `nb`          |
| Persiska                 | `fa`          |
| Polska                  | `pl`          |
| Portugisiska (Brasilien)     | `pt-br`       |
| Portugisiska (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumänska                | `ro`          |
| Ryska                 | `ru`          |
| Samoan                  | `sm`          |
| Serbiska (kyrillisk)      | `sr-Cyrl`     |
| Serbiska (latinsk) (Serbien)         | `sr-Latn`     |
| Slovakiska                  | `sk`          |
| Slovenska               | `sl`          |
| Spanska                 | `es`          |
| Svenska                 | `sv`          |
| Tahitian                | `ty`          |
| Tamilska                   | `ta`          |
| Telugu                  | `te`          |
| Thailändska                    | `th`          |
| Tonganska                  | `to`          |
| Turkiska                 | `tr`          |
| Ukrainska               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamesiska              | `vi`          |
| Walesiska                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Talarigenkänning

I följande tabell visas vilka språk som stöds för de olika Talarigenkänning API: erna. Mer information om Talarigenkänning finns i [översikten](speaker-recognition-overview.md) .

| Språk | Locale (BCP-47) | Text beroende verifiering | Text oberoende verifiering | Text oberoende identifiering |
|----|----|----|----|----|
|Engelska (USA)  |  sv-SE  |  ja  |  ja  |  ja |
|Kinesiska (mandariner, förenklad) | zh-CN     |     saknas |     ja |     ja|
|Engelska (Australien)     | en – AU     | saknas     | ja     | ja|
|Engelska (Kanada)     | en-CA     | saknas |     ja |     ja|
|Engelska (Storbritannien)     | en-GB     | saknas     | ja     | ja|
|Franska (Kanada)     | fr-CA     | saknas     | ja |     ja|
|Franska (Frankrike)     | fr-FR     | saknas     | ja     | ja|
|Tyska (Tyskland)     | de-DE     | saknas     | ja     | ja|
|Italienska | it-IT     |     saknas     | ja |     ja|
|Japanska     | ja-JP | saknas     | ja     | ja|
|Portugisiska (Brasilien) | pt-BR |     saknas |     ja |     ja|
|Spanska (Mexiko)     | ES – MX     | saknas |     ja |     ja|
|Spanska (Spanien)     | es-ES | saknas     | ja |     ja|

## <a name="next-steps"></a>Nästa steg

* [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
* [Se känna igen tal i C #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
