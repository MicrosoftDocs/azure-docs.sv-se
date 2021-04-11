---
title: Villkorlig kognitiva kunskaper
titleSuffix: Azure Cognitive Search
description: Den villkorliga kunskapen i Azure Kognitiv sökning möjliggör filtrering, skapande av standardvärden och sammanslagning av värden i en färdigheter-definition.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666356"
---
# <a name="conditional-cognitive-skill"></a>Villkorlig kognitiva kunskaper

Den **villkorliga** kompetensen gör det möjligt för Azure kognitiv sökning scenarier som kräver en boolesk åtgärd för att fastställa vilka data som ska tilldelas till utdata. I dessa scenarier ingår filtrering, tilldelning av ett standardvärde och sammanslagning av data baserat på ett villkor.

Följande pseudocode visar vad den villkorliga kompetensen uppnår:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Den här kompetensen är inte kopplad till ett Azure Cognitive Services-API och du debiteras inte för att använda den. Du bör dock fortfarande [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md) för att åsidosätta resurs alternativet "ledig" som begränsar dig till ett litet antal berikningar per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. util. ConditionalSkill


## <a name="evaluated-fields"></a>Utvärderade fält

Den här kompetensen är speciell eftersom dess indata är beräknade fält.

Följande objekt är giltiga värden för ett uttryck:

-   Antecknings Sök vägar (sökvägar i uttryck måste avgränsas med "$ (" och ")")
 <br/>
    Exempel:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Litteraler (strängar, siffror, sant, falskt, null) <br/>
    Exempel:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Uttryck som använder jämförelse operatorer (= =,! =, >=, >, <=, <) <br/>
    Exempel:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Uttryck som använder booleska operatorer (&&, | |,!, ^) <br/>
    Exempel:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Uttryck som använder numeriska operatorer (+,-, \* ,,/,%) <br/>
    Exempel: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Eftersom den villkorliga kompetensen stöder utvärdering kan du använda den i scenarier med mindre omvandling. Se till exempel [kunskaps definition 4](#transformation-example).

## <a name="skill-inputs"></a>Kompetens inmatningar
Indata är skiftlägeskänsliga.

| Indata   | Beskrivning |
|-------------|-------------|
| moduletype   | Indatatypen är ett [utvärderat fält](#evaluated-fields) som representerar villkoret som ska utvärderas. Det här villkoret bör utvärderas till ett booleskt värde (*Sant* eller *falskt*).   <br/>  Exempel: <br/> "= sant" <br/> "= $ (/Document/Language) = = ' fr ' ' <br/> "= $ (/Document/Pages/ \* /Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | Den här indatatypen är ett [utvärderat fält](#evaluated-fields) som representerar värdet som ska returneras om villkoret utvärderas till *Sant*. Konstanter strängar ska returneras inom enkla citat tecken ("och"). <br/>Exempel värden: <br/> "="-kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/entities/ \* )" <br/> |
| whenFalse   | Den här indatatypen är ett [utvärderat fält](#evaluated-fields) som representerar värdet som ska returneras om villkoret utvärderas till *false*. <br/>Exempel värden: <br/> "="-kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/entities/ \* )" <br/>

## <a name="skill-outputs"></a>Kunskaps utmatningar
Det finns ett enda utdata som bara kallas "utdata". Den returnerar värdet *whenFalse* om villkoret är falskt eller *whenTrue* om villkoret är sant.

## <a name="examples"></a>Exempel

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Exempel på kunskaps definition 1: filtrera dokument för att endast returnera franska dokument

Följande utdata returnerar en matris med meningar ("/document/frenchSentences") om språket i dokumentet är franska. Om språket inte är franska anges värdet *Null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Om "/document/frenchSentences" används som *kontext* för en annan färdighet, körs den kunskapen bara om "/Document/frenchSentences" inte är inställt på *Null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Exempel på kunskaps definition 2: Ange ett standardvärde för ett värde som inte finns

Följande utdata skapar en anteckning ("/document/languageWithDefault") som är inställd på språket för dokumentet eller "es" om språket inte är angivet.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Exempel på kunskaps definition 3: sammanfoga värden från två fält till ett

I det här exemplet har vissa meningar en *frenchSentiment* -egenskap. När *frenchSentiment* -egenskapen är null vill vi använda *englishSentiment* -värdet. Vi tilldelar utdata till en medlem som heter *sentiment* ("/Document/sentences/*/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Transformerings exempel
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Exempel på kunskaps definition 4: data omvandling i ett enda fält

I det här exemplet får vi en *sentiment* som är mellan 0 och 1. Vi vill omvandla det till mellan-1 och 1. Vi kan använda den villkorliga kompetensen för att göra denna mindre omvandling.

I det här exemplet använder vi inte den villkorliga aspekten av kunskapen eftersom villkoret alltid är *Sant*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Särskilda överväganden
Vissa parametrar utvärderas, så du måste vara särskilt noga med att följa dokumenterat mönster. Uttryck måste börja med ett likhets tecken. En sökväg måste avgränsas med "$ (" och ")". Se till att lägga till strängar inom enkla citat tecken. Det hjälper utvärderaren att skilja mellan strängar och faktiska sökvägar och operatorer. Se också till att lägga till blank steg runt operatorer (t. ex. a "*" i en sökväg betyder något annat än multiplicering).


## <a name="next-steps"></a>Nästa steg

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
