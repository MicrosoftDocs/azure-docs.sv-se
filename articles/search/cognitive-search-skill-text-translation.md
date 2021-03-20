---
title: Kognitiv kompetens för text översättning
titleSuffix: Azure Cognitive Search
description: Utvärderar text och, för varje post, returnerar text som har översatts till det angivna mål språket i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654872"
---
#   <a name="text-translation-cognitive-skill"></a>Kognitiv kompetens för text översättning

**Text översättnings** kunskapen utvärderar text och returnerar texten översatt till det angivna mål språket för varje post. Den här kunskapen använder [Translator text API v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) som finns i Cognitive Services.

Den här funktionen är användbar om du förväntar dig att dokumenten kanske inte är på ett språk, vilket innebär att du kan normalisera texten till ett enda språk innan du indexerar den genom att översätta den.  Det är också användbart vid lokalisering av användnings fall där du kanske vill ha kopior av samma text som finns på flera språk.

[Translator text API v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) är en icke-regional kognitiv tjänst, vilket innebär att dina data inte garanterar att de stannar i samma region som din Azure kognitiv sökning eller anslutna Cognitive Services resurs.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. TranslationSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 50 000 tecken som mäts av [`String.Length`](/dotnet/api/system.string.length) . Om du behöver dela upp dina data innan du skickar dem till text översättnings kunskapen bör du överväga att använda [text delnings kunskaper](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Indata | Beskrivning |
|---------------------|-------------|
| defaultToLanguageCode | Kunna Språk koden som dokumenten ska konverteras till för dokument som inte uttryckligen anger till språk. <br/> Se en [fullständig lista över språk som stöds](../cognitive-services/translator/language-support.md). |
| defaultFromLanguageCode | Valfritt Språk koden som översätter dokument från för dokument som inte anger från språket explicit.  Om defaultFromLanguageCode inte anges kommer den automatiska språk identifieringen som tillhandahålls av Translator Text API att användas för att fastställa från språket. <br/> Se en [fullständig lista över språk som stöds](../cognitive-services/translator/language-support.md). |
| suggestedFrom | Valfritt Språk koden som används för att översätta dokument från när varken fromLanguageCode-indata eller defaultFromLanguageCode-parametern anges och den automatiska språk identifieringen Miss lyckas.  Om suggestedFrom-språket inte anges kommer engelska (en) att användas som suggestedFrom-språk. <br/> Se en [fullständig lista över språk som stöds](../cognitive-services/translator/language-support.md). |

## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn     | Beskrivning |
|--------------------|-------------|
| text | Den text som ska översättas.|
| toLanguageCode    | En sträng som anger vilket språk texten ska översättas till. Om den här indatamängden inte anges kommer defaultToLanguageCode att användas för att översätta texten. <br/>Se en [fullständig lista över språk som stöds](../cognitive-services/translator/language-support.md)|
| fromLanguageCode  | En sträng som anger det aktuella språket för texten. Om den här parametern inte anges kommer defaultFromLanguageCode (eller automatisk språk identifiering om defaultFromLanguageCode inte anges) att användas för att översätta texten. <br/>Se en [fullständig lista över språk som stöds](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata    | Beskrivning |
|--------------------|-------------|
| translatedText | Sträng resultatet för text översättningen från translatedFromLanguageCode till translatedToLanguageCode.|
| translatedToLanguageCode  | En sträng som anger språk koden som texten översattes till. Användbart om du översätter till flera språk och vill kunna hålla reda på vilken text som är det språket.|
| translatedFromLanguageCode    | En sträng som anger språk koden som texten har översatts från. Användbart om du väljer alternativet automatisk språk identifiering som utdata ger dig resultatet av identifieringen.|

##  <a name="sample-definition"></a>Exempel definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Exempelindata

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Exempelutdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Fel och varningar
Om du anger en språk kod som inte stöds för antingen från eller till språket, genereras ett fel och texten översätts inte.
Om texten är tom skapas en varning.
Om texten är större än 50 000 tecken kommer endast de första 50 000 tecknen att översättas och en varning utfärdas.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)