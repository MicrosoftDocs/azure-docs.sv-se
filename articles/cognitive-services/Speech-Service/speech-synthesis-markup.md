---
title: SSML (Speech syntes Markup Language) – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Använd tal syntes märknings språk för att kontrol lera uttal och prosody i text-till-tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: aa5e499df976c08fef8f0b81eff1e189eebb5503
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026316"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Förbättra syntesen med SSML (Speech syntes Markup Language)

SSML (Speech syntes Markup Language) är ett XML-baserat kodspråk som låter utvecklare ange hur indata ska konverteras till syntetiskt tal med hjälp av text till tal-tjänsten. Jämfört med oformaterad text gör SSML att utvecklare kan finjustera färgdjup, uttal, tal frekvens, volym och annat text till tal-utdata. Normal interpunktion, till exempel pausa efter en punkt eller med rätt intonation när en mening slutar med ett frågetecken, hanteras automatiskt.

Tal tjänst implementeringen av SSML baseras på World Wide Web Consortiumens [tal syntess språk Version 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Kinesiska, japanska och koreanska tecken räknas som två tecken för fakturering. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standard, neurala och anpassade röster

Välj mellan standard-och neurala röster eller skapa en egen anpassad röst som är unik för din produkt eller ditt varumärke. 75 + standard röster är tillgängliga på över 45 språk och nationella inställningar, och 5 neurala-röster är tillgängliga på fyra språk och nationella inställningar. En fullständig lista över språk som stöds, nationella inställningar och röster (neurala och standard) finns i [språk stöd](language-support.md).

Mer information om standard-, neurala-och anpassade röster finns i [text till tal-översikt](text-to-speech.md).


> [!NOTE]
> Du kan höra röster i olika format och läsa exempel texten på [sidan text till tal](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Specialtecken

När du använder SSML bör du tänka på att specialtecken, till exempel citat tecken, apostrofer och hakparenteser måste föregås. Mer information finns i [Extensible Markup Language (XML) 1,0: bilaga D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>SSML-element som stöds

Varje SSML-dokument skapas med SSML-element (eller taggar). Dessa element används för att justera bredd, prosody, volym och mycket annat. Följande avsnitt innehåller information om hur varje element används och när ett element krävs eller är valfritt.

> [!IMPORTANT]
> Glöm inte att använda dubbla citat tecken runt attributvärden. Standarder för välformulerad, giltig XML kräver att attributvärden omges av dubbla citat tecken. Till exempel `<prosody volume="90">` är ett välformulerat, giltigt element, men `<prosody volume=90>` det är inte. SSML kan inte identifiera attributvärden som inte är inom citat tecken.

## <a name="create-an-ssml-document"></a>Skapa ett SSML-dokument

`speak` är rot elementet och **krävs** för alla SSML-dokument. `speak`-Elementet innehåller viktig information, till exempel version, språk och definition av terminologi för markering.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `version` | Anger den version av SSML-specifikationen som används för att tolka dokument markeringen. Den aktuella versionen är 1,0. | Obligatorisk |
| `xml:lang` | Anger språket för rot dokumentet. Värdet får innehålla gemener, gemener och versaler (t. ex. `en` ) eller språk koden och land/region (t. ex. `en-US` ). | Obligatorisk |
| `xmlns` | Anger den URI till dokumentet som definierar ord listan (element typerna och attributnamnet) för SSML-dokumentet. Aktuell URI är http://www.w3.org/2001/10/synthesis . | Obligatorisk |

## <a name="choose-a-voice-for-text-to-speech"></a>Välj röst för text till tal

`voice`Elementet måste anges. Den används för att ange vilken röst som används för text till tal.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Obligatorisk |

**Exempel**

> [!NOTE]
> I det här exemplet används `en-US-JennyNeural` rösten. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Använd flera röster

I `speak` -elementet kan du ange flera röster för text till tal-utdata. Dessa röster kan vara på olika språk. För varje röst måste texten vara figursatt i ett- `voice` element.

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `name` | Identifierar rösten som används för text till tal-utdata. En fullständig lista över vilka röster som stöds finns i [språk stöd](language-support.md#text-to-speech). | Obligatorisk |

> [!IMPORTANT]
> Flera röster är inkompatibla med ord gränsens funktion. Ord gränsen måste inaktive ras för att flera röster ska kunna användas.

### <a name="disable-word-boundary"></a>Inaktivera ord gränser

Beroende på språket Speech SDK anger du `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` egenskapen till `false` på en instans av `SpeechConfig` objektet.

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns i <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns i <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns i <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns i <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns i <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns i <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns i <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

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

## <a name="adjust-speaking-styles"></a>Ändra tal format

> [!IMPORTANT]
> Justeringen av tal format fungerar bara med neurala-röster.

Som standard används text-till-tal-tjänsten för att syntetisera text med ett neutralt tal format för både standard-och neurala-röster. Med neurala-röster kan du ändra tal formatet till Express olika känslor som cheerfulness, empati och lugn, eller optimera rösten för olika scenarier, t. ex. kund tjänst, newscasting och röst assistent, med hjälp av- `mstts:express-as` elementet. Detta är ett valfritt element som är unikt för tal tjänsten.

För närvarande stöds anpassning av format justeringar för dessa neurala-röster:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Förhandsgranskningsvyn
* `zh-CN-XiaohanNeural` Förhandsgranskningsvyn
* `zh-CN-XiaomoNeural` Förhandsgranskningsvyn
* `zh-CN-XiaoxuanNeural` Förhandsgranskningsvyn
* `zh-CN-XiaoruiNeural` Förhandsgranskningsvyn

Du kan ändra utseendet på tal formatet så att det bättre passar ditt användnings fall. Du kan ange ett starkare eller mjukare format med `styledegree` om du vill göra talet mer lättfattliga programspecifika eller subdued.

För närvarande stöds anpassning av format justeringar för dessa neurala-röster:
* `zh-CN-XiaoxiaoNeural`

Förutom att justera tal formaten och format graderna kan du också justera `role` parametern så att rösten imiterar en annan ålder och kön. Till exempel kan en hane-röst öka bredden och ändra intonation till imitera en hona röst.

För närvarande stöds roll uppspelnings justeringar för dessa neurala-röster:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Ovanstående ändringar tillämpas på menings nivå, och format och roll-spelar varierar med röst. Om en stil eller roll-uppspelning inte stöds returnerar tjänsten tal på det neutrala standard sättet. Du kan se vilka formatmallar och roll uppspelningar som stöds för varje röst via [röst listans API](rest-text-to-speech.md#get-a-list-of-voices) eller genom att använda en kod fri plattform för att [skapa ljud innehåll](https://aka.ms/audiocontentcreation) .

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
> För tillfället `styledegree` stöder endast zh-cn-XiaoxiaoNeural. `role` stöder endast zh-CN-XiaomoNeural och zh-CN-XiaoxuanNeural.

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `style` | Anger tal formatet. För närvarande är det röst alternativ att tala om format. | Krävs om du justerar tal formatet för en neurala röst. Om du använder `mstts:express-as` , måste format tillhandahållas. Om ett ogiltigt värde har angetts ignoreras det här elementet. |
| `styledegree` | Anger intensiteten för tal format. **Godkända värden**: 0,01 till 2. Standardvärdet är 1 vilket innebär den fördefinierade stil intensiteten. Den minsta enheten är 0,01 vilket resulterar i en något tendens för mål formatet. Värdet 2 resulterar i en dubblerad standard stil.  | Valfritt (för tillfället `styledegree` stöder endast zh-cn-XiaoxiaoNeural.)|
| `role` | Anger den talande roll som spelas upp. Rösten fungerar som en annan ålder och kön.  | Valfritt (för tillfället `role` stöder endast zh-cn-XiaomoNeural och zh-cn-XiaoxuanNeural.)|

Använd den här tabellen för att avgöra vilka tal format som stöds för varje neurala röst.

| Röst                   | Format                     | Beskrivning                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Uttrycker en formell, trygg och auktoritativ ton för nyhets leverans |
|                         | `style="newscast-casual"` | Uttrycker en mångsidig och vardaglig ton för allmän nyhets leverans        |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
|                         | `style="chat"`            | Uttrycker en vardaglig och avslappnad ton                         |
|                         | `style="cheerful"`        | Uttrycker en positiv och trevlig ton                         |
|                         | `style="empathetic"`      | Uttrycker en uppfattning om Caring och förståelse               |
| `en-US-JennyNeural`     | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
|                         | `style="chat"`            | Uttrycker en vardaglig och avslappnad ton                         |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="newscast"`        | Uttrycker en mångsidig och vardaglig ton för allmän nyhets leverans   |
| `en-US-GuyNeural`       | `style="newscast"`        | Uttrycker en formell och professionell ton för nya berättarröstinspelningar |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Uttrycker en häftig, samlad och bestående attityd när du talar. Ton, färgdjup och prosody är mycket mer enhetligt jämfört med andra typer av tal.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Uttrycker en formell och professionell ton för nya berättarröstinspelningar |
|                         | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
|                         | `style="assistant"`       | Uttrycker en varm och avslappnad ton för digitala assistenter    |
|                         | `style="chat"`            | Uttrycker en vardaglig och avslappnad ton för CHI2TEST           |
|                         | `style="calm"`            | Uttrycker en häftig, samlad och bestående attityd när du talar. Ton, färgdjup och prosody är mycket mer enhetligt jämfört med andra typer av tal.                                |
|                         | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="sad"`             | Uttrycker en sorrowful-ton, med högre avstånd, mindre intensitet och lägre Vocal energi. Vanliga indikatorer för den här känslo skulle vara whimpers eller grått under tal.            |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.          |
|                         | `style="affectionate"`    | Uttrycker en varm och affectionate ton, med högre färgdjup och Vocal energi. Högtalaren är i ett tillstånd där du kan dra uppmärksamheten till lyssnaren. Den "personlighet" av högtalaren är ofta av betydelse.          |
|                         | `style="gentle"`          | Uttrycker en viss, avslutningen och Pleasant ton, med lägre färgdjup och Vocal energi         |
|                         | `style="lyrical"`         | Uttrycker känslor i ett Melodic-och sentimental-sätt         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Uttrycker en vänlig och användbar ton för kund support  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Uttrycker en häftig, samlad och bestående attityd när du talar. Ton, färgdjup och prosody är mycket mer enhetligt jämfört med andra typer av tal.    |
|                         | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="sad"`             | Uttrycker en sorrowful-ton, med högre avstånd, mindre intensitet och lägre Vocal energi. Vanliga indikatorer för den här känslo skulle vara whimpers eller grått under tal.            |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="sad"`             | Uttrycker en sorrowful-ton, med högre avstånd, mindre intensitet och lägre Vocal energi. Vanliga indikatorer för den här känslo skulle vara whimpers eller grått under tal.            |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic-och despondent-ton med lägre färgdjup och energi    |
|                         | `style="embarrassed"`     | Uttrycker en osäker och tveka ton när talare känner sig osäker   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="sad"`             | Uttrycker en sorrowful-ton, med högre avstånd, mindre intensitet och lägre Vocal energi. Vanliga indikatorer för den här känslo skulle vara whimpers eller grått under tal.            |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.    |
|                         | `style="embarrassed"`     | Uttrycker en osäker och tveka ton när talare känner sig osäker   |
|                         | `style="affectionate"`    | Uttrycker en varm och affectionate ton, med högre färgdjup och Vocal energi. Högtalaren är i ett tillstånd där du kan dra uppmärksamheten till lyssnaren. Den "personlighet" av högtalaren är ofta av betydelse.          |
|                         | `style="gentle"`          | Uttrycker en viss, avslutningen och Pleasant ton, med lägre färgdjup och Vocal energi         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic-och despondent-ton med lägre färgdjup och energi    |
|                         | `style="gentle"`          | Uttrycker en viss, avslutningen och Pleasant ton, med lägre färgdjup och Vocal energi         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Uttrycker en takt och entusiastisk ton, med högre färgdjup och Vocal energi                         |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |
|                         | `style="disgruntled"`     | Uttrycker en disdainful och en klagad ton. Tal för den här känslo visar avnöjes och bevarar.              |
|                         | `style="serious"`         | Uttrycker en strikt och kommando rads ton. Talare låter ofta styvare och mycket mindre avslappnad med fast takt.    |
|                         | `style="depressed"`       | Uttrycker en melancholic-och despondent-ton med lägre färgdjup och energi    |
|                         | `style="gentle"`          | Uttrycker en viss, avslutningen och Pleasant ton, med lägre färgdjup och Vocal energi         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Uttrycker en sorrowful-ton, med högre avstånd, mindre intensitet och lägre Vocal energi. Vanliga indikatorer för den här känslo skulle vara whimpers eller grått under tal.            |
|                         | `style="angry"`           | Uttrycker en Angry-och uppretad-ton, med lägre färgdjup, högre intensitet och högre Vocal energi. Högtalaren är i ett tillstånd att vara Irate, avstängt och felfritt.       |
|                         | `style="fearful"`         | Uttrycker en Scared-och nerv ton, med högre färgdjup, högre Vocal energi och snabbare hastighet. Högtalaren är i läget tenseness och uneasiness.                          |

Använd den här tabellen för att avgöra vilka roller som stöds för varje neurala röst.

| Röst                   | Roll                       | Beskrivning                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | Rösten imiterar en ung vuxen hona.                 |
|                         | `role="OlderAdultMale"`    | Rösten imiterar en äldre vuxen hane.                   |
|                         | `role="Girl"`              | Rösten imiterar en flicka.                               |
|                         | `role="Boy"`               | Rösten imiterar en pojke.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | Rösten imiterar en ung vuxen hona.                 |
|                         | `role="OlderAdultFemale"`  | Rösten imiterar en äldre vuxen hona.                 |
|                         | `role="OlderAdultMale"`    | Rösten imiterar en äldre vuxen hane.                   |

**Exempel**

Det här SSML-kodfragmentet illustrerar hur `<mstts:express-as>` elementet används för att ändra tal formatet till `cheerful` .

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

Det här SSML-kodfragmentet illustrerar hur `styledegree` attributet används för att ändra intensiteten för tal format för XiaoxiaoNeural.
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

Det här SSML-kodfragmentet illustrerar hur `role` attributet används för att ändra roll-Play för XiaomoNeural.
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

## <a name="add-or-remove-a-breakpause"></a>Lägg till eller ta bort en paus/pausa

Använd `break` elementet för att infoga pauser (eller brytningar) mellan ord, eller förhindra att pauser automatiskt läggs till av tjänsten text till tal.

> [!NOTE]
> Använd det här elementet om du vill åsidosätta standard beteendet för text till tal (TTS) för ett ord eller en fras om det syntetiska talet för ordet eller frasen låter unnaturlig. Ställ in `strength` på `none` för att förhindra en prosodic rast, som infogas automatiskt av text till tal-tjänsten.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `strength` | Anger den relativa varaktigheten för en paus med något av följande värden:<ul><li>inget</li><li>x-svaga</li><li>svaga</li><li>medel (standard)</li><li>kraftfull</li><li>x – stark</li></ul> | Valfritt |
| `time` | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. värdet ska vara mindre än 5000ms. Exempel på giltiga värden är `2s` och `500ms` | Valfritt |

| Styrka                      | Beskrivning |
|-------------------------------|-------------|
| Ingen, eller om inget värde anges | 0 MS        |
| x-svaga                        | 250 MS      |
| svaga                          | 500 ms      |
| medel                        | 750 ms      |
| kraftfull                        | 1000 MS     |
| x – stark                      | 1250 MS     |

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Lägg till tystnad

Använd `mstts:silence` elementet för att infoga pauser före eller efter text, eller mellan de två intilliggande meningarna.

> [!NOTE]
>Skillnaden mellan `mstts:silence` och `break` är som `break` kan läggas till var som helst i texten, men tystnad fungerar bara i början eller slutet av inmatad text, eller på gräns 2 närliggande meningar.


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `type` | Anger platsen för tystnad som ska läggas till: <ul><li>`Leading` – i början av texten </li><li>`Tailing` – i slutet av texten </li><li>`Sentenceboundary` – mellan intilliggande meningar </li></ul> | Obligatorisk |
| `Value` | Anger den absoluta varaktigheten för en paus i sekunder eller millisekunder. värdet ska vara mindre än 5000ms. Exempel på giltiga värden är `2s` och `500ms` | Obligatorisk |

**Exempel** I det här exemplet `mtts:silence` används för att lägga till 200 MS av tystnaden mellan två meningar.
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

`p` och `s` element används för att ange stycken respektive meningar. Om dessa element saknas bestämmer text till tal-tjänsten automatiskt strukturen för SSML-dokumentet.

`p`Elementet kan innehålla text och följande element:,,,,,, `audio` `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` och `s` .

`s`Elementet kan innehålla text och följande element:,,,,, `audio` `break` `phoneme` `prosody` `say-as` `mstts:express-as` och `sub` .

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Använd fonem för att förbättra uttal

`ph`Elementet används för fonetiskt uttal i SSML-dokument. `ph`Elementet får bara innehålla text, inga andra element. Tillhandahålla alltid läsliga tal som reserv.

Fonetiska alfabet består av telefoner, som består av bokstäver, siffror eller tecken, ibland i kombination. Varje telefon beskriver ett unikt ljud av tal. Detta är i motsats till det latinska alfabetet, där en bokstav kan representera flera talade ljud. Överväg de olika uttalna av bokstaven "c" i orden "Candy" och "upphör", eller de olika uttal av bokstavs kombinationen "th" i orden "sak" och "de".

> [!NOTE]
> Fonem-taggen stöds inte för de här 5 rösterna (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural och MT-MT-GarceNeural) för tillfället.

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `alphabet` | Anger det fonetiska alfabetet som ska användas vid syntetiskt uttal av strängen i `ph` attributet. Strängen som anger alfabetet måste anges med små bokstäver. Följande är de möjliga alfabet som du kan ange.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationellt fonetiskt alfabet</a></li><li>`sapi`&ndash; [Fonetiskt alfabet i Speech service](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Universal Phone-uppsättning</a></li></ul><br>Alfabetet gäller endast i- `phoneme` elementet.. | Valfritt |
| `ph` | En sträng som innehåller telefoner som anger uttal av ordet i `phoneme` elementet. Om den angivna strängen innehåller okända telefoner avvisar tjänsten text till tal (TTS) hela SSML-dokumentet och genererar ingen av tal utmatningen som anges i dokumentet. | Krävs om du använder fonem. |

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

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Använd anpassat lexikon för att förbättra uttal

Ibland kan inte text till tal-tjänsten uttala ett ord. Till exempel namnet på ett företag eller en medicinsk term. Utvecklare kan definiera hur enkla entiteter ska läsas i SSML med `phoneme` `sub` taggarna och. Men om du behöver definiera hur flera entiteter ska läsas, kan du skapa ett anpassat lexikon med `lexicon` taggen.

> [!NOTE]
> Anpassat lexikon stöder UTF-8-kodning för närvarande.

> [!NOTE]
> Anpassat lexikon stöds inte för de här 5 rösterna (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural och MT-MT-GarceNeural) för tillfället.


**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribut**

| Attribut | Beskrivning                               | Obligatorisk/valfri |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adressen för det externa PLS-dokumentet. | Krävs.           |

**Användning**

Om du vill definiera hur flera entiteter ska läsas kan du skapa ett anpassat lexikon, som lagras som en XML-eller pls-fil. Följande är en exempel-XML-fil.

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

`lexicon`Elementet innehåller minst ett- `lexeme` element. Varje `lexeme` -element innehåller minst ett `grapheme` element och ett eller flera `grapheme` element `alias` , och `phoneme` . `grapheme`Elementet innehåller text som beskriver <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">Orthography </a>. `alias`Elementen används för att ange uttal av en akronym eller en förkortad term. `phoneme`Elementet innehåller text som beskriver hur `lexeme` uttalas.

Det är viktigt att Observera att du inte kan ange uttal av en fras direkt med hjälp av det anpassade lexikonet. Om du behöver ange uttal för en akronym eller en förkortad term, anger du först ett `alias` och sedan associerar du `phoneme` med det `alias` . Exempel:

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

Du kan också ange ett förväntat `alias` eller förkortat villkor för förkortningen. Exempel:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`Elementet får inte innehålla blank steg när du använder IPA.

Mer information om den anpassade lexikon filen finns i avsnittet uttal av ord [lexikons specifikation (pls) Version 1,0](https://www.w3.org/TR/pronunciation-lexicon/).

Publicera sedan din anpassade lexikon fil. Även om det inte finns några begränsningar för var filen kan lagras rekommenderar vi att du använder [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md).

När du har publicerat ditt anpassade lexikon kan du referera till det från din SSML.

> [!NOTE]
> `lexicon`Elementet måste finnas i `voice` elementet.

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

När du använder det här anpassade lexikonet kommer "BTW" att läsas som "på väg". "Oskadligi" kommer att läsas med den tillhandahållna IPA "bɛ ˈ ni ː Nji".

**Begränsningar**
- Fil storlek: den maximala storleks gränsen för den anpassade fil storleken är 100 KB, om den överskrider den här storleken kommer syntes förfrågan att Miss förväntas.
- Uppdatering av lexikon-cache: anpassat lexikon cachelagras med URI som nyckel på TTS-tjänst när den läses in första gången. Det går inte att läsa in ett lexikon med samma URI inom 15 minuter, så den anpassade lexikon ändringen måste vänta högst 15 minuter innan den börjar gälla.

**Fonetiska uppsättningar för tal tjänst**

I exemplet ovan använder vi det internationella fonetiska alfabetet, även kallat IPA telefon uppsättning. Vi rekommenderar att utvecklare använder IPA, eftersom det är den internationella standarden. För vissa IPA-tecken har de "fördelade" och "desammansatt"-versionen när de representeras med Unicode. Anpassat lexikon har endast stöd för uppdelade Unicode.

Med tanke på att IPA inte är lätt att komma ihåg definierar tal tjänsten en fonetisk uppsättning för sju språk ( `en-US` ,,,,, `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` och `zh-TW` ).

Du kan använda `sapi` as-Vale för `alphabet` attributet med anpassade lexikon som visas nedan:

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

Mer information om den detaljerade röst tjänsten fonetiskt alfabet finns i [röst tjänstens fonetiska uppsättningar](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Justera prosody

`prosody`Elementet används för att ange ändringar av bredd, profil, intervall, hastighet, varaktighet och volym för text till tal-utdata. `prosody`Elementet kan innehålla text och följande element:,,,,,, `audio` `break` `p` `phoneme` `prosody` `say-as` `sub` och `s` .

Eftersom prosodic-attributvärden kan variera över ett brett intervall, tolkar tal igenkännings verktyget de tilldelade värdena som ett förslag på vad de faktiska prosodic-värdena för den valda rösten ska vara. Text till tal-tjänsten begränsar eller byter ut värden som inte stöds. Exempel på värden som inte stöds är ett färgdjup på 1 MHz eller en volym på 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `pitch` | Anger textens bas linje bredd. Du kan uttrycka bredden som:<ul><li>Ett absolut värde, uttryckt som ett tal följt av "Hz" (Hertz). Till exempel `<prosody pitch="600Hz">some text</prosody>`.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" och följt av "Hz" eller "St", som anger ett belopp för att ändra bredden. Till exempel: `<prosody pitch="+80Hz">some text</prosody>` eller `<prosody pitch="-2st">some text</prosody>` . "St" anger att ändrings enheten är semitone, som är hälften av en ton (ett halv steg) i standard skalan för diatonic.</li><li>Ett konstant värde:<ul><li>x-låg</li><li>börjar</li><li>medel</li><li>hög</li><li>x – hög</li><li>standard</li></ul></li></ul> | Valfritt |
| `contour` |Kon turen stöder nu både neurala-och standard röster. Kontur representerar ändringar i färgdjup. Dessa ändringar visas som en matris med mål vid angivna tids positioner i tal utmatningen. Varje mål definieras av uppsättningar av parameter par. Exempel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Det första värdet i varje parameter uppsättning anger platsen för bredd ändringen som en procent andel av längden på texten. Det andra värdet anger hur mycket du vill höja eller sänka avståndet med ett relativt värde eller ett uppräknings värde för färgdjup (se `pitch` ). | Valfritt |
| `range` | Ett värde som representerar text områdets avstånd. Du kan uttrycka `range` använda samma absoluta värden, relativa värden eller uppräknings värden som används för att beskriva `pitch` . | Valfritt |
| `rate` | Anger textens tal hastighet. Du kan uttrycka `rate` som:<ul><li>Ett relativt värde, uttryckt som ett tal som fungerar som en multiplikator för standardvärdet. Värdet *1* resulterar till exempel i ingen ändring av hastigheten. Värdet *0,5* resulterar i en halving av hastigheten. Värdet *3* resulterar i en rese frekvens.</li><li>Ett konstant värde:<ul><li>x – långsam</li><li>långsam</li><li>medel</li><li>snabb</li><li>x-fast</li><li>standard</li></ul></li></ul> | Valfritt |
| `duration` | Tids perioden som ska förflyta när tal syntes tjänsten läser texten, i sekunder eller millisekunder. Till exempel *2s* eller *1800ms*. Varaktighet stöder endast standard röster.| Valfritt |
| `volume` | Anger volym nivån för tal rösten. Du kan uttrycka volymen som:<ul><li>Ett absolut värde, uttryckt som ett tal i intervallet 0,0 till 100,0, från *tyst* till *högsta*. Till exempel 75. Standardvärdet är 100,0.</li><li>Ett relativt värde, uttryckt som ett tal som föregås av "+" eller "-" som anger ett belopp för att ändra volymen. Till exempel + 10 eller-5,5.</li><li>Ett konstant värde:<ul><li>trafiken</li><li>x-Soft</li><li>programvaru</li><li>medel</li><li>starka</li><li>x-högt</li><li>standard</li></ul></li></ul> | Valfritt |

### <a name="change-speaking-rate"></a>Ändra tal hastighet

Tal frekvensen kan tillämpas på neurala-röster och standard röster på ord-eller menings nivå.

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

Volym ändringar kan tillämpas på standard-röster på ord-eller menings nivå. Volym ändringar kan bara tillämpas på neurala-röster på menings nivå.

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

### <a name="change-pitch"></a>Ändra avstånd

Du kan ändra bredden på standard-röster på ord-eller menings nivå. Förändringar av förändringar kan bara tillämpas på neurala-röster på menings nivå.

**Exempel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändra bredd för kontur

> [!IMPORTANT]
> Förändringar av kon tur kon turen stöds nu med neurala-röster.

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
## <a name="say-as-element"></a>Säg som-element

`say-as` är ett valfritt element som anger innehålls typen (t. ex. antal eller datum) för elementets text. Detta ger vägledning till tal syntes motorn om hur du uttalar texten.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `interpret-as` | Anger innehålls typen för elementets text. En lista med typer finns i tabellen nedan. | Obligatorisk |
| `format` | Innehåller ytterligare information om den exakta formateringen av elementets text för innehålls typer som kan ha tvetydiga format. SSML definierar format för innehålls typer som använder dem (se tabellen nedan). | Valfritt |
| `detail` | Anger detalj nivån som ska läsas. Det här attributet kan till exempel begära att tal syntes motorn uttalar skiljetecken. Inga standard värden har definierats för `detail` . | Valfritt |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Följande är de innehålls typer som stöds för `interpret-as` `format` attributen och. Inkludera `format` endast attributet om `interpret-as` har angetts till datum och tid.

| tolka som | format | Tolkning |
|--------------|--------|----------------|
| `address` | | Texten talas som en adress. Tal syntes motorn uttalar:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Som "Jag är på 150th-domstolen norra östra Redmond Washington". |
| `cardinal`, `number` | | Texten talas som ett kardinal nummer. Tal syntes motorn uttalar:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Det finns tre alternativ. " |
| `characters`, `spell-out` | | Texten talas som enskilda bokstäver (rättstavade). Tal syntes motorn uttalar:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />T. ex. "T E S." |
| `date` | DMY, MDÅ, YMD, ådm, YM, My, MD, DM, d, m, y | Texten talas som ett datum. `format`Attributet anger datumets format (*d = dag, m = månad och y = år*). Tal syntes motorn uttalar:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Som "idag är den Nineteenth oktober 2016". |
| `digits`, `number_digit` | | Texten talas som en sekvens med enskilda siffror. Tal syntes motorn uttalar:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Som "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Texten talas som ett bråk tals tal. Tal syntes motorn uttalar:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Som "tre åttondelar av en tum". |
| `ordinal` | | Texten talas som ett ordnings tal. Tal syntes motorn uttalar:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Som "Välj det tredje alternativet". |
| `telephone` | | Texten talas som ett telefonnummer. `format`Attributet får innehålla siffror som representerar en landskod. Till exempel "1" för USA eller "39" för Italien. Tal syntes motorn kan använda den här informationen för att vägleda sitt uttal av ett telefonnummer. Telefonnumret kan också innehålla lands koden, och i så fall prioriteras lands koden i `format` . Tal syntes motorn uttalar:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As "My Number är rikt nummer 8 8 8 5 5 5 1 2 1 2". |
| `time` | hms12, hms24 | Texten talas som en tid. `format`Attributet anger om tiden anges med en 12-timmarsformat (hms12) eller en 24-timmarsklocka (hms24). Använd kolon för att avgränsa tal som representerar timmar, minuter och sekunder. Följande är giltiga tids exempel: 12:35, 1:14:32, 08:15 och 02:50:45. Tal syntes motorn uttalar:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Som "tåget är i en del av fyra A M." |

**Användning**

`say-as`Elementet får bara innehålla text.

**Exempel**

Tal syntes motorn läser följande exempel som "din första förfrågan var för ett rum den Nineteenth oktober 20 10 med tidig ankomst till 12 35 PM."

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

`audio` är ett valfritt element som gör att du kan infoga MP3-ljud i ett SSML-dokument. Bröd texten i ljud elementet kan innehålla oformaterad text eller SSML som talas om ljud filen inte är tillgänglig eller kan spelas upp. Dessutom `audio` kan elementet innehålla text och följande element:,,,,,, `audio` `break` `p` `s` `phoneme` `prosody` `say-as` och `sub` .

Alla ljud som ingår i SSML-dokumentet måste uppfylla följande krav:

* MP3-filen måste vara värd för en HTTPS-slutpunkt som är tillgänglig för Internet. HTTPS krävs, och den domän som är värd för MP3-filen måste presentera ett giltigt betrott TLS/SSL-certifikat.
* MP3-filen måste vara en giltig MP3-fil (MPEG v2).
* Bit hastigheten måste vara 48 kbit/s.
* Samplings frekvensen måste vara 16 000 Hz.
* Den sammanlagda tiden för alla text-och ljudfiler i ett enskilt svar får inte överstiga 90 (90) sekunder.
* MP3-filen får inte innehålla kundspecifik eller annan känslig information.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatorisk/valfri                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Anger ljud filens plats/URL. | Krävs om du använder ljud elementet i ditt SSML-dokument. |

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

## <a name="add-background-audio"></a>Lägg till bakgrunds ljud

Med `mstts:backgroundaudio` -elementet kan du lägga till bakgrunds ljud till dina SSML-dokument (eller blanda en ljudfil med text till tal). Med `mstts:backgroundaudio` kan du repetera en ljudfil i bakgrunden, tona in i början av text till tal och tona ut i slutet av text till tal.

Om bakgrunds ljudet som tillhandahålls är kortare än text-till-tal-eller övertoningen, kommer den att upprepas. Om det är längre än text till tal, stoppas det när toningen är färdig.

Endast en bakgrunds ljud fil tillåts per SSML-dokument. Du kan dock blanda `audio` taggar i `voice` elementet för att lägga till ytterligare ljud till ditt SSML-dokument.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribut**

| Attribut | Beskrivning | Obligatorisk/valfri |
|-----------|-------------|---------------------|
| `src` | Anger plats/URL för bakgrunds ljud filen. | Krävs om du använder bakgrunds ljud i ditt SSML-dokument. |
| `volume` | Anger bakgrunds ljud filens volym. **Godkända värden**: `0` till `100` inklusiv. Standardvärdet är `1`. | Valfritt |
| `fadein` | Anger bakgrunds ljudets varaktighet "tona in" som millisekunder. Standardvärdet är `0` , vilket motsvarar ingen toning i. **Godkända värden**: `0` till `10000` inklusiv.  | Valfritt |
| `fadeout` | Anger bakgrunds ljudets varaktighet tonar ut i millisekunder. Standardvärdet är `0` , vilket motsvarar ingen toning. **Godkända värden**: `0` till `10000` inklusiv.  | Valfritt |

**Exempel**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Bok märkes element

Med bok märkes elementet kan du infoga anpassade markörer i SSML för att hämta förskjutningen för varje markör i ljud strömmen.
Vi kommer inte att läsa bok märkes elementen.
Bok märkes elementet kan användas för att referera till en speciell plats i text-eller taggkontrollen.

> [!NOTE]
> `bookmark` elementet fungerar bara för `en-US-AriaNeural` röst i regionen USA, västra 2 ( `westus2` ) för tillfället.

**Syntax**

```xml
<bookmark mark="string"/>
```

**Attribut**

| Attribut | Beskrivning                                   | Obligatorisk/valfri                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Anger referens texten för `bookmark` elementet. | Krävs. |

**Exempel**

Som exempel kanske du vill veta tids förskjutningen för varje blomma ord enligt följande

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Hämta bok märke med Speech SDK

Du kan prenumerera på `BookmarkReached` händelsen i tal-SDK för att hämta bok märkes förskjutningar.

> [!NOTE]
> `BookmarkReached` händelsen är endast tillgänglig sedan tal SDK-versionen 1.16.0.

`BookmarkReached` händelser aktive ras när ljud data för utdata blir tillgängliga, vilket är snabbare än uppspelning till en utdataenhet.

* `AudioOffset` rapporterar hur lång tid det tar att mata ut ljudet mellan början av syntes och bok märkes elementet. Detta mäts i hundra-nanosekunder-enheter (HNS) med 10 000 HNS motsvarande 1 millisekunder.
* `Text` är referens texten för Bookmark-elementet, som är den sträng som du anger i `mark` attributet.

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns i <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

I exemplet ovan `BookmarkReached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns i <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

I exemplet ovan `BookmarkReached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns i <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

I exemplet ovan `BookmarkReached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns i <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

I exemplet ovan `bookmark_reached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Mer information finns i <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

I exemplet ovan `bookmarkReached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Mer information finns i <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

I exemplet ovan `BookmarkReached` kommer händelsen att utlösas två gånger och konsolens utdata kommer att
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Mer information finns i <a href="https://docs.microsoft.com/swift/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>.

---

## <a name="next-steps"></a>Nästa steg

* [Språk stöd: röster, nationella inställningar, språk](language-support.md)
