---
title: Referens indata och utdata i färdighetsuppsättningar
titleSuffix: Azure Cognitive Search
description: Förklarar anteckningens syntax och hur du refererar till en anteckning i indata och utdata för en färdigheter i en pipeline för AI-anrikning i Azure Kognitiv sökning.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935542"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Så här refererar du till anteckningar i ett Azure Kognitiv sökning-färdigheter

I den här artikeln får du lära dig hur du refererar till kommentarer i kunskaps definitioner, med hjälp av exempel för att illustrera olika scenarier. När innehållet i ett dokument flödar genom en uppsättning kunskaper får den omfattande anteckningar. Anteckningar kan användas som indata för ytterligare nedströms berikning eller mappas till ett utmatnings fält i ett index. 
 
Exemplen i den här artikeln baseras på *innehålls* fältet som genereras automatiskt av [Azure Blob-indexerare](search-howto-indexing-azure-blob-storage.md) som en del av dokumentets cracking-fas. När du refererar till dokument från en BLOB-behållare ska du använda ett format, till exempel `"/document/content"` , där *innehålls* fältet är en del av *dokumentet*. 

## <a name="background-concepts"></a>Koncept i bakgrunden

Innan du tittar på syntaxen ska vi gå tillbaka till några viktiga begrepp för att bättre förstå de exempel som beskrivs längre fram i den här artikeln.

| Period | Beskrivning |
|------|-------------|
| Omfattande dokument | Ett berikat dokument är en intern struktur som skapas och används av pipelinen för att innehålla alla anteckningar relaterade till ett dokument. Tänk på ett berikat dokument som ett träd med anteckningar. En anteckning som skapats från en tidigare anteckning blir i allmänhet dess underordnade.<p/>Omfattande dokument finns bara för varaktigheten för färdigheter-körning. När innehållet har mappats till Sök indexet behövs inte längre det berikade dokumentet. Även om du inte interagerar med berikade dokument direkt, är det praktiskt att ha en psykiska modell av dokumenten när du skapar en färdigheter. |
| Anriknings kontext | Sammanhanget där berikning sker, vad gäller vilket element som är berikat. Som standard är anriknings kontexten på `"/document"` nivån, begränsad till enskilda dokument. När en kunskap körs, blir utmatningarna för den aktuella egenskapen [Egenskaper för den definierade kontexten](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Exempel 1: enkel antecknings referens

I Azure Blob Storage antar vi att du har en rad olika filer som innehåller referenser till personens namn som du vill extrahera med hjälp av enhets igenkänning. I kunskaps definitionen nedan `"/document/content"` är en text representation av hela dokumentet och "människor" är en extraktion av fullständiga namn för entiteter som identifieras som personer.

Eftersom standard kontexten är `"/document"` kan listan över personer nu refereras som `"/document/people"` . I det här fallet `"/document/people"` är en anteckning som nu kan mappas till ett fält i ett index eller som används i en annan färdighet i samma färdigheter.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Exempel 2: referera till en matris i ett dokument

Det här exemplet bygger på den tidigare, som visar hur du anropar ett anriknings steg flera gånger i samma dokument. Anta att det tidigare exemplet genererade en sträng mat ris med 10 person namn från ett enda dokument. Ett rimligt nästa steg kan vara en andra anrikning som extraherar efter namnet från ett fullständigt namn. Eftersom det finns 10 namn vill du att det här steget ska kallas 10 gånger i det här dokumentet, en gång för varje person. 

Om du vill anropa rätt antal iterationer anger du kontexten som `"/document/people/*"` , där asterisken ( `"*"` ) representerar alla noder i det berikade dokumentet som underordnade för `"/document/people"` . Även om den här kunskapen bara definieras en gång i kunskaps mat ris, anropas den för varje medlem i dokumentet tills alla medlemmar bearbetas.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

När anteckningar är matriser eller samlings samlingar kan du vilja rikta in sig på specifika medlemmar i stället för matrisen som helhet. Exemplet ovan genererar en anteckning `"last"` som kallas under varje nod som representeras av kontexten. Om du vill referera till den här serien med anteckningar kan du använda syntaxen `"/document/people/*/last"` . Om du vill referera till en viss anteckning kan du använda ett explicit index: `"/document/people/1/last` "för att referera till efter namnet på den första person som identifierats i dokumentet. Observera att i denna syntax är "0 indexerad".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Exempel 3: referens medlemmar inom en matris

Ibland måste du gruppera alla anteckningar av en viss typ för att skicka dem till en viss färdighet. Överväg en hypotetisk anpassad färdighet som identifierar det vanligaste efter namnet från alla efter namn som extraherats i exempel 2. Om du bara vill använda de sista namnen på den anpassade kunskapen anger du kontexten som `"/document"` och inmatat som `"/document/people/*/lastname"` .

Observera att kardinalitet för `"/document/people/*/lastname"` är större än dokumentet. Det kan finnas 10 LastName-noder när det bara finns en dokument-nod för det här dokumentet. I så fall kommer systemet automatiskt att skapa en matris  `"/document/people/*/lastname"` som innehåller alla element i dokumentet.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Se även
+ [Så här integrerar du en anpassad färdighet i en anriknings pipeline](cognitive-search-custom-skill-interface.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa färdigheter (REST)](/rest/api/searchservice/create-skillset)
+ [Så här mappar du omfattande fält till ett index](cognitive-search-output-field-mapping.md)