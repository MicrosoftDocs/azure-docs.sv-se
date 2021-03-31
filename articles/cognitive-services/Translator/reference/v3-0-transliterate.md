---
title: Translator-metod
titleSuffix: Azure Cognitive Services
description: Omvandla text på ett språk från ett skript till ett annat skript med hjälp av Translator-metoden Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895432"
---
# <a name="translator-30-transliterate"></a>Translator 3,0: translittererad

Konverterar text på ett språk från ett skript till ett annat.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>api-version</td>
    <td>*Obligatorisk parameter*.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0` .</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för texten som ska konverteras från ett skript till ett annat. Möjliga språk listas i `transliteration` omfånget som erhålls genom att fråga tjänsten för de [språk som stöds](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Obligatorisk parameter*.<br/>Anger det skript som används av inmatad text. Leta upp [språk som stöds](./v3-0-languages.md) med hjälp av `transliteration` omfånget för att hitta tillgängliga skript för det valda språket.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Obligatorisk parameter*.<br/>Anger utdata-skriptet. Leta upp [språk som stöds](./v3-0-languages.md) med hjälp av `transliteration` omfånget för att hitta de tillgängliga skripten för den valda kombinationen av inmatnings språk och indata-skript.</td>
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
    <td>*Begär ande huvud för begäran*.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json` .</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td>*Begär ande huvud för begäran*.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Observera att du kan utelämna detta sidhuvud om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId` .</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med namnet `Text` , som representerar strängen som ska konverteras.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 10 element.
* Text värden för ett mat ris element får inte överstiga 1 000 tecken inklusive blank steg.
* Hela texten som ingår i begäran får inte överstiga 5 000 tecken inklusive blank steg.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje element i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `text`: En sträng som är resultatet av konverteringen av Indatasträngen till utdata-skriptet.
  
  * `script`: En sträng som anger det skript som används i utdata.

Ett exempel på JSON-svar är:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Beskrivning</th>
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

I följande exempel visas hur du konverterar två japanska strängar till en romerskt japansk.

JSON-nyttolasten för begäran i det här exemplet:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Om du använder en sväng i ett kommando rads fönster som inte stöder Unicode-tecken tar du följande JSON-nyttolast och sparar den i en fil med namnet `request.txt` . Se till att spara filen med `UTF-8` encoding.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```