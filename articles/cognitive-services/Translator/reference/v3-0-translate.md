---
title: Translator Translate-metoden
titleSuffix: Azure Cognitive Services
description: Förstå parametrar, rubriker och brödtextmeddelanden för metoden Translate för att Azure Cognitive Services Translator för att översätta text.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 148aa722515d9364ce5af85b3f7c3b39958c9c91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388388"
---
# <a name="translator-30-translate"></a>Translator 3.0: Översätta

Översätter text.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Begärandeparametrar

Begärandeparametrar som skickas i frågesträngen är:

### <a name="required-parameters"></a>Obligatoriska parametrar

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Den version av API:et som begärs av klienten. Värdet måste vara <code>3.0</code> .</td>
  </tr>
  <tr>
    <td>på</td>
    <td><em>Obligatorisk parameter</em>.<br/>Anger språket för utdatatexten. Målspråket måste vara ett av de språk <a href="./v3-0-languages.md">som stöds i</a> <code>translation</code> omfånget. Använd till exempel för <code>to=de</code> att översätta till tyska.<br/>Det går att översätta till flera språk samtidigt genom att upprepa parametern i frågesträngen. Använd till exempel för <code>to=de&to=it</code> att översätta till tyska och italienska.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Valfria parametrar

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>Från</td>
    <td><em>Valfri parameter</em>.<br/>Anger språket för indatatexten. Ta reda på vilka språk som är tillgängliga att översätta från genom att leta upp <a href="./v3-0-languages.md">språk som stöds</a> med hjälp av <code>translation</code> omfånget . Om <code>from</code> parametern inte anges används automatisk språkidentifiering för att fastställa källspråket. <br/><br/>Du måste använda <code>from</code> parametern i stället för automatisk identifiering när du använder <a href="/azure/cognitive-services/translator/dynamic-dictionary">funktionen för dynamisk ordlista.</a></td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Valfri parameter</em>.<br/>Definierar om texten som översätts är oformaterad text eller HTML-text. Html-kod måste vara ett välformade, fullständigt element. Vid översättning av HTML-text har utdatatexten följande specialtecken i rymt format: "&", "<" och ">". Detta beror på om indata-HTML-texten har tecken som är rymna. Möjliga värden är: <code>plain</code> (standard) eller <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Valfri parameter</em>.<br/>En sträng som anger översättningens kategori (domän). Den här parametern används för att hämta översättningar från ett anpassat system som skapats med <a href="../customization.md">Custom Translator</a>. Lägg till kategori-ID:t från Custom Translator <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projektinformation till</a> den här parametern för att använda ditt distribuerade anpassade system. Standardvärdet är: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska behandlas i översättningar. Möjliga värden är: <code>NoAction</code> (standard) <code>Marked</code> eller <code>Deleted</code> . Information om hur du hanterar svordomar finns i <a href="#handle-profanity">Hantering av olämpligt språk.</a></td>
  </tr>
  <tr>
    <td>svordomarMarker</td>
    <td><em>Valfri parameter</em>.<br/>Anger hur svordomar ska markeras i översättningar. Möjliga värden är: <code>Asterisk</code> (standard) eller <code>Tag</code> . Information om hur du behandlar svordomar finns <a href="#handle-profanity">i Hantering av olämpligt språk.</a></td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Valfri parameter</em>.<br/>Anger om justeringsprojektion ska inkluderas från källtexten till den översatta texten. Möjliga värden är: <code>true</code> eller <code>false</code> (standard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Valfri parameter</em>.<br/>Anger om meningsgränser ska inkluderas för indatatexten och den översatta texten. Möjliga värden är: <code>true</code> eller <code>false</code> (standard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Valfri parameter</em>.<br/>Anger ett återställningsspråk om det inte går att identifiera språket i indatatexten. Automatisk språkidentifiering tillämpas när <code>from</code> parametern utelämnas. Om identifieringen misslyckas <code>suggestedFrom</code> antas språket.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet för indatatexten.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Valfri parameter</em>.<br/>Anger skriptet för den översatta texten.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Valfri parameter</em>.<br/>Anger att tjänsten tillåts att gå tillbaka till ett allmänt system när det inte finns något anpassat system. Möjliga värden är: <code>true</code> (standard) eller <code>false</code> .<br/><br/><code>allowFallback=false</code> anger att översättningen endast ska använda system som har tränats för <code>category</code> som anges i begäran. Om en översättning för språk X till språk Y kräver länkning via pivotspråket E måste alla system i kedjan (X->E och E->Y) vara anpassade och ha samma kategori. Om inget system hittas med den specifika kategorin returnerar begäran statuskoden 400. <code>allowFallback=true</code> anger att tjänsten tillåts att gå tillbaka till ett allmänt system när det inte finns något anpassat system.
</td>
  </tr>
</table> 

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>Autentiseringshuvuden</td>
    <td><em>Begärandehuvud som krävs.</em><br/>Se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Begärandehuvud som krävs.</em><br/>Anger nyttolastens innehållstyp.<br/> Godkänt värde är <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Innehållslängd</td>
    <td><em>Begärandehuvud som krävs.</em><br/>Längden på begärandetexten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Valfritt</em>.<br/>Ett klientgenererat GUID som unikt identifierar begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID:t i frågesträngen med hjälp av en frågeparameter med namnet <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Begärandetexten är en JSON-matris. Varje matriselement är ett JSON-objekt med en strängegenskap med `Text` namnet , som representerar strängen som ska översättas.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 100 element.
* Hela texten i begäran får inte överskrida 10 000 tecken, inklusive blanksteg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i indatamatrisen. Ett resultatobjekt innehåller följande egenskaper:

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket via följande egenskaper:

      * `language`: En sträng som representerar koden för det identifierade språket.

      * `score`: Ett flyttalsvärde som anger konfidensen i resultatet. Poängen är mellan noll och ett och en låg poäng indikerar ett lågt förtroende.

    Egenskapen `detectedLanguage` finns bara i resultatobjektet när automatisk språkidentifiering begärs.

  * `translations`: En matris med översättningsresultat. Storleken på matrisen matchar antalet målspråk som anges via `to` frågeparametern. Varje element i matrisen innehåller:

    * `to`: En sträng som representerar språkkoden för målspråket.

    * `text`: En sträng som ger den översatta texten.

    * `transliteration`: Ett objekt som ger den översatta texten i skriptet som anges av `toScript` parametern .

      * `script`: En sträng som anger målskriptet.   

      * `text`: En sträng som ger den översatta texten i målskriptet.

    Objektet `transliteration` inkluderas inte om transkribering inte sker.

    * `alignment`: Ett objekt med en enda strängegenskap med namnet `proj` , som mappar indatatext till översatt text. Justeringsinformationen anges bara när parametern för begäran `includeAlignment` är `true` . Justering returneras som ett strängvärde i följande format: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  Kolonet separerar start- och slutindex, bindestrecket separerar språken och blanksteget separerar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket, och de justerade orden kan vara icke-sammanhängande. När det inte finns någon justeringsinformation är justeringselementet tomt. Se [Hämta justeringsinformation för](#obtain-alignment-information) ett exempel och begränsningar.

    * `sentLen`: Ett objekt som returnerar meningsgränser i indata- och utdatatexterna.

      * `srcSentLen`: En heltalsmatris som representerar längden på meningarna i indatatexten. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

      * `transSentLen`: En heltalsmatris som representerar längden på meningarna i den översatta texten. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

    Meningsgränser inkluderas bara när parametern för begäran `includeSentenceLength` är `true` .

  * `sourceText`: Ett objekt med en enda strängegenskap `text` med namnet , som ger indatatexten i standardskriptet för källspråket. `sourceText` egenskapen finns bara när indata uttrycks i ett skript som inte är det vanliga skriptet för språket. Om indata till exempel var arabiska skrivna i ett latinskt skript skulle det vara `sourceText.text` samma arabiska text som konverterades till det arabiska skriptet.

Exempel på JSON-svar [](#examples) finns i exempelavsnittet.

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
    <tr>
    <td>X-RequestId</td>
    <td>Värdet som genereras av tjänsten för att identifiera begäran. Den används i felsökningssyfte.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Anger den systemtyp som användes för översättning för varje "till"-språk som begärdes för översättning. Värdet är en kommaavgränsad lista med strängar. Varje sträng anger en typ:<br/><ul><li>Anpassad – Begäran innehåller ett anpassat system och minst ett anpassat system användes under översättningen.</li><li>Team – Alla andra begäranden</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Statuskoder för svar

Följande är möjliga HTTP-statuskoder som en begäran returnerar. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Åtgärden lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna saknas eller är ogiltig. Korrigera begärandeparametrarna innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna har angetts och är giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran har inte auktoriserats. Kontrollera informationsfelmeddelandet. Detta indikerar ofta att alla kostnadsfria översättningar som medföljer en utvärderingsprenumeration har använts.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Det gick inte att uppfylla begäran eftersom en resurs saknas. Kontrollera felmeddelandet med information. När du använder en <code>category</code> anpassad betyder detta ofta att det anpassade översättningssystemet ännu inte är tillgängligt för att betjäna begäranden. Begäran bör göras på nytt efter en väntetid (t.ex. 1 minut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran eftersom klienten har överskridit gränsen för begäran.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärandeidentifierare från svarshuvud <code>X-RequestId</code> och klientidentifierare från begärandehuvud <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är för tillfället otillgänglig. Försök att skicka begäran igen. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärandeidentifierare från svarshuvud <code>X-RequestId</code> och klientidentifierare från begärandehuvud <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran även ett JSON-felsvar. Felkoden är ett 6-siffrigt tal som kombinerar den 3-siffriga HTTP-statuskoden följt av ett 3-siffrigt tal för att kategorisera felet ytterligare. Vanliga felkoder finns på referenssidan [för v3 Translator.](./v3-0-reference.md#errors) 

## <a name="examples"></a>Exempel

### <a name="translate-a-single-input"></a>Översätta en enda indata

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svarstexten är:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Matrisen `translations` innehåller ett element som tillhandahåller översättning av den enskilda texten i indata.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Översätta en enskild indata med automatisk språkidentifiering

Det här exemplet visar hur du översätter en enskild mening från engelska till förenklad kinesiska. Begäran anger inte indataspråket. Automatisk identifiering av källspråket används i stället.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svarstexten är:

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
Svaret liknar svaret från föregående exempel. Eftersom automatisk identifiering av språk begärdes innehåller svaret även information om det språk som identifierats för indatatexten. Automatisk identifiering av språk fungerar bättre med längre indatatext.

### <a name="translate-with-transliteration"></a>Översätta med transkribering

Vi utökar det föregående exemplet genom att lägga till transkribering. Följande begäran begär en kinesiskaöversättning som skrivits i ett latinskt skript.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svarstexten är:

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

Översättningsresultatet innehåller nu en `transliteration` -egenskap som ger den översatta texten med latinska tecken.

### <a name="translate-multiple-pieces-of-text"></a>Översätta flera textdelar

Att översätta flera strängar samtidigt handlar helt enkelt om att ange en matris med strängar i begärandetexten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Svaret innehåller översättningen av alla textdelar i exakt samma ordning som i begäran.
Svarstexten är:

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

### <a name="translate-to-multiple-languages"></a>Översätta till flera språk

Det här exemplet visar hur du översätter samma indata till flera språk i en begäran.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Svarstexten är:

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

Normalt behåller Translator-tjänsten svordomar som finns i källan i översättningen. Graden av svordomar och den kontext som gör ord olämpliga skiljer sig åt mellan kulturer, och därför kan graden av svordomar i målspråket förstärks eller minskas.

Om du vill undvika svordomar i översättningen, oavsett förekomsten av svordomar i källtexten, kan du använda alternativet för filtrering av svordomar. Med alternativet kan du välja om du vill ta bort svordomar, om du vill markera svordomar med lämpliga taggar (vilket ger dig möjlighet att lägga till din egen efterbearbetning) eller om du inte vill att någon åtgärd ska vidtas. Godkända värden för `ProfanityAction` är `Deleted` , och `Marked` `NoAction` (standard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Action</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Det här är standardbeteendet. Svordomar kommer att överföras från källa till mål.<br/><br/>
    <strong>Exempelkälla (japanska):</strong>彼はジャ för att få en です...<br/>
    <strong>Exempelöversättning (engelska):</strong>Han är en åsna.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Olämpliga ord tas bort från utdata utan ersättning.<br/><br/>
    <strong>Exempelkälla (japanska):</strong>彼はジャ för att få en です...<br/>
    <strong>Exempelöversättning (engelska):</strong>Han är en.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Olämpliga ord ersätts med en markör i utdata. Markören är beroende av <code>ProfanityMarker</code> parametern .<br/><br/>
För <code>ProfanityMarker=Asterisk</code> ersätts olämpliga ord med <code>***</code> :<br/>
    <strong>Exempelkälla (japanska):</strong>彼はジャ för att få en です...<br/>
    <strong>Exempelöversättning (engelska):</strong>Han är \* \* \* en .<br/><br/>
För <code>ProfanityMarker=Tag</code> omges olämpliga ord av XML-taggar &lt; svordomar &gt; och &lt; /svordomar: &gt;<br/>
    <strong>Exempelkälla (japanska):</strong>彼はジャ för att få en です...<br/>
    <strong>Exempelöversättning (engelska):</strong>Han är &lt; ett svordomsöversättningar &gt; &lt; /svordomar &gt; .
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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Översätta innehåll med pålägg och bestämma vad som ska översättas

Det är vanligt att översätta innehåll som innehåller markeringar, till exempel innehåll från en HTML-sida eller innehåll från ett XML-dokument. Inkludera frågeparameter `textType=html` vid översättning av innehåll med taggar. Dessutom kan det ibland vara bra att undanta specifikt innehåll från översättning. Du kan använda attributet `class=notranslate` för att ange innehåll som ska finnas kvar på det ursprungliga språket. I följande exempel översätts inte innehållet i det `div` första elementet, medan innehållet i det andra `div` elementet översätts.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Här är ett exempel på en begäran som illustreras.

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

### <a name="obtain-alignment-information"></a>Hämta justeringsinformation

Justering returneras som ett strängvärde i följande format för varje ord i källan. Informationen för varje ord avgränsas med ett blanksteg, inklusive för icke-blankstegsavgränsade språk (skript) som kinesiska:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exempel på justeringssträng: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Kolonet separerar med andra ord start- och slutindex, bindestrecket separerar språken och avgränsar orden. Ett ord kan justeras med noll, ett eller flera ord på det andra språket, och de justerade orden kan vara icke-sammanhängande. När det inte finns någon justeringsinformation är elementet Justering tomt. Metoden returnerar i så fall inget fel.

Om du vill ta emot justeringsinformation anger `includeAlignment=true` du i frågesträngen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true&quot; -H &quot;Ocp-Apim-Subscription-Key: <client-secret>&quot; -H &quot;Content-Type: application/json; charset=UTF-8&quot; -d &quot;[{'Text':'The answer lies in machine translation.'}]"
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

Justeringsinformationen börjar med , vilket innebär att de första tre tecknen i källtexten ( ) mappas till de två första tecknen `0:2-0:1` `The` i den översatta texten ( `La` ).

#### <a name="limitations"></a>Begränsningar
Att hämta justeringsinformation är en experimentell funktion som vi har aktiverat för prototyper av forskning och erfarenheter av potentiella frasmappningar. Vi kan välja att sluta stödja detta i framtiden. Här är några av de anmärkningsvärda begränsningarna där justeringar inte stöds:

* Justering är inte tillgängligt för text i HTML-format, t.ex. textType=html
* Justering returneras endast för en delmängd av språkparen:
  - Engelska till/från andra språk förutom traditionell kinesiska, kantonska (traditionell) eller serbiska (kyrillisk).
  - från japanska till koreanska eller från koreanska till japanska.
  - från japanska till förenklad kinesiska och förenklad kinesiska till japanska. 
  - från förenklad kinesiska till traditionell kinesiska och traditionell kinesiska till förenklad kinesiska. 
* Du får ingen justering om meningen är en skannad översättning. Exempel på en skannad översättning är "This is a test", "I love you" (Jag älskar dig) och andra meningar med hög frekvens.
* Justering är inte tillgängligt när du använder någon av metoderna för att förhindra översättning enligt beskrivningen [här](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Hämta meningsgränser

Om du vill ta emot information om meningslängden i källtexten och översatt text anger `includeSentenceLength=true` du i frågesträngen.

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

### <a name="translate-with-dynamic-dictionary"></a>Översätta med dynamisk ordlista

Om du redan känner till översättningen som du vill använda för ett ord eller en fras kan du ange den som pålägg i begäran. Den dynamiska ordlistan är endast säker för rätt substantiv, till exempel personliga namn och produktnamn.

Pålägget som ska tillhandahållas använder följande syntax.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Tänk dig till exempel den engelska meningen "Ordet wordomatic är en ordlistepost". Om du vill bevara _ordet wordomatic_ i översättningen skickar du begäran:

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

Den här funktionen fungerar på samma sätt med `textType=text` eller med `textType=html` . Funktionen bör användas sparsamt. Ett lämpligt och mycket bättre sätt att anpassa översättning är att använda Custom Translator. Custom Translator använder kontext och statistiska sannolikheter fullt ut. Om du har eller har råd att skapa träningsdata som visar ditt arbete eller din fras i ett sammanhang får du mycket bättre resultat. [Läs mer om att Custom Translator](../customization.md).
