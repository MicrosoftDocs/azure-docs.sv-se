---
title: Text delning kognitiv kunskap
titleSuffix: Azure Cognitive Search
description: Dela upp text i segment eller sidor med text baserat på längd i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84981956"
---
# <a name="text-split-cognitive-skill"></a>Text delning kognitiv kunskap

**Texten Split** -kompetensen avbryter text i segment med text. Du kan ange om du vill dela upp texten i meningar eller på sidor med en viss längd. Den här kunskapen är särskilt användbar om det finns maximalt text längds krav i andra färdigheter. 

> [!NOTE]
> Den här kunskapen är inte kopplad till ett Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md), men för att åsidosätta det **kostnads fria** resurs alternativet som begränsar dig till ett litet antal dagliga berikare per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. SplitSkill 

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `textSplitMode`    | Antingen "Pages" eller "meningar" | 
| `maximumPageLength` | Om textSplitMode är inställt på "Pages", refererar detta till den maximala sid längden som mäts av `String.Length` . Minimivärdet är 300.  Om textSplitMode är inställt på "Pages" försöker algoritmen dela upp texten i segment som har flest "maximumPageLength" i storlek. I det här fallet gör algoritmen det bästa sättet att dela upp meningen på en mening, så storleken på segmentet kan vara något lägre än "maximumPageLength". | 
| `defaultLanguageCode` | valfritt En av följande språk koder: `da, de, en, es, fi, fr, it, ko, pt` . Standardvärdet är engelska (en). Några saker att tänka på:<ul><li>Om du skickar ett languageCode-CountryCode-format används endast languageCode-delen av formatet.</li><li>Om språket inte finns i den föregående listan, delar den delade kompetensen texten vid tecken gränser.</li><li>Att tillhandahålla en språkkod är användbart för att undvika att klippa ut ett ord på en halv till ett språk som inte är blank steg, till exempel kinesiska, japanska och koreanska.</li><li>Om du inte känner till språket (dvs. om du behöver dela upp texten för indatamängden i [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), är standardvärdet engelska (en) tillräckligt. </li></ul>  |


## <a name="skill-inputs"></a>Kompetens inmatningar

| Parameternamn       | Beskrivning      |
|----------------------|------------------|
| `text`    | Den text som ska delas upp i under sträng. |
| `languageCode`    | Valfritt Språk kod för dokumentet. Om du inte känner till språket (d.v.s. om du behöver dela upp texten för inläsningar i [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)) är det säkert att ta bort den här indatamängden.  |

## <a name="skill-outputs"></a>Kunskaps utmatningar 

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| `textItems`   | En matris med del strängar som har extraherats. |


##  <a name="sample-definition"></a>Exempel definition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Exempel på indata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Fel fall
Om ett språk inte stöds genereras en varning och texten delas upp vid tecken gränser.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
