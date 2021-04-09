---
title: Metod för översättnings språk
titleSuffix: Azure Cognitive Services
description: Språk metoden hämtar de språk som för närvarande stöds av andra åtgärder i Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 8471983d24cfed39e6521e19366f6023e519adaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895483"
---
# <a name="translator-30-languages"></a>Translator 3,0: språk

Hämtar den uppsättning språk som för närvarande stöds av andra åtgärder i Translator. 

## <a name="request-url"></a>Begärans-URL

Skicka en `GET` begäran till:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td><em>Obligatorisk parameter</em>.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0` .</td>
  </tr>
  <tr>
    <td>omfång</td>
    <td>*Valfri parameter*.<br/>En kommaavgränsad lista med namn som definierar den grupp av språk som ska returneras. Tillåtna grupp namn är: `translation` , `transliteration` och `dictionary` . Om ingen omfattning anges returneras alla grupper, vilket motsvarar att skicka `scope=translation,transliteration,dictionary` . Information om vilka språk som stöds är lämpliga för ditt scenario genom att se beskrivningen av [objektet Response](#response-body).</td>
  </tr>
</table> 

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>Accept-Language</td>
    <td>*Valfritt huvud för begäran*.<br/>Språket som ska användas för användargränssnittssträngar. Några av fälten i svaret är namn på språk eller namn på regioner. Använd den här parametern för att definiera språket som namnen returneras i. Språket anges genom att tillhandahålla en välformulerad BCP 47-språktagg. Använd exempelvis värdet `fr` för att begära namn på franska eller Använd värdet `zh-Hant` för att begära namn på traditionell kinesiska.<br/>Namn anges på det engelska språket när ett mål språk inte anges eller när lokalisering inte är tillgängligt.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt huvud för begäran*.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran.</td>
  </tr>
</table> 

Autentisering krävs inte för att hämta språk resurser.

## <a name="response-body"></a>Själva svaret

En klient använder `scope` Frågeparametern för att definiera vilka grupper av språk som den är intresse rad av.

* `scope=translation` tillhandahåller språk som stöds för att översätta text från ett språk till ett annat språk.

* `scope=transliteration` tillhandahåller funktioner för att konvertera text på ett språk från ett skript till ett annat skript.

* `scope=dictionary` innehåller språk par för vilka `Dictionary` åtgärder returnerar data.

En klient kan hämta flera grupper samtidigt genom att ange en kommaavgränsad lista med namn. Skulle till exempel `scope=translation,transliteration,dictionary` kunna returnera språk som stöds för alla grupper.

Ett lyckat svar är ett JSON-objekt med en egenskap för varje begärd grupp:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

Värdet för varje egenskap är följande.

* `translation` immaterialrätt

  `translation`Egenskapens värde är en ord lista med (nyckel, värde) par. Varje nyckel är en språk tagg för BCP 47. En nyckel identifierar ett språk för vilken text kan översättas till eller översättas från. Värdet som är kopplat till nyckeln är ett JSON-objekt med egenskaper som beskriver språket:

  * `name`: Visnings namnet för språket i det språk som begärs via `Accept-Language` sidhuvud.

  * `nativeName`: Visnings namnet för språket i det språkspecifika språket för det här språket.

  * `dir`: Vägbeskrivning, som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster-till-höger-språk.

  Ett exempel är:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` immaterialrätt

  `transliteration`Egenskapens värde är en ord lista med (nyckel, värde) par. Varje nyckel är en språk tagg för BCP 47. En nyckel identifierar ett språk för vilken text kan konverteras från ett skript till ett annat skript. Värdet som är kopplat till nyckeln är ett JSON-objekt med egenskaper som beskriver språket och skript som stöds:

  * `name`: Visnings namnet för språket i det språk som begärs via `Accept-Language` sidhuvud.

  * `nativeName`: Visnings namnet för språket i det språkspecifika språket för det här språket.

  * `scripts`: Lista över skript som ska konverteras från. Varje element i `scripts` listan har egenskaper:

    * `code`: Kod som identifierar skriptet.

    * `name`: Visnings namnet för skriptet i det språk som begärs via `Accept-Language` rubrik.

    * `nativeName`: Visnings namnet för språket i det språk som är inbyggt för språket.

    * `dir`: Vägbeskrivning, som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster-till-höger-språk.

    * `toScripts`: Lista över skript som är tillgängliga för att konvertera text till. Varje element i `toScripts` listan har egenskaper `code` , `name` , `nativeName` och `dir` enligt beskrivningen ovan.

  Ett exempel är:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` immaterialrätt

  `dictionary`Egenskapens värde är en ord lista med (nyckel, värde) par. Varje nyckel är en språk tagg för BCP 47. Nyckeln identifierar ett språk som alternativa översättningar och tillbaka översättningar är tillgängliga för. Värdet är ett JSON-objekt som beskriver käll språket och mål språken med tillgängliga översättningar:

  * `name`: Visnings namnet för käll språket i det språk som begärdes via `Accept-Language` rubrik.

  * `nativeName`: Visnings namnet för språket i det språkspecifika språket för det här språket.

  * `dir`: Vägbeskrivning, som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster-till-höger-språk.

  * `translations`: Lista över språk med alterative-översättningar och exempel för frågan uttryckt i käll språket. Varje element i `translations` listan har egenskaper:

    * `name`: Visnings namnet på mål språket i språkvarianten som begärs via `Accept-Language` rubrik.

    * `nativeName`: Visnings namnet på mål språket i det språkspecifika språket för mål språket.

    * `dir`: Vägbeskrivning, som är `rtl` för höger-till-vänster-språk eller `ltr` för vänster-till-höger-språk.
    
    * `code`: Språk koden identifierar mål språket.

  Ett exempel är:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

Objektets struktur ändras inte utan en ändring i API-versionen. I samma version av API: t kan listan över tillgängliga språk ändras över tid eftersom Microsoft Translator kontinuerligt utökar listan över språk som stöds av tjänsterna.

Listan över språk som stöds kommer inte att ändras ofta. Om du vill spara nätverks bandbredden och förbättra svars tiden bör ett klient program överväga att cachelagra språk resurser och motsvarande enhets tag gen ( `ETag` ). Sedan kan klient programmet regelbundet (till exempel en gång var 24: e timme) fråga tjänsten för att hämta den senaste uppsättningen språk som stöds. Om du skickar det aktuella `ETag` värdet i ett `If-None-Match` rubrik fält kan tjänsten optimera svaret. Om resursen inte har ändrats returnerar tjänsten status kod 304 och en tom svars text.

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>ETag</td>
    <td>Aktuellt värde för enhets tag gen för de begärda språken som stöds. För att göra efterföljande begär Anden mer effektiva kan klienten skicka `ETag` värdet i ett `If-None-Match` rubrik fält.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används i fel söknings syfte.</td>
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
    <td>304</td>
    <td>Resursen har inte ändrats sedan den version som angavs av begärandehuvuden `If-None-Match` .</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna saknas eller är ogiltig. Korrigera parametrarna för begäran innan du försöker igen.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet `X-RequestId` och klient-ID: n från begär ande huvudet `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår rapporterar du det med: datum och tid för felet, begärande-ID från svars huvudet `X-RequestId` och klient-ID: n från begär ande huvudet `X-ClientTraceId` .</td>
  </tr>
</table> 

Om ett fel inträffar returnerar begäran även ett JSON-felsvar. Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder finns på [referens sidan för v3 Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Exempel

I följande exempel visas hur du hämtar språk som stöds för text översättning.

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```