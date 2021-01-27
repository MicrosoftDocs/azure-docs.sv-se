---
title: Metoden Translator BreakSentence
titleSuffix: Azure Cognitive Services
description: Metoden Translator BreakSentence identifierar placeringen av menings gränser i en text del.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2da614fe829d0aa82bfa57337baf44491993c68f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895551"
---
# <a name="translator-30-breaksentence"></a>Translator 3,0: BreakSentence

Anger placeringen av menings gränser i en text del.

## <a name="request-url"></a>Begärans-URL

Skicka en `POST` begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

| Frågeparameter | Description |
| -------| ----------- |
| api-version <img width=200/>   | **Parameter för obligatorisk fråga**.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0` . |
| language | **Valfri frågeparameter**.<br/>Språk tag gen som identifierar språk för inmatad text. Om ingen kod anges används automatisk språk identifiering. |
| skript    | **Valfri frågeparameter**.<br/>Skript tag gen som identifierar skriptet som används av inmatad text. Om ett skript inte anges kommer standard språket för språket att antas.  | 

Begärandehuvuden innehåller:

| Sidhuvuden | Description |
| ------- | ----------- |
| Authentication-huvud (er) <img width=200/>  | **Begär ande huvud för begäran**.<br/>Se <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>. |
| Content-Type | **Begär ande huvud för begäran**.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json` . |
| Innehålls längd    | **Begär ande huvud för begäran**.<br/>Längden på begär ande texten.  | 
| X-ClientTraceId   | **Valfritt**.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Observera att du kan utelämna detta sidhuvud om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId` .  | 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med en sträng egenskap med namnet `Text` . Menings gränser beräknas för `Text` egenskapens värde. En exempel begär ande text med en text ruta ser ut så här:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 100 element.
* Text värden för ett mat ris element får inte överstiga 50 000 tecken inklusive blank steg.
* Hela texten som ingår i begäran får inte överstiga 50 000 tecken inklusive blank steg.
* Om `language` Frågeparametern anges måste alla mat ris element vara på samma språk. Annars används automatisk identifiering av språk för varje mat ris element oberoende av varandra.

## <a name="response-body"></a>Själva svaret

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `sentLen`: En matris med heltal som representerar längden på meningarna i text elementet. Matrisens längd är antalet meningar och värdena är längden på varje mening. 

  * `detectedLanguage`: Ett objekt som beskriver det identifierade språket via följande egenskaper:

     * `language`: Kod för det identifierade språket.

     * `score`: Ett flyt värde som indikerar förtroendet i resultatet. Poängen är mellan noll och en och en låg poäng indikerar en låg exakthet.
     
    Observera att `detectedLanguage` egenskapen endast visas i result-objektet när språk automatisk identifiering begärs.

Ett exempel på JSON-svar är:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Sidhuvuden</th>
  <th>Description</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används i fel söknings syfte.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svars status koder

Följande är de möjliga HTTP-statuskod som en begäran returnerar. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Description</th>
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

I följande exempel visas hur du hämtar menings gränser för en enda mening. Språket i meningen identifieras automatiskt av tjänsten.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```