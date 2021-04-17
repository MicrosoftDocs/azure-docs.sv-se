---
title: Speech Synthesis Markup Language (SSML) – Speech Service
titleSuffix: Azure Cognitive Services
description: Använda Speech Synthesis Markup Language för att styra uttal och prosody i text till tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1d21691af4d52892f507695a56331816b14bf517
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588385"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Förbättra syntesen med Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) är ett XML-baserat märkspråk som gör att utvecklare kan ange hur indatatext konverteras till syntetiserat tal med hjälp av text till tal-tjänsten. Jämfört med oformaterad text gör SSML att utvecklare kan finjustera tonhöjd, uttal, talfrekvens, volym och mer av text till tal-utdata. Normala skiljetecken, till exempel att pausa efter en punkt eller använda rätt intonation när en mening slutar med ett frågetecken, hanteras automatiskt.

Speech Service-implementeringen av SSML baseras på World Wide Web Consortium Speech [Synthesis Markup Language version 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [Prissättning.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="neural-and-custom-voices"></a>Neurala och anpassade röster

Använd en mänsklig neural röst eller skapa en egen anpassad röst som är unik för din produkt eller ditt varumärke. En fullständig lista över språk, språk och röster som stöds finns i [språkstöd.](language-support.md) Mer information om neurala och anpassade röster finns i [Översikt över text till tal.](text-to-speech.md)


> [!NOTE]
> Du kan höra röster i olika format och tonhöjder läsa exempeltext [med hjälp Text till tal sidan](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Specialtecken

När du använder SSML bör du komma ihåg att specialtecken, till exempel citattecken, apostrofer och hakparenteser måste hoppas över. Mer information finns i [Extensible Markup Language (XML) 1.0: Bilaga D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML-dokument skapas med SSML-element (eller taggar). De här elementen används för att justera tonhöjd, prosody, volym med mera. I följande avsnitt beskrivs hur varje element används och när ett element krävs eller är valfritt.

> [!IMPORTANT]
> Glöm inte att använda dubbla citattecken runt attributvärden. Standarder för välformade, giltiga XML kräver att attributvärden omges av dubbla citattecken. Är till `<prosody volume="90">` exempel ett välformade, giltigt element, men `<prosody volume=90>` det är det inte. SSML kanske inte känner igen attributvärden som inte är inom citattecken.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak` är rotelementet och krävs **för** alla SSML-dokument. Elementet `speak` innehåller viktig information, till exempel version, språk och definition av påläggsvokabulär.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `version` | Anger vilken version av SSML-specifikationen som används för att tolka dokumentpålägget. Den aktuella versionen är 1.0. | Obligatorisk |
| `xml:lang` | Anger språket i rotdokumentet. Värdet kan innehålla en språkkod med gemener, två bokstäver (till exempel ), eller språkkoden och `en` land/region med versaler (till exempel `en-US` ). | Obligatorisk |
| `xmlns` | Anger URI:n till dokumentet som definierar påläggsvokabulären (elementtyper och attributnamn) för SSML-dokumentet. Den aktuella URI:en är http://www.w3.org/2001/10/synthesis . | Obligatorisk |

## <a name="choose-a-voice-for-text-to-speech"></a>Välja en röst för text till tal

Elementet `voice` är obligatoriskt. Den används för att ange rösten som används för text till tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text-till-tal-utdata. En fullständig lista över röster som stöds finns i [Språkstöd.](language-support.md#text-to-speech) | Obligatorisk |

**Exempel**

> [!NOTE]
> I det här exemplet används `en-US-JennyNeural` rösten. En fullständig lista över röster som stöds finns i [Språkstöd.](language-support.md#text-to-speech)

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använda flera röster

I elementet `speak` kan du ange flera röster för text-till-tal-utdata. Dessa röster kan finnas på olika språk. För varje röst måste texten vara omsluten i ett `voice` -element.

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text-till-tal-utdata. En fullständig lista över röster som stöds finns i [Språkstöd.](language-support.md#text-to-speech) | Obligatorisk |

> [!IMPORTANT]
> Flera röster är inkompatibla med funktionen för ordgräns. Funktionen för ordgräns måste inaktiveras för att flera röster ska kunna användas.

### <a name="disable-word-boundary"></a>Inaktivera ordgräns

Beroende på språket för Speech SDK anger du egenskapen `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` till på en instans av `false` `SpeechConfig` objektet.

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>i .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>i .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>i .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>i .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>i .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>i .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>i .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Justera talformat

Som standard syntetiserar text till tal-tjänsten text med ett neutralt talformat för neurala röster. Du kan justera talstilen för att uttrycka olika känslor, till exempel känslor, empati och lugn, eller optimera rösten för olika scenarier som kundtjänst, nyhetssändningar och röstassistent med hjälp av `mstts:express-as` elementet . Det här är ett valfritt element som är unikt för Speech-tjänsten.

För närvarande stöds justeringar av talstilen för följande neurala röster:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (Förhandsversion)
* `zh-CN-XiaohanNeural` (Förhandsversion)
* `zh-CN-XiaomoNeural` (Förhandsversion)
* `zh-CN-XiaoxuanNeural` (Förhandsversion)
* `zh-CN-XiaoruiNeural` (Förhandsversion)

Intensiteten i talstilen kan ändras ytterligare så att den passar ditt användningsfall bättre. Du kan ange ett starkare eller mjukare format med `styledegree` för att göra talet mer uttrycksfullt eller undermålat. För närvarande stöds justeringar av talstilen för neurala kinesiska röster (mandarin, förenklad).

Förutom att justera talstilarna och stilgraden kan du även justera parametern så att rösten `role` imiterar en annan ålder och ett annat kön. En mans röst kan till exempel höja rösten och ändra intonationen till att imitera en kvinnas röst, men röstnamnet ändras inte. För närvarande stöds rolljusteringar för dessa neurala kinesiska röster (mandarin, förenklad):
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Ändringarna ovan tillämpas på meningsnivå och formaten och rollspelen varierar beroende på röst. Om ett format eller rollspel inte stöds returnerar tjänsten tal på standardneutralt talsätt. Du kan se vilka format och roller [](rest-text-to-speech.md#get-a-list-of-voices) som stöds för varje röst via API:et för röstlistor eller via den [kodfria](https://aka.ms/audiocontentcreation) Skapa ljudinnehåll plattformen.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> För tillfället stöder endast `styledegree` kinesiska (mandarin, förenklad) neurala röster. `role` har endast stöd för zh-CN-XiauanNeuan och zh-CN-XiaoxuanNeuan.

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `style` | Anger talstilen. För närvarande är talformat röstspecifika. | Krävs om du justerar talstilen för en neural röst. Om du `mstts:express-as` använder måste stil anges. Om ett ogiltigt värde anges ignoreras det här elementet. |
| `styledegree` | Anger intensiteten för talstilen. **Godkända värden:** 0,01 till 2. Standardvärdet är 1, vilket innebär den fördefinierade stilintensiteten. Den minsta enheten är 0,01, vilket resulterar i en något tendens för målformatet. Värdet 2 resulterar i en fördubbling av standardformatintensiteten.  | Valfritt (för tillfället stöder `styledegree` endast kinesiska (mandarin, förenklad) neurala röster.)|
| `role` | Anger det talade rollspel. Rösten fungerar som en annan ålder och ett annat kön, men röstnamnet ändras inte.  | Valfritt (för tillfället har `role` endast stöd för zh-CN-Xiaaznezh och zh-CN-XiaoxuanNe anomalie.)|

Använd den här tabellen för att avgöra vilka talformat som stöds för varje neural röst.

| Röst                   | Format                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Uttrycker en formell, säker och auktoritativ ton för nyhetsleverans |
|                         | `style="newscast-casual"` | Uttrycker en mångsidig och informell ton för allmän nyhetsleverans        |
|                         | `style="narration-professional"` | Uttrycka en professionell, objektiv ton för innehållsläsning        |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupport  |
|                         | `style="chat"`            | Uttrycker en informell och avslappnad ton                         |
|                         | `style="cheerful"`        | Uttrycker en positiv och glad ton                         |
|                         | `style="empathetic"`      | Uttrycker en känsla av omståelse och förståelse               |
| `en-US-JennyNeural`     | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupporten  |
|                         | `style="chat"`            | Uttrycker en informell och avslappnad ton                         |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="newscast"`        | Uttrycker en mångsidig och informell ton för allmän nyhetsleverans   |
| `en-US-GuyNeural`       | `style="newscast"`        | Uttrycker en formell och professionell ton för att berätta nyheter |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Uttrycker en cool, samlad och sammansatt attityd när du talar. Tonfall, tonfall och prosody är mycket mer enhetlig jämfört med andra typer av tal.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Uttrycker en formell och professionell ton för att berätta nyheter |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupport  |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="chat"`            | Uttrycker en informell och avslappnad ton för chit-chat           |
|                         | `style="calm"`            | Uttrycker en cool, samlad och sammansatt attityd när du talar. Tonfall, tonfall och prosody är mycket mer enhetlig jämfört med andra typer av tal.                                |
|                         | `style="cheerful"`        | Uttrycker en positiv och entusiastisk ton, med högre tonhöjd och röstkraft                         |
|                         | `style="sad"`             | Uttrycker en bra tonfall med högre tonhöjd, mindre intensitet och lägre röstkraft. Vanliga indikatorer på den här känslorna skulle vara smpers eller känslor under tal.            |
|                         | `style="angry"`           | Uttrycker en stört och retad ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och störande.       |
|                         | `style="fearful"`         | Uttrycker en orolig och nervös ton, med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite lugnare och mycket mindre avslappnad med fast takt.          |
|                         | `style="affectionate"`    | Uttrycker en varm och färgad ton, med högre tonhöjd och röstkraft. Talaren är i ett tillstånd av att dra till sig lyssnarens uppmärksamhet. Talarens "personlighet" är ofta endearing av sin natur.          |
|                         | `style="gentle"`          | Uttrycker en glad, glad och glad ton med lägre tonhöjd och röstkraft         |
|                         | `style="lyrical"`         | Uttrycker känslor på ett sentimentalt sätt         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kundsupporten  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Uttrycker en cool, samlad och sammansatt attityd när du talar. Tonfall, tonfall och prosody är mycket mer enhetlig jämfört med andra typer av tal.    |
|                         | `style="cheerful"`        | Uttrycker en positiv och engagerande ton med högre tonhöjd och röstkraft                         |
|                         | `style="sad"`             | Uttrycker en ydig tonfall med högre tonhöjd, mindre intensitet och lägre röstkraft. Vanliga indikatorer på den här känslorna skulle vara klumpiga eller undermrena under tal.            |
|                         | `style="angry"`           | Uttrycker en stämt och upphöjd ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och förlamad.       |
|                         | `style="fearful"`         | Uttrycker en nedtoning och en röstton med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite tåligare och mycket mindre avslappnad med fast takt.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Uttrycker en positiv och entusiastisk ton, med högre tonhöjd och röstkraft                         |
|                         | `style="sad"`             | Uttrycker en bra tonfall med högre tonhöjd, mindre intensitet och lägre röstkraft. Vanliga indikatorer på den här känslan är smpers eller underhållare under tal.            |
|                         | `style="angry"`           | Uttrycker en stämt och upphöjd ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och störande.       |
|                         | `style="fearful"`         | Uttrycker en nedtoning och en röstton med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite tåligare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic and despondent-ton med lägre tonhöjd och energi    |
|                         | `style="embarrassed"`     | Uttrycker en osäker och tveksam ton när talaren känner sig osäker   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Uttrycker en positiv och engagerande ton med högre tonhöjd och röstkraft                         |
|                         | `style="sad"`             | Uttrycker en ydig tonfall med högre tonhöjd, mindre intensitet och lägre röstkraft. Vanliga indikatorer på den här känslorna skulle vara klumpiga eller undermrena under tal.            |
|                         | `style="angry"`           | Uttrycker en stört och retad ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och störande.       |
|                         | `style="fearful"`         | Uttrycker en orolig och nervös ton med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite lugnare och mycket mindre avslappnad med fast takt.    |
|                         | `style="embarrassed"`     | Uttrycker en osäker och tveksam ton när talaren känner sig osäker   |
|                         | `style="affectionate"`    | Uttrycker en varm och färgad ton, med högre tonhöjd och röstkraft. Talaren är i ett tillstånd av att dra till sig lyssnarens uppmärksamhet. Talarens "personlighet" är ofta endearing av sin natur.          |
|                         | `style="gentle"`          | Uttrycker en glad, glad och glad ton med lägre tonhöjd och röstkraft         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Uttrycker en positiv och engagerande ton med högre tonhöjd och röstkraft                         |
|                         | `style="angry"`           | Uttrycker en stört och retad ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och störande.       |
|                         | `style="fearful"`         | Uttrycker en nedtoning och en röstton med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite tåligare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic and despondent-ton med lägre tonhöjd och energi    |
|                         | `style="gentle"`          | Uttrycker en glad, vänlig och färgad ton med lägre tonhöjd och röstkraft         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Uttrycker en positiv och entusiastisk ton, med högre tonhöjd och röstkraft                         |
|                         | `style="angry"`           | Uttrycker en stämt och upphöjd ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och förlamad.       |
|                         | `style="fearful"`         | Uttrycker en nedtoning och en röstton med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |
|                         | `style="disgruntled"`     | Uttrycker en föraktlig och klagande ton. Tal av den här känslan visar displeasure och förakt.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommandoton. Talaren låter ofta lite tåligare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic and despondent-ton med lägre tonhöjd och energi    |
|                         | `style="gentle"`          | Uttrycker en glad, vänlig och färgad ton med lägre tonhöjd och röstkraft         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Uttrycker en bra tonfall med högre tonhöjd, mindre intensitet och lägre röstkraft. Vanliga indikatorer på den här känslorna skulle vara klumpiga eller undermrena under tal.            |
|                         | `style="angry"`           | Uttrycker en stört och retad ton, med lägre tonhöjd, högre intensitet och högre röstkraft. Talaren är i ett tillstånd av att vara irate, displeased och störande.       |
|                         | `style="fearful"`         | Uttrycker en orolig och nervös ton, med högre tonhöjd, högre röstkraft och snabbare hastighet. Talaren är i ett tillstånd av tempus och oförselighet.                          |

Använd den här tabellen för att kontrollera vilka roller som stöds och deras definitioner.

|Roll                     | Beskrivning                |
|-------------------------|----------------------------|
|`role="Girl"`            | Rösten liknar en kvinna. |
|`role="Boy"`             | Rösten liknar en pojkar. |
|`role="YoungAdultFemale"`| Rösten liknar en ung, vuxen kvinna.|
|`role="YoungAdultMale"`  | Rösten liknar en ung, vuxen man.|
|`role="OlderAdultFemale"`| Rösten liknar en äldre vuxen kvinna.|
|`role="OlderAdultMale"`  | Rösten liknar en äldre vuxen man.|
|`role="SeniorFemale"`    | Rösten liknar en erfaren kvinna.|
|`role="SeniorMale"`      | Rösten liknar en erfaren man.|


**Exempel**

Det här SSML-kodfragmentet `<mstts:express-as>` illustrerar hur elementet används för att ändra talstilen till `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Det här SSML-kodfragmentet illustrerar hur attributet används för att ändra intensiteten i talstilen för `styledegree` XiaxoaoNexo.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Det här SSML-kodfragmentet illustrerar hur attributet används för att `role` ändra rollspel för XiauaNeodo.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Lägga till eller ta bort en paus

Använd elementet för att infoga pauser (eller pauser) mellan ord eller förhindra pauser som läggs till `break` automatiskt av text till tal-tjänsten.

> [!NOTE]
> Använd det här elementet för att åsidosätta standardbeteendet för text till tal (TTS) för ett ord eller en fras om det syntetiserade talet för det ordet eller frasen inte låter onaturligt. Ange `strength` till för att förhindra en `none` prosodic-brytning som infogas automatiskt av text till tal-tjänsten.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `strength` | Anger den relativa varaktigheten för en paus med något av följande värden:<ul><li>inget</li><li>x-svag</li><li>Svag</li><li>medium (standard)</li><li>Stark</li><li>x-strong</li></ul> | Valfritt |
| `time` | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. Det här värdet ska vara mindre än 5 000 ms. Exempel på giltiga värden `2s` är och `500ms` | Valfritt |

| Styrka                      | Description |
|-------------------------------|-------------|
| Ingen, eller om inget värde har angetts | 0 ms        |
| x-svag                        | 250 ms      |
| Svag                          | 500 ms      |
| medel                        | 750 ms      |
| Stark                        | 1 000 ms     |
| x-strong                      | 1250 ms     |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Lägg till tystnad

Använd elementet `mstts:silence` för att infoga pauser före eller efter text eller mellan de två intilliggande meningarna.

> [!NOTE]
>Skillnaden mellan och är att kan läggas till på valfri plats i texten, men tystnad fungerar bara i början eller slutet av indatatexten, eller vid gränsen på 2 angränsande `mstts:silence` `break` `break` meningar.


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `type` | Anger platsen för tystnad som ska läggas till: <ul><li>`Leading` – i början av texten </li><li>`Tailing` – i slutet av texten </li><li>`Sentenceboundary` – mellan angränsande meningar </li></ul> | Obligatorisk |
| `Value` | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. Det här värdet ska vara mindre än 5 000 ms. Exempel på giltiga värden `2s` är och `500ms` | Obligatorisk |

**Exempel** I det här exemplet `mtts:silence` används för att lägga till 200 ms tystnad mellan två meningar.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Ange stycken och meningar

`p` - `s` och -element används för att ange stycken respektive meningar. I avsaknad av dessa element avgör text till tal-tjänsten automatiskt strukturen för SSML-dokumentet.

Elementet `p` kan innehålla text och följande element: , , , , `audio` , , , och `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

Elementet `s` kan innehålla text och följande element: , , , , `audio` , , och `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Syntax**

```XML
<p></p>
<s></s>
```

**Exempel**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Använda fonem för att förbättra uttal

Elementet `ph` används för fonetiskt uttal i SSML-dokument. Elementet `ph` får bara innehålla text, inga andra element. Tillhandahåll alltid tal som kan läsas av människor som reserv.

Fonetiska alfabet består av telefoner, som består av bokstäver, siffror eller tecken, ibland i kombination. Varje telefon beskriver ett unikt talljud. Detta skiljer sig från det latinska alfabetet, där alla bokstäver kan representera flera talade ljud. Tänk på de olika uttalen av bokstaven "c" med orden "godis" och "upphör" eller de olika uttalen av bokstavskombinationen "th" i orden "thing" och "those".

> [!NOTE]
> Phonemes-taggen stöds inte för dessa 5 röster (et-EE-AnuNelv, ga-IE-OrlaNe lv, lt-LT-OnaNe lv, lv-EveritaNe mt-GarceNe lv) för tillfället.

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `alphabet` | Anger det fonetiska alfabetet som ska användas vid synkronisering av uttalet av strängen i `ph` attributet. Strängen som anger alfabetet måste anges med gemener. Följande är de möjliga alfabet som du kan ange.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationellt fonetiska alfabet</a></li><li>`sapi`&ndash; [Taltjänstens fonetiska alfabet](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Universal Phone Set</a></li></ul><br>Alfabetet gäller endast `phoneme` för i -elementet. | Valfritt |
| `ph` | En sträng som innehåller telefoner som anger uttalet av ordet i `phoneme` elementet . Om den angivna strängen innehåller okända telefoner avvisar TTS-tjänsten (text till tal) hela SSML-dokumentet och genererar ingen av de talutdata som anges i dokumentet. | Krävs om du använder fonem. |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Använda anpassad lexikon för att förbättra uttalet

Ibland kan text till tal-tjänsten inte exakt uttala ett ord. Till exempel namnet på ett företag eller en medicinsk term. Utvecklare kan definiera hur enskilda entiteter läses i SSML med `phoneme` taggarna `sub` och . Men om du behöver definiera hur flera entiteter ska läsas kan du skapa en anpassad lexikon med `lexicon` taggen .

> [!NOTE]
> Custom lexicon stöder för närvarande UTF-8-kodning.

> [!NOTE]
> Custom lexicon stöds inte för dessa 5 röster (et-EE-AnuNeua, ga-IE-OrlaNedon, lt-LT-OnaNeison, lv-LV-EveritaNegräns och mt-MT-GarceNeison) för tillfället.


**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribut**

| Attribut | Beskrivning                               | Obligatoriskt/valfritt |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adressen till det externa PLS-dokumentet. | Krävs.           |

**Användning**

Om du vill definiera hur flera entiteter läses kan du skapa en anpassad lexikon som lagras som en XML- eller PLS-fil. Följande är en XML-exempelfil.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Elementet `lexicon` innehåller minst ett `lexeme` element. Varje `lexeme` element innehåller minst ett element och ett eller flera element , och `grapheme` `grapheme` `alias` `phoneme` . Elementet `grapheme` innehåller text som beskriver <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografin </a>. Elementen `alias` används för att ange uttalet av en förkortning eller en förkortad term. Elementet `phoneme` innehåller text som beskriver hur `lexeme` uttalas.

Observera att du inte kan ange uttalet av en fras direkt med hjälp av det anpassade lexikonet. Om du behöver ange uttalet för en förkortning eller en förkortad term anger du först ett `alias` och associerar sedan `phoneme` med den `alias` . Exempel:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

Du kan också ange din `alias` förväntade akronym eller förkortade term direkt. Exempel:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> Elementet `phoneme` får inte innehålla blanksteg när du använder IPA.

Mer information om anpassad lexikonfil finns [i Uttal Lexicon Specification (PLS) Version 1.0.](https://www.w3.org/TR/pronunciation-lexicon/)

Publicera sedan din anpassade lexikonfil. Även om vi inte har några begränsningar för var den här filen kan lagras rekommenderar vi att du använder [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md).

När du har publicerat ditt anpassade lexikon kan du referera till det från din SSML.

> [!NOTE]
> Elementet `lexicon` måste finnas inuti elementet `voice` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

När du använder den här anpassade lexikonen läses "SMILE" som "By the way". "Benigni" kommer att läsas med den angivna IPA:en "bmsnji".

**Begränsningar**
- Filstorlek: storleksgränsen för anpassade Lexicon-filer är högst 100 kB. Om den här storleken är större än så misslyckas syntesbegäran.
- Uppdatering av lexikoncache: custom lexicon cachelagras med URI som nyckel i TTS-tjänsten när den läses in för första gången. Lexikon med samma URI läse inte in igen inom 15 minuter, så anpassad lexikonändring måste vänta högst 15 minuter för att gälla.

**Fonetiska uppsättningar för Speech-tjänsten**

I exemplet ovan använder vi det internationella fonetiska alfabetet, även kallat IPA-telefonuppsättning. Vi föreslår att utvecklare använder IPA eftersom det är den internationella standarden. För vissa IPA-tecken har de versionen "förkompilerad" och "decomposed" när de representeras med Unicode. Custom Lexicon har endast stöd för de nedkompilerade Unicode-formaten.

Med tanke på att IPA inte är lätt att komma ihåg definierar Speech-tjänsten en fonetiska uppsättning för sju språk ( `en-US` , , , , , , och `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` ).

Du kan använda `sapi` som värde för `alphabet` attributet med anpassade lexikoner enligt nedan:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Mer information om det detaljerade fonetiska alfabetet för Speech-tjänsten finns i [Taltjänstens fonetiska uppsättningar.](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Justera prosody

Elementet `prosody` används för att ange ändringar för tonhöjd, intervall, frekvens, varaktighet och volym för text-till-tal-utdata. Elementet `prosody` kan innehålla text och följande element: , , , , `audio` , , , och `break` `p` `phoneme` `prosody` `say-as` `sub` `s` .

Eftersom prosodic-attributvärden kan variera över ett brett intervall tolkar taligenkänningen de tilldelade värdena som ett förslag på vad de faktiska prosodic-värdena för den valda rösten ska vara. Text till tal-tjänsten begränsar eller ersätter värden som inte stöds. Exempel på värden som inte stöds är en pitch på 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `pitch` | Anger baslinjehöjden för texten. Du kan uttrycka din tonhöjd som:<ul><li>Ett absolut värde, uttryckt som ett tal följt av "Hz" (Hertz). Till exempel `<prosody pitch="600Hz">some text</prosody>`.</li><li>Ett relativt värde, uttryckt som ett tal före "+" eller "-" och följt av "Hz" eller "st", som anger hur mycket som ska ändra tonhöjden. Till exempel: `<prosody pitch="+80Hz">some text</prosody>` eller `<prosody pitch="-2st">some text</prosody>` . "st" anger att ändringsenheten är semikolon, vilket är hälften av en ton (ett halvsteg) på den diatoniska standardskalan.</li><li>Ett konstant värde:<ul><li>x-low</li><li>Låg</li><li>medel</li><li>hög</li><li>x-high</li><li>standard</li></ul></li></ul> | Valfritt |
| `contour` |Now Now har stöd för både neurala och standardröster. En stapel representerar ändringar i tonhöjden. Dessa ändringar representeras som en matris med mål vid angivna tidspositioner i talutdata. Varje mål definieras av uppsättningar med parameterpar. Exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje uppsättning parametrar anger platsen för tonhöjdsändringen som en procentandel av textens varaktighet. Det andra värdet anger hur mycket som ska höjas eller sänka tonhöjden med hjälp av ett relativt värde eller ett uppräkningsvärde för pitch (se `pitch` ). | Valfritt |
| `range` | Ett värde som representerar intervallet för textens tonhöjd. Du kan uttrycka `range` med samma absoluta värden, relativa värden eller uppräkningsvärden som används för att beskriva `pitch` . | Valfritt |
| `rate` | Anger talfrekvensen för texten. Du kan uttrycka `rate` som:<ul><li>Ett relativt värde, uttryckt som ett tal som fungerar som en multiplikator av standardvärdet. Till exempel resulterar värdet *1* i att hastigheten inte ändras. Värdet *0,5* resulterar i en halvering av hastigheten. Värdet *3 resulterar* i en tredling av priset.</li><li>Ett konstantvärde:<ul><li>x-långsam</li><li>Långsam</li><li>medel</li><li>snabb</li><li>x-fast</li><li>standard</li></ul></li></ul> | Valfritt |
| `duration` | Den tidsperiod som ska förfluta när tjänsten för talsyntes (TTS) läser texten, i sekunder eller millisekunder. Till exempel *2:or* eller *1800 ms*. Varaktighet stöder endast standardröster.| Valfritt |
| `volume` | Anger volymnivån för talrösten. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt som ett tal i intervallet 0,0 till 100,0, från *tystaste* till *högsta*. Till exempel 75. Standardvärdet är 100.0.</li><li>Ett relativt värde, uttryckt som ett tal före "+" eller "-" som anger hur mycket volymen ska ändras. Till exempel +10 eller -5,5.</li><li>Ett konstantvärde:<ul><li>Tyst</li><li>x-soft</li><li>Mjuk</li><li>medel</li><li>Högt</li><li>x-hög</li><li>standard</li></ul></li></ul> | Valfritt |

### <a name="change-speaking-rate"></a>Ändra talfrekvens

Talfrekvensen kan tillämpas på neurala röster och standardröster på ord- eller meningsnivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Ändra volym

Volymändringar kan tillämpas på standardröster på ord- eller meningsnivå. Volymändringar kan bara tillämpas på neurala röster på meningsnivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Ändra tonhöjd

Tonhöjdsändringar kan tillämpas på standardröster på ord- eller meningsnivå. Tonhöjdsändringar kan bara tillämpas på neurala röster på meningsnivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändra tonhöjd

> [!IMPORTANT]
> Pitch-ändringar stöds nu med neurala röster.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as-element

`say-as` är ett valfritt element som anger innehållstypen (till exempel nummer eller datum) för elementets text. Detta ger vägledning till talsyntesmotorn om hur texten ska uttalas.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `interpret-as` | Anger innehållstypen för elementets text. En lista över typer finns i tabellen nedan. | Obligatorisk |
| `format` | Innehåller ytterligare information om den exakta formateringen av elementets text för innehållstyper som kan ha tvetydiga format. SSML definierar format för innehållstyper som använder dem (se tabellen nedan). | Valfritt |
| `detail` | Anger detaljnivån som ska talas. Det här attributet kan till exempel begära att talsyntesmotorn avskriver skiljetecken. Inga standardvärden har definierats för `detail` . | Valfritt |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Följande är de innehållstyper som stöds för `interpret-as` attributen `format` och . Inkludera `format` attributet endast om `interpret-as` har angetts till datum och tid.

| tolka som | format | Tolkning |
|--------------|--------|----------------|
| `address` | | Texten talas som en adress. Talsyntesmotorn uttalar:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Som "I'm at 150th court north east redmond washington." |
| `cardinal`, `number` | | Texten talas som ett kardinaltal. Talsyntesmotorn uttalar:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Som "Det finns tre alternativ". |
| `characters`, `spell-out` | | Texten talas som enskilda bokstäver (utstavade). Talsyntesmotorn uttalar:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Som "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Texten talas som ett datum. Attributet `format` anger datumformatet (*d=day, m=month och y=year*). Talsyntesmotorn avlöser:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Idag är oktober 2 000 160.000". |
| `digits`, `number_digit` | | Texten talas som en sekvens med enskilda siffror. Talsyntesmotorn avlöser:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Som "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Texten talas som ett bråktal. Talsyntesmotorn avlöser:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Som "tre åttondelar av en tum". |
| `ordinal` | | Texten talas som ett ordningstal. Talsyntesmotorn avlöser:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Som "Välj det tredje alternativet". |
| `telephone` | | Texten talas som ett telefonnummer. Attributet `format` kan innehålla siffror som representerar en landskod. Till exempel "1" för USA eller "39" för Italien. Talsyntesmotorn kan använda den här informationen för att vägleda uttalet av ett telefonnummer. Telefonnumret kan också innehålla landskoden, och i så fall har det företräde framför landskoden i `format` . Talsyntesmotorn avlöser:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Som "My number is area code eight eight eight five five five one two one two". |
| `time` | hms12, hms24 | Texten talas som en tid. Attributet anger om tiden anges med `format` en 12-timmarsklocka (hms12) eller en 24-timmarsklocka (hms24). Använd ett kolon för att avgränsa tal som representerar timmar, minuter och sekunder. Följande är giltiga tidsexempel: 12:35, 1:14:32, 08:15 och 02:50:45. Talsyntesmotorn uttalar:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Som "The train avviker vid fyra A M." |

**Användning**

Elementet `say-as` får bara innehålla text.

**Exempel**

Talsyntesmotorn talar följande exempel som "Din första begäran var för ett rum i oktober kl. 20 med tidig ankomst kl. 15.50".

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Lägg till inspelat ljud

`audio` är ett valfritt element som gör att du kan infoga MP3-ljud i ett SSML-dokument. Brödtexten i ljudelementet kan innehålla oformaterad text eller SSML-markering som talas om ljudfilen inte är tillgänglig eller inte kan spelas upp. Dessutom kan elementet `audio` innehålla text och följande element: , , , , , `audio` , , och `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Ljud som ingår i SSML-dokumentet måste uppfylla följande krav:

* MP3 måste finnas på en HTTPS-slutpunkt som är tillgänglig via Internet. HTTPS krävs och domänen som är värd för MP3-filen måste innehålla ett giltigt, betrott TLS/SSL-certifikat.
* MP3 måste vara en giltig MP3-fil (MPEG v2).
* Bithastigheten måste vara 48 kbit/s.
* Samplingsfrekvensen måste vara 16 000 Hz.
* Den sammanlagda totala tiden för alla text- och ljudfiler i ett enda svar får inte överstiga nittio (90) sekunder.
* MP3 får inte innehålla kundspecifik eller annan känslig information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatoriskt/valfritt                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Anger plats/URL för ljudfilen. | Krävs om du använder ljudelementet i SSML-dokumentet. |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Lägg till bakgrundsljud

Med `mstts:backgroundaudio` elementet kan du lägga till bakgrundsljud i dina SSML-dokument (eller blanda en ljudfil med text till tal). Med kan du loopa en ljudfil i bakgrunden, tona in i början av text till tal och tona ut i slutet av `mstts:backgroundaudio` text till tal.

Om bakgrundsljudet är kortare än text till tal eller tona ut loopas det. Om det är längre än text-till-tal stoppas den när toningen är klar.

Endast en bakgrundsljudfil tillåts per SSML-dokument. Du kan dock intersperse-taggar `audio` i elementet för att lägga till ytterligare ljud i `voice` SSML-dokumentet.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatoriskt/valfritt |
|-----------|-------------|---------------------|
| `src` | Anger plats/URL för bakgrundsljudfilen. | Krävs om du använder bakgrundsljud i SSML-dokumentet. |
| `volume` | Anger volymen för bakgrundsljudfilen. **Godkända värden:** `0` till `100` inkluderande. Standardvärdet är `1`. | Valfritt |
| `fadein` | Anger varaktigheten för bakgrundsljudet "tona in" som millisekunder. Standardvärdet är `0` , vilket motsvarar ingen toning. **Godkända värden:** `0` till `10000` inkluderande.  | Valfritt |
| `fadeout` | Anger varaktigheten för bakgrundsljudet tonas ut i millisekunder. Standardvärdet är `0` , vilket motsvarar ingen toning. **Godkända värden:** `0` till `10000` inkluderande.  | Valfritt |

**Exempel**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Bokmärkeselement

Med bokmärkeselementet kan du infoga anpassade markörer i SSML för att hämta förskjutningen av varje markör i ljudströmmen.
Vi kommer inte att läsa upp bokmärkeselementen.
Bokmärkeselementet kan användas för att referera till en specifik plats i text- eller taggsekvensen.

> [!NOTE]
> `bookmark` -elementet fungerar bara `en-US-AriaNeural` för rösten just nu.

**Syntax**

```xml
<bookmark mark="string"/>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatorisk/valfri                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Anger referenstexten för `bookmark` elementet. | Krävs. |

**Exempel**

Du kanske till exempel vill veta tidsförskjutningen för varje blommans ord enligt följande

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Hämta bokmärke med hjälp av Speech SDK

Du kan prenumerera på händelsen `BookmarkReached` i Speech SDK för att hämta bokmärkesförskjutningarna.

> [!NOTE]
> `BookmarkReached` -händelsen är endast tillgänglig sedan Speech SDK version 1.16.0.

`BookmarkReached` händelser utlöses när utdata från ljuddata blir tillgängliga, vilket är snabbare än uppspelning till en utdataenhet.

* `AudioOffset` rapporterar utdataljudens förfluten tid mellan början av syntesen och bokmärkeselementet. Detta mäts i hundra nanosekundersenheter (HNS) med 10 000 HNS motsvarande 1 millisekunder.
* `Text` är referenstexten för bokmärkeselementet, vilket är den sträng som du anger i `mark` attributet.

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>i .

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

I exemplet med SSML ovan `BookmarkReached` utlöses händelsen två gånger och konsolens utdata blir
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>i .

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

I exemplet med SSML ovan `BookmarkReached` utlöses händelsen två gånger och konsolens utdata blir
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>i .

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

I exemplet med SSML ovan utlöses händelsen två gånger och konsolens utdata `BookmarkReached` blir
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>i .

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

I exemplet med SSML ovan utlöses händelsen två gånger och konsolens utdata `bookmark_reached` blir
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>i .

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

I exemplet med SSML ovan utlöses händelsen två gånger och konsolens utdata `bookmarkReached` blir
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>i .

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

I exemplet med SSML ovan utlöses händelsen två gånger och konsolens utdata `BookmarkReached` blir
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>i .

---

## <a name="next-steps"></a>Nästa steg

* [Språkstöd: röster, språk, språk](language-support.md)
