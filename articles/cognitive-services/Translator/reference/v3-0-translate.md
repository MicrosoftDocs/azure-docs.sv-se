---
title: Översättnings metod för Translator
titleSuffix: Azure Cognitive Services
description: Förstå parametrarna för parametrar, sidhuvud och brödtext för översättnings metoden för Azure Cognitive Services Translator för att översätta text.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: d46fef0159b983f2685be40e2a0ab5471b96883b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895449"
---
# <a name="translator-30-translate"></a>Translator 3,0: Översätt

Översätter text.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

### <a name="required-parameters"></a>Obligatoriska parametrar

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Den version av API: t som klienten begär. Värdet måste vara <code>3.0</code> .</td>
  </tr>
  <tr>
    <td>på</td>
    <td><em>Obligatorisk parameter</em>.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de <a href="./v3-0-languages.md">språk som stöds</a> som ingår i <code>translation</code> omfånget. Använd till exempel för <code>to=de</code> att översätta till tyska.<br/>Det går att översätta till flera språk samtidigt genom att upprepa parametern i frågesträngen. Använd till exempel för <code>to=de&to=it</code> att översätta till tyska och italienska.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Valfria parametrar

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>Från</td>
    <td><em>Valfri parameter</em>.<br/>Anger språket för inmatad text. Hitta vilka språk som kan omvandlas från genom att leta upp <a href="./v3-0-languages.md">språk som stöds</a> med hjälp av <code>translation</code> omfånget. Om <code>from</code> parametern inte anges används automatisk språk identifiering för att fastställa käll språket. <br/><br/>Du måste använda- <code>from</code> parametern i stället för automatisk identifiering när du använder funktionen för <a href="/azure/cognitive-services/translator/dynamic-dictionary">dynamiskt ord listor</a> .</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Valfri parameter</em>.<br/>Definierar om texten som ska översättas är oformaterad text eller HTML-text. Alla HTML måste vara ett välformulerat, fullständigt element. Möjliga värden är: <code>plain</code> (standard) eller <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Valfri parameter</em>.<br/>En sträng som anger en kategori (domän) för översättningen. Den här parametern används för att hämta översättningar från ett anpassat system som skapats med <a href="../customization.md">anpassad översättare</a>. Lägg till kategori-ID från din anpassad översättning <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projekt information</a> till den här parametern för att använda det distribuerade anpassade systemet. Standardvärdet är: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska behandlas i översättningar. Möjliga värden är: <code>NoAction</code> (standard) <code>Marked</code> eller <code>Deleted</code> . Information om hur du hanterar svordomar finns i <a href="#handle-profanity">svordoms hantering</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska markeras i översättningar. Möjliga värden är: <code>Asterisk</code> (standard) eller <code>Tag</code> . Information om hur du hanterar svordomar finns i <a href="#handle-profanity">svordoms hantering</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill ta med justerings projektion från käll texten till översatt text. Möjliga värden är: <code>true</code> eller <code>false</code> (standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Valfri parameter</em>.<br/>Anger om du vill ta med menings gränser för inmatad text och översatt text. Möjliga värden är: <code>true</code> eller <code>false</code> (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Valfri parameter</em>.<br/>Anger ett återställnings språk om språket i inmatad text inte kan identifieras. Automatisk identifiering av språk används när <code>from</code> parametern utelämnas. Om identifieringen Miss lyckas <code>suggestedFrom</code> kommer språket att antas.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger den inmatade textens skript.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skript för den översatta texten.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Valfri parameter</em>.<br/>Anger att tjänsten kan återgå till ett allmänt system när det inte finns något anpassat system. Möjliga värden är: <code>true</code> (standard) eller <code>false</code> .<br/><br/><code>allowFallback=false</code> anger att översättningen bara ska använda system som är utbildade för den <code>category</code> som anges i begäran. Om en översättning för språk X till språk Y kräver länkning genom ett Pivot-språk E, måste alla system i kedjan (X->E och E->Y) vara anpassade och ha samma kategori. Om det inte finns något system med en viss kategori returnerar begäran en 400-status kod. <code>allowFallback=true</code> anger att tjänsten kan återgå till ett allmänt system när det inte finns något anpassat system.
</td>
  </tr>
</table> 

Begärandehuvuden innehåller:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>Authentication-huvud (er)</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Anger nytto lastens innehålls typ.<br/> Accepterat värde är <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Valfritt</em>.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med namnet `Text` , som representerar strängen som ska översättas.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 100 element.
* Hela texten som ingår i begäran får inte överstiga 10 000 tecken inklusive blank steg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket via följande egenskaper:

      * `language`: En sträng som representerar koden för det identifierade språket.

      * `score`: Ett flyt värde som indikerar förtroendet i resultatet. Poängen är mellan noll och en och en låg poäng indikerar en låg exakthet.

    `detectedLanguage`Egenskapen finns bara i result-objektet när språk automatisk identifiering begärs.

  * `translations`: En matris med översättnings resultat. Storleken på matrisen matchar antalet mål språk som anges i `to` Frågeparametern. Varje element i matrisen innehåller:

    * `to`: En sträng som representerar språk koden för mål språket.

    * `text`: En sträng som ger den översatta texten.

    * `transliteration`: Ett objekt som ger den översatta texten i skriptet som anges av `toScript` parametern.

      * `script`: En sträng som anger mål skriptet.   

      * `text`: En sträng som ger den översatta texten i mål skriptet.

    `transliteration`Objektet tas inte med om transkriberingsspråk inte sker.

    * `alignment`: Ett objekt med en enskild sträng egenskap med namnet `proj` , som mappar inmatad text till översatt text. Justerings informationen anges bara när parametern request `includeAlignment` är `true` . Justeringen returneras som ett sträng värde av följande format: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  Kolonet separerar start-och slut index, strecket separerar språken och blank steg separerar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket och de justerade orden kan vara icke-sammanhängande. Om ingen justerings information är tillgänglig är justerings elementet tomt. Se [Hämta justerings information](#obtain-alignment-information) för ett exempel och begränsningar.

    * `sentLen`: Ett objekt som returnerar menings gränser i indata-och utmatnings texter.

      * `srcSentLen`: En heltals mat ris som representerar längden på meningarna i indata. Matrisens längd är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`: En heltals mat ris som representerar längden på meningarna i den översatta texten. Matrisens längd är antalet meningar och värdena är längden på varje mening.

    Menings gränser inkluderas endast när parametern request `includeSentenceLength` är `true` .

  * `sourceText`: Ett objekt med en enskild sträng egenskap med namnet `text` , som ger inmatad text i standard skriptet för käll språket. `sourceText` Egenskapen finns bara när indatamängden uttrycks i ett skript som inte är det vanliga skriptet för språket. Om indatatypen till exempel är Arabiskt skriven i latinskt skript, `sourceText.text` är samma arabiska text konverterad till enscripting.

Exempel på JSON-svar finns i avsnittet [exempel](#examples) .

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
    <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används i fel söknings syfte.</td>
  </tr>
  <tr>
    <td>X-MT-system</td>
    <td>Anger den system typ som användes för översättning för varje to-language som begärdes för översättning. Värdet är en kommaavgränsad lista med strängar. Varje sträng anger en typ:<br/><ul><li>Anpassad-begäran innehåller ett anpassat system och minst ett anpassat system användes under översättningen.</li><li>Team – alla andra begär Anden</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Åtgärden lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna saknas eller är ogiltig. Korrigera parametrarna för begäran innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrol lera att autentiseringsuppgifterna har angetts och är giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran är inte auktoriserad. Se fel meddelandet information. Detta indikerar ofta att alla kostnads fria översättningar som ingår i en utvärderings prenumeration har använts.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Begäran kunde inte uppfyllas eftersom en resurs saknas. Se fel meddelandet information. När du använder en anpassad <code>category</code> indikerar detta ofta att det anpassade översättnings systemet ännu inte är tillgängligt för att betjäna begär Anden. Begäran bör göras om efter en vänte tid (t. ex. 1 minut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet <code>X-RequestId</code> och klient-ID: n från begär ande huvudet <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet <code>X-RequestId</code> och klient-ID: n från begär ande huvudet <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran även ett JSON-felsvar. Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referens sidan för v3 Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Översätt en enstaka inmatare

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations`Matrisen innehåller ett-element som innehåller översättningen av den enda texten i indatatypen.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Översätt en enstaka inmatning med språk automatisk identifiering

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska. Det angivna språket anges inte i begäran. Automatisk identifiering av käll språket används i stället.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Svaret liknar svaret från föregående exempel. Eftersom automatisk automatisk identifiering av språk begärdes, innehåller svaret även information om språket som har identifierats för indata. Automatisk identifiering av språk fungerar bättre med längre inmatad text.

### <a name="translate-with-transliteration"></a>Översätt med transkriberingsspråk

Nu ska vi utöka det tidigare exemplet genom att lägga till transkriberingsspråk. Följande begäran ber om en kinesisk översättning som skrivits i det latinska skriptet.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Översättnings resultatet innehåller nu en `transliteration` egenskap som ger den översatta texten med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätt flera delar av text

Att översätta flera strängar samtidigt är bara en fråga om att ange en sträng mat ris i begär ande texten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Svaret innehåller översättningen av alla text delar i exakt samma ordning som i begäran.
Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Översätt till flera språk

Det här exemplet visar hur du översätter samma inaktuella ingångar till flera språk i en begäran.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svars texten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Hantera svordomar

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Antalet svordomar och den kontext som gör ord svordomar skiljer sig åt mellan olika kulturer, och därför kan det leda till att svordomarna på mål språket förstärks eller minskas.

Om du inte vill få några svordomar i översättningen, oavsett om det finns svordomar i käll texten, kan du använda filtrerings alternativet för svordomar. Med alternativet kan du välja om du vill se hur svordomar tas bort, om du vill markera svordomar med lämpliga taggar (vilket ger dig möjlighet att lägga till din egen efter bearbetning) eller om du inte vill ha någon åtgärd. De godkända värdena för `ProfanityAction` är `Deleted` `Marked` och `NoAction` (standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Action</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Det här är standardbeteendet. Svordomar skickas från källa till mål.<br/><br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: he är en Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Svordomar ord tas bort från utdata utan ersättning.<br/><br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: han är en.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Svordomar ord ersätts med en markör i utdata. Markören är beroende av <code>ProfanityMarker</code> parametern.<br/><br/>
För <code>ProfanityMarker=Asterisk</code> , kommer svordoms-ord att ersättas med <code>***</code> :<br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: han är en \* \* \* .<br/><br/>
För <code>ProfanityMarker=Tag</code> , kommer svordomar att ord omges av XML-taggarnas &lt; svordomar &gt; och &lt; /Profanity &gt; :<br/>
    <strong>Exempel källa (japanska)</strong>: 彼はジャッカスです från en omfattande<br/>
    <strong>Exempel översättning (engelska)</strong>: he är en &lt; svordoms &gt; Jackass &lt; /Profanity &gt; .
  </tr>
</table> 

Exempel:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Detta returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Jämför med:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Den senaste begäran returnerar:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätt innehåll med markering och Bestäm vad som översätts

Det är vanligt att översätta innehåll som innehåller markeringar som innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera frågeparameter `textType=html` vid översättning av innehåll med taggar. Dessutom är det ibland användbart att undanta ett särskilt innehåll från översättningen. Du kan använda attributet `class=notranslate` för att ange innehåll som ska finnas kvar på det ursprungliga språket. I följande exempel kommer innehållet i det första `div` elementet inte att översättas, medan innehållet i det andra `div` elementet kommer att översättas.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är en exempel förfrågan som illustreras.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Hämta information om anpassning

Justeringen returneras som ett sträng värde av följande format för varje ord i källan. Informationen för varje ord avgränsas med ett blank steg, inklusive för icke-separerade språk (skript) som kinesiska:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exempel på justerings sträng: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Kolon separerar exempelvis start-och slut index, strecket separerar språken och avståndet separerar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket och de justerade orden kan vara icke-sammanhängande. Om ingen justerings information är tillgänglig är justerings elementet tomt. Metoden returnerar inga fel i detta fall.

Om du vill ta emot information `includeAlignment=true` om anpassning anger du på frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Justerings informationen börjar med `0:2-0:1` , vilket innebär att de första tre tecknen i käll texten ( `The` ) mappar till de första två tecknen i den översatta texten ( `La` ).

#### <a name="limitations"></a>Begränsningar
Att hämta justerings information är en experimentell funktion som vi har aktiverat för prototyper av forskning och upplevelser med möjliga fras mappningar. Vi kan välja att sluta stödja detta i framtiden. Här följer några viktiga begränsningar där justering inte stöds:

* Justering är inte tillgängligt för text i HTML-format, dvs. textType = HTML
* Justering returneras bara för en delmängd av språk paren:
  - Engelska till/från andra språk än traditionell kinesiska, kantonesiska (traditionell) eller Serbiska (kyrilliska).
  - från japanska till koreanska eller från koreanska till japanska.
  - från japanska till förenklad kinesiska och förenklad kinesiska till japanska. 
  - från förenklad kinesiska till traditionell kinesiska och traditionell kinesiska till förenklad kinesiska. 
* Du får ingen justering om meningen är en konserverad översättning. Exempel på en konserverad översättning är "det här är ett test", "Jag älskar dig" och andra meningar med hög frekvens.
* Justering är inte tillgängligt när du tillämpar någon av metoderna för att förhindra översättning enligt beskrivningen [här](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Hämta menings gränser

Om du vill få information om menings längden i käll texten och översatt text anger `includeSentenceLength=true` du på frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Svaret är:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Översätt med dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är bara säker för korrekt substantiv, till exempel personliga namn och produkt namn.

Den markering som ska tillhandahållas använder följande syntax.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Anta till exempel den engelska meningen "ordet WordOMatic är en ord lista." Om du vill behålla ordet _WordOMatic_ i översättningen skickar du begäran:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Resultatet är:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Den här funktionen fungerar på samma sätt med `textType=text` eller med `textType=html` . Funktionen bör användas sparsamt. Ett lämpligt och mycket bättre sätt att anpassa översättning är genom att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan råd om att skapa tränings data som visar ditt arbete eller en fras i ett sammanhang får du bättre resultat. [Läs mer om anpassad översättare](../customization.md).